# Sign-in and infrastructure

Notes from adding Microsoft sign-in and planning Terraform for the Puzzles apps (October 2026).

## Sign-in options

Both apps sign in through Supabase with a browser redirect (PKCE). Any OAuth provider Supabase supports fits that same flow.

| Option | Cost | Notes |
| --- | --- | --- |
| Google | Free | In use. |
| Microsoft | Free | Being added. Personal (Outlook, Hotmail, Xbox) and work accounts. Supabase's `azure` provider; needs the `email` scope. |
| GitHub, Discord, GitLab, Twitch, Spotify, Slack, Notion, LinkedIn, Zoom, X | Free | Same flow; small audiences for a puzzle app. |
| Email magic link or code | Free | Needs your own SMTP sender: Supabase's built-in one only emails project members. Sends best from your own domain, which `github.io` isn't. |
| Facebook | Free | Meta app review and business verification before public use. |
| Passkeys | Free | Android passkeys need `/.well-known/assetlinks.json` at the domain root, which `joclegg.github.io/puzzles-web` can't serve. |
| Anonymous | Free | Doesn't sync between devices until linked to a real login. |
| Apple | $99 a year | Apple Developer Program. |
| Phone or SMS | Per message | Twilio or similar. |

Supabase links logins that share the same verified email address into one account.

## Microsoft sign-in

- [joclegg/puzzles-web#17](https://github.com/joclegg/puzzles-web/pull/17): "Sign in to sync" opens a choice of Google or Microsoft.
- [joclegg/puzzles-android#17](https://github.com/joclegg/puzzles-android/pull/17): the same choice on the launcher (`SignInProvider`, `SignInDialog`).
- The button works once the Azure app registration exists and Supabase's Azure provider is on. Both come from `puzzles-infra` (below).

### Switching accounts

- Android: signing out clears the whole `sync` DataStore, including what has been uploaded, so the next sign-in, to any account, uploads every game and changed setting on the phone.
- Web: signing out used to keep its upload records, so a new account only got later changes. puzzles-web#17 makes sign-out queue every stamped game and setting again, to match Android.
- Nothing is deleted on sign-out, on the device or on the server. A login with a different email address starts as a new, empty account until a device uploads to it.

## puzzles-infra

A new repository, `joclegg/puzzles-infra`, holds all Terraform for the apps, not only Azure. The database schema stays in `puzzles-backend`.

The first version (end state only, no bootstrap code):

- `microsoft.tf`: the `Puzzles` app registration in Azure. Personal and work accounts, redirect to `https://nrlmaucpuesfmvqhtupe.supabase.co/auth/v1/callback`, `email` and `xms_edov` ID token claims, and a client secret replaced every 6 months.
- `supabase.tf`: turns on Supabase's Azure provider with that client ID and secret. Only those auth keys are managed.
- `Terraform` workflow: `fmt`, `validate`, plan on pull requests, apply after each merge to `main`.
- State in HCP Terraform, workspace `puzzles`, execution mode Local, so runs happen in GitHub Actions.

Bootstrap, done once by a local agent, not kept in the repository:

- An Azure identity for GitHub Actions, signing in with OIDC: federated credentials for `repo:joclegg/puzzles-infra:ref:refs/heads/main` and `repo:joclegg/puzzles-infra:pull_request` (audience `api://AzureADTokenExchange`), and the Microsoft Graph application permission `Application.ReadWrite.OwnedBy` with admin consent.
- The HCP Terraform organisation and the `puzzles` workspace.
- In `puzzles-infra`: variables `TF_CLOUD_ORGANIZATION`, `AZURE_CLIENT_ID`, `AZURE_TENANT_ID`; secrets `TF_API_TOKEN`, `SUPABASE_ACCESS_TOKEN`, `PUZZLES_DISPATCH_TOKEN`.
- Adding `puzzles-infra` as a submodule here, and to `update-submodules.yml`.

## Existing infrastructure to bring into Terraform

1. Supabase (`supabase/supabase` provider)
   - The project `nrlmaucpuesfmvqhtupe`, imported rather than recreated.
   - Auth settings set in the dashboard: site URL, redirect URLs (the website and `com.joclegg.puzzles://auth`), sign-ups, anonymous sign-in off, token lifetimes.
   - The Google provider, with its client ID and secret.
2. GitHub (`integrations/github` provider)
   - Settings for the five repositories, and branch protection if used.
   - GitHub Pages for puzzles-web.
   - Actions secrets: `SUPABASE_ACCESS_TOKEN` (puzzles-backend), `PUZZLES_DISPATCH_TOKEN` (web, android, backend, infra) and the Android signing secrets `PUZZLES_KEYSTORE_BASE`, `PUZZLES_KEYSTORE_PASSWORD`, `PUZZLES_KEY_ALIAS`, `PUZZLES_KEY_PASSWORD`. Terraform can't make the tokens or the keystore; each value is stored once as a `puzzles-infra` secret and copied out.

Left out: the database schema (migrations in puzzles-backend) and the repositories' workflows.

## The Google sign-in client

There's no infrastructure-as-code way to make it, as far as known in mid-2026:

- Google has no public API for consumer "Sign in with Google" OAuth clients or the consent screen, so Terraform, gcloud, Deployment Manager, Infrastructure Manager and Config Connector can't make them.
- The IAP OAuth Admin API (`google_iap_brand`, `google_iap_client`, `gcloud iap oauth-*`) only made organisation-internal clients, and was deprecated in January 2025 with shutdown due in early 2026.
- `gcloud iam oauth-clients` (`google_iam_oauth_client`) is for Workforce Identity Federation, not app users.
- Firebase and Identity Platform's Google provider still takes a client ID and secret made by hand.

So: make the client and consent screen once in Google Cloud Console, with redirect URI `https://nrlmaucpuesfmvqhtupe.supabase.co/auth/v1/callback`; store the ID and secret as `puzzles-infra` secrets; Terraform sets them in Supabase. Terraform can still manage the Google Cloud project, its APIs and its access. Google client secrets don't expire, so there's no rotation to automate.

# OpenV — txAdmin integration trace

Status: **reviewed source, not implemented** · 2026-10-14

This fork remains licensed under its upstream MIT license; retain copyright/license notices. This document describes intended OpenV integration without changing existing behavior.

## Confirmed source and behavior

- `core/modules/AdminStore/providers/CitizenFX.ts`: hard-coded `https://idms.fivem.net` OIDC issuer, authorize/token/userinfo/JWKS endpoints and `txadmin_test` client configuration.
- `core/routes/authentication/oauthMethods.ts`: sets OAuth state, performs authorization-code exchange, fetches profile.
- `core/routes/authentication/providerRedirect.ts` / `providerCallback.ts`: redirects and authenticates admins; maps returned `nameid` to legacy `fivem:` identifier.
- `core/lib/player/idUtils.ts`: parses identifiers and extracts legacy `fivem:` identity from Cfx forum-style nameid.
- `core/routes/authentication/changeIdentifiers.ts`: looks up `https://policy-live.fivem.net/api/getUserInfo/{id}`.
- `core/routes/authentication/verifyPassword.ts`: already offers local password authentication; must not be broken.
- `core/modules/UpdateChecker/queryChangelogApi.ts`: queries `https://changelogs-live.fivem.net/api/changelog/versions/{win32|linux}/server`.
- `core/modules/UpdateChecker/index.ts`: periodic update check and UI event.
- `core/modules/FxRunner/`, `FxMonitor/`, `FxPlayerlist/`: existing server management must remain operational.
- `core/modules/DiscordBot/`: optional integration, not a requirement for community authentication.

## OpenV work packages

1. Introduce a configurable community OIDC provider backed by the community-operated Go identity service. Maintain PKCE/state/CSRF controls and scoped admin permissions. Do not use legacy nameid parsing as the canonical OpenV identity.
2. Keep existing local administrator login and provide an explicit, auditable account-linking workflow; never silently merge accounts by matching display names.
3. Preserve server control, bans/warnings, allowlists, player IDs, deployment recipes, resource controls and monitoring.
4. Replace update checks with an OpenV-controlled signed release manifest, with working local status if offline.
5. Audit any Cfx-specific links and embedded resource/API integrations without removing optional Discord or third-party integrations.
6. Add migration and regression tests for historical admin and player records; verify permissions and session expiry.

## Acceptance

Admin can use local password login or properly configured OpenV identity; permissions and historical actions survive migration; server management works with OpenV; no mandatory Cfx identity or update calls; security review and real FXServer/OpenV integration tests pass.

Unverified: complete txAdmin dependency inventory, interoperability with a built OpenV runtime, exact bundled txAdmin version within supplied FXServer artifact. Do not label complete until tested.

# Evidence: Elliot Braem's review comments

Source: 115 comments by `elliotBraem` on 39 PRs in NEARBuilders/* and MultiAgency/* created 2026-03-22 → 2026-09-22 (`activity`, `citynode.app`, `nearbuilders.org`, `nearmerch.com`, `nostr.nearbuilders.org`, `dashboard`). Plus nearmerch.com#34, #44, #45, #53, #64, #65, #70, #80 and near-social-js#59 (Dec 2025), used for blind calibration tests. Quotes are verbatim; counts are approximate.

## Shape verdicts (step 4)
- nearmerch.com#65, CSS polish mixed with a login rewrite: "I'd like if this PR was limited to just the CSS fixes, and the update to the header. Revert the login change"; "I think all these small CSS fixes are the most worthwhile to get in"
- nearmerch.com#64: "I think there are some pieces here worth merging, but I'm not entirely convinced by the full benefits to do it"; "These are nicer UX improvements, but maybe we should workshop the login page as a whole"
- nearmerch.com#44, issue specified the approach: "No, look at issue; it's based on image type"
- nearmerch.com#64 / #65, feature made obsolete by an earlier PR: "We can just remove this since we no longer are doing account linking"; "See also #64, we may just want to remove the full account linking now that we no longer have google or github connected"
- nearmerch.com#70, simplify: "Good progress, but we should be able to simplify and clean up a lot of this up"

## 1. Infer, don't redeclare (~12)
- nearbuilders.org#38 `ui/src/lib/queries/activity.ts`: "This should be inferred by API client" … "Anywhere else in queries too. We should never re-instate types when we can infer them direct from the apiClient (through the oRPC contract)"
- nearbuilders.org#37 `plugins/activity/src/services/activity.ts`: "Derive these from the schema in /contract"
- nearbuilders.org#41: "infer the project type from apiClient"
- nearbuilders.org#72 / #77: "Can this type be inferred from the apiClient?"
- nearbuilders.org#162 `nostr-feed.tsx`: "infer types from apiClient"
- nearbuilders.org#202: "This could be inferred from contract" / "I think this can be inferred from somewhere. Or doesn't need to be redefined"
- citynode.app#136 `ui/src/lib/team-workspace.ts`: "Try and infer from authClient schema's, rather than redefine own types"
- nearmerch.com#163: "Something like this shouldn't be redefined, and instead inferred from schema or api contract"

## 2. Workarounds are a smell (~9)
- nearmerch.com#45 chained wallet popup: "Remove this; you can't have a popup success indirectly open another popup. Must come from click action … This change instead needs to come from wallet side … But this is work for another day"
- nearbuilders.org#202 `scripts/fix-generated-auth-types.mjs`: "Remove this" ×2, then "What was the error? You were missing GetFullOrganizationInput in code?"
- nearbuilders.org#101 `Promise.race` timeout on `getProfile`: "Why did this get put in?"
- nearbuilders.org#72 `(context as any).near?.primaryAccountId`: "create a ticket on …better-near-auth with trace to resolve this issue in auth plugins"
- nearbuilders.org#162 `relay-proxy.mjs`: "Remove this, made an upstream change to …everything-dev/host to allow wss: connectSrc"
- dashboard#25 `createRpcLink`: "this was a workaround, probably cuz rspack was failing"; `router.server.tsx`: "shouldn't need these workarounds"; `connectNear`: "promise wrapped is bad, just use authClient directly"
- dashboard#27 `updateHostConfig`: "this was a workaround, removed"

## 3. Belongs upstream / inherit from parent (~9)
- nearbuilders.org#80 MCP plugin: "I wonder if this can be derived from open api spec" → closed: "made upstream change to host … MCP available here: /api/mcp"
- nearbuilders.org#162: "nostr-bindings and nostr-comments should be the EXACT same plugins as what is in nostr.nearbuilders.org … or even better, have nostr.nearbuilders.org own these plugins, and we just load them remotely through the bos.config.json"; "Make a PR with any changes to the nostr.nearbuilders.org"
- nearbuilders.org#109, #116: "Closing in favor of …feedback.nearbuilders.org"
- dashboard#25 `__root.tsx`: "have this come from bos.config.json. consider __root a framework owned file. You can always set title & description in a route's head"
- dashboard#25 `bos.config.json`: "no need to describe host or auth -- these will stay up to date with parent (dev.everything.near)"
- dashboard#27: "children repo's don't need renovate"; docker-compose: "just keep consistent with parent"

## 4. Scope: every line earns its place (~12)
- near-social-js#59: demo dependency "What is this?"; bundler config change "What?"; compat fallback "????"
- nearbuilders.org#162: "remove this mcp plugin, not related"; class change `bg-background`→`bg-card`: "revert"
- nearbuilders.org#41: `const noop = () => {}` "What's this?"; `ProfileTabButton` "What???"; readOnly cards: "That wasn't clear enough, but we don't need readOnly activity cards. Revert that"
- nearbuilders.org#12 `expectedOwnerId`: "What's this??"; "I have a feeling I know why you did this, but we'll remove this interface"
- nearmerch.com#182: "I don't like that you changed it to an enum from the projects types that are loaded from db"
- nearmerch.com#44: "Don't need to rename from product"; `package.json` script edit: "No stop it lol"
- nearmerch.com#53 `plugin.dev.ts`: "Revert all changes to this file"; new dependency: "Did you need this?"

## 5. State mirrors the data model (nearmerch.com#44)
- "I don't think we need to maintain an activeProduct on product page, need selectedVariant or activeVariant"
- "This is too hardcoded, it can just be derived from the data"; "Query the product data, look at it from API; you'll notice you can remove your color mapping here"
- "We don't need to check for variant afterwards; Think of it this way: we are loading a product page with product data, product has variants, which we can select (one is selected by default…). We can see the available options for Size, Color"
- "we should just do: addToCart(selectedVariant). Rest seems like a code smell"

## 6. Pre-release: one path (nearmerch.com#70)
- `getProduct(slugOrId)`: "Do we need this? What's the backwards compatibility for? getProduct would always be a publicKey lookup. Don't pass slug here"; "we ALWAYS know the last 12 characters are publicKey, we don't gotta check so much … publicKey is our source of truth and our operations should be simple because of this"
- store: "I don't think you need this method; we just always do a find with public Key"; upsert: "Revert all this; don't need migration specific, conditional code in an upsert"
- review: "our 'sync' doesn't gotta be complicated or do a workaround; our db migration should handle this and if it doesn't, we just drop all our products and do a fresh sync that way"
- nostr.nearbuilders.org#6: delete the DB-backed path in a follow-up "rather than dual-writing"

## 7. Less code (~11)
- nearbuilders.org#16: "Way too much code change, too many gaurdrails everywhere; just need to check it when proposal is created"; "It looks like we have this in three different places. Only need it in either api or builder plugin"
- nearbuilders.org#218: "I'm wondering if it would make more sense to have a status field on a proposal … I don't love the withdrawal specific proposal flow"
- nearmerch.com#163: "do we really need a new manual_fulfillments table? Why not just use orders and provider config"; "This all feels like too much of a separate flow from the others"
- nearmerch.com#179: "We support multiple providers and don't want this custom code to grow too big, so what if we did a config instead: { [providerId]: { unsupportedCountryCodes, restrictedStateKeywords, shippingRegions } }"
- citynode.app#98: "Can we collapse all of these migrations into a single?"; nearmerch.com#70: "Why the two migrations? … just generate one, or what issue were you running into??"; `sync-db.ts`: "Duplicated utility functions"

## 8. Reuse what exists (~9)
- nearmerch.com#34: "social.getProfile"; `import type { Profile } from "near-social-js"`; props: "Let's remove the near and fix: accountId: string, profile: Profile"; "Don't need this method"
- citynode.app#136 `team-auth.ts`: "Fold this into existing lib/auth, it should be able to use the existing auth gen"; "See packages/everything-dev contract.ts and auth.ts"; sidebar: "I don't think we gotta pass it around as a prop so much. It should be very similiar to how organizations already work"; `"api": "workspace:*"`: "Shouldn't be necessary"
- nearmerch.com#64 error-code mapping: "Don't the error messages from better-near-auth already cover these codes? Basically same messages"
- nearmerch.com#53 extra dependency: "We need this import still? I think better-near-auth covers it"

## 9. Framework-idiomatic (~10)
- Dev through host: nearmerch.com#64 "Revert, even in dev it goes through the host. window.location.origin works in both"; nearmerch.com#44 bundler port change: "Don't make this change, make sure you're doing: bun run dev. Opening from host, localhost:3001"; nearbuilders.org#218: "just bun bos dev from the root, not from /ui"
- Router guards: nearmerch.com#65 "/account is a protected route that will check session and redirect to login already, so we don't need a session check here in the header. Could just be <Link to \"/account\" /> always, let the router handle the rest"
- dashboard#25: `useMatchRoute` "tanstack router idiomatic"; "you want to load data in loader; that means it happens server side and data is ready when client loads"; `_admin.tsx beforeLoad` "notice how we check admin here"
- nearbuilders.org#12 pathname checks: "I don't like conditionally checking routes, this is nesting problem"
- nearmerch.com#53 hand-parsed search params: "You could use a zod schema here instead, much cleaner"

## 10. Data and identifiers (~5)
- nearbuilders.org#159 (CHANGES_REQUESTED): "prompt for user id instead of discord username … usernames can change. User id's are always numeric, you can validate against this"
- nearbuilders.org#77: "I would prefer comma separated list" (env var with a JSON default)
- citynode.app#98 schema: "I'm kinda confused what some of these tables are"; nearmerch.com#163: "should this have it's own database?"

## 11. Silent regressions and dead features (~6)
- nearbuilders.org#101: "Notice we removed the prefetch of the proposals, their ids, and the upvotes -- is this intentional?"
- nearbuilders.org#202: "feedback and github don't really make sense since we don't have this activity data flowing -- can we comment it out for now?"
- nearmerch.com#163: "This doesn't even do anything"; "We still need to implement email?"
- citynode.app#75 cache rewrite: "am a little worried about this one"
- nearmerch.com#53 "coming soon" admin sections: "Nice; after we merge this; would like user management where accounts are managed by nearAccountId"

## 12. Leftovers (~7)
- citynode.app#136: "There are trace logs still in code too, should be cleaned up once all passes"
- nearbuilders.org#96: "Why are there so many builder not found logs?"
- citynode.app#98: "Should some of these test files be cleaned up? Or for browser tests, do them against the actual UI"
- nearbuilders.org#101: "should we delete the docs screenshots?"; nearmerch.com#53 committed `database.db`: "You can delete this, I added to gitignore"

## 13. Tests (nearmerch.com#80)
- `e2e/full-flow.spec.ts` login guard: "This is a bad guess. Setup playwright with a authenticated storage state, then it won't go to login"; `playwright.config.ts`: "Add the storage for auth here"
- "For full E2E test; look at the checkout and webhook tests; you can use the same mock requests … do a successful callback with payment SUCCESS. Do a test with callback payment failed"
- `package.json`: "Can you add a \"tests.yml\" to github actions that run the playwright tests; should use bun"
- `marketplace.spec.ts`: "Flaky, should use a dataid or some better playwright identifier … Playwright best practices: https://playwright.dev/docs/best-practices"

## 14. CI and supply chain (~7)
- dashboard#25: "changes to github actions are security related: pinning version hashes, scoping secrets to proper workflows"; "you upgraded these to v2, but we're not ready for v2"
- dashboard#24, #26 (dependabot): "Don't merge, close pls"
- citynode.app#98 CSP: "Was this necessary?? I think the [\"https:\"] covers it, ya?"
- citynode.app#136: pasted the failing Playwright output; nearbuilders.org#202/#203/#204: "Pull from main, CI should pass now"

## 15. Product sense (~5)
- nearbuilders.org#17: "This looks more like a dashboard rather than a profile. Can you implement a /dashboard?"
- nearbuilders.org#153: "Optionally could just drop the anonymous entirely" → "Remove anon"
- nearmerch.com#65: "We should also move this admin button to the footer"
- activity#51: "Can you please make the other fixes … https://metatags.io/?url=…"
- nearmerch.com#53: "It was a nice thought, but just navigating to /account is fine"; "Just alwways redirect to /account"

## What he leaves alone
On nearmerch.com#53 (911+/129−, 18 files) he left 9 comments. None were about missing server-side admin auth, missing migrations, or an open redirect. Across all PRs he doesn't comment on formatting.

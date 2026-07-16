# AWS-Amplify

## AWS Amplify Hosting Skew Protection Deployments
**The problem it solves**

When you deploy a new version of your app, there's a moment where some users are still loaded with the *old* frontend code (JS/CSS) in their browser, while your servers have already switched to serving the *new* backend/API. If the old frontend tries to call the new backend (or vice versa), things can break — you get errors, 404s, or weird glitches. This mismatch is called "version skew," and it's especially common with apps that get deployed often.

**What happens when you enable skew protection**

Amplify starts "pinning" each visitor to the exact deployment version their browser first loaded, using a cookie (called `_dpl`) or, for more advanced cases, a header/query parameter. This ensures that Amplify always serves the assets for that particular deployment, keeping the client and server synchronized, even if you push new deployments while they're still using the site. So instead of a user's old page suddenly trying to talk to a brand-new server and breaking, they keep talking to the *matching* version of the server until they refresh and get the new one naturally.

A few practical notes:
- For static apps, Amplify keeps serving one week of past deployments; for server-rendered (SSR) apps, it guarantees skew protection for up to eight previous deployments.
- There's no extra cost for turning this on.
- Only deployments made *after* you turn it on are protected — it doesn't retroactively fix past deploys.
- Turning it on requires Amplify to update its CDN cache settings, so your next deployment after enabling it can take a bit longer (up to ~10 minutes).
- You can't use it on apps still on the old WEB_DYNAMIC platform (Next.js 11 or earlier).

**What happens when you disable it**

Amplify stops pinning users to specific deployments — it goes back to only serving the latest content to everyone. So the moment you deploy something new, *all* visitors immediately get the new version, regardless of what they had loaded before. This is simpler, but it re-introduces the risk of that old-client-meets-new-server mismatch during the transition.

**In short:** enabled = "keep each visitor consistent with the version they started on, avoid breakage during deploys." Disabled = "everyone always gets the newest version instantly, but you're back to risking version-mismatch errors during a deploy."

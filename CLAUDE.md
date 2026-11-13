# JMW Replies: Instagram DM reply tracker

Context handoff from a Claude chat (13 Nov 2026). Read this first.

## Goal
Jen (Jenni Mae Wellness) gets hundreds of DMs from Stan's automated DM sequences. People who reply (usually "thank you") get lost in the slow Instagram app. Carlo wants one phone screen listing everyone waiting on a reply, with replying from the same screen.

Full spec (Claude Doc): https://claude.ai/artifact/WZiuGSjyAzRLgvgzksxjGp

## What it is
- A single static `index.html` (no build, no server), hosted on GitHub Pages, added to the iPhone home screen.
- Repo to create: `jmw-replies` (public, GitHub Pages from `main` / root).
- The Meta access key is pasted into the page once and stored only in the phone's localStorage. Never commit a key.

## v1 features (built, in index.html)
1. Waiting list: every chat where the other person sent the last message, newest first, last 7 days.
2. Reply timer per chat (24h window; greyed when passed).
3. Chat view: last 20 messages, text only.
4. Text reply from the page (POST /{ig_id}/messages).
5. "Open in IG" fallback button (https://ig.me/m/{username}).
Also: loading progress bar + status line, list fills in page by page (25 chats/request), 25s timeout per request, per-chat draft saving.

## NOT building (v1)
Lead tags/labels, AI lead detection, push notifications, automated or bulk replies, starting new DMs, 7-day Human Agent tag (needs App Review), photos/voice notes, mark-as-read, search/history/CRM, analytics, multiple accounts, any server/backend, App Store app.

## Key facts learned
- New Meta apps only offer "API setup with Unified login" (not "Instagram login"). Its tokens start with `EAA` and must use `graph.facebook.com/v26.0`, addressing the IG account by ID (not /me). graph.instagram.com rejects EAA tokens.
- The page supports both: `IGAA...` keys use graph.instagram.com + /me; `EAA...` keys use graph.facebook.com + /{ig_id}. For EAA it tries /me/accounts to find the IG account, else the user enters the Instagram account ID on the setup screen.
- Calling Meta directly from the browser works (CORS OK; confirmed by a real Meta error response in the browser).
- claude.ai artifact pages CANNOT call Meta (CSP blocks external fetch), hence GitHub Pages.
- Permissions needed: `instagram_business_basic`, `instagram_business_manage_messages` only.
- Jen's Instagram must have Settings > Messages and story replies > Message controls > Connected tools > "Allow access to messages" ON.
- Reply window: 24h after their last message (up to 7 days for Click to Direct ad chats). No cold DMs via API.
- Stan (Stan Store auto-DMs) is probably the default app under Meta Conversation Routing, so replies from our page might be refused ("not thread owner") or take thread control from Stan. Must test on one chat first; fallback is "Open in IG".
- Stan sends no follow-ups. Edge case accepted: if someone comments again, Stan's new auto-DM becomes the last message and the chat drops off the list until they reply.
- Only the 20 most recent messages per conversation can be read via API.

## Status
- index.html built and tested against mocked data (list, timer, chat view, send).
- Meta app "Replies" created by Carlo (use case: Manage messaging & content on Instagram, Unified login).
- A first EAA key was exposed in a screenshot: generate a fresh one and revoke the old (Business settings > Users > System users > Revoke tokens).
- Not yet tested against Jen's real inbox. Not yet hosted.

## Next steps
1. Create repo `jmw-replies`, commit index.html (never a key), enable GitHub Pages.
2. Open https://<user>.github.io/jmw-replies/ on iPhone Safari, Add to Home Screen, paste a fresh EAA key (+ IG account ID if asked).
3. Test: list matches Instagram for 5 recent chats; DM from a personal account appears; send one reply; confirm Stan still triggers for a new person.
4. If load is slow, check the final status line (chats checked, seconds) and tune LOOKBACK_DAYS / PAGE_SIZE.

## Carlo's working preferences
Keep it simple. No em dashes. Challenge unclear requirements. Build order: question requirements, delete, simplify, accelerate, automate.

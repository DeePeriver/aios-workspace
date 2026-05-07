# Tennis Radar — Setup Walkthrough

> A non-technical, hand-held setup. Follow it top to bottom — the radar will work the first time.

---

## For Claude

You are walking a non-technical owner through setup. Follow these rules:

- Assume the user is non-technical unless they tell you otherwise.
- Explain what you are doing in plain English **before** doing it.
- Celebrate small wins ("Supadata is connected — the radar can now pull tennis videos and transcripts from YouTube!").
- If something fails, do not dump error logs. Explain the problem simply and suggest the fix.
- Never skip a verification step. If a check fails, stop and help the user fix it before moving on.
- Pause at the milestones marked below ("Ask:") so the owner can confirm before you continue.

---

## Overview

The tennis radar pulls from three sources:

1. **YouTube** (via the Supadata API — needs one free API key)
2. **Competitor websites** (via Claude's built-in WebFetch — no key, free)
3. **Open-web news** (via Claude's built-in WebSearch — no key, free)

This walkthrough sets up the **only** thing that needs setup: the Supadata key. Should take about 5 minutes.

---

## Prerequisites

We're checking your workspace has the basics. Run each command; if a check fails, the fix is below it.

### You're in your workspace root

```bash
ls
```

You should see folders like `context/`, `outputs/` (and probably a few others). If you don't, you're in the wrong directory — `cd` into your tennis workspace and run `ls` again.

### A `.env` file exists (or we'll make one)

```bash
ls .env 2>/dev/null && echo "already exists" || echo "needs creating"
```

We'll create it in the next step if it's missing. Nothing to do yet.

[VERIFY] Both checks above ran without errors.

**Ask:** "Everything looks good. Ready to set up your Supadata API key?"

---

## Get your Supadata API key

This part you do in your browser — Claude can't click for you. Go slow and don't worry, it's all free.

1. Open **https://supadata.ai** in your browser.
2. Click **Sign Up** (top right). Use Google sign-in or email — both are fine.
3. Once logged in, you'll land on the Dashboard. If not, click **Dashboard** in the top nav.
4. In the left sidebar, click **API Keys**.
5. Click **Create API Key**. Name it something memorable like `tennis-radar`.
6. **Copy the key** — it starts with `supa_`. Keep this tab open until the next step is done; you can't see the key again after you leave the page.

Free plan gives you 100 credits per month. The radar uses about 45 credits per run, so a bi-weekly cadence fits comfortably under the limit.

**Ask:** "Got your key copied? Paste it here when ready and I'll save it safely."

---

## Save the key to your workspace

The key goes into a hidden file called `.env` at your workspace root. This file holds all your API keys — never commit it to git, never share it.

Claude will handle the writing. Here's what's about to happen:

1. If `.env` doesn't exist yet, Claude creates it.
2. If `.env` exists, Claude **appends** the key — nothing already in the file gets overwritten.
3. If a `SUPADATA_API_KEY` line already exists with a different value, Claude stops and asks you before replacing it.

After saving, the file should look like this (your key will be different):

```
SUPADATA_API_KEY=supa_xxxxxxxxxxxxxxxx
```

[VERIFY]

```bash
grep SUPADATA_API_KEY .env
```

Expected: one line showing your key. If you see nothing, the save didn't work — re-paste the key and try again.

**Ask:** "Key is in place. Ready to test the connection?"

---

## Make sure `.env` won't reach git

If your workspace is a git repository, we need to make sure `.env` is in `.gitignore` so the key never gets committed. (Skip this section if you're not using git.)

```bash
ls .git 2>/dev/null && echo "git repo — continue" || echo "no git — skip this section"
```

If "git repo — continue":

1. Check whether `.gitignore` already lists `.env`:
   ```bash
   grep -E "^\.env" .gitignore 2>/dev/null && echo "already protected" || echo "needs adding"
   ```
2. If "needs adding", append `.env` to `.gitignore` (Claude does this with `Edit`, never with `Write` — appending only, no overwrite).

[VERIFY]

```bash
grep -E "^\.env" .gitignore
```

Expected: a line showing `.env` (or `.env*`).

---

## Test the connection

One quick API call to make sure the key works.

```bash
set -a && source .env && set +a && \
  curl -s -H "x-api-key: $SUPADATA_API_KEY" "https://api.supadata.ai/v1/me"
```

Expected response: a JSON blob showing your plan name and credit usage. Something like:

```json
{"plan":"free","usedCredits":0,"maxCredits":100}
```

If you see a JSON response with credit info — **the radar is connected**. Supadata is live, and Claude can now pull tennis content from YouTube any time you ask for a radar run.

**If you see `{"error":"Unauthorized"}` or a 401:**
- The key is wrong or has a typo. Open `.env` and check the line — it should be exactly `SUPADATA_API_KEY=supa_...` with no quotes, no spaces, no extra characters.
- If still failing, generate a fresh key at https://supadata.ai → Dashboard → API Keys and replace it.

**If you see a network error:**
- Your internet may be down. Try again in a minute.

---

## You're set

Setup is done. Here's what's now possible:

- Run a full radar: just say **"run the tennis radar"** and Claude does the rest.
- Watch one competitor: **"radar on Solinco"**.
- Quick check: **"any new tennis videos worth watching this week?"**

The radar reads `references/targets.md` to know which competitors to track. If you haven't filled that in yet, open it and replace the example competitors (Solinco, Luxilon) with your real ones — or keep them and add yours.

You can re-run this walkthrough any time: just ask Claude to "walk me through tennis radar setup" and they'll bring you back here.

# How to Publish TradePreflight to GitHub

## Before You Start

**Do NOT close your bhatiaverse VSCode window.**
You will open tradepreflight as a *separate* VSCode window alongside it.
Both can be open at the same time.

---

## Step 1: Install GitHub CLI (`gh`)

Open Terminal and run:

```bash
brew install gh
```

Wait for it to finish, then verify:

```bash
gh --version
```

You should see something like `gh version 2.x.x`

---

## Step 2: Log in to GitHub via CLI

```bash
gh auth login
```

It will ask you:
- **Where do you use GitHub?** → GitHub.com
- **What is your preferred protocol?** → HTTPS
- **Authenticate Git with your GitHub credentials?** → Yes
- **How would you like to authenticate?** → Login with a web browser

It will give you a one-time code → press Enter → browser opens → paste the code → authorize.

Verify it worked:
```bash
gh auth status
```

Should show: `Logged in to github.com as amanbhatia`

---

## Step 3: Open tradepreflight in a New VSCode Window

In Terminal:
```bash
code /Volumes/Work/projects/bhatiaverse/tradepreflight
```

Or in VSCode: **File → New Window** → **File → Open Folder** → navigate to `bhatiaverse/tradepreflight`

You now have two VSCode windows open — bhatiaverse and tradepreflight. That's correct.

---

## Step 4: Create the GitHub Repo

In Terminal (from anywhere):
```bash
gh repo create tradepreflight \
  --public \
  --description "Multi-agent order intelligence — run your preflight before every trade"
```

This creates `github.com/amanbhatia/tradepreflight` instantly.

---

## Step 5: Copy Agent Files from Bhatiaverse

In Terminal:
```bash
cd /Volumes/Work/projects/bhatiaverse/tradepreflight

cp ../app/api/order-intelligence/agents/behavioral.js agents/
cp ../app/api/order-intelligence/agents/structure.js agents/
cp ../app/api/order-intelligence/agents/pattern.js agents/
cp ../app/api/order-intelligence/agents/station.js agents/
cp ../app/api/order-intelligence/agents/oi.js agents/
cp ../app/api/order-intelligence/lib/resolve-token.js lib/
cp ../app/api/order-intelligence/lib/sector-map.js lib/
```

Verify files are there:
```bash
ls agents/ lib/
```

---

## Step 6: Initialize Git and Push

```bash
cd /Volumes/Work/projects/bhatiaverse/tradepreflight

git init
git remote add origin https://github.com/amanbhatia/tradepreflight.git
git add .
git commit -m "Initial release — TradePreflight v1.0.0"
git branch -M main
git push -u origin main
```

Open `https://github.com/amanbhatia/tradepreflight` in your browser — you should see the README rendered with everything.

---

## Step 8: Add Topics for Discoverability

```bash
gh repo edit bhatiaaman/tradepreflight \
  --add-topic trading \
  --add-topic zerodha \
  --add-topic kite-connect \
  --add-topic nifty \
  --add-topic options \
  --add-topic order-intelligence \
  --add-topic india \
  --add-topic algotrading
```

---

## Step 9: Create an Official Release (timestamps your authorship)

```bash
gh release create v1.0.0 \
  --title "TradePreflight v1.0.0" \
  --notes "Initial open-source release.

Five-agent order intelligence system for Indian markets built by Amandeep Bhatia.

Agents: Behavioral · Structure · Pattern · Station · OI

Licensed under Apache 2.0."
```

This creates a permanent timestamped release on GitHub — strongest form of public prior art.

---

## Step 10: Post the Tweet Thread

1. Open `TWEET_THREAD.md` in VSCode
2. Go to Twitter/X
3. Post **Tweet 1** first (the hook)
4. Reply to your own tweet with **Tweet 2**, and so on through Tweet 10
5. In Tweet 9, make sure the GitHub link is correct: `github.com/amanbhatia/tradepreflight`

---

## Step 11: Publish the Blog Post

**Recommended: Dev.to** (free, good SEO, timestamps publicly)

1. Go to [dev.to](https://dev.to) → Sign up / Log in with GitHub
2. Click **Write a post**
3. Open `BLOG_POST.md` → copy entire content → paste
4. Add tags: `trading`, `javascript`, `india`, `opensource`
5. Publish

The published date on Dev.to is your permanent public timestamp.

---

## Done ✓

Your authorship is now established through:
- GitHub repo with your name in README, LICENSE, and package.json
- Timestamped v1.0.0 release
- Tweet thread with your handle
- Blog post with your name and publish date

---

## Quick Reference — All Commands in Order

```bash
# 1. Install gh
brew install gh

# 2. Login
gh auth login

# 3. Create repo
gh repo create tradepreflight --public --description "Multi-agent order intelligence — run your preflight before every trade"

# 4. Copy files
cd /Volumes/Work/projects/bhatiaverse/tradepreflight
cp ../app/api/order-intelligence/agents/behavioral.js agents/
cp ../app/api/order-intelligence/agents/structure.js agents/
cp ../app/api/order-intelligence/agents/pattern.js agents/
cp ../app/api/order-intelligence/agents/station.js agents/
cp ../app/api/order-intelligence/agents/oi.js agents/
cp ../app/api/order-intelligence/lib/resolve-token.js lib/
cp ../app/api/order-intelligence/lib/sector-map.js lib/

# 5. Push
git init
git remote add origin https://github.com/amanbhatia/tradepreflight.git
git add .
git commit -m "Initial release — TradePreflight v1.0.0"
git branch -M main
git push -u origin main

# 7. Add topics
gh repo edit tradepreflight --add-topic trading --add-topic zerodha --add-topic kite-connect --add-topic nifty --add-topic options --add-topic order-intelligence --add-topic india --add-topic algotrading

# 8. Create release
gh release create v1.0.0 --title "TradePreflight v1.0.0" --notes "Initial open-source release by Amandeep Bhatia. Five-agent order intelligence for Indian markets. Apache 2.0."
```

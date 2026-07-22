# DEPLOY.md — Self-host the flaky Vercel widgets

Some third-party services used by `README.md` share a single Vercel deployment
owned by an individual maintainer. When they hit Vercel's free-tier bandwidth
limit (or the maintainer pauses the project), **every** README on GitHub that
points at their URL breaks simultaneously.

The permanent fix is to **run your own instance** on your own Vercel account
(free tier is more than enough for a single-user profile card). Everyone who
uses these widgets seriously eventually does this — it takes about 10 minutes.

## Services this repo depends on

| Service | Public URL (unstable) | Upstream repo |
|---|---|---|
| GitHub stats + top-langs | `github-readme-stats.vercel.app` | [anuraghazra/github-readme-stats](https://github.com/anuraghazra/github-readme-stats) |
| GitHub trophies | `github-profile-trophy.vercel.app` | [ryo-ma/github-profile-trophy](https://github.com/ryo-ma/github-profile-trophy) |
| Activity graph | `github-readme-activity-graph.vercel.app` | [Ashutosh00710/github-readme-activity-graph](https://github.com/Ashutosh00710/github-readme-activity-graph) |

## One-time setup

1. Create a free Vercel account: <https://vercel.com/signup> (sign in with GitHub).
2. Create a **fine-grained** GitHub Personal Access Token with **`read:user`** and **`repo`** scopes (only needed for private-repo counts): <https://github.com/settings/tokens>.
3. For each service you want to host, click the "Deploy" button on the upstream
   repo's README (they all provide a one-click Vercel deploy button), or use the CLI:

   ```bash
   # Example: self-host github-readme-stats
   git clone https://github.com/anuraghazra/github-readme-stats.git
   cd github-readme-stats
   npx vercel                # first run: link to your Vercel account
   npx vercel env add PAT_1  # paste your GitHub PAT when prompted
   npx vercel --prod         # promotes to production
   ```

4. Vercel gives you a URL like `https://github-readme-stats-abc123.vercel.app`.
5. In this repo's `README.md`, find the commented-out block near each affected
   card and swap the hostname:

   ```diff
   - src="https://github-readme-stats.vercel.app/api?username=santhosh-sathishkumar&..."
   + src="https://github-readme-stats-abc123.vercel.app/api?username=santhosh-sathishkumar&..."
   ```

6. Commit + push. Your cards now render from your own deployment and never
   break because of someone else's bandwidth bill.

## Free-tier limits to know

- Vercel Hobby: 100 GB bandwidth / month, 100 GB-hours function execution.
- A single profile README easily fits inside this — the images are SVG and
  aggressively cached by GitHub's `camo` proxy.

## Alternative: don't self-host

If you'd rather not run infra, `README.md` already falls back to `shields.io`
badges (which are effectively infinite-uptime), so the profile stays
presentable even when the upstream services are down.


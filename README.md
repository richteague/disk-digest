#  disk-digest

A Slack-bot that will scrape astro-ph and identify papers that would be relevant for your research group based on a series of keywords. It will then post a summary of these papers to the channel it is added to. It should also highlight authors in the Slack channel.

<br>

## Requirements

1. A Claude API key to generate the paper summaries. This generally costs around $0.02 per day. Note that a Claude API key is _separate_ to a standard Claude Code subscription.

2. The ability to build and install an app on your Slack workspace.

3. [Node.js](https://nodejs.org/) v18 or later.

<br>

## Setup

There are two stages for the installation. One is to build a Slack app that can post the summaries to the channel of your choice. The second is to install the scraper locally or another server than can be run daily.

### Slack App

1. Go to [api.slack.com/apps](https://api.slack.com/apps) and click **Create New App → From scratch**. Give it a name (e.g. "Disk Digest") and select your workspace.

2. In the left sidebar go to **OAuth & Permissions**. Under **Bot Token Scopes** add the following scopes:
   - `chat:write` — post messages
   - `chat:write.public` — post to channels without joining them
   - `users:read` — look up member names for author matching

3. Scroll to the top of the same page and click **Install to Workspace**, then **Allow**.

4. Copy the **Bot OAuth Token** (starts with `xoxb-...`) and paste it into your `.env` file as `SLACK_BOT_TOKEN`.

5. In Slack, right-click the channel you want the digest posted to → **View channel details** → scroll to the bottom to find the **Channel ID** (starts with `C...`). Paste this into your `.env` as `SLACK_CHANNEL_ID`.


### Local Server
1. Clone the repo and run `npm install`
2. Copy `.env.example` to `.env` and fill in your credentials:
   - `SLACK_BOT_TOKEN` — from your Slack app's **OAuth & Permissions** page
   - `SLACK_CHANNEL_ID` — right-click your channel in Slack → View channel details
   - `ANTHROPIC_API_KEY` — from **console.anthropic.com**
3. Run with `node disk-digest.js`

<br>

## Running Daily (Cron)

To have the digest post automatically each day, add a cron job. Open your crontab with `crontab -e` and add:

```
0 9 * * * cd /path/to/disk-digest && node disk-digest.js >> /tmp/disk-digest.log 2>&1
```

This runs the script every day at 9 AM. Adjust the time and path as needed. Note that arXiv typically posts new submissions around 00:00 UTC, so choose a time after that in your timezone.

<br>

## Modifying Keywords

The keywords can be found in `disk-digest.js` under `DISK_TERMS` and `CONTEXT_TERMS`. Any string can be added to these lists to refine the papers that are summarized.
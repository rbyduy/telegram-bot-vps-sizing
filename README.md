# VPS for Telegram Bot: How Much RAM Do You Need, What Should It Cost, and How Do You Keep It Online?

A command-and-reply Telegram bot idles at 50–150 MB of RAM, so a single gigabyte holds three or four of them. A 1 GB VPS runs roughly $2 to $8 a month depending on the provider, and JustHost.asia lists its 1 GB NVMe plan at $3.81/month, or $3.05/month if you prepay for a year.

That makes the purchase smaller than most guides suggest. Take 1 GB unless the bot carries a database, media files, or a local Bot API server, then spend the evening you saved on the systemd unit instead. Below: every plan on JustHost's tariff page, the RAM figures from three providers that undercut the usual "4 GB minimum" advice, and the deployment details that decide whether the bot is still answering after a reboot.

> **Quick pick.** One or two text bots: 1 GB (JustHost New Star, $3.81/mo, or $3.05/mo on 12-month prepay). Bot plus PostgreSQL, Docker, or image and voice handling: 2 GB (Arcturus, $6.92/mo). Local Bot API server or an LLM in the loop: 4 GB (Bright Star, $9.80/mo).
> 👉 [Check the current JustHost VPS lineup and prices](https://justhost.asia/en/services/vps/tariffs-all?ref=196392)

## The full JustHost VPS lineup

Nine NVMe configurations are listed on the tariff page. The first price is the monthly rate; the second is the per-month rate when you pay for 12 months upfront.

| Plan | CPU / RAM | NVMe disk | Price | Order |
| --- | --- | --- | --- | --- |
| VPS Promo | 1 core / 512 MB | 5 GB | $1.99/mo | [ Order](https://justhost.asia/en/services/vps/tariffs/promo-c1r512nvme?ref=196392) |
| New Star | 1 core / 1 GB | 10 GB | $3.81/mo, $3.05/mo on 12-mo prepay | [ Order](https://justhost.asia/en/services/vps/tariffs/new-star-c1r1024nvme?ref=196392) |
| Sirius | 1 core / 1 GB | 20 GB | $4.85/mo, $3.88/mo on 12-mo prepay | [ Order](https://justhost.asia/en/services/vps/tariffs/sirius?ref=196392) |
| Arcturus | 1 core / 2 GB | 30 GB | $6.92/mo, $5.53/mo on 12-mo prepay | [ Order](https://justhost.asia/en/services/vps/tariffs/arcturus?ref=196392) |
| Double Star | 2 cores / 2 GB | 20 GB | $7.08/mo, $5.67/mo on 12-mo prepay | [ Order](https://justhost.asia/en/services/vps/tariffs/double-star-c2r2048nvme?ref=196392) |
| Alpha Centauri | 2 cores / 2 GB | 40 GB | $8.99/mo, $7.19/mo on 12-mo prepay | [ Order](https://justhost.asia/en/services/vps/tariffs/alpha_centauri?ref=196392) |
| Bright Star | 4 cores / 4 GB | 30 GB | $9.80/mo, $7.84/mo on 12-mo prepay | [ Order](https://justhost.asia/en/services/vps/tariffs/bright-star-c4r4096nvme?ref=196392) |
| Vega | 2 cores / 4 GB | 80 GB | $13.44/mo, $10.75/mo on 12-mo prepay | [ Order](https://justhost.asia/en/services/vps/tariffs/vega?ref=196392) |
| Capella | 4 cores / 4 GB | 120 GB | $18.30/mo, $14.64/mo on 12-mo prepay | [ Order](https://justhost.asia/en/services/vps/tariffs/capella?ref=196392) |

A few things the table doesn't show:

- Ports are unmetered but fair-share: 10 Mbps on Promo, 100 Mbps on New Star, 200 Mbps on Double Star, 300 Mbps on Sirius, Arcturus and Bright Star, scaling to 750 Mbps on Capella. For text messages none of this matters. For a bot that relays photos and voice notes, the 10 Mbps Promo port is the real bottleneck.
- New Star, Double Star and Bright Star are listed as available on orders of three months or more.
- The tariff page continues past 4 GB (Rigel at 8 GB, then larger), and JustHost also sells HDD and SSD variants and IPv6-only servers from $1.60.
- JustHost's main VPS page shows a separate "Special" block with lower headline prices for near-identical specs. At the time of writing it listed Sirius Special (1 core, 1 GB, 20 GB NVMe, 300 Mbps) at $2.81/mo, or $2.25/mo on annual prepay, against $4.85 for the Sirius entry above. Check that block against the tariff list before ordering; on identical specs the cheaper listing wins.

All plans come with root access, IPv4 and IPv6, and DDoS protection, and JustHost advertises 45+ locations including CN2 routes for mainland China traffic.

## How much RAM does a Telegram bot actually need?

Less than the plan tables on most hosting sites imply. Three independent providers converge on the same range:

- The FAQ of a host that sells Telegram-specific plans puts idle usage at 50–150 MB, with peaks during traffic spikes. Their 1 GB plan handles 3–4 bots, the 2 GB plan 10–15, the 4 GB plan 25+.
- A no-KYC host says most bots sit at 256–512 MB, AI-powered bots need 2 GB, and they sell 1 GB as the "comfortable starter."
- DataPasa describes a typical Python or Node.js bot as using "well under a few hundred megabytes," with 1 vCPU and 1 GB running several of them comfortably.

One outlier argues for much more: Xenax Cloud's planning table suggests 2 vCPU and 4 GB for a simple command-and-reply bot, and 8 GB for a bot with database queries and several workers. That same page states these are planning examples rather than measured benchmarks or official Telegram requirements. Treat them as a generous upper bound, not a floor.

Working rules that follow from the numbers:

- **Command-and-reply bot, SQLite for state:** 1 GB. This covers the majority of bots people actually build.
- **PostgreSQL, Docker, or handling images and voice:** 2 GB. Once a database and a container runtime share the box, 1 GB gets tight.
- **Local Bot API server or LLM calls with large payloads:** 4 GB. Telegram's tdlib-based local Bot API server runs on a 4 GB machine, allows uploads up to 2 GB, and lifts some rate limits. That is the one genuinely good reason to buy 4 GB for a bot.
- **512 MB:** one small bot on a lean install, nothing else. No database, no Docker, no media cache.

RAM is rarely what takes a bot offline. Revoked tokens, unhandled exceptions, expired certificates and a crashed disk do that, and none of them scale with the plan size.

## Which JustHost plan maps to your bot

**One text bot or a notification scheduler:** New Star, 1 core and 1 GB for $3.81/mo. Ten gigabytes of NVMe is plenty for the code, a virtualenv and a SQLite file, and the 100 Mbps port is irrelevant when you're sending text.

👉 [Order the 1 GB New Star plan at $3.81/month](https://justhost.asia/en/services/vps/tariffs/new-star-c1r1024nvme?ref=196392)

**Several small bots, or you want room for logs and cached media:** Sirius gives the same 1 GB with double the disk and triple the port for $4.85/mo, and the Special listing is cheaper still if it's available.

**Bot plus PostgreSQL:** Arcturus at $6.92/mo is the better 2 GB buy, not Double Star. It's 16 cents cheaper, has 10 GB more disk, and the extra core on Double Star only pays off if your handlers are CPU-bound or you run several worker processes. A bot waiting on Telegram's API is idle most of the time, so a second core buys nothing.

**Local Bot API server, media pipeline, or an LLM in the request path:** Bright Star, 4 cores and 4 GB for $9.80/mo. Vega and Capella sell the same 4 GB with far more disk and port for bots that store files locally.

👉 [See the 4 GB Bright Star plan](https://justhost.asia/en/services/vps/tariffs/bright-star-c4r4096nvme?ref=196392)

Location barely matters if you use long polling: the bot dials out to Telegram, so a server in Frankfurt and one in Singapore both work. It matters more with webhooks, where Telegram connects to you, and it matters a lot if your users are in a region with poor international routing, which is where JustHost's China-optimised routes come in.

## Is $3.81 a month a fair price?

The same RAM tier costs wildly different amounts depending on the provider and what else is bundled. Three data points, all verified on provider pages:

| Provider | 1 GB tier | 2 GB tier | 4 GB tier | What you're paying for |
| --- | --- | --- | --- | --- |
| JustHost.asia | $3.81/mo | $6.92–7.08/mo | $9.80/mo | 45+ locations, unmetered fair-share ports |
| DataPasa | $1.81/mo | $3.54/mo | $7.09/mo | Single location (Falkenstein, Germany), hourly prepaid billing |
| OliveVPS | $7.99/mo | $15.99/mo | $35.99/mo | Telegram stack preinstalled (Node.js, Python, PM2, telegram-bot-api), 7-day money-back |

The 1 GB tier spans a factor of four, the 4 GB tier a factor of five. Nothing in the specs explains that gap, because the gap isn't in the specs. It's in location count, billing model, preinstalled tooling and support.

DataPasa is the cheapest of the three and the most restrictive: one German datacenter, and you run on a prepaid balance rather than a monthly invoice. OliveVPS charges the most and hands you a container image with your runtime, process manager and Bot API already installed, which is worth something if you'd otherwise spend an evening on setup. JustHost sits in the middle with the widest location list of the three.

## Long polling or webhook?

Long polling works on a fresh server with nothing configured. The bot opens the connection to Telegram, so you need no domain, no certificate and no open inbound ports. For a first deploy, and for bots behind a home network or CGNAT, it's the sane default.

Webhooks flip the direction: Telegram posts each update to an HTTPS endpoint you own. That means a domain pointed at the server and a reverse proxy with a valid certificate, which Caddy handles in three lines:


bot.yourdomain.com {
  reverse_proxy localhost:8443
}


The payoff is lower latency, lower CPU per update, and better behaviour under load, which is why most production bots use webhooks. Payment providers usually require a webhook for callbacks anyway. If you're running a media-heavy bot, note that Telegram only accepts webhook endpoints on a small set of ports with proper TLS, so check the current requirements rather than assuming any open port will do.

## Keeping the process running

A bot started from an SSH session dies the moment you disconnect. For anything that should stay up, run it as a service. A systemd unit covers crashes and reboots:


[Unit]
Description=Telegram bot
After=network.target

[Service]
WorkingDirectory=/opt/bot
EnvironmentFile=/opt/bot/.env
ExecStart=/opt/bot/.venv/bin/python main.py
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target


Then `systemctl enable --now tg-bot` and `journalctl -fu tg-bot` to watch the logs. Docker users get the same effect with `--restart=unless-stopped`, one service per bot in a compose file.

Two details worth getting right from day one. Keep one unit per bot with its own directory and `.env`, so a bad deploy of one bot doesn't restart the others. And understand what a restart policy can't do: it recovers a process, not a revoked token, a syntax error, or an external API that stopped responding. Those show up in the logs and nowhere else, so either read them or wire up an alert.

## What the invoice doesn't show

**Prepaying a year saves about 20%.** On New Star that's $36.60 for twelve months instead of $45.72 paid monthly. Reasonable if you've already run the bot for a month and know it's staying.

**Hourly billing cuts both ways.** DataPasa bills from a prepaid balance, and if that balance reaches zero the server is paused, held for 24 hours, then deleted. A weekend test bot costs cents, but a forgotten top-up costs the database. Any provider that bills by the hour deserves that question before you trust it with stateful data.

**JustHost's tariff page doesn't state a refund window or a renewal price.** Both are worth an email to support before you hand over 12 months of prepaid fees. Promotional first-term pricing is normal in this market, and the only way to know what year two costs is to ask.

**Check the support reputation against what you're building.** JustHost's Trustpilot page carries the company's own description (45 locations, NVMe, DDoS protection, "VPS plans start at just $2.25/month," which lines up with the annual rate of the Special offers rather than the $1.99 Promo plan) alongside customer reviews that are mostly negative on two points: support response times and uptime. One reviewer describes a full outage in early March 2026 with no status page and fifteen hours without a reply; another reports a near two-day outage and having to change datacenter after speeds collapsed. Those are individual experiences rather than a measured uptime figure, but they're consistent with a budget host: cheap capacity, thin support. Keep your own backups and don't put revenue-critical work on the cheapest tier without a fallback.

## Before you point the bot at production

1. **Token in an environment file, not in Git.** If it leaks, revoke it through BotFather and issue a new one.
2. **Run under a dedicated user, not root.**
3. **Firewall: SSH, plus 443 only if you use webhooks.** Nothing else needs to be open.
4. **Outbound port 25 is closed by default.** If the bot sends verification or notification email, use an HTTPS API such as Resend, Postmark or Amazon SES.
5. **Treat message text, uploaded files and callback data as untrusted input.** Validate before it reaches a query or a shell.
6. **Back up the database and any state directory off the server.** A snapshot on the same disk doesn't survive a dead disk.
7. **Stay inside Telegram's rules.** Mass DMs, scraper bots and unsolicited broadcast tools get accounts and servers banned. MTProto userbots built on Telethon or Pyrogram are a different category and are generally allowed when they follow Telegram's terms, though it's worth checking your host's acceptable use policy before you build one.

## Which plan to order

One text bot, no database: New Star at 1 GB, $3.81/mo. Add PostgreSQL, Docker or media handling: Arcturus at 2 GB, $6.92/mo, which is cheaper than the two-core plan with the same RAM. Local Bot API server or an LLM in the loop: Bright Star at 4 GB, $9.80/mo. Testing an idea over a weekend and nothing more: the $1.99 Promo box, or an hourly provider if you want to pay cents.

Two things to confirm before you pay: what the plan renews at once the discounted term ends, and how long the refund window is. Neither is on the tariff page.

👉 [Compare all JustHost VPS tariffs before you pick a plan](https://justhost.asia/en/services/vps/tariffs-all?ref=196392)

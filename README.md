# Pi-hole blocklists

Subscribe to the categories that suit your network. Each file contains one exact DNS hostname per entry. You can use these lists alongside your existing Pi-hole subscriptions.

| List | Raw subscription URL | What to expect |
| --- | --- | --- |
| Ads | `https://raw.githubusercontent.com/40n100w/list/main/ads.txt` | Blocks requests to ad-serving infrastructure. Some ad slots may remain empty. |
| Trackers | `https://raw.githubusercontent.com/40n100w/list/main/trackers.txt` | Blocks requests to third-party analytics and measurement services. Visits and conversions may go unreported. |
| Telemetry (optional) | `https://raw.githubusercontent.com/40n100w/list/main/telemetry.txt` | Blocks selected app diagnostics and usage reporting. This can reduce the information available to developers and support teams. |

## Subscribe in Pi-hole

1. In the Pi-hole v6 admin interface, open **Lists**. Copy a raw URL from the table into the address field and add it as a **blocklist**. Repeat for each category you want.
2. Open **Tools → Update Gravity** and run the update, or run `pihole -g` on the Pi-hole host. Check that Gravity downloaded and parsed each list.
3. From a client that uses Pi-hole for DNS, look up a hostname from an enabled file. In Pi-hole’s **Query Log**, check that the request from that client was blocked by Gravity. The DNS response itself depends on your blocking mode.

If a site or app breaks, reproduce the issue while watching the Query Log for that client. Temporarily allow the suspected hostname and retry. To undo a whole category, disable its subscription and update Gravity. Other enabled subscriptions may also block the same hostname.

## Coverage

These lists target requests that Pi-hole can distinguish by hostname. Entries are exact names. Pi-hole can also block an alias when its DNS response points to a listed name through a CNAME. Ads delivered from the same hostname as the content, including many in-stream video ads, need filtering at another layer. A client that sends DNS queries outside Pi-hole can bypass these lists.

### Social feeds and shared hosts

This list favors keeping posts, comments, photos, and videos available. Pi-hole can block a separate ad request by hostname. If an app delivers a sponsored post and ordinary posts through the same API, blocking that API can remove the feed. A browser content blocker can hide individual promoted elements on sites such as Reddit and YouTube. Its rules apply in the browser. [Reddit promoted posts](https://github.com/easylist/easylist/blob/457e48991cca23c62b2f70350c15decd8a54d83b/easylist/easylist_specific_hide.txt#L7726) · [YouTube ad slots](https://github.com/easylist/easylist/blob/457e48991cca23c62b2f70350c15decd8a54d83b/easylist/easylist_specific_hide.txt#L8890)

The list omits hosts with documented uses for program streams, publishers’ own recommendations, and privacy controls. It also omits hosts where a site-specific exception or shared player suggests a risk. Some ads from those hosts will still load. The audit record gives the reason and evidence for each removal. Other Pi-hole subscriptions can make different blocking decisions. [Compatibility audit](sources/ads-exclusions.tsv)

The compatibility check uses 46 representative social-media hostnames for features including post images, embeds, and video playback. With all three subscriptions enabled, an isolated Pi-hole forwarded their IPv4 and IPv6 queries to a test DNS server. Live feeds and signed-in sessions were outside this test. [Social content hosts checked](sources/social-content.tsv)

## How the hosts were selected

The entries come from hostnames used in ad integrations, analytics clients, and application source code. A category describes the reason for including a host. Pi-hole blocks every request to that name, even if the vendor uses it for more than one purpose. Vendors can change endpoints, and sites can proxy requests through their own domains.

### Advertising evidence

[ads.txt](ads.txt) contains 583 exact hostnames. The [per-host source index](sources/ads.tsv) links each entry to a bidder implementation, publisher integration, mobile SDK, or crawl record showing an advertising request.

| Coverage | Examples | Expected effect |
| --- | --- | --- |
| Web auctions | `prebid-us.creativecdn.com`, `ow.pubmatic.com` | Prevents the browser from requesting bids from those endpoints. |
| Display and sponsored recommendations | `display.bidder.taboola.com`, `native.bidder.taboola.com` | Blocks these bidding endpoints while allowing Taboola’s shared widget loader through this subscription. |
| In-app ads | `ads.inmobi.com`, `ms.applovin.com` | Can stop an SDK from loading an ad. A reward that requires watching the ad may become unavailable. |
| Video ads | `vid.springserve.com`, `dvrtr.innovid.com` | Blocks specific video ad requests. A player may wait for the request to time out or require an ad before playback. |
| Hosted ad servers | `servedby.revive-adserver.net`, `ads.supplyframe.com` | Blocks ad-slot requests on publisher sites using these servers. |
| Audio ad requests | `jamx.deliveryengine.adswizz.com`, `play.adtonos.com` | Blocks separate requests for audio ads. Ads already mixed into a podcast or radio stream can still play. |

Regional entries use the exact hostname observed in the integration. For example, RTB House uses separate hostnames for its Asian, European, and US bid requests. The list includes all three. [RTB House browser adapter](https://github.com/prebid/Prebid.js/blob/318f442924c3928455e2db3093bdfa6ecbecbe91/modules/rtbhouseBidAdapter.js#L111)

The web-auction review started with production endpoints in Prebid.js. For older bidder names, the review checked for additional evidence from HTTP Archive or existing DNS lists. For older AMP tags, recent crawl records helped distinguish hosts still seen on sites from old sample integrations. Some mobile entries come from endpoint constants in the vendor’s versioned Android SDK. [Prebid.js adapters](https://github.com/prebid/Prebid.js/tree/318f442924c3928455e2db3093bdfa6ecbecbe91/modules) · [AMP vendor integrations](https://github.com/ampproject/amphtml/tree/bd7a182eb85f04fd48884c7639da89bd8fb1d5d5/ads/vendors) · [HaGeZi](https://github.com/hagezi/dns-blocklists) · [oisd](https://github.com/sjhgvr/oisd)

Of the 583 names, 343 appeared verbatim in Third Party Web’s March 2026 HTTP Archive data. That count describes the web crawl. Mobile SDKs, regional bidders, and connected-TV clients reach a different mix of hosts. For crawl-based additions, resource paths such as `/pbs/openrtb2/auction` or `/vast/` helped identify the host’s role. [HTTP Archive observations via Third Party Web](https://github.com/patrickhulce/third-party-web/blob/a631d7a773c9338a20446ba0d106fabc0f87b732/data/2026-03-01-observed-domains.json) · [DuckDuckGo Tracker Radar](https://github.com/duckduckgo/tracker-radar/tree/a358daca7f84a2d8275f0b8dca460231dd902886)

### Tracker evidence

Twenty hosts appear in AMP analytics vendor configurations, including Chartbeat’s `ping.chartbeat.net` and Quantcast’s `pixel.quantserve.com`. Four more receive Amplitude events or session replays. Google’s GA4 Event Builder identifies the two Google Analytics collection hosts. The source links in [trackers.txt](trackers.txt) point to the revisions reviewed.

### Telemetry evidence

| Hostnames | What the client sends | Source |
| --- | --- | --- |
| `analytics-api.home-assistant.io`, `analytics.brew.sh` | Home Assistant and Homebrew analytics | [Home Assistant](https://github.com/home-assistant/core/blob/dev/homeassistant/components/analytics/const.py) · [Homebrew](https://github.com/Homebrew/brew/blob/main/Library/Homebrew/utils/analytics.rb) |
| `crash.syncthing.net`, `data.syncthing.net` | Syncthing crash and usage reports, respectively | [Syncthing defaults](https://github.com/syncthing/syncthing/blob/main/lib/config/optionsconfiguration.go) |
| `incoming.telemetry.mozilla.org`, `mobile.events.data.microsoft.com` | Firefox telemetry and Microsoft telemetry used by VS Code, respectively | [Firefox preferences](https://github.com/mozilla-firefox/firefox/blob/main/modules/libpref/init/all.js) · [VS Code telemetry client](https://github.com/microsoft/vscode/blob/main/src/vs/platform/telemetry/common/1dsAppender.ts) |
| `notify.bugsnag.com`, `sessions.bugsnag.com` | BugSnag error and session reports from participating apps | [BugSnag Android endpoints](https://github.com/bugsnag/bugsnag-android/blob/next/bugsnag-android-core/src/main/java/com/bugsnag/android/EndpointConfiguration.kt) |
| `telemetry.astro.build`, `telemetry.nextjs.org`, `telemetry.nuxt.com` | Usage reports from developer tools | [Astro](https://github.com/withastro/astro/blob/main/packages/telemetry/src/post.ts) · [Next.js](https://github.com/vercel/next.js/blob/canary/packages/next/src/telemetry/post-telemetry-payload.ts) · [Nuxt](https://github.com/nuxt/telemetry/blob/main/src/module.ts) |

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

These lists target requests that Pi-hole can distinguish by hostname. An entry covers that hostname only. Ads delivered from the same hostname as the content, including many in-stream video ads, need filtering at another layer. A client that sends DNS queries outside Pi-hole can bypass these lists.

## How the hosts were selected

The entries come from hostnames used in ad integrations, analytics clients, and application source code. A category describes the reason for including a host. Pi-hole blocks every request to that name, even if the vendor uses it for more than one purpose. Vendors can change endpoints, and sites can proxy requests through their own domains.

### Advertising evidence

The auction hosts come from the endpoints that Prebid bidder adapters use to request bids. Examples include `hbopenbid.pubmatic.com`, `fastlane.rubiconproject.com`, and `web.ads.aps.amazon-adsystem.com`. Blocking one can stop an auction, and some ad-supported players may wait for it to time out. [Prebid bidder implementations](https://github.com/prebid/Prebid.js/tree/318f442924c3928455e2db3093bdfa6ecbecbe91/modules)

The four Google ad hosts appear in AMP’s AdSense and DoubleClick integrations or Google’s video ad sample. The entries target the hostnames those clients request. [AMP AdSense](https://github.com/ampproject/amphtml/blob/bd7a182eb85f04fd48884c7639da89bd8fb1d5d5/extensions/amp-ad-network-adsense-impl/0.1/amp-ad-network-adsense-impl.js) · [AMP DoubleClick](https://github.com/ampproject/amphtml/blob/bd7a182eb85f04fd48884c7639da89bd8fb1d5d5/extensions/amp-ad-network-doubleclick-impl/0.1/amp-ad-network-doubleclick-impl.js) · [Google IMA sample](https://github.com/googleads/googleads-ima-html5/blob/65b0e2f88a7decea45d930b3808d544ba0e517f7/simple/ads.js)

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

# Pi-hole blocklists

Copy the link for each list you want into Pi-hole’s **Lists** page, choose **Add blocklist**, then update Gravity.

| List | What it targets |
| --- | --- |
| [ads.txt](https://raw.githubusercontent.com/40n100w/list/main/ads.txt) | Hosts that auction or deliver ads, including Amazon Publisher Services placements and banner ads on other sites. |
| [trackers.txt](https://raw.githubusercontent.com/40n100w/list/main/trackers.txt) | Third-party trackers that count page views, log activity in apps, or record browsing sessions. |
| [telemetry.txt](https://raw.githubusercontent.com/40n100w/list/main/telemetry.txt) | Optional list for blocking usage statistics, performance data, and crash reports sent by browsers, apps, and developer tools. |

Hulu, YouTube, and Prime Video can still show in-stream ads. Some ads share a hostname with the video, and some players wait for an ad request to finish before the show resumes. Pi-hole also lets through sponsored posts delivered from the same hostname as the rest of a social feed.

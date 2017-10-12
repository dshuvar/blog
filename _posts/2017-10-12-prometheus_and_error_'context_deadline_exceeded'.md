---
published: false
---
## It note for people who were been surprised falling down monitoring system.

If you see state "down" and error "context deadline exceeded" for all you endpoints on target page your prometheus.
Try do next: increase value in settings for:
scrape_interval
evaluation_interval
scrape_timeout
and reload your app.

For example now part main configs looks like that:

```
# A scrape configuration scraping a Node Exporter and the Prometheus server
#

global:
  scrape_interval:     10s
  evaluation_interval: 10s
  scrape_timeout: 10s
```







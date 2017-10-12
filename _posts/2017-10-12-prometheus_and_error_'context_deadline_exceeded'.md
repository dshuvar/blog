---
published: true
---
## It note for people who were been surprised falling down monitoring system.

If you see state "down" and error "context deadline exceeded" for all you endpoints on target page your prometheus.

Try do next for solved this problem: 

1.increase value in settings:
- scrape_interval
- evaluation_interval
- scrape_timeout
2. and reload your app.


For example now part main configs looks like that:

```
# A scrape configuration scraping a Node Exporter and the Prometheus server
#

global:
  scrape_interval:     10s
  evaluation_interval: 10s
  scrape_timeout: 10s
```

Values defines for:

scrape_interval - how frequently to scrape targets from this job.

evaluation_interval - how frequently to evaluate rules.

scrape_timeout - per-scrape timeout when scraping this job.

---
title: Moving to Cloudflare + Github Pages
date: 2018-06-04 22:21:00 Z
tags:
- hosting
---

This week I have been moving all my published sites to Github Pages. First of all, now I am able to stop my 5$ Digital Ocean droplet. I used it for other services that required backend, but now it was only an Nginx serving multiple sites.

![cloudflare.png](/uploads/cloudflare.png)

Once a site is published on Github Pages, we can move on and get the benefits of having a CDN. [Cloudflare](https://dash.cloudflare.com/) gives you a free SSL certificate as well as speed (minify, compression, etc.) and caching. This will give you a lot of [Google's PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/) points.

**Steps:**
1. Create a Github repo and enable Github Pages
2. Enable a custom domain
3. Go to your domain provider and change the DNS to point to the ones from Cloudflare: edna.ns.cloudflare.com and noel.ns.cloudflare.com
4. Setup your Cloudflare account and include four A records, pointing to Github.

![dns.png](/uploads/dns.png)

And there you go. Fast and free hosting.

---
layout: post
title:  "Migrating Blog to GitHub Pages"
---

The first two blog posts focused on creating this blog in GCP and using Codefresh however the cost of running the blog (with near zero traffic) was more than zero, a nice (free) alternative is [GitHub Pages](https://pages.github.com/) which provides free hosting and supports custom domains.

The GCP costs for the few weeks the blog ran were just over $60 which was primarily made up of the compute costs to run the blog along with the Kubernetes cluster for Codefresh.

![GCP Costs](/assets/3-gcp-costs.png)

The move to [GitHub Pages](https://pages.github.com/) was trivial as [Jekyll](https://jekyllrb.com/docs/github-pages/) is natively supported, meaning all the content stays the same.  I followed the [setup guide](https://pages.github.com/) and moved the folder structure over from the original blog repo to the [new repo](https://github.com/danielholden/danielholden.github.io).  Overnight the HTTPS certificate was generated which allows the site to enforce HTTPS and the DNS settings are straightforward as follows:

![DNS Settings](/assets/3-dns.png)

The source is now accessible here:

[https://github.com/danielholden/danielholden.github.io](https://github.com/danielholden/danielholden.github.io)

With no need for a build pipeline as this is all done by [GitHub Pages](https://pages.github.com/).
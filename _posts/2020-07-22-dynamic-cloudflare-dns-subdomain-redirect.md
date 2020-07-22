---
published: true
layout: post
title: CloudFlare dynamic DNS subdomain redirects
subtitle: Quick & easy DNS Redirection
cover-img: /assets/img/crawfish-banner.jpg
thumbnail-img: >-
  /assets/img/2020-07-21-dynamic-cloudflare-dns-for-github-pages/cf-logo-v-rgb-edited-square.png
share-img: >-
  /assets/img/2020-07-21-dynamic-cloudflare-dns-for-github-pages/cf-logo-v-rgb-edited-square.png
tags: [dns, infrastructure, cloudflare, github]
---
In my last post about CloudFlare I outlined how we could set up an Apex domain for GitHub Pages via a CNAME record.  But, what if you wanted to share alias urls that used a different or unique subdomain and you wanted those to be redirected/routed to the same site?  This is necessary for GitHub Pages since there is limitation for only one custom domain that can be configured per site.

Well Cloudflare offers a pretty simple, yet very useful capability to manage these routes with [Page Rules](https://support.cloudflare.com/hc/en-us/articles/218411427-Understanding-and-Configuring-Cloudflare-Page-Rules-Page-Rules-Tutorial-). In the free account you get 3 page rules to setup however you want; additional ones can be purchased.  But, 3 should suffice if all you need are a couple global/generic routing rules.

In my case I thought that it would be useful if I wanted to share links using the very friendly domain _blog.cajuncoding.com_, but have that redirect to the primary apex domain (since I don't actually have a separate site. 

In CloudFlare, just navigate to **Page Rules** & create a new page rule:
<img src="../assets/img/2020-07-22-dynamic-cloudflare-dns-subdomain-redirect/navigate-to-page-rules.png " class="fullsize" data-zoomable />

Now this works just like a simple masking expression where we can use wildcards to make this flexible.  In my case I wanted all requests (regardless of thier path) to just redirect to the main site.  

So a user could browse to this post using either of these urls and arrive where I expect them to:
- [https://cajuncoding.com/*](https://cajuncoding.com/2020-07-22-dynamic-cloudflare-dns-subdomain-redirect)
- [https://blog.cajuncoding.com/*2020-07-22-dynamic-cloudflare-dns-subdomain-redirect*](https://cajuncoding.com/2020-07-22-dynamic-cloudflare-dns-subdomain-redirect)

We do that by matching all requests for _blog.cajuncoding.com_ and use a wildcard **/*** for the remaining part of the path. Then we use the variable replacement feature using **$1** (e.g. backreference to the wildcard capture) to ensure that the target/destination route still contains the full path.  CloudFlare is already handling our protocal (http/https) for us so that is of no consequence.

### Page Rule Patterns
So the Matching Pattern is: `blog.cajuncoding.com/*`

And the destination pattern is: `cajuncoding.com/$1`

You can choose if you want the redirection to tell the clients that it's permanent (like I do) or temporary; meaning that you may really want to have a different site in the future.

### And here's the configured rule:
<img src="../assets/img/2020-07-22-dynamic-cloudflare-dns-subdomain-redirect/blog-page-rule-configuration.png " class="fullsize" data-zoomable />

### Why a redirection?
The reason that I have to redirect and can't use this as the main domain is related to a limitation in GitHub Pages that only allows me to configure one custom domain for it to use.  

Since I'm using GitHub pages to power my main site I need to redirect all traffic to the correct domain for GitHub to understand and handle the requests. As documented in GitHub docs here, I'm following thier direction:

<img src="../assets/img/2020-07-22-dynamic-cloudflare-dns-subdomain-redirect/github-cname-limits.png " class="fullsize" data-zoomable />


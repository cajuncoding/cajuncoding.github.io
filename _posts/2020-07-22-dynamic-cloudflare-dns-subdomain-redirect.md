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
In my [last post about CloudFlare](2020-07-21-dynamic-cloudflare-dns-for-github-pages) I outlined how we could set up an Apex domain for GitHub Pages via a CNAME record.  But, what if you wanted to share alias urls that used a different or unique subdomain and you wanted those to be redirected/routed to the same site?  This is necessary for GitHub Pages since there is a limitation for only one custom domain that can be configured per site.

Well Cloudflare offers a pretty simple, yet very useful capability to manage these routes with [Page Rules](https://support.cloudflare.com/hc/en-us/articles/218411427-Understanding-and-Configuring-Cloudflare-Page-Rules-Page-Rules-Tutorial-). In the free account you get 3 page rules to setup however you want; additional ones can be purchased.  But, 3 should suffice if all you need are a couple global/generic routing rules.

In my case I thought that it would be useful to share links using the very friendly domain _blog.cajuncoding.com_, but have that redirect to the primary apex domain -- since I don't actually have a separate site, and [GitHub Pages doesn't support it](#github-limit-of-one-domain). 

In CloudFlare, just navigate to **Page Rules** & create a new page rule:
<img src="../assets/img/2020-07-22-dynamic-cloudflare-dns-subdomain-redirect/navigate-to-page-rules.png " class="fullsize" data-zoomable />

Now this works just like a simple matching expression where we can use wildcards to make the pattern flexible (_not quite like regex; more like the good-ole DOS dir *.* command_).  In my case I wanted all requests (regardless of the full url path) to just redirect to the main site and work as expected.  

So a user could browse to this post using either of these urls and arrive where I expect them to:
- [https://cajuncoding.com/*](https://cajuncoding.com/2020-07-22-dynamic-cloudflare-dns-subdomain-redirect)
- [https://blog.cajuncoding.com/*](https://cajuncoding.com/2020-07-22-dynamic-cloudflare-dns-subdomain-redirect)

We do that by matching all requests for _blog.cajuncoding.com_ and use a wildcard **/*** for the remaining part of the path. Then we use the variable replacement feature using **$1** (e.g. backreference to the wildcard capture) to ensure that the target/destination route still contains the full path.  We also want to be protocol agnostic, so we match any incoming protocal (by not including it), and ensure that the destination is using **https**; this is helpful in my case because we already have CloudFlare enforcing _https_ so this saves any additional redirections.

### Page Rule Patterns
So the Matching Pattern is: `blog.cajuncoding.com/*`

And the destination pattern is: `cajuncoding.com/$1`

You can choose if you want the redirection to tell the client that it's permanent (like I do) or temporary; meaning that you may really want to have a different site in the future.

### And here's the configured rule:
<img src="../assets/img/2020-07-22-dynamic-cloudflare-dns-subdomain-redirect/blog-page-rule-configuration.png " class="fullsize" data-zoomable />

<a name="github-limit-of-one-domain">
### Why a redirection?
The reason that I have to redirect and can't use this as the main domain is related to a limitation in GitHub Pages that only allows me to configure one custom domain for it to use.  

Since I'm using GitHub pages to power my main site I need to redirect all traffic to the correct domain for GitHub to understand and handle the requests. As documented in GitHub docs here, I'm following thier direction:

<img src="../assets/img/2020-07-22-dynamic-cloudflare-dns-subdomain-redirect/github-cname-limits.png " class="fullsize" data-zoomable />


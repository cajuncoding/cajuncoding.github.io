---
published: true
layout: post
title: Cloudflare dynamic DNS subdomain redirects
subtitle: Quick & easy DNS Redirection
cover-img: /assets/img/crawfish-banner.jpg
thumbnail-img: >-
  /assets/img/2020-07-21-dynamic-cloudflare-dns-for-github-pages/cf-logo-v-rgb-edited-square.png
share-img: >-
  /assets/img/2020-07-21-dynamic-cloudflare-dns-for-github-pages/cf-logo-v-rgb-edited-square.png
tags:
  - dns
  - infrastructure
  - cloudflare
  - github
---
In my [last post about Cloudflare](/2020-07-21-dynamic-cloudflare-dns-for-github-pages) I outlined how we could set up an apex domain for GitHub Pages via a CNAME record.  But, what if you wanted to share alias urls that used a different or unique subdomain and you wanted those to be redirected/routed to the same site?  This is necessary for GitHub Pages since there is a limitation for only one custom domain that can be configured per site.

Well Cloudflare offers a pretty simple, yet very useful capability to manage these routes with [Page Rules](https://support.cloudflare.com/hc/en-us/articles/218411427-Understanding-and-Configuring-Cloudflare-Page-Rules-Page-Rules-Tutorial-). In the free account you get 3 page rules to setup however you want; additional ones can be purchased.  But, 3 should suffice if all you need are a couple global/generic routing rules.

In my case I thought that it would be useful to share links using the very friendly domain _blog.cajuncoding.com_, but have that redirect to the primary apex domain -- since I don't actually have a separate site, and [GitHub Pages doesn't support it](#github-limit-of-one-domain). 

A Page Rule is basically just a processing rule for all url requests that are routed and proxied through CloudFlare, therefore **the url must be routed/proxied through CloudFlare for PageRules to work**.  This means that any url you want ot build a page rule for needs to also have a corresponding DNS entry that is handling it.

### First we must have a DNS Configuration for Page Rule to match:
In my case, I need to have a DNS CNAME set up for the ***blog.cajuncoding.com*** subdomain, and I just route that to the apex domain *cajuncoding.com* as follows:  

<img src="../assets/img/2020-07-22-dynamic-cloudflare-dns-subdomain-redirect/setup-blog-prefix-dns-cname.png " class="fullsize" data-zoomable />  

Now with only a DNS setup, my page will not work becuase GitHub Pages (where I'm hosting the site) only supports two bindings when you use an Apex domain, as I am, with ***cajuncoding.com***:
1. The default binding to the Apex domain: *cajuncoding.com*
2. One additional default subdomain binding for **www**: *www.cajuncoding.com*

So, the subdomain *blog.cajuncoding.com* will not work because GitHub Pages cannot not handle this subdomain. But, we can fix this with Url Routing in Cloudflare. And in my use-case (and probably many others), I don't mind having page redirection/forwarding since my goal is just to provide friendly url in various places (like LinkedIn).

### Now we can set up the Page Rule in CloudFlare:
In CloudFlare, just navigate to **Page Rules** & create a new page rule:
<img src="../assets/img/2020-07-22-dynamic-cloudflare-dns-subdomain-redirect/navigate-to-page-rules.png " class="fullsize" data-zoomable />

Now this works just like a simple matching expression where we can use wildcards to make the pattern flexible (_not quite like regex; more like the good-ole DOS dir *.* command_). In my case I wanted all requests, regardless of the full url path, to just redirect to the main site and work as expected.  

So a user could browse to this post using either of these urls and arrive where I expect them to:
- [https://cajuncoding.com/*](https://cajuncoding.com/2020-07-22-dynamic-cloudflare-dns-subdomain-redirect)
- [https://blog.cajuncoding.com/*](https://cajuncoding.com/2020-07-22-dynamic-cloudflare-dns-subdomain-redirect)

This is done by matching all requests for _blog.cajuncoding.com_, using a wildcard **/*** for the remaining part of the path. Then we use the variable replacement feature using **$1** (e.g. back-reference to the wildcard capture) to ensure that the target/destination route still contains the full path, of the original request.  We also want to be protocol agnostic, so we match any incoming protocol (by not including it), and ensure that the destination is using **https**; this is helpful in my case because I already have Cloudflare enforcing _https_ so this saves any additional potential redirection from _http_ to _https_.

### Page Rule Patterns
So the Matching Pattern is: `blog.cajuncoding.com/*`

And the destination pattern is: `https://cajuncoding.com/$1`

You can choose if you want the redirection to tell the client that it's permanent (like I do) or temporary; meaning that you may really want to have a different site in the future.

### And here's the configured rule:
<img src="../assets/img/2020-07-22-dynamic-cloudflare-dns-subdomain-redirect/blog-page-rule-configuration.png " class="fullsize" data-zoomable />

<a name="github-limit-of-one-domain">
### Why a redirection?
The reason that I have to redirect and can't use this as the main domain is related to a limitation in GitHub Pages that only allows the use of one custom domain per site. 

Since I'm using GitHub Pages to power my main site I need to redirect all traffic to the correct domain for GitHub Pages to understand and handle the requests -- of which it will only support my apex name and the default subdomain binding for _www_. 
  
As documented in [GitHub docs here](https://docs.github.com/en/github/working-with-github-pages/troubleshooting-custom-domains-and-github-pages), I'm following their direction:

<img src="../assets/img/2020-07-22-dynamic-cloudflare-dns-subdomain-redirect/github-cname-limits.png " class="fullsize" data-zoomable />

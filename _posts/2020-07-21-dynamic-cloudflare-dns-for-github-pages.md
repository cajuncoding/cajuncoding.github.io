---
published: true
layout: post
title: CloudFlare dynamic DNS setup for GitHub Pages
subtitle: Using a CNAME record instead of traditional A record..
cover-img: /assets/img/crawfish-banner.jpg
thumbnail-img: >-
  /assets/img/post-dynamic-cloudflare-dns-for-github-pages/cf-logo-v-rgb-edited-square.png
share-img: >-
  /assets/img/post-dynamic-cloudflare-dns-for-github-pages/cf-logo-v-rgb-edited-square.png
tags:
  - architecture
  - github
---
So, here's something practical that might be of interest if you have (or want) a domain custom domain (apex or sub-domain) to point to your GitHub Pages site.

In my last post, I walked through the rabbit hole of research and humble learning I had about some awesome stuff htat has been around since 2008... GitHub Pages and Jekyll site building.

But, I didn't offer much of a practical reason to even read the article... as it was mostly just an account of my own stumbling into knowledge.  But stumble & learn I did!

But there was a simple practical element about CloudFlare that's def. worth sharing.

There's several good blogs out there that walk you through setting up the DNS, for a custom domain name to point to a Githug Pages site, the traditional way using an **A** record (apex domain) pointing to specific IP addresses. I personally got a lot of value from [Rick Pauloo's great write-up here](https://richpauloo.github.io/2019-11-17-Linking-a-Custom-Domain-to-Github-Pages/) for this exact process. But it uses the static documented IP addresses and creates **A** records within your DNS. 

And GitHub documentation states that their IP addresses may change from time to time, and they provide an API to return the latest IP addresses . . . I'm not 100% if this is a real risk for GitHub pages also (as it's hosted under different domain at Github.io), but the point is that managing IP address changes isn't as simple as I'd like... and can be completely eliminated because **CloudFlare has made this easier than ever.**

CloudFlare addresses this with what they call [**CName Flattening**](https://blog.cloudflare.com/introducing-cname-flattening-rfc-compliant-cnames-at-a-domains-root/).

I'll leave you to read the nitty gritty in that link, but for all-intents-and-purposes here, it is CloudFlare's solution that allows us to set up an Apex domain with a **CName** record (e.g. map to another domain name) instead of an **A** record (maps to an statically configured IP address)!

<img src="/assets/img/post-dynamic-cloudflare-dns-for-github-pages/setting-up-cloudflare-apex-domain-dns.jpg" style="width: 100%;height: auto;max-width: 800px;" />

That's it, one screeshot, Done! Our DNS is now complete.  

### Enabling the proper Domain handling on the Github side...

To allow serving our content via our custom domain, we do need to enable this on the GitHub side, but they make it just as easy here too. 

[Documented here...](https://docs.github.com/en/github/working-with-github-pages/about-custom-domains-and-github-pages) We could createa CNAME file manually. 

But their UI is super easy and takes care of this for us.  In your repo, navigate to **Settings -> Options** and scroll on down to **GitHub Pages**.  Finally enter the domain, just as you did in CloudFlare, here so that GitHub will properly route/handle all requests from that domain by serving content from your repo.

<img src="/assets/img/post-dynamic-cloudflare-dns-for-github-pages/github-settings-options.jpg" style="width: 100%;height: auto;max-width: 800px;" />

<img src="/assets/img/post-dynamic-cloudflare-dns-for-github-pages/github-pages-custom-domain-settings.jpg" style="width: 100%;height: auto;max-width: 800px;" />

_Fyi, this part was also addressed in [Rick Pauloo's write-up above](https://richpauloo.github.io/2019-11-17-Linking-a-Custom-Domain-to-Github-Pages/)_.

**That's it, you now have a custom apex domain routed by CloudFlare to a site hosted in Github Pages -- both offerring enterprise performance and reliability!**

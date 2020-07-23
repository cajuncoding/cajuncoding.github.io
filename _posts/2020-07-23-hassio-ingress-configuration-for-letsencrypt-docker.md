---
published: true
layout: post
title: Configuring HassIO Ingress for Add-Ons with LetsEncrypt Docker
subtitle: >-
  Enable support for HassIO Add-Ons in LetsEncrypt Docker (linuxserver
  letsencrypt)
cover-img: /assets/img/homeassistant-banner.png
thumbnail-img: /assets/img/homeassistant-large.png
share-img: /assets/img/homeassistant-large.png
tags:
  - hassio
  - homeassistant
  - docker
  - letsencrypt
  - unraid
---
Setting up [HassIO](https://www.home-assistant.io/blog/2017/07/25/introducing-hassio/) *(the awesome docker based OS/supervisor for [HomeAssistant](https://www.home-assistant.io/))* for safe & secure external access in Unraid can be a little trickier than expected, especially for the HassIO Add-Ons like _Configurator_, _Terminal_, _VSCode_, etc.  Using the Linuxserver LetsEncrypt docker (which provides NGINX reverse proxy for various sites) requires some additional configuration elements to ensure that WebSockets are fully enabled.

I've had my system safely & securely is fully configured for external access and everything worked well except for the HassIO plugins that attempt to establish web socket connections to `/api/hassio_ingress`.... I identified these failures by looking at the debug console of developer tools in my Chrome browser.

<img src="../assets/img/2020-07-23-hassio-ingress-configuration-for-letsencrypt-docker/hassio-ingress-errors.png " class="medium center" data-zoomable />

I was having a similar issue as those in the discussion on the HassIO issue here:  
[https://github.com/home-assistant/hassio-addons/issues/1043#issuecomment-656590946](https://github.com/home-assistant/hassio-addons/issues/1043#issuecomment-656590946)

And, also similar question posted in the HomeAssistant forum here:  
[https://community.home-assistant.io/t/unable-to-connect-with-nginx-reverse-proxy/14875](https://community.home-assistant.io/t/unable-to-connect-with-nginx-reverse-proxy/14875)

However from tips on the HomeAssistant forum as well as the issue (above) I realized that if I access directly via IP address on my local network then the add-ons suprisingly worked. The developer tools in Chrome were no longer showing the web-socket errors...  So finally I had a clue that it was in fact related to my routing and reverse-proxy setup!

My particular issues were with the **Terminal** add-on & the **Visual Studio Code** add-on which I could not get to work at all; so I ended up just un-installing them. But, I'm sure the root cause to many of these are all the same issue related to HassIO and reverse proxy configuration.

In my latest setup, I'm using LetsEncrypt Docker (linuxserver/letsencrypt) on an Unraid server for all reverse-proxy routing, and had gotten it all working well, using the sample configuration for HomeAssistant provided in the docker:
`.../nginx/proxy-conf/homeassistant.subdomain.conf.sample`

However, that sample was setup for the standard core HomeAssistant install and not HassIO, which I've finally figured out requires additional proxy configuration for websockets to the `api/hassio_ingress` route.

That's when it finally clicked! I finally got my add-ons working by ensuring that websockets were fully enabled for this route in addition to the default HomeAssistant API route (provided in the default config of LetsEncrypt docker).

By adding the following additional location handler/configuration to my Nginx proxy configuration we can safely and securely enable the websocket support needed.

This is added below the other existing configuration for the default HomeAssistant websockets api route: `api/websockets`; just add this below the first in your configuration.

You will need to update the IP address or domain name (e.g. dnsmasq) to ensure it correctly points to your HassIO instance, on the line: `set $upstream_app <IP_OF_HASS>`

Note: For security & best practices (narrow scope), I added this section to specifically only handle the `hassio_ingress` route, as opposed to blindly allowing all requests at my root location to be upgraded as websockets (wildcard for any request to my HomeAssistant); the less secure wildcare solution was mentioned by some answers in the HomeAssistant forum, but I recommend against it.

```
# Duplicate websocket configuration specifically for HassIO add-ons (e.g. /api/hassio_ingress)
# Details of how this config works can be read here:
# https://www.serverlab.ca/tutorials/linux/web-servers-linux/how-to-proxy-wss-websockets-with-nginx/
location /api/hassio_ingress {
        resolver 127.0.0.11 valid=30s;
        set $upstream_app 192.168.1.???;
        set $upstream_port 8123;
        set $upstream_proto http;
        proxy_pass $upstream_proto://$upstream_app:$upstream_port;

        proxy_set_header Host $host;

        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
}
```

Et Voila! My add-ons were up and running and firing on all cylinders!

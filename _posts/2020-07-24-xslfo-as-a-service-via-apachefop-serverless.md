---
published: false
layout: post
title: Configuring HassIO Ingress for Add-Ons with LetsEncrypt Docker
subtitle: Enable support for HassIO Add-Ons in LetsEncrypt Docker (linuxserver letsencrypt)
cover-img: /assets/img/crawfish-banner.jpg
thumbnail-img: >-
  /assets/img/2020-07-24-xslfo-as-a-service-via-apachefop-serverless/apache_fop_logo.png
share-img: >-
  /assets/img/2020-07-24-xslfo-as-a-service-via-apachefop-serverless/apache_fop_logo.png
tags:
  - code
  - pdf-templating
  - xslfo
  - apache-fop
---
Over the years I've pretty consistenly had requirements for generating reports in PDF format across just about every client I've worked with. From automating 

Though, I used it heavily in several solutions for several clients to help them automate their paper processes with dynamic generation of printable outputs, Pdf files, invoices, shipping/packaging labels, newletters, and even dynamically generated printable outputs from CMS systems for large enterprises.

In the world of generating Pdf outputs/files/reports, there are many options from code-oriented approaches like PdfSharp to Reporting Engines that support exporting as Pdf like Crystal Reports, and a whole host of options in-between (like Html to Pdf converters).

But a repeating constraint that I've encountered consistently (even in large enterprises) is the need to limit licensing requirements due to red-tape (for large enterprises) or cost (for smaller businesses).

In either case we gravitate towards open source optoins to accomplish their goals. Which brings me to two primay options:
 - Crystal Reports (or other equivalent)  *-- report export approach*
 - PDFSharp (or other equivalent) *-- code only approach*
 - CSS Paged Media -- templating approach using Html + CSS; new, less known, no mature rendering engines.

Niether of these satisfy software design best practices.  They both result in 


Now CSS Page Media is a great and viable alternative, and [this article here](https://readwritecode.net/ebooks/2019/04/27/xsl-fo-is-dead-css-paged-media-is-prime-suspect.html) does a nice job of breaking down the differences. Reaching the same conclusion that I have over and over:  
> *"XSL-FO isn’t dead yet and is still more than up to the task of being the basis of an XML based publishing system today"*

As a fully managed C# solution, it worked pretty darn well considering it's limitations. Generally I was able to 

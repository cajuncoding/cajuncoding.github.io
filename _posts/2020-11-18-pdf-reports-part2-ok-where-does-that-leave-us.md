---
published: true
layout: post
title: 'PDF Reporting from Web Apps, How hard can it be?'
subtitle: 'Part 2: Why markup based PDF Templating is the way to go...'
cover-img: /assets/img/crawfish-banner.jpg
thumbnail-img: /assets/img/pdf-icon.png
share-img: /assets/img/pdf-icon.png
tags:
  - azure-functions
  - serverless
  - 'c#'
  - pdf
  - pdf templating
  - .net
  - apache-fop
---

[In the last post](/2020-11-17-pdf-reports-part1-how-hard-can-it-be) we walked through a pretty exhaustive mental excercise
focused on key considerations for quality PDF rendering from a web application...  We spent most of the time reviewing
the dramatic difference between web rendering and printable-media rendering -- something that, I've observed, novice development 
teams don't fully appreciate.

### So...Where does that leave us?
Well, to summarize the previous post of the above, we introduced the idea of a templated based approach to rendering 
PDF based reports using a markup language that has robust support for printable-media and avoids the pitfalls of normal 
Html/CSS.  And, offers flexibility via paid-for as well as open source implementations that we might use depending on 
our use-cases and/or cost constraints.

Now, that narrows things down really-really well as the industry has provided some solutions that meet this criteria 
over the years and the following are the main players:
- **Xsl Formatting Objects** -- _A markup language for XML document formatting that is most often used to generate PDF files._

- **CSS Paged Media** -- _XHTML markup combined with a CSS module that specifies how pages are generated and laid out to hold fragmented content in a paged presentation (XHTML + CSS)._

- **Proprietary/Specialized Markup Solutions** -- _There are other options that meet our goals, but with their own specialized markup languages and rendering solutions._

_XSL-FO_ & _CSS Paged Media_ are both industry accepted standards that are completely agnostic of implementation technology, and are supported 
by a variety of solutions – free, open source, & paid-for. The other solutions are proprietary or specialized, though there are open source options
that fall into this bucket as well (e.g. [JasperReports](https://en.wikipedia.org/wiki/JasperReports)).  However, since those are very specialized
I would gravitate to the first two industry specification based solutions first, unless there was a compelling requirement that was met
by one of the proprietary/specialized solutions.

The kicker here is that the best solutions provide for a templating language that is a purely declarative markup language.  Therefore, we are free 
to use any templating technology we want including, but not limited to: Razor Templating in .Net, XSLT, JSP, JavaScript 
based templating (e.g. React, VueJS) in NodeJS, etc.  As long as it’s capable of rendering well-formed markup output 
(XHTML + CSS, or Xml).

The key benefit is simply that we have low risk of being limited by the templating language. We are free to develop 
a robust data model separate from the templating presentation – which means that half of the process doesn’t change 
if we need to change the presentation logic, layer, code, or even technology being used.  

We achieve all the numerous benefits of known best practices for web development such as MVC, MVP, MVVM (all 
enabling separation of Model data from Presentation) when rendering printable reports. This is great for 
developer productivity and flexibility to remain congruent with existing technology stack(s) in use, as well 
as for long term technology supportability/maintainability.

#### CSS Paged Media:
CSS Paged Media is the most modern (as in nearly bleeding edge) approach.  I say that because the spec. is still 
evolving by the W3C.  And the best viable options have license costs, and the price isn’t what I consider cheap either; 
based on my experience with clients and project delivery constraints.

Based on my own research, this is a non-exhaustive list of the prevalent solutions that offer good quality rendering 
& feature rich options (you may find others by searching but they likely have significant support and/or feature limitations):

 - [PDFReactor](https://www.pdfreactor.com/)
   - License Costs required
   - Good compliance with CSS Paged Media spec.
 - [Prince (formerly PrinceXML)](https://www.princexml.com/)
   - License Costs required
   - Good compliance with CSS Paged Media spec.
 - [Antennahouse CSS Formatter](https://www.antennahouse.com/)
   - License Costs required
   - Great compliance with CSS Paged Media spec.
 - [WeasyPrint](https://weasyprint.org/)
   - Free Open Source
   - Not very compliant with CSS Paged Media spec; some non-standard proprietary implementations
   - Python only

Now I know what you Front End friendly developers are thinking . . . surely CSS is the more modern and better way to go.  
Yes, it’s the more modern of the two, but there are some additional facts to consider before assuming that it is better
for all environments (or teams).  

There are some great paid-for options, but the free open source solution is not as compliant with the spec. and 
uses some proprietary syntax for certain layout aspects, etc...

In addition, the CSS can become extremely complex for layouts – as in thousands of lines of CSS.  Thereby requiring 
the use of other technologies to better enable development such as CSS preprocessors, build processes, etc.  

All of which senior Front End developers are likely very familiar with, but when you remember the context that most 
PDF rendering should occur in a back-end context – for security and data integrity reasons – then you may encounter 
a conflicting skillset issue.  

I did some preliminary research and it appears that WeasyPrint could likely be deployed in a serverless environment 
using Azure Functions support for Python, but this is not yet proven.  And there is the fact that it’s not realy spec. 
compliant.

So, in my experience, it’s important to recognize how this may introduce unnecessary complexity into your project!

#### Xsl Formatting Objects (XSL-FO) 

Meanwhile [XSL-FO is very much alive and kicking](https://readwritecode.net/ebooks/2019/04/28/xsl-fo-is-alive-and-kicking.html).  
XSL-FO is by the most mature spec for paged media rendering and was basically feature complete spec. in 2012. It’s 
been around a while, is very mature, is used in many industries and companies, and has well known free open source 
solutions as well as paid solutions; though the license costs are also pretty steep.

Based on my own research, this is a non-exhaustive list of the prevalent solutions that offer good quality rendering 
& feature rich options:

 - [RenderX XEP](http://www.renderx.com/tools/xep.html)
   - License Costs required (pricey)
   - Great compliance with XSL-FO spec.
 - [Antennahouse Formatter](https://www.antennahouse.com/)
   - License Costs required (pricey)
   - Great compliance with XSL-FO spec.
 - [Apache FOP (Apache Formatting Objects Processor](https://xmlgraphics.apache.org/fop/)
   - Free Open Source
   - Good compliance with XSL-FO spec.
   - Available also as:
       - [Apache FOP serverless via Azure Functions](https://github.com/cajuncoding/ApacheFOP.Serverless)
	     - Free Open Source
		 - Uses real Apache FOP in an ultra-lightweight Java API for Azure Functions.
       - [JSReport Engine with FOP Recipe](https://jsreport.net/learn/fop-pdf)
	     - License Cost required, but reasonably priced
		 - Provides ability to use real Apache FOP within their framework
 - [FO.NET](https://github.com/prepare/FO.NET)
   - Free Open Soure
   - Technically a different solution because it's all C#, but it is actually a port of Apache FOP
   - Based on an old -- but functional -- version of Apache FOP; no longer updated/supported.
   - Functional (e.g. moderate) compliance with XSL-FO spec.

For XSL-FO you have similar options for the paid-for solutions, as well as a long-standing reliable open source option 
from Apache.  The Apache FOP option has also been included in many other ports, frameworks, wrappers, etc. over the years 
because it offers the greater control of PDF layout and rendering needed.

In addition, the Apache FOP solution is a purely SDK based approach that can scale easily and even runs perfectly 
well in an Azure Function serverless environment.

One of the trade off between XSL-FO and CSS Paged Media approach is that the markup is all self-contained in an XSL-FO document – 
meaning all details are part of the source markup.  There are no separate layout files such as external CSS files like are possible 
with CSS Paged Media, so in this respect XSL-FO is more analogous to Embedded and Inline Styling use. 
So the trade-off is that the markup itself is larger and more complex instead of only the CSS files being extremely complex as 
they are with CSS Paged Media.  

But depending on the perspective, this isn’t necessarily a negative issue. This idea of styling re-use can be mostly managed effectively 
in the templating engine with partial templates, template includes, variable replacements, or and dynamically generated markup 
so the actual template code can still be very DRY with lots of style re-use throughout the final markup. 

And this may lend itself to a less complex development environment, especially for dynamically rendered reports.  Theres no needfor 
additional build processes, CSS pre-processors, etc. And, it has a nice advantage in that it fits very well with a PDF as a Service 
approach such as that provided by the [ApacheFOP.Serverless project](https://github.com/cajuncoding/ApacheFOP.Serverless) whereby a single 
markup file can be sent to the service more easily than a set of many files; granted with CSS Paged Media we could also use embedded styles,
but that also mitigates some of the value proposition of CSS.

### Conclusions:

If there is a very (_I mean very_) compelling requirement and/or use-case that is met particulalarly well 
([see #6 in the prior post](/2020-11-17-pdf-reports-part1-how-hard-can-it-be)) 
by a specialized solution that is also great at handling _printable media_ ([see #2 & #3](/2020-11-17-pdf-reports-part1-how-hard-can-it-be)).
Or if you already have that solution in your technology stack/landscape, then one of the specialized options, such as JasperReports, might be 
a good solution for your environment. However, since they are very specialized I would gravitate to one of the following 
options for several reasons, including the fact that they are based on industry standard specification(s).

Now, if you have a very cutting edge environment, and your use-cases or requirements will truly get value from the increible power 
of CSS (likely with a paid license product). And you have a lot of front-end development expertise on the team that is 
willing to work with the back-end team to develop a build process and workflow for developing and managing report templates.
Then CSS Paged Media is an awesome spec. with some powerful solution options that are capable of rending high quality 
and absolutely beautiful PDF results!  But there is complexity, an evolving spec., and most likely license costs to consider.

If however, you want to minimize complexity of your technology stack and focus primarily on having a solid, reliable, 
platform for rendering quality PDF outputs with _paged-media_ control, then Xsl Formatting Objects is in many cases a 
better selection. Your back-end development team will likely have equal or better expertise in managing the reporting 
templates along with other synergies since they are likely rendered from the back-end application anyway. And the maturity of 
XSL-FO ensures that you can start with ApacheFOP, as reliable free open source solution, and move in the direction of 
licensed options easily when/if needed.

I hope this excercise has been helpful... now Geaux Code some awesome PDF reports!

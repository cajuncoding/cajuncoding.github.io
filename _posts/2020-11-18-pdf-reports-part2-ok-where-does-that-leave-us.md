---
published: true
layout: post
title: PDF Reportings from Web Apps, How hard can it be?
subtitle: 'Why markup based Pdf Templating is the way to go...'
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

This is a multi-part series that I hope may be helpful to navigate the complexities of technology selection
and other important considerations for implementing Pdf Reporting in today's modern web applications.

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
over the years and the following are the two main players
- **Xsl Formatting Objects** -- _A markup language for XML document formatting that is most often used to generate PDF files._

- **CSS Paged Media** -- _CSS module specifies how pages are generated and laid out to hold fragmented content in a paged presentation (XHTML + CSS)._

These are both industry accepted standards that are completely agnostic of implementation technology, and are supported 
by a variety of solutions – free, open source, & paid-for.

And both of these provide a templating language that is a purely declarative markup language.  Therefore, we are free 
to use any templating technology we want including, but not limited to: Razor Templating in .Net, XSLT, JSP, JavaScript 
based templating (e.g. React, VueJS) in NodeJS, etc.  As long as it’s capable of rendering well-formed markup output 
(XHTML + CSS, or Xml).

The key benefit is simply that we have low risk of being limited by templating language. We are free to develop 
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
& feature rich options (you will find many others are outlined here):

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

There are some great paid-for options, but the free open source solution is and not as compliant with the spec. and 
uses some proprietary syntax for certain layout aspects, etc..

In addition, the CSS can become extremely complex for layouts – as in thousands of lines of CSS.  Thereby requiring 
the use of other technologies to better enable development such as CSS preprocessors, build processes, etc.  

All of which senior Front End developers are likely very familiar with, but when you remember the context that most 
Pdf rendering should occur in a back-end context – for security and data integrity reasons – then you may encounter 
a conflicting skillset issue.  

I did some preliminary research and it appears that WeasyPrint could likely be deployed in a serverless environment 
using Azure Functions support for Python, but this is not yet proven.  And there is the fact that it’s not realy spec. 
compliant.

So, in my experience, it’s important to recognize how this may introduce unnecessary complexity into your project!

#### Xsl Formatting Objects (XSL-FO) 

Meanwhile [XSL-FO is very much alive and kicking](https://readwritecode.net/ebooks/2019/04/28/xsl-fo-is-alive-and-kicking.html).  
XSL-FO is by the most mature spec for paged media rendering and was basically feature complete spec. in 2012. It’s 
been around a while, is very mature, is used in many industries and companies, and has well known free open source 
solutions as well as paid solutions; though the license costs are also pretty steep in my experience.

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
   - Functional (moderate) compliance with XSL-FO spec.

For XSL-FO you have similar options for the paid-for solutions, as well as a long-standing reliable open source option 
from Apache.  The Apache FOP option has been included in many other ports, frameworks, wrappers, etc. over the years to 
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
additional build processes, CSS pre-processors, etc. And, it has a nice advantage in that it fits very well with a Pdf as a Service 
approach such as that provided by the [ApacheFOP.Serverless project](https://github.com/cajuncoding/ApacheFOP.Serverless) whereby a single 
markup file can be sent to the service more easily than a set of many files; granted with CSS Paged Media we could also use embedded styles,
but that also mitigates some of the value proposition of CSS.

### Conclusions:

So if you have a very cutting edge environment, and your use-cases or requirements will truly get value from the increible power 
of CSS (likely with a paid license product). And you have a lot of front-end development expertise on the team that is 
willing to work with the back-end team to develop a build process and workflow for developing and managing report templates.
Then CSS Paged Media is an awesome spec. with some powerful solution options that are capable of rending high quality 
and absolutely beautiful PDF results!  But there is complexity, an evolving spec., and most likely license costs to consider.

If however, you want to minimize complexity of your technology stack and focus primarily on having a solid, reliable, 
platform for rendering quality PDF outputs with _paged-media_ control, then Xsl Formatting Objectes is in many cases a 
better selection. Your back-end development team will likely have equal or better expertise in managing the reporting 
templates along with other synergies since they are likely rendered from the back-end application anyway. And the maturity of 
XSL-FO ensures that you can start with ApacheFOP, as reliable free open source solution, and move in the direction of 
licensed options easily when/if needed.

















When rendering reports from a web application, there are far more options available than can be counted. 
However, we can greatly simplify the set of options by narrowing it down via a set of good practices and criteria 
that are critical for specific use cases.  In doing so, we can learn a lot about what makes a good report rending 
option vs the pitfalls of many of the less optimal choices.

For now, I am going to focus on Pdf rendering as it is still the “king of the hill” when it comes to portable and 
printable file formats that are universally viewable across machines & devices (computers, phones/tablets, operating systems, etc.).

In my experience, here is a non-exhaustive list of valuable criteria for consideration (esp. for technology selection):

#### 1. Does it rely on automatic (auto-magic) conversion?

In a web environment, we may need to render reports in a variety of formats such as Html, Pdf, Excel, etc.  The first thing 
to highlight is that these formats are dramatically different, therefore relying on any universal converter (e.g. HtmlToPdf 
or HtmlToExcel) will almost always yield unsatisfactory results for professional and enterprise use-cases. 

So we can get these out of the way, and immediately eliminate all “converter” options as viable solutions because we will 
assume that for professional use cases a much greater amount of control and quality will be needed for all output types – especially Pdf.

#### 2. Is it optimized for true Print output (not Web/Screen output)

Many solutions simply ignore the fact that controlling the layout of printed content differs pretty dramatically from web/screen 
layouts. 

Web layouts revolve around the concept of an infinitely scrollable canvas (in both horizontal & vertical directions) with a 
Viewport (screen size) that displays a section of that infinite canvas.  

This is in sharp contrast to printed outputs that revolve around the “page” concept where content must fit on a single page or 
it will roll over the next page; referred to as paged-media. Pages may have varying orientations and sizes but they are not 
infinite.  In fact, pages are highly controllable and may include repeatable content on each page such as header, footers, etc.
Trying to make a web layout fit into a printable layout is like trying to put a square peg into. A round hole. 

It can work in some cases but it’s never optimal and very often will have poor results.  I’m sure you’ve noticed how the 
vast majority of web pages look printed (e.g. Print Preview) is used in the browser.  Even when specialized CSS Print Stylesheets 
are used is still no control over the concept of a page; you must accept how the chosen browser engine chooses to render the 
output and break/roll content over into pages.  And you cannot, in a very controlled manner, define a header/footer. 

And, for now we are ignoring the additional complexity that is also inherent in trying to implement this in a scalable and 
reliable way ([as many have ](https://medium.com/compass-true-north/go-service-to-convert-web-pages-to-pdf-using-headless-chrome-5fd9ffbae1af)). 
Now, if the use-case is speficially to convert Html to Pdf for storage or snapshots to meet 
regulatory or compliance requirements then this may be exactly what is needed, but that is an edge use case and should not 
be conflated with rendering quality print outputs.

Therefore, normal browser-based rendering and use of browser engines such as Chrome to generate Pdf’s from normal Html & CSS 
is an inherently flawed process.

#### 3. Is a Templating approach to rendering supported?
Many solutions revolve around a PDF API approach that requires generating, manipulating, and outputting data into a PDF 
document via code.  This approach is not only exhaustive & error prone, but it’s also extremely constraining and will 
require massively greater amount of effort for a development team to deliver.  I refer to this as a code-based approach 
and is analogous to a coordinate based approach to creating output.

However, rendering Pdf outputs using a templated approach that provides great separation between presentation (the template) 
and data (the Model) is far more flexible and productive.  And, it brings to the forefront all the well known and well 
documented benefits of separating data from presentation that is prolific in all other aspects of web development 
(e.g. MVC, MVP, MVVM, etc.).

Now there are very valid use-cases for the code-based processing of Pdfs, but this is generally most beneficial for 
pre-processing and post-processing Pdf files and not the raw creation of quality reports as Pdf.  For example, if images or 
separate Pdf files need to be merged into a Report, then that is a fantastic use-case for a code based library to work with 
while maintaining all the flexibility and control of layout within the template (e.g. you can render placeholder(s) out 
where all images should be inserted into).

So we can wrap this by saying that for quality report rendering a templating approach is critical.

#### 4. Is the engine or the templating language proprietary or is it an open standard?
Now that we understand the value of templating we can start to value whey nearly all reporting engines focused on printable 
reports (e.g. Crystal Reports, SQL Server Reporting Services, JasperReports, etc.) all have a designer.  It was to provide 
this separation of data from presentation.

The issue we are now focused on is the nature of the templating engine and its capabilities.  On the positive side, proprietary
 reporting engines may support very advanced functionality (if the product chooses to).  But immediately you will encounter a
 negatively mitigating aspect – the proprietary nature of the templating engine.

You may also notice that there is not really a templating language per-se, but rather a designer tool.  This is a dependency
 that is useful for some environments whereby normal users  (not developers) are creating new reports based on the same or similar data in an ongoing basis. But, this is a limitation in many environments whereby the development team will create the reports and ongoing maintenance of those reports is more important than the need to create net new reports.
These designer tools usually have limited or no support for re-useable components between reports, and even when they do 
it’s very complex. In addition, they usually encourage bad habits such as data-binding to databases which tightly couples 
the reports with the data that it consumes.

However, the most critical issue with many reporting tools, is the proprietary nature of their rendering engines.  This is 
especially an issue for Crystal Reports whereby the engine is still (yes even in 2020) based on COM+ technology.  This means 
that it cannot be containerized outside of Windows, will always have scalability and performance concerns, and usually requires 
hacky .Net code to force garbage cleanup for the prevalent memory leaks that still exist!

Other tools like JasperReports use Java engine, which is more portable and feature rich, however it is still a proprietary 
engine and server based approach.  Therefore, once time is invested in developing the reports it may take significant 
investment to change or evolve in the future.  

And finally, these technologies may or may not work well in Cloud serverless environments which can make significant 
differences for enterprises that need to decrease server management complexity, costs, overhead, etc.

There is one more thing to touch on regarding templating engines as they relate to some code-based approaches described 
in #3 above.  Often these libraries claim to “support” a templating based approach, but what they mean is that you can 
build your own templating language using their SDK if you choose.  This may be intriguing in that “I want to prove that 
I can do it” kind of way, but in case it’s not blazingly obvious this approach will be riddled with complexity and limitations.

So, then what is the alternative?  Well the industry has evolved a couple alternatives over the years that address these 
concerns by gaining the value of Markup language (e.g. HTML) without the pitfalls… but I’m getting slightly ahead of myself.

#### 5. Service API or Library SDK/API support.
This may seem obvious, but in my experience it really isn’t.  It’s also related to the proprietary nature of rendering 
engines discussed in #4 above, but it is not necessarily the same.

Many solutions available (via Google search) offer a command line interface (CLI), but do not necessarily provide an 
actual SDK or API to consume in your web application.

The use of solutions such as this result in development of custom code wrappers for the CLI, which will also introduce 
unnecessary complexity, while at the same time likely provide many limitations and issues in multi-threaded web environments.

Suffice it to say that we should ignore any solutions that don’t offer a good SDK or API that can be easily integrated 
into a web application in a stable/reliable way.

#### 6. Support for your use-cases/requirements without unnecessary complexity.
It goes without saying that any solution should meet your existing use-case requirements.  But, I really mean it, it should 
meet your use-case requirements.

Ensuring that this is true means being familiar with your needs, both now and any strategic needs on the horizon. And, do not 
forgoe #2 above because without the control needed to generate high quality outputs for paged-media, you will either 
compromise your requirements or invest a tremendous amount of time and resources only to encounter a road block here.

Another point to note here is that product/solution maturity is very important for a professional enterprise environment. 
I’ll use NodeJS NPM packages as an example for this…. You can quickly find many libraries that claim to render Pdf outputs 
but these packages are very often immature and feature-poor; this applies to many libraries out there in any language 
(C#, Python, NodeJS, Java, etc.). 

#### 7. Price/Cost!
Ok, this is always a big question… and the smaller a company you work with, the bigger the question it is.  But in my 
personal experience, it’s really just as big of a concern for large company environments also -- usually due to the tremendous 
red-tape involved in procurement processes, project budget constraints, etc.

So, the important aspect is flexibility in price/costs as it relates to our existing technology stack, development team 
expertise, etc.

Any solution that has both paid and/or open source options that are robust enough to meet #7 above, is a winner in this category!


### Where does that leave us?

[In this next post](/2020-07-22-dynamic-cloudflare-dns-subdomain-redirect)we will take a deeper look into where this leaves us...



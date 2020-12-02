---
published: true
layout: post
title: 'PDF Reporting from Web Apps, How hard can it be?'
subtitle: 'Part 1: Considerations for a robust PDF or Web reporting solution?'
cover-img: /assets/img/crawfish-banner.jpg
thumbnail-img: /assets/img/pdf-icon.png
share-img: /assets/img/pdf-icon.png
tags:
  - azure-functions
  - serverless
  - 'c#'
  - NodeJS
  - pdf
  - pdf templating
  - .net
  - apache-fop
---

This is a multi-part series that I hope may be helpful to navigate the complexities of technology selection
and other important considerations for implementing PDF Reporting in today's modern web applications.

When rendering reports from a web application, there are more options and technologies available than can be counted. 
However, we can greatly simplify the set of possible solutions, we should even consider, by narrowing it down via a 
set of good practices and criteria that are critical for specific use cases.  In doing so, we can learn a lot about what 
makes a good report rendering option vs the pitfalls of many of the less optimal choices.

For now, I am going to focus on PDF rendering as it is still the *king of the hill* when it comes to portable and 
printable file formats that are universally viewable across machines & devices (computers, phones/tablets, operating systems, etc.).

In my experience, here is a non-exhaustive list of valuable criteria for consideration (esp. for technology selection):

### 1. Does it rely on automatic (auto-magic) conversion?

In a web environment, we may need to render reports in a variety of formats such as Html, PDF, Excel, etc.  The first thing 
to highlight is that these formats are dramatically different, therefore relying on any universal converter (e.g. HtmlToPdf 
or HtmlToExcel) will almost always yield unsatisfactory results for professional and enterprise use-cases. 

So we can get these out of the way, and immediately eliminate all “converter” options as viable solutions because we will 
assume that for professional use cases a much greater amount of control and quality will be needed for all output types – especially PDF.

### 2. Is it optimized for true Print output (not Web/Screen output)

Many solutions simply ignore the fact that controlling the layout of printed content differs pretty dramatically from web/screen 
layouts. 

Web layouts revolve around the concept of an infinitely scrollable canvas (in both horizontal & vertical directions) with a 
Viewport (screen size) that displays a section of that infinite canvas.  

This is in sharp contrast to printed outputs that revolve around the “page” concept where content must fit on a single page or 
it will roll over the next page; referred to as paged-media. Pages may have varying orientations and sizes but they are not 
infinite.  In fact, pages are highly controllable and may include repeatable content on each page such as header, footers, etc. These concepts simply do not exist in web page latouts. Thats why attempts to make a web layout fit into a printable layout is like trying to put a square peg into a round hole; sure a small enough square peg can be used, but its never going to be a good fit.

It can work in some cases but it’s never optimal and very often will have poor results.  I’m sure you’ve noticed how the vast majority of web pages look when 
printed in the browser (e.g. Print Preview). Even when specialized CSS Print Stylesheets 
are used, there is still no control over the concept of a page; you must accept how the chosen browser engine chooses to render the 
output and break or roll content over into pages.  And you cannot, in a very controlled manner, define a header/footer. 

And, for now we are ignoring the additional complexity that is also inherent in trying to implement this in a scalable and 
reliable way ([as others have tried](https://medium.com/compass-true-north/go-service-to-convert-web-pages-to-pdf-using-headless-chrome-5fd9ffbae1af)). 
Now, if the use-case is specifically to convert Html to PDF for storage or snapshots to meet 
regulatory or compliance requirements then this may be exactly what is needed, but that is an edge use case and should not 
be conflated with rendering quality print outputs.

Therefore, normal browser-based rendering and use of browser engines such as Chrome to generate PDF’s from normal Html & CSS 
is an inherently flawed process.

### 3. Is a Templating approach to rendering supported?
Many solutions revolve around a PDF API approach that requires generating, manipulating, and outputting data into a PDF 
document via code.  This approach is not only extremely complex & error prone, but it’s also extremely constraining and will 
require massively greater amount of effort for a development team to deliver.  I refer to this as a code-based approach 
and is analogous to a coordinate based approach to placing letters, text, etc. on a page to create the PDF output.

However, rendering PDF outputs using a templated approach that provides great separation between presentation (the template) 
and data (the Model) is far more flexible and productive.  And, it brings to the forefront all the well known and well 
documented benefits of separating data from presentation that is prolific in all other aspects of web development 
(e.g. MVC, MVP, MVVM, etc.).

Now there are very valid use-cases for the code-based processing of PDFs, but this is generally most beneficial for 
pre-processing and post-processing PDF files and not the raw creation of quality reports as PDF.  For example, if images or 
separate PDF files need to be merged into a Report, then that is a fantastic use-case for a code based library to work with, as a post-processing step,
while maintaining all the flexibility and control of layout within a  template approach (e.g. you might render placeholder(s) out 
where all images should be inserted into, or template separate secrions and merge in a followup step).

So we can conclude this discussion by saying that for quality report rendering a templating approach is critical.

### 4. Is the engine or the templating language proprietary or is it an open standard?
Now that we understand the value of templating we can start to value why nearly all reporting engines that are focused on printable 
reports (e.g. Crystal Reports, SQL Server Reporting Services, JasperReports, etc.) all have a "report designer".  It was to provide 
this separation of data from presentation.

The issue we are now focused on is the nature of the templating engine and its capabilities.  On the positive side, proprietary 
reporting engines may support very advanced functionality (if the product chooses to).  But immediately you will encounter a 
negatively mitigating aspect – the proprietary nature of the templating engine.

You may also notice that there is not really a templating language per-se, but rather a designer tool. This is a dependency 
that is useful for some environments whereby normal users (not developers) are creating new reports based on the same or 
similar data in an ongoing basis. But, this is a limitation in many environments whereby the development team will create 
the reports and ongoing maintenance of those reports is more important than the need to create net new reports.

These designer tools usually have limited or no support for re-useable components between reports, and even when they do 
it’s very complex. In addition, they usually encourage bad developer habits such as data-binding to databases which tightly couples 
the reports with the data that it consumes. And the designer tools are also why some advanced layout capabilities, like 
dynamically changing page orientation and/or page sizes based on the template or the data, is not possible.  Because 
these values are usually set at 'design' time and therefore can't be changed -- this is a limitation I've encountered 
while working with clients that used Crystal Reports and SSRS.

However, the most critical issue with many reporting tools, is the proprietary nature of their rendering engines. This is 
especially an issue for Crystal Reports whereby the engine is still (yes even in 2020) based on COM+ technology. This means 
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

### 5. True Library SDK/API support and/or a Service API (SOA).
This may seem obvious, but in my experience it really isn’t.  It’s also a topic related to the proprietary nature of rendering 
engines discussed in #4 above, but it is not necessarily the same.

Many solutions available (via Google search) offer a command line interface (CLI), but do not necessarily provide an 
actual SDK or API to consume in your web application.  The use of solutions such as this result in development of custom code wrappers for the CLI, which will also introduce 
unnecessary complexity, while at the same time likely provide many limitations and issues in multi-threaded web environments.

And, beware many solutions that offer an SDK might just be exactly that -- a thin wrapper around a 
CLI implementation. So it is important to understand how it actually works under the hood and to assess 
the risks that may be associated with its scalability and behavior in a multi-threaded or heavy load web 
environment.

Suffice it to say that we should really stay away from any solutions that don’t offer a good SDK or API that can be easily integrated 
into a web application in a stable/reliable manner.

### 6. Support for your use-cases/requirements without unnecessary complexity.
It goes without saying that any solution should meet your existing use-case requirements.  But, I really mean it, it should 
meet your use-case requirements.

Ensuring that this is true means being familiar with your needs, both now and any strategic needs on the horizon. And, do not 
forgoe #2 above because without the control needed to generate high quality outputs for paged-media, you will either 
compromise your requirements or invest a tremendous amount of time and resources only to encounter a road block here.

Another point to note here is that product/solution maturity is very important for a professional enterprise environment. 
I’ll use NodeJS NPM packages as an example for this…. You can quickly find many libraries that claim to render PDF outputs 
but these packages are very often immature and feature-poor; this applies to many libraries out there in any language 
(C#, Python, NodeJS, Java, etc.). 

### 7. Price/Cost!
Ok, this is always a big question… and the smaller a company you work with, the bigger the question it is.  But in my 
personal experience, it’s really just as big of a concern for large company environments also -- usually due to the tremendous 
red-tape involved in procurement processes, project budget constraints, etc.

So, the important aspect is flexibility in price/costs as it relates to our existing technology stack, development team 
expertise, etc.

Any solution that has both paid and/or open source options that are robust enough to meet #6 above, is a winner in this category!


## Where does that leave us?

[In the next post](/2020-11-18-pdf-reports-part2-ok-where-does-that-leave-us) we will take a deeper look into where this leaves us...

---
published: true
layout: post
title: Netwontsoft.Json dependency conflicts with Azure Functions (v2)
subtitle: Why is this even a problem? Well, here's a little glance under the hood...
cover-img: /assets/img/crawfish-banner.jpg
thumbnail-img: >-
  /assets/img/2020-07-21-dynamic-cloudflare-dns-for-github-pages/azure-fn-logo-raster.png
share-img: >-
  /assets/img/2020-07-21-dynamic-cloudflare-dns-for-github-pages/azure-fn-logo-raster.png
tags:
  - azure-functions
  - serverless
  - c#
  - .net
  - under-the-hood
---
If you're encountering an error with Newtonsoft.Json dependency conflicts in Azure Functions (both .net Framework V1 or .Net Core V2), you're not alone. And luckily the solution is most likely really straight-forward (unless you're also doing of [these gotchas](#gotchas)).  

We encountered this issue recently and, though it was initially confusing, in-the-end it does make total sense -- even the gotchas. Unfortunately, it ended up taking longer than you'd think to fully understand the resolution, so I hope this additional context helps someone else out because the the fix fairly simple in the end.

Feel free to [jump down to the resolution](#resolution), but I think it's also really important to understand not only how to fix it, but why it needed to be fixed at all.

## This is a pretty common issue
First, if you're not familiar with some details of Azure Functions, here's a great post by [Hari Haran to start with](https://medium.com/@hharan618/common-issues-while-development-of-azure-functions-76b08299af58)!

The Newtonsoft.Json dependency conflicts are also talked about in-depth on various Github issues [here](https://github.com/Azure/azure-functions-vs-build-sdk/issues/304), and [here](https://github.com/Azure/azure-functions-host/issues/4049), and [here](https://github.com/Azure/Azure-Functions/issues/1079), etc.

## Our issue
We encountered the Newtonsoft.Json conflict in our Azure Functions project for background processing (another series to come later) after adding a new feature to download data from Google Ads service using their API library. The devs pulled in the latest Google Ads API library and went to work. Once a bunch of code was written and unit-tested they integrated into the main Azure Functions project to start end-to-end testing and immediately encountered Newtonsoft.Json dependency conflicts!

## The actual error:

You may see Errors like this (*hopefully this is how you find this post*):
```
Error	NU1107	Version conflict detected for Newtonsoft.Json. Install/reference Newtonsoft.Json 12.0.1 directly to project <YOUR_PROJECT_NAME_HERE> to resolve this issue. 
  <YOUR_DEPENDENCY_PROJECT_NAME_HERE> -> Newtonsoft.Json (>= 12.0.1) 
  <YOUR_PROJECT_NAME_HERE> -> Microsoft.NET.Sdk.Functions 1.0.28 -> Newtonsoft.Json (= 11.0.2).	
```

<img src="../assets/img/2020-08-01-azure-functions-newtonsoft-json-conflict/actual-newtonsoft-json-conflict-error.png " class="fullsize center" data-zoomable />
  
Take note that is an **Error** so the project will not run at this time.

## Now Why is this?  

Well just as [Mr. Haran](https://medium.com/@hharan618/common-issues-while-development-of-azure-functions-76b08299af58) noted in his article, we can't use two different versions of Newtonsoft.Json at the same time in the same process (generally speaking).

In our case, it is because the Azure Functions SDK in use requires a lower version than the Google Ads API library that was now added to our project(s). And even though the new code and dependencies were in their own C# class library, as a dependency, this will be a problem. Now why is that?  Why does the SDK require a lower version?

The short & sweet details are quickly noted in the Github issue comment [here, by Andrew Hill](https://github.com/Azure/azure-functions-host/issues/4049#issuecomment-536836589). In other words, the runtime can only load one version of an assembly/dependency into the current [App Domain](https://docs.microsoft.com/en-us/dotnet/framework/app-domains/application-domains) at a time (e.g. process or  AssemblyLoadContext for .Net Core). And, the dependency is lazy-initialized (on-demand) only after some executing code, that needs it, starts to run. Like static constructors, when the class is first loaded it's dependencies are initialized lazily, but then cached for future performance with the assumption that the code will not change since it's a compiled assembly. So now this begs the question: "*what version gets loaded?*" And this isn't a new concern...

More details about how .Net resolves assemblies is outlined here (developers should definitely take the time to learn these concepts):
 - [.Net Framework Assembly Resolving](https://docs.microsoft.com/en-us/dotnet/framework/deployment/how-the-runtime-locates-assemblies)
 - [.Net Core Assembly Loading Algorithm](https://docs.microsoft.com/en-us/dotnet/core/dependency-loading/loading-managed)

## Ok, so what's the fix?
Can't we just upgrade our SDK? Sort-of, we can't jump to v3.X because we aren't using Azure Functions V3 (*thankfully the version naming is now in sync*).  And there is no v2 (*again Microsoft skipped it to sync up the version naming*).  Now, we could move up to the latest version of v1.0.X, but alas that only partially solves the issue because even v1.0.37 is still using Newtonsoft.Json v11.0.2 (for .Net Standard):

<img src="../assets/img/2020-08-01-azure-functions-newtonsoft-json-conflict/azure-fn-sdk-dependencies-1-0-37.png " class="medium center" data-zoomable />

**And, this introduces more testing risk** that we really didn't have time to work on at the moment -- it was not planned for in our sprint or project plan at this time.  So, knowing that, I did add a backlog task to upgrade our libraries in the future because that is definitely a good thing to do eventually. 

But, if you're reading this and don't realize -- upgrading the version is higher risk because things can (*and fairly often will*) break.

## But wait, I remember some Web.config magic that worked in my MVC app?
In a normal .Net web application dependency conflicts were always a risk, which is why [Binding Redirection](https://docs.microsoft.com/en-us/dotnet/framework/configure-apps/redirect-assembly-versions) was created!  And it works extremely well for it's purpose. That's why it's soo important for developers to truly understand how their language works under-the-hood! So that when these things occur you not only "fix" it, but you truly understand why it's fixed!

Ok, so if you already know this skip ahead.... but you may have had the same question I did -- "*How or why don't we just use Binding Redirection to fix our issue in Azure Functions?*"  Well because it's not support yet (shrug).

There are some pretty clever workarounds to implement a form of binding redirection for Azure Functions v1, [like this documented here](https://www.thewissen.io/azure-functions-binding-redirects/), but we are using **Azure Functions v2 using .Net Core** (*for it's various benefits like performance*).

<a name="resolution"></a>
## So, what's the real resolution?

Well, I spent all this time doing research with some Google-Fu...but I didn't really look closely enough at the error that Visual Studio was showing.  As it turns out the error is telling us what we can do, but it's just not super clear. Microsoft has added in the ability to mostly account for this into the compiler (MSBuild Task) and the error does explain it...sorta:  

`Install/reference Newtonsoft.Json 12.0.1 directly to project <YOUR_PROJECT_NAME_HERE>`

But, what does that really mean?  Well, there is some additional documentation hidden in plain sight, but oh-so easily overlooked in the [Readme for Azure Functions on Github right here](https://github.com/Azure/azure-functions-vs-build-sdk#q-i-need-a-different-newtonsoftjson-version-what-do-i-do)!

For posterity, I'm capturing the current Q&A on Json in this thumbnail:
> <img src="../assets/img/2020-08-01-azure-functions-newtonsoft-json-conflict/azure-fn-qa-capture-browser-window.png " class="thumbnail" data-zoomable />

So applying that (incredibly easy solution), all we need to really do is add a direct reference to our primary project -- the one that is using the Azure Functions SDK -- then things do start to get a lot better!  Not perfect (more on that in a sec.) but much better:

In Visual Studio for .Net Core, we can just double-click the project name and we see the project file's raw Xml and edit it... or use Nuget Package manager to just install the version of Json needed (*should be the version that matches the library you are using, or the latest version needed among various libraries*). For us this was **Newtonsoft.Json v12.0.1**.

<img src="../assets/img/2020-08-01-azure-functions-newtonsoft-json-conflict/add-newtonsoft-ref-into-main-project.png " class="fullsize" data-zoomable />

#### Snippet:
```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs" Version="3.0.5" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.28" />
    <PackageReference Include="RestSharp" Version="106.6.10" />
    <PackageReference Include="Newtonsoft.Json" Version="12.0.1" /> <-- THIS REF. was manually Added -->
  </ItemGroup>
```

**FYI, In our case, our primary project does not even need Newtonsoft.Json.** We aren't using it in our primary code at all and that's why there was no reference before. But, this allows us some modicum of control over the version of Newtonsoft.Json that will be resolved when *our own application code runs*!  But there are [still some gotchas](#gotchas) that must be understood to help avoid future pain from random runtime errors (heaven forbid after it's all in Production)!

## You can also validate this as follows:

In Visual Studio Dependencies:
<img src="../assets/img/2020-08-01-azure-functions-newtonsoft-json-conflict/vs-dependencies-validation.png " class="fullsize center" data-zoomable />

And after Build we now see a **Warning** instead of an **Error**:
```
Warning	NU1608	Detected package version outside of dependency constraint: Microsoft.NET.Sdk.Functions 1.0.28 requires Newtonsoft.Json (= 11.0.2) but version Newtonsoft.Json 12.0.1 was resolved.
```

<img src="../assets/img/2020-08-01-azure-functions-newtonsoft-json-conflict/newtonsoft-json-dependency-conflict-warning.png " class="fullsize center" data-zoomable />

## We are ok now!

This Warning means that our code can compile & run, and it tells us clearly that the version that will be resolved is the version we want for our own code -- **the version that Google Ads library needs!**

<a name="gotchas"></a>
## Caveats and Gotchas that you should Understand!

Both the [FAQ here](https://github.com/Azure/azure-functions-vs-build-sdk#q-why-is-newtonsoftjson-locked-in-the-first-place), and some [astute comments here in the Github issue](https://github.com/Azure/azure-functions-vs-build-sdk/issues/304#issuecomment-507477114), address a **critical gotcha** that must be understood to avoid runtime pains!

Our application will be running with the expected version of **Newtonsoft.Json v12.0.1**, but the Azure Functions Runtime on the server may not (*because it appears to run in it's own process or AssemblyLoadContext before communicating with our code*)! 

That's why this dependency constraint existed in the first place and it really makes sense once you understand the fundamentals.  But we didn't eliminate the issue of multiple versions, we just enabled our application to resolve our version so that it could run.

There are still interaction points with the core Azure Function runtime where we must avoid sharing classes because they will not be compatible (per Assembly class identification).  The app will compile because of the late-binding, but these exceptions/errors will occur at runtime!

This is most likely to occur if you are using core Newtonsoft.Json classes like JObject, or JToken as a primary means of passing data into and out of the Azure Functions (e.g. as DTO). You likely would be doing this through variable binding, but could also be through other SDK method parameters, etc.  As the FAQ shows, here's an example:
> <img src="../assets/img/2020-08-01-azure-functions-newtonsoft-json-conflict/azure-fn-faq-jobject-binding-example.png " class="medium center" data-zoomable />

Notice the JObject class is used as the class type for the queue binding. This is instructing the Azure Functions runtime to use that class type for dynamic (late-binding) de-serialization of the data from the Queue that will trigger this Azure Function. When this JObject is created it will be from v11.0.1 (*Newtonsoft.Json*) as determined by the Azure Function runtime, but when passed into your function your code will execute with v12.0.1 (*because we've now fixed that*), and **an exception will occur immediately!**

As stated in the FAQ:
> That jObject instance will be fulfilled by the runtime version of JObject. 
> If there is a version mismatch, the runtime will not be able to give you the version of JObject you are using from your custom Newtonsoft.Json version.

### So how to avoid this Gotcha?
To avoid this gotcha is pretty easy (and in my opinion is just generally a better practice), always use your own classes and/or primitive values (e.g. Json String) for integration (or inter-process) communication. 

If you have data going to/from a Queue then you can still use Json serialization, but the binding should de-serialize into your own class, and you won't have any issues.  Alternatively you could just take the data/payload in as a serialized string (Json) and quickly deserialize yourself.... 

In our case this was a non-issue, because we always use our own [DTO Classes](https://en.wikipedia.org/wiki/Data_transfer_object).

Here's a quick snippet of what the sample from the FAQ would be with your own DTO class named *HelloQueuePayload*:
```csharp
[FunctionName("hello")]
public static async Task ProcessQueue([QueueTrigger] HelloQueuePayload helloQueuePayload)
{
    // do stuff;
}

```

Or here's an example of taking in a serialized string as the payload; which is ever-so-slightly less recommended, but for really still perfectly fine if you implement validation):

```csharp
[FunctionName("hello")]
public static async Task ProcessQueue([QueueTrigger] String helloQueuePayloadText)
{
  var helloQueuePayload = JsonConvert.DeserializeObject<HelloQueuePayload>(helloQueuePayloadText);
    // do stuff;
}

```


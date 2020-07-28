---
published: true
layout: post
title: My migration from Mvelopes to YNAB!
subtitle: >-
  A Rant on my Love-Hate realationship with Mvelopes - hoping "You Need A
  Budget" (YNAB) can return my budgeting sanity!
cover-img: /assets/img/crawfish-banner.jpg
thumbnail-img: >-
  /assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/mvelops-to-ynab-thumbnail.png
share-img: >-
  /assets/img/2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv/mvelops-to-ynab-thumbnail.png
tags:
  - mvelopes
  - ynab
  - finances
  - budget
---
I've been using Mvelopes as a budgeting and financial tracking system for many years! And, I'm a fan of the concept, but the actual software has been a hot-mess, pretty much since I joined. The old v4 Flash based UI is still in use today due to all the issues in v5; I mean, really? Flash and/or Silverlight UI's were soo 2008! And [Silverlight](https://theorem.co/verticals/silverlight-modernization) was already going the way of the [Dodo](https://en.wikipedia.org/wiki/Dodo) by 2015.

But, I digress, Finicity did finally build **Mvelopes v5** as a pure Javascript (SPA) application. And I was truly excited.... **but truly let down!** 

## Mvelopes v5 has been such a let-down:
Finicity dropped the ball on v5 in more ways than I can count . . . occasionally I wonder how they stayed in business through this transition; but then I realize that the product team & development team definitely are not under-payed, they're probably compensated appropriately:
- The app was slow and buggy; still plagued with memory leaks.
  - I, still to this day, have to hit F5 to reload after dragging/dropping 10+ transactions.
  - The **Planning -> Income** panel has randomly forgotten or lost my income amounts and payment dates on multiple occassions.
  - Making a change to any income amount (**Planning -> Income**) causes ALL related funding plan details, from that income, to be deleted without warning; click over to the Monthly Funding tab and lots of data is just gone.
    - *I've minimized my pain from this with some hackery that [I'll share here...](./2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv%20copy.md)*
  - What's that error about 24 characters? You won't let me type more than **24 characters** into any Title or Despcription field anymore?
    - *This one item drives me crazier than ANYTHING! Hardly any useful titles can be put into a paltry 24 character field!*
- Mvelopes v5 is still missing TONS of features... its not even remotely close to having feature parity with v4.
  - *Not that most of v4 features worked all that well, but I was able to make it work for me.*
- The Mobile App has limited use, and takes much too long to launch, login, scroll+click, to find the Mvelope of interest.
  - *There is just too much friciton in the user experience to serve the primary purpoase of helping you pro-actively know the status of a budget item prior to making a purchase (e.g. while waiting in line, or about to drive there in your car, etc.)*
- They FORCED users to migrage with an aweful migration plan that broke more times than it worked
  - *Eventually I think they let people revert back . . . but umm, yeah all data changes simce the migration are lost -- not worth it for me.*
- And, core features for importing transactions just never got better -- in today's world, two-factor authenticaion **is not optional**, and their support for it is abysmal. For my own accounts they fail to import transactions about 4 out of 5 times
  - Ok it's a little better after the first 6-12 months or so, but it still works only 3 out of 5 times.
- Did I mention they still don't have feature Parity yet as of July 2020 ([beta launched in Feb. 2018](https://www.mvelopes.com/2018/02/))?

I say all of this because, I'm not alone in wanting to find a better alternative! But, I've struggled with the idea of starting over, though I think I've finally gotten over the friction of "oh man I've got to re-budget all over again". To the point that I'm now at, "oh man, if I can find a working solution and just inveest the time once, then maybe I can stop dreading (and start enjoying) my budget balancing days again!".

## So what's a good budgeting platform look like for me?
I had casually investigated alternatives before, and of course [Mint](https://www.mint.com/) was always an option, but it never met my goals for a budgeting app because it didn't offer the proactive elements I want. Even though it's free, I would be happier with some of my own advanced Google Sheets/Excel spreadsheets for the same (*free*) price.  There are many other great blogs and videos comparing Mint to new options so I'll leave that to you to follow up on.

For me, these are some of the core capabilities that a budgeting platform needs to provide for me, and I'm happy and willing to pay for it:
 - Provide direct importing of all transactions from linked accounts
   - *This needs to work consistently.  I don't mind troubleshooting 1 out of 5 times, but any more and I'm more annoyed and now already losing efficiency.*
 - Provide an easy & efficient way to allocate these transactions to my budget (e.g. help me finish, and don't annoy me while doing it)
   - *Memory leaks that force me to refresh my browser to keep working disqualifiees this Mvelopes from being both easy and efficient.*
 - Allow me to control how many budget items and how granular I want to manage my budget
 - Help me track expenses and costs of a budget over time (e.g. Coffee Budget) so that I can look back on it retro-spectively.
   - *This is extremely useful for reflecting on things like Vacation costs that we budget for individually*.
 - Help me pro-actively keep my costs in check; I need to be able to see quickly/easily anytime a budget item is at risk or over budget for a month.
   - A tangential benefit here is also to quickly help me identify any risk/fraudulent/incorrect charges to any of my accounts
     - *Because in today's world this has & will happen eventually.*
 - Allow me to search through all my transactions find random purchases for review.
 - And finally let me Export my transactions and budget deails.
   - *Because everything should be backed-up, and I want to have a copy of my own data.*

A system that empowers me to be productive with my budget, helps me pro-actively stay on track for long term savings goals, and makes the process tolerable (or maybe even enjoyable) is worth paying for!

# You Need a Budget (YNAB) looks very promising!
So, for a long time I didn't think there were many alternatives that met my key goals for a great budgeting platform. But, it appears that I may have been mistaken [as new research has shown](https://www.google.com/search?q=transition+from+mvelopes&oq=transition+from+mvelopes) (driven by my now maxxed-out frustration with Mvelopes):
<img src="../assets/img/2020-07-27-transition-from-mvelopes-to-ynab/google-search-transition-from-mvelopes.png " class="medium center" data-zoomable />

[The first search result here](https://support.youneedabudget.com/t/x147a0/my-transition-from-mvelopes-to-ynab), immediately gave me more hope than I'd had before.  And, after several days of research, pondering, and taking in the concepts (*mainly from Youtube*) I realized that YNAB has a solid following, and seems very interested in publishing really good and helpful information for it's users.  Plus, if you [watch Hannah](https://www.youtube.com/playlist?list=PLq0_N-XTl2yDWGTHHHYhfB_KumLx1zANh), it's pretty much impossible to not be convinced that YNAB might just change your life (she's really a great entertainer)!

So, there you have it, I'm inspired to leave Mvelopes and try something better . . . (crossing my fingers that the something bettetr is [YNAB](https://www.youneedabudget.com/).

And that's the inspiration for this series of posts:
 - [My migration from Mvelopes to YNAB!](./2020-07-27-transition-from-mvelopes-to-ynab.md)
 - [Backing-up my Mvelopes *Monthly Funding Plan* data up (with some hackery)!](./2020-07-27-mvelopes-script-to-export-funding-plan-details-as-csv%20copy.md)
 - Backing-up my Mvelopes Transaction Data up!
 - Transitioning from Mvelopes to You Need a Budget (YNAB)!

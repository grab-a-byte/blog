# Open source going commercial is not rug pulling

### 16/11/2025


This post is split into 2 parts. The first part is a fairly short, quite straight-to-the-point reason as to why I think going commercial is not rug pulling, the second part is very close to "old man shouts at cloud" and you are welcome to skip it if you so chose.

# Part 1

Recently, in the .NET ecosystem, there have been a fair few projects that have gone from being free and open-source to a commercial licence which has caused quite some stir within the .NET ecosystem.

Examples of some of these libraries are
- [Mass Transit](https://masstransit.io/)
  - As seen in their post [here](https://masstransit.io/introduction/v9-announcement)
- [MediatR](https://mediatr.io/) and [AutoMapper](https://automapper.io/)
  - Both announced [here](https://www.jimmybogard.com/automapper-and-mediatr-going-commercial/)
- [Fluent assertions](https://fluentassertions.com/)
  - Announced in its [V8 changelog](https://fluentassertions.com/releases/#800)

This has led to seeing some posts online about this being a rug pull or just not in the spirit of open source, and I don't think that this is really fair to the developers who make these libraries.

When we learn how to develop software, we are taught how to use dependencies and that it is good practice to use them to solve your problems. You are never told the consequence of doing so and are also never given any insinuation of cost associated with it. Then when we develop in a professional capacity, naturally we think in the same way we have learned, use the free dependecies to build on top of to make your own software. This almost built-in and taught idea that dependencies should be free is an odd one when you really think about it. 

<div style="text-align: center; width: 40rem">

![Fragile software tower](./images/not-rug-pulling/software-tower.png)

</div>

If we look at all the other software we use on a frequent basis and how they are funded in either time or money and it shows a difference from developers chosing their tools and paying for them to using what they can because it's free (and of course if something is free it's either because you are the product or it's used by someone else for something else).

## Compilers
Compilers are one of the most common tools we use and we expect them at this point to be free. A lot of languages are built and supported by large organisations where their interest is improving the tool for their own need and have some agenda to making them work for themselves. This cost is essentially swallowed by the organisations making them (e.g. Google for Go and JS with V8, Microsoft for C# and the .NET toolchain). It wasn't always this way, Microsoft themselves started out by selling BASIC compilers/interpreters. _If you would like more information on this you can look at [More Compilers](#more-compilers) part of this post_.

## Editors
Many people within the .NET ecosphere are probably using Visual Studio which has a community edition that is free but is most likely paid for you through the organisation you work at. Some people will use JetBrains Rider and JetBrains as a company as a whole shows that people will pay for an editor if it has enough features. With the rise of LSP ([Language Server Protocol](https://en.wikipedia.org/wiki/Language_Server_Protocol)) more features are moving to that and using an editor like VS Code, Neovim, Helix or Zed (free editors) which may have had an impact on people's perception of paying for an editor. _If you would like more information on this you can look at [More Editors](#more-editors) part of this post_

## Operating System
Most of us working in the .NET space are most likely developing on Windows (to be able to use Visual Studio) with a licence that was paid for by the organisation. Even machines for personal use will have had a licence paid for it by you for home use. You may not see the cost and may not have picked up a copy of Windows but it will be included in the cost of purchasing a Laptop or Desktop PC that you buy from your local computer store. _If you would like more information on this you can look at [More Operating Systems](#more-operating-systems) part of this post_

## Office Suites
Far too many businesses are run on Microsoft Office and Microsoft Office alone. This again is a paid offering and is even more recently a subscription. Yes free alternatives are available (e.g. LibreOffice) however I am yet to see an organisation use these.

## Conclusion

So, with all these things in mind that are paid for, why is it any different for a core piece of the software you are creating? If it is core to your business, it should be made in house or if it's not core but you need it, it should be paid for. These developers have spent countless hours developing something that a business is relying on and as we as developers are in our job creating software, there deserves to be compensation for it. 

The best way forward I belive is what a lot of the newly commercialised parts of the software are doing, make it free to start for hobbyist and for people trying to make a business, then as the organisation grows, they can pay for a licence for the software to help ensure the foundation on which they build is well maintained.

This model is already in use by some large companies such as Unity and Epic games who have revenue threasholds before you need to start paying them.

# Part 2

As warned in the intro, some of this may seem like 'old man shouts at cloud' but it also contains some more in-depth links you can follow for more information.

<div style="text-align: center; width: 40rem">

![Old man yells at cloud](images/not-rug-pulling/old-man-yells-at-cloud.png)

</div>

As developers, we seem to have this innate, built-in expectation that everything we use should be free (especially in the .NET world from my perspective), the moment that anything is announced as having to cost there is an immediate response of "GET THIS OUT OF HERE NOW!!!", this might be a small hyperbole, but probably not far off. It's entirely possible that this come from the knowledge that we all know we will not convince the organisations we work for to pay for software as it will affect their bottom line no matter how small a cost it may be.

## More Compilers
Compilers were once a thing that was bought for use, even Microsoft started by creating compilers/interpreters and selling that either to companies or to individuals to make their money([see Altair BASIC](https://en.wikipedia.org/wiki/Altair_BASIC) or [Microsoft BASIC](https://en.wikipedia.org/wiki/Microsoft_BASIC)). Nowadays, we just expect a compiler for whatever language we use to be free.

- .NET (C#, F# Visual Basic)
  - This is funded by Microsoft, they use the technology themselves which is their incentive to keep contibuting to it, however imagine a world where suddenly, Microsoft suddenly just decide to use Rust, suddenly development on .NET
 and improvements wouldn't happen and I imagine that if we were asked to pay for it, few of us would.
- Rust
  - This was originally a project that was made by someone in their spare time and was then tested in Mozilla (and funded by Mozilla) before being moved to the [Rust Foundation](https://rustfoundation.org/) who now fund it by getting corporate membership which allow companies control over the direction of Rust and it's devleopment.
- Go
  - Go was made by Google and was made to solve problems the designers found in lanaguages like C++ and issues they were seeing at Google. Google made it for itself and out of kindess (probably for employability reasons) shared it with the world. It's development is funded because Google are still using it internally and want to keep it alive.

## More Editors
Editors used to be paid for and for many of them still can be. We can see the fact that JetBrains as a company are surviving and striving as evidence that enough people will pay for an IDE. Visual Studio however used to have a split between the "Express" version and the version you actually paid for until 2014 when it was decided to make the "Community" edition which got the main featues of the IDE. Even now though it is still a paid experience for Professional and Enterpise which is targeted to make organisations pay for the development (someting I agree with).

However these days I think you will find more and more people using an editor like [VS Code](https://code.visualstudio.com/) which is free for everyone. I can imagine a new organisation today would chose to use this over more in-depth and useful tools for dvelopers in order to improve their bottom line. However this is also where people start because it's free. When you learn you use the free tool and then when money is made, none goes to the tools you use.

## More Operating Systems
We all pay for Windows one way or another. We either
  - Buy an official licence and then they sell the data they get from you.
  - Have the 'free upgrade' they gave existing users and they sell the data they get from you.
  - It's part of the laptop/PC you bought and it's been paid for by the manufacturer to put it on the machine.

Many of the astute among you will say "Ah but what about Linux". Linux is indeed free however if you look at how it's used and where it's used and where it's funding comes from, it's organisations. The Linux Foundation takes funds from many companies to fund it (see [https://www.linuxfoundation.org/about/members](https://www.linuxfoundation.org/about/members) for more details) and a lot of these organisations use Linux as part of their operations e.g:
  - All phone companies and Google use Android which sits on a Linux Base.
  - AWS (Amazon Web Services) uses it to run a majority of it's cloud infrastructure and services.
  - Microsoft use if for Azure as well as for WSL (Windows Subsystem for Linux) on the desktop.

These companies do give money and time to the Linux project both in terms of their membership but also in terms of engineers working on fixing issues in the kernal or drivers so their products can keep working.

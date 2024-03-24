---
title: 'Dotnet Interop:-  C# and Visual Basic'
date: 2020-04-01T20:47:35Z
draft: false
tags: ['Dotnet Interop', 'csharp', 'visual basic']
toc: true
---


This is Part 1 in my series of Blog Posts on DotNet interop.
## Why Visual Basic?
I decided to use visual basic as my first way to show Interop as C# and Visual Basic both share the same programming paradigm of object orientation.

## A sample of the languages!
Below I have snippets of two classes which are mirrors of each other in both Visual Basic and C#.


### Visual Basic
{{< gist grab-a-byte f7d0c226dc56c8af6d10e0088b183791 MyVBClass.vb >}}

### C#
{{< gist grab-a-byte 05117138274f0babd4b0a508dd1e5be4 MyCSharpClass.cs >}}

(Those more astute of you will have notice Visual Basic’s superiority with a lack of Semi-Colons and Brackets …)

## So How Do We Get Started
Both of the above classes were created in their relevant .NET Standard lib file from Visual Studio.
![Github Edit Pencil Icon](/dotnet-interop/libprojimage.png)

Once these have been created and compiled, you will end up with a DLL file inside of the respecting ‘bin’ folders of each project. If these are in individual Solutions and compiled completely separately, you would need to copy this file over to the project you wish to use it in (say copy the Visual Basic DLL to a C# Console App), add a reference (using brose for file), and voila! That all that’s required, you can now use the Visual Basic code just like C# code as shown in the below example.

{{< gist grab-a-byte 268eda490d2669e04bd70d9010c874a0 VbInCSharp.cs >}}
If you didn’t know any better, you may assume that you were using a standard C# library!

To prove this point further, I have also created an Example that uses the popular C# Library Newtonsoft.JSON inside of a Visual Basic Project.

{{< gist grab-a-byte 7380afd6d40ea1d1727851c646c883e9 NewtonsoftInVB.vb >}}
Again, as simple as adding the NuGet Package and using it. Rather spectacular if you ask me.


## What if I can’t be bothered to copy files?

There is another point to be made here, if you are writing both in the same solution, you can add both projects to a solution file and Visual Studio is clever enough to compile both separately as well as allow you to link. I can’t imagine a scenario where you would want to do this (except for demo purposes) but it is cool this feature exists.

One small caveat with the above is that if you are using the VB Class Library in the C# console project and you update the VB Class Library, you will need to rebuild it before the changes are picked up by visual studio as usable in the C# project.

I am yet to find anything in VB which cannot be used in C# but I have only used VB a small amount and have kept it simple for this example, I would love to hear if nayone has found anything which causes complications and any question, feel free to ask and I will try to answer.

Thanks for reading!

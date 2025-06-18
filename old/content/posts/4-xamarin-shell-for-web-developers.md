---
title: 'Xamarin Shell for Web Developers'
date: 2020-01-27T17:18:40+01:00
draft: false
toc: false
---

Coming from web development, I have become very used to and familiar with the MVC architecture for development. It allows for good decoupling of components and also allows for excellent use of Dependency Injection (DI). Having moved to start teaching myself mobile app development through Xamarin Forms , I found the view centric approach to navigation very odd and strange. It was tightly coupled to the framework and I spent hours trying to come up with the perfect navigation service that was generic enough to use.

Luckily for me, the team at Xamarin have done a lot of the heavy lifting with a feature introduced in Xamarin Forms 4.0 known as Xamarin Shell. This gave us a nice new way to navigate around our application using URL’s. Whilst still coupled to the framework, it makes it easier to pass parameters and allows the ability to think more similarly to the way web applications work.

In this repo, I created a simple proof of concept for the idea which I will describe below, feel free to have a look at the code along the way.

So to liken this to MVC, we will break this down into the respective parts and describe how we can transition this into the Xamarin Shell Model.

Model:- The model part of your application does not change. It is still the simple data being stored that we all know and love from web development.

View:- The view also does not change (at least on the Xamarin forms side of thing) And is a simple XAML File that your designers can play with until their hearts content. The only difference here is the navigation will be performed using Shell, however it is likely you will decouple this from the view (something I have not done in the demo app)

Controller:- The controller of a traditional MVC app is usually handled by the framework, passing in parameters and binding them for you. This is where we have to do the most work. Firstly, the Shell Navigation uses a URL to navigate to, this then fires off the following events in order :-

1) The code behind for the target page gets bound to the parameters. These are done using Attributes provided by Xamarin which look like the following

    {{< gist grab-a-byte 0f3cf7353969c4b5b8769cbaf11db641 XamShellQueryProperty.cs >}}


    This binds the parameter pass from the URL Navigation to a property in the code behind.
2) In the code behinds OnAppearing method, fire off a Request to get data needed fore the ViewModel. In the example project, I simply use the whole response form the Request, however this could be built up however you like. Think of this similar to making calls to services in a MVC application. I do this using Jimmy Bogard’s MediatR, which is resolved using Xamarin Forms built in Dependency Resolver in the code behind.

    {{< gist grab-a-byte 1ea094625dd0c7fa85f6bcc41b06395a XamShellMediatRResolver.cs  >}}

3) Finally, once the calls have been made, you can create the viemodel to bind to your view which is then rendered.

Of course to achieve this, you need to set up the DependencyService from Xamarin Forms with MediatR itself in order to achieve this simplicity. This however is no different to building up your favourite Dependency Injection container in ASP.NET MVC and setting the resolver for the Dependency Service at app startup. I do this with the package Microsoft.Extensions.DependencyInjection and using the ServiceProvider class as shown in the code below

{{< gist grab-a-byte  1ea094625dd0c7fa85f6bcc41b06395a XamShellDependencyInjection.cs >}}

Once this is set up, as MediatR uses Constructor Injection in its framework, all the services get resolved through constructor injection just as if it was a ASP.NET Controller and onward.

The final piece of the puzzle if for any routes which would appear outside your SHell XAML file. These include things like details pages which will need to be navigated to, but only on the press of a button and so are not part of the main App Navigation structure. For these, in the AppShell.xaml.cs file., you will need to call RegisterRoute for each page you wish to add. I set up a method I call from the constructor to do this.

{{< gist grab-a-byte  52d24d52e493190c911b058b7334580b XamShellRegisterRoutes.cs  >}}

Hope someone finds this useful!
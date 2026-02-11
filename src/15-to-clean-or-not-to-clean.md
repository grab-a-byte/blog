# To clean or not to clean

### XX/3/2026

## Productivity boost
As we start as software developers we have levels where we start to feel gains in productivty boost. To give a few of these steps, they look as follows:

- Starting to code
  - You start to code and every change to the code you make changes something, it feels like a superpower and it feels productive!
- You learn about Unit Tests
  - Now you are able to ensure that the code you wrote continues to function as you expect by having a lot of checks to ensure it behves as you expected even after a change! Now you get more productive because you don't have to look at areas you haven't changed to know they aren't broken. 
- Clean Code enters the mix
  - You learn about clean code and how it can help with modularity and even increase the ease of unit testing. It makes it faster to iterate and all this seperation of concerns means you no longer need to know whats going on behind the interface and you can use it and be productive with it. 
- Design Patterns
  - You learn about design patterns an you gain even more productivity. Now you don't need to think about the solution to the technical problem, someone already did that for you!

And of course with all this extra productivity, you're able to make perfect code that works 100% of the time all the time right?

Of course not, we all still make mistakes, interactions happen we can't expect and code changes so fast we can't possibly now what's happening. However we still feel more productive for it and it's accepted throughout the industry. 

But you know who isn't neccisarily more productive? They are 2 of your closest friends when it comes to programming and running yor programs. They are of course your CPU and your RAM. Both of these need to work overtime to run all these extra pieces of code with all these levels of indirection as they follow insturctions to pointers all around memory and keeping enough in the CPU cache becomes a difficult job. But of course this all sound like speculation, how can we prove it?

Of course in order to look at how the performance can be affected by these, we can use a benchmark to determine how fast the code will run. So lets benchmark it! To check this out, we start with a project that is fully using all the best practices, it will be a basic and simple calculation service which takes in a JSON request with a left, right and calculation type fields (the example code in the repo only supports addition and subtraction). It performs the relavent operation, stores it in the 'database' (an in-memory list) and returns the result.

For this benchmark, we then record how long it takes for the API to respond to all requests and take that. To ensure that outliers and erroneous results would be removed, the test was ran at many different evels and multiple times, 
- The test was ran with sending the following number of requests
  - 10
  - 100
  - 1,000
  - 10,000
- It was also ran 10 times per run
  - Out of these 10 we printed the minimum, maximum and average of the runs
  - Each was ran 3 times and then averaged again to get final numbers

## Starting Point
So lets have a look at the initial project.

<div style="max-width: 70%">

![Initial project structure](images/to-clean-or-not-to-clean/initial-project-structure.png)

</div>

This project uses all the latest and greatest techniquies available to use to date! It has Services, Interfaces, Repositories, it's using Dependency Injection and some pretty great libraries names [MediatR(for CQRS)](https://mediatr.io/) and [Fluent Validation](https://docs.fluentvalidation.net/en/latest/) to make it easier to validate our incoming requests! This structure may look familiar if you've worked in larger dotnet projects in the past.


## Related links
- [Github Repository](https://github.com/grab-a-byte/to-clean-or-not-to-clean) 
  - I apologise the slides were done in Apple Keynote and exported to PowerPoint to apologies for any strange formatting issues
- [Clean Code Horrible Performance Video](https://youtu.be/tD5NrevFtbU?si=ZUYDMo9WlqxWH1Eq)

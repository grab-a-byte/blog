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

So to begin with, lets look at the how this performs in the benchamark (for brevity I am only showing the averaged tables, if you wish to see each indivial run, see the ['end recordings' file](https://github.com/grab-a-byte/to-clean-or-not-to-clean/blob/main/StartPoint/end-recordings.md) in github):

| Num Requests | Min(ms) | Max(ms) | Average(ms) |
| ------------ | ------- | ------- | ----------- |
| 10           | 0       | 47      | 4.7         |
| 100          | 2       | 3       | 2.2         |
| 1,000        | 14      | 24      | 16.9        |
| 10,000       | 140     | 222     | 170.2       |

For the test of the article, I will be mainly focussing on the bottom right figure which is the average of the averages across the 10k requests. Why this particular number? It is where we are seeing the most variance in the results so its easier to draw conclusions form this figure. I'll mention when the tables are available wih full figures for your viewing in the 'end-recordings' document.

## Removing Libraries
So lets start by doing something rather drastic, removing libraries! This might seem radical however it only requires a few minor changes to the codebase (likely more in a fully fledged project)
- Removing the MediatR library
- Removing Fluent Validations
- Injecting the MedatR Handlers directly (by replicating the interface in our project)
- Replacing the Fluent Validation with a static validation function that returns an object of the same shape to be a drop-in replacement. 

To show how the code looks now, here are a couple of snippets from the code: 

```c#
public class CalculatorController(ICalculationRequestHandler calculationRequestHandler) : ControllerBase
{
    [HttpPost()]
    public async Task<IActionResult> Calculate([FromBody] CalculationRequest request)
    {
        ValidationResult validationsResult = RequestValidator.Validate(request);
        if (validationsResult.IsValid is false)
        {
            return BadRequest(JsonSerializer.Serialize(validationsResult.Errors));
        }

        CalcuationResponse res = await calculationRequestHandler.Handle(request, CancellationToken.None);
        return Ok(res);
    }
}
```

and the new founded validation code looks like so

```c#
public record ValidationResult(bool IsValid, IEnumerable<string> Errors);

public static class RequestValidator
{
    private const int MaximumValue = int.MaxValue / 2;
    private static readonly string[] KnownCalculationTypes = ["Subtraction", "Addition"];

    public static ValidationResult Validate(CalculationRequest request)
    {
        List<string> errorMessages = [];
        if (request.Left > MaximumValue)
        {
            errorMessages.Add($"Left hand side must be less than {MaximumValue}");
        }
        if (request.Right > MaximumValue)
        {
            errorMessages.Add($"Right hand side must be less than {MaximumValue}");
        }
        if (KnownCalculationTypes.Contains(request.CalculationType) is false)
        {
            errorMessages.Add("Service only supports addtion and subtraction");
        }

        return new ValidationResult(
            IsValid: errorMessages.Count != 0,
            Errors: errorMessages);
    }
}
```

The changes here aren't too radical and yet the amount of performance we gain from this is quite surprising. The figure for this is from `170.2` all the down to *drumroll please* `160.4`! What an astronomical change. That `9.8`ms makes this a difference of about 5% in speed. 

To put into perspective how much that is, it is the equivleent of taking a
- Intel i5 14600K
  - Released October 2023

and changing it into a 
- Intel i5 13600K
  - Release September 2022

Which mean we have essential removed just over a year of hardware performance by using these libraries. 

Why could this be? Well there are a few reasons which I'd like to list below:
- Libraries are made to be generic
- They do more work under the hood in order to be generic
- The code you have to write for these tasks will likely be simpler and more tuned to your specific use case

But not only this, I would argue there are further points to be made and gathered form this such as the following:
- Simpler Developer Experience (DX)
  - Go to definition will find your function
  - You chose when to be async and use Tasks, not your library
- No need for MediatR Handler/Request seperation and now going to definiton will jsut work giving less context switching to the developer
- Validation logic can be all in one place instead of spread through many validators

Finally, by removing how many libraries you use, you by default remove the chances of being "rug pulled" by these libraries.
- (I have a seperate article on why I hate the term and idea of rug pulling but it's the term most people know. You can read the article at [Open source going commercial is not rug pulling](./14-open-source-not-rug-pulls.md))

So from this you are able to protect yourself and the core parts of your business application as if it is in-house, you can debug it, own it and not have any chance of you not being able to use that piece of software.

## Removing interfaces

Now this may seem like a very radical idea. Many people will read the above title and scream preposterous! Interfaces are how we ensure that our code is decoupled and is reuable and can be dependency injected and that we can mock for test and.... so on and so on. 

It's strange to think that for years we didn't write code with interfaces because there wasn't enough hardware power to do so. Yes I may be talking about back in the days when you used punchcards or FORTRAN or COBOl or many other similar languages but we still managed to perservere without them. 

So what will this look like when we try and apply it to the toy application we have built for this benchmarking scenario? 

Well we have a few steps to do:
- Remove the idea of a handler and move the handler code to where it's being used (our 1 controller endpoint in this case)
- Delete the interfaces (and possibly services too)
- Deregister them from dependency injection (can't inject an interface that doesnt exist)

And that's all of it! Well almost all of it, I've actually kept the repository interface as when it comes to testing being able to mock and change your data storage to return whatever state is required for the test. While this seem counter intuitive, I think the only place where keeping interfaces around is in areas of Input/Output (IO) such as network requests or database access as this allows you to make your unit tests isolateable. 

So with this in mind, at this point in the testing, we are going to start with a average time of *167.4ms* (all shall be relvealed why this number differs from the previous number later). So what do we gain by completing all this work and effort? 

*162.8*

That's a difference of about *4.6ms*. If you prefer a percentage based approach, that's about a 2.7% speedup in performace from removing these. Why would this be the case? Well lets think about a few points. 
- Interfaces by their very nature do not know what they are calling
  - Imagine you trying to optimize the kitchen of your favourite restaurant when you don't know what staff or cookware or appliances they have. 
- Every time you want to perform a function call, there is a lookup that the code has to do to know what it's calling due to the polymorphic nature of code
  - It's like if you need a task doing and you have to ask your manager, who needs to ask their manager who then needs to ask one of their managed employees who knows and then it goes back through the chain instead of you just being able to ask Dave in the cafeteria for a coffee directly. 
- By removing this indirection, we allow the compiler to see more of what is actually happening in our codebase
  - This opens up the door for a myriad of optimizations that simply aren't possible if the compiler doesn't know what it's calling. 

## Final results from optimizing
So for those who are eagerly awaiting some more results, lets look at what applying all these things does for us. 

So when we started, we were at *170.2ms* and when applying these, we go to *162.8ms*. That is a whole whopping *7.4ms* difference or a whopping *4%* perfomace boost! 

Hmm... that doesnt really seem like much does it? Well there si 1 figure I have left out up to this point. 

*153.4ms*

Now that seems like it would be a major improvement. Even better than the score I listed previously so where has this magical number came from? Well this figure is from ASP.NET Core just serving and handling the request with a `200 OK` repsonse. With that in mind, lets build a new table!

| Overall time | ASP.NET | Difference |
|--------------|---------|------------|
| 170.2 | 153.4 | 16.8 |
| 162.8 | 153.4 | 9.4 |

This means our real difference is from *16.8ms* to *9.4ms* which is actually a *44%* speedup in the code we can control. That seems more like it!


## Related links
- [Github Repository](https://github.com/grab-a-byte/to-clean-or-not-to-clean) 
  - I apologise the slides were done in Apple Keynote and exported to PowerPoint to apologies for any strange formatting issues
- [Clean Code Horrible Performance Video](https://youtu.be/tD5NrevFtbU?si=ZUYDMo9WlqxWH1Eq)

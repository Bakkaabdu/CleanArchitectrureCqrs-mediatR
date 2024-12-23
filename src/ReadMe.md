Project for my learning trip in the .net world 

In this article, we will present a detailed guide on how to integrate the CQRS and Mediator patterns into a Web API. We will explain the definitions of both patterns and highlight their benefits to explore how they can improve the performance of our applications.

CQRS pattern
What is CQRS?, this acronym stands for Command and Query Responsibility Segregation, and this architectural pattern is used to separate the operations that read data from the operations that write or update data, let’s explore the problem this architecture aims to solve, in case you’re wondering why you should use it.

The problem
It is common for applications to perform a large number of read/write operations, however, when the users of the application and the quantity of the operations grows exponentially, it is likely that the application will suffer issues such as:

Performance: Read and write operations differ in their performance requirements and characteristics. Managing these operations in the same model can lead to suboptimal performance, as resources are not allocated efficiently based on each operation’s specific needs.

Scalability: There is usually a discrepancy between the number of read and write operations in an application. Certain parts of the application may require handling a significant number of reads, while others may have to manage a high volume of writes. In such cases, it can be inefficient and costly to scale the entire system to handle both types of operations equally.

Flexibility: In tightly coupled architectures where read and write operations are interconnected, making changes will probably cause problems that will cost time and money.

Consistency: Maintaining consistency between read and write operations within the same model can be challenging, as it can lead to data inconsistencies, race conditions, and concurrency issues, potentially compromising the integrity of the system.

Complexity: When the application reads data, it may need to execute complex queries that produce Data Transfer Objects (DTOs) with different structures. This can result in complex object mapping. On the other hand, when writing data, the model may include complex validation and business logic, which can lead to an overly detailed model with multiple responsibilities.

![Problem](https://miro.medium.com/v2/resize:fit:828/format:webp/0*MVH32XT82TGw-B-7 " ")


The Solution
As previously mentioned, CQRS is a pattern that separates queries and commands within our application. This separation allows users to create and use different data models according to specific scenarios. Therefore, the application becomes more flexible, changes can now be made without breaking other modules, and there is no longer a need to lock tables or records for updates, which can negatively impact performance. Additionally, you can customize your data to fit your specific query needs, even denormalizing it if necessary. This means that you can use different storage technologies depending on the query requirements, and customize everything you need. Furthermore, you can scale and optimize read or write operations separately, leading to more efficient use of resources and less investment in infrastructure.

![Solution](https://miro.medium.com/v2/resize:fit:828/format:webp/0*3ped-iqWF62NXf46 " ")

Mediator pattern
The mediator design pattern aims to reduce dependencies between objects by restricting direct communication and instead creating a way for them to collaborate only through the mediator object. In simpler terms, there is an object that encapsulates and manages how other objects interact with each other.

The problem
Flexibility: As business requirements evolve, it becomes increasingly important to have flexible applications that can adapt to changing needs. However, applications that are tightly coupled can make it difficult to make changes or add new features. A change in one object can have a ripple effect throughout the entire application, this can cause delays in development and increase the risk of introducing bugs or other issues.

Complex management: Each object should be aware of the interfaces and implementations of other objects it interacts with. However, this can make the system more complicated to comprehend, maintain, and develop and this issue grows over time like a snowball, directly impacting the developers, management, and the final product used by the clients.

Coupling: Objects that communicate directly are usually tightly coupled, in these cases making changes to those objects could impact others, leading to more changes in models, scripts, etc.

![Problem](https://miro.medium.com/v2/resize:fit:750/format:webp/0*oDu74sr7jrhteo9S " ")

The Solution
In the image below we can see how the Mediator pattern works, we have some services that can communicate with each other through a mediator object.

By allowing services to communicate without direct references, the application becomes more flexible, with simplified interactions and reduced dependencies, making it easier to maintain.

In this article, we are going to use the Mediator pattern through a library called MediatR. This library offers classes that allow efficient communication between multiple objects in a loosely coupled manner, simplifying the exchange of information, and making it easier to manage existing features and integrate new ones. It’s important to note that MediatR follows the Single Responsibility Principle and Dependency Inversion Principle.

![Process](https://miro.medium.com/v2/resize:fit:640/format:webp/0*W6H-6C4yfacJrcYm " ")

![Solution](https://miro.medium.com/v2/resize:fit:750/format:webp/0*h5w1jFyIcJEtGwGs " ")


Requirements
Visual Studio (Community)
.Net 8
ASP Web API project
MediatR (library)
NuGet package for Mediator

The library MediatR offers the necessary functionality for implementing the Mediator pattern. You can obtain the library from NuGet packages in Visual Studio or via one of these methods:

.NET CLI:

dotnet add package MediatR --version 12.2.0
Package manager console:

Install-Package MediatR -Version 12.2.0
Direct download from the NuGet website:

MediatR



Step 1: Adding the MediatR dependency.

If you are using a custom class for adding dependencies use this option:

services.AddMediatR(cfg => cfg.RegisterServicesFromAssemblies(AppDomain.CurrentDomain.GetAssemblies()));

![Code](https://miro.medium.com/v2/resize:fit:1400/format:webp/0*v322fCZsTkIgGH_T " ")


If not add it directly to the Program.cs:

builder.Services.AddMediatR(cfg => cfg.RegisterServicesFromAssemblies(typeof(Program).Assembly));

![Code](https://miro.medium.com/v2/resize:fit:828/format:webp/1*LdeoV3fTQrBdse7c836Dkg.png " ")


Step 2: Creating the folder structure

In the application layer we are going to create a folder for our entities and split our Commands and Queries into folders:

![Code](https://miro.medium.com/v2/resize:fit:582/format:webp/0*u2VzlGU7411J0TDI " ")


Inside the Commands and Queries folders, you are going to create a folder for each function you need to execute:

![Code](https://miro.medium.com/v2/resize:fit:638/format:webp/0*PmSx3xCjnpU8CQ1z " ")


Step 3: Creating base classes

The classes required for using the CQRS and Mediator pattern are the Query/Command and Handler classes. The image below illustrates these classes:

Commands

CreateCustomerCommand.cs
CreateCustomerHandler.cs
CreateCustomerValidator.cs
Queries

GetByIdCustomerQuery.cs
GetByIdCustomerHandler.cs
GetByIdCustomerValidator.cs
Note: The Validator class is optional, in this project the library used for validation is FluentValidation.

![Code](https://miro.medium.com/v2/resize:fit:640/format:webp/0*7wyZk1V8f6e5lDKJ " ")


Step 4: Create the Query/Command class

First, we need to create our Query/Command class. In this class, we will inherit from the IRequest interface provided by MediatR. This interface informs MediatR that a request is being made and that it needs to be handled by the Handler class, you need to specify the type of request you are executing and pass it to IRequest.

Note: In this case, I have used a base response class to provide a generic response, but it is not mandatory.

![Query Class](https://miro.medium.com/v2/resize:fit:828/format:webp/0*fBXnmQtAa86VfIhm " ")

![Query Class](https://miro.medium.com/v2/resize:fit:828/format:webp/0*qS5on2v76llMrd0t " ")


Step 5: Create the Handler class

This class will handle our requests based on whether it is a command or a query. For command requests, the class will update the application’s state as needed, while for query requests, the handler will fetch data based on the queries and return it to the client. In this class, we inherit the IRequestHandler, this interface takes the request (command/query) and the return type as parameters.

![QueryHandler Class](https://miro.medium.com/v2/resize:fit:828/format:webp/0*nhUWZOqrfTT1v3md " ")

![CommandHandler Class](https://miro.medium.com/v2/resize:fit:828/format:webp/0*jXtf9Apa2TuBCmY4 " ")

Step 6: Implementing MediatR in the Controller

To execute the commands and queries, you need to inject the MediatR library. Therefore, you must create a global read-only variable of the interface IMediator and initialize this variable using a constructor.

![MediatR](https://miro.medium.com/v2/resize:fit:828/format:webp/0*hHviuPM7Va677TAD " ")

After setting up the global read-only variable _mediator, you can access the Command/Query functionalities through it. The MediatR library will handle the mapping of the requests to the appropriate handlers, ensuring that the commands and queries are processed correctly.

![Query implementation](https://miro.medium.com/v2/resize:fit:828/format:webp/0*rnzHq_1zoxR3lvN0 " ")

![Command implementation](https://miro.medium.com/v2/resize:fit:828/format:webp/0*rnzHq_1zoxR3lvN0 " ")




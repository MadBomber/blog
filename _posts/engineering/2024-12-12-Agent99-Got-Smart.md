---
title: "Agent99 Got Smart"
categories:
  - Engineering
tags:
  - Agents
  - Tools
---
# Agent99 Got Smarter

This is the first in a series of articles introducing the new Ruby gem [**agent99**](https://rubygems.org/gems/agent99) which provides a reference implementation of a framework for the execution and management of software agents.  You may skip ahead and go [directly to the software.](https://github.com/MadBomber/agent99)

## Background

I wrote my first computer program in the fall of 1970.  I've been developing software systems ever since.  That means I'm old.  Sometimes I say things that other developers do not understand.  For example Agent 99 is smarter than Agent 86.

One of my favorite spy shows of the mid-1960s is [**Get Smart**](https://www.youtube.com/watch?v=16_6XrPlV-w) which I think is an appropriate title for today's interest in bringing artificial intelligence (AI) into just about every software project -- which is not necessarily a good idea.

Agent 99, portrayed by Barbara Feldon, is a highly skilled and intelligent secret agent. With her quick thinking and resourcefulness, she frequently saves the day. Despite operating in a male-dominated environment, Agent 99 is portrayed as a competent and independent agent. 

**Ruby**, like agent 99, working in this Python dominated world of AI ...

- **offers a refreshing alternative** for developers seeking to build elegant and expressive AI applications.
- **encourages innovation** and paradigm shifts, challenging the status quo established by Python in AI development.
- **finds its niche** by appealing to developers who prioritize readability and maintainability in their code.
- **fosters unique projects** that leverage its metaprogramming capabilities, paving the way for creative solutions in AI.
- **competes by pushing for more integration** of Ruby-based frameworks, making them viable options in the AI landscape.
- **highlights the strengths of its object-oriented programming** style, leading to more modular and testable AI code.
- **inspires a community of passionate developers** who prefer its syntax and features over the more popular Python.

Now lets talk about the basic concepts behind the [Agent99 Framework](https://github.com/MadBomber/agent99) as implemented in a Ruby gem.

```ruby
gem install agent99
```
 
The Agent99 framework documentation[ ]table of contents](https://github.com/MadBomber/agent99/blob/main/docs/README.md) lists the major documents with their summaries that describe the framework.  What it does not cover well, and what I propose to discuss here, is the basic concept of what a software agent is and how the single responsiblity priciple applies.  I also want to cover how the Agent99 framework differs in its approach to agent description and management.

## What is a Reference Implementation?

The Agent99 gem is an implementation of a protocol using the Ruby programming language.  The same protocol can be implemented in any other language thus making it possible to mix-and-match software agents without regard to their language.  So long as the agent supports the Agent99 protocol it can be used in a system of other Agent99-based processes.

In this article all code examples are in Ruby.

The following sections talk briefly about software agents in general and the single responsibility priniciple.

## Understanding Software Agents and the Single Responsibility Principle

Software agents and the Single Responsibility Principle (SRP) are increasingly important concepts in modern software development. Software agents help break down complex systems into manageable, autonomous components, while SRP promotes the creation of maintainable, testable, and adaptable systems. Understanding and implementing these concepts can improve code quality and encourage innovation and agility in development teams. Embracing agents and SRP is beneficial for building scalable and efficient software solutions, particularly in areas like AI, automation, and micro-services.

## What are Software Agents?

Simply put a software agent is code that does one thing as best it can.  In the Agent99 framework it is an instance of a sub-class of **Agent99::Base** class.  The sub-class implements specific methods that sets it apart from other agents.  For example the **capabilities** method.

```ruby
def capabilities = %w[ greeter hello_world ]
```

Other methods are responsible for handling the receipt of request, response and control methods.  Customizations of the **init** or **initialize** and **fini** may also be required to setup state/resources and the cleanup those resources on termination.

  NOTE: the current implementation of Agent99 defines the **capabilities** value as an Array(String) where the elements are like topics.  The roadmap calls for this feature to be expanded into descriptive text much like how we define Tools used in functional callbacks from the LLM processing.

## The Single Responsibility Principle (SRP)

The Single Responsibility Principle is one of the key principles of object-oriented design encapsulated in the SOLID principles. It states that a class or module should have only one reason to change, implying that it should have only one job or responsibility.

### Importance of SRP

1. **Maintainability**: Classes with a single responsibility are easier to read, understand, and modify, leading to more maintainable code.
2. **Testability**: By isolating the responsibilities, each class can be tested independently, simplifying unit testing.
3. **Flexibility**: Changing one responsibility has minimal impact on other parts of the code, minimizing the risk of introducing bugs.

### Application of SRP in Software Development

When implementing SRP, consider the following:

- **Identify Responsibilities**: Break down functionalities into discrete responsibilities. Each class or module should focus on a specific task.
- **Modular Design**: Design your system with loosely coupled components to enhance separation of concerns. This makes it easier to swap out or modify components.
- **Use Design Patterns**: Design patterns like the Observer, Strategy, and Factory can help adhere to SRP by promoting clear interfaces and responsibilities.

## The Intersection of Agents and SRP

The concept of software agents naturally aligns with the Single Responsibility Principle. Each agent can be designed as a separate class or module that encapsulates a specific functionality. For example:

- An agent for processing customer orders may focus solely on order management, while another agent can handle payment processing.
- A notification agent can be responsible for sending alerts or messages to users, ensuring separation from the business logic involved in order processing.

Designing agents with SRP in mind facilitates better modularity and reusability. It allows developers to make changes to one agent without affecting others, ultimately leading to a more robust architecture.

## Agent99 is a Reference Framework

In its current version the Agent99 Framework conceptually is no different from any other implementation.  It provides for a centralized registry where agents register their capability so that they can be discovered by other agents or applications looking for places to handle missions.  Communications between agents in handled by a distributed message system.  Agent99 shows the use of AMQP and NATS-based messaging systems.

### Agent Structure

Agents in Agent99 inherit from **Agent99::Base** which provides core functionality through several key modules:

- HeaderManagement - For message header processing
- AgentDiscovery - For capability advertisement and discovery
- ControlActions - For handling control messages
- AgentLifecycle - For managing agent startup/shutdown
- MessageProcessing - For handling message dispatch

Each agent must define its TYPE (server, client, or hybrid) and capabilities. The framework supports three message types: requests, responses, and control messages.

### Centralized Registry

The registry service maintains agent availability and capabilities through a **RegistryClient.**  It makes sense to me that the central registry is a web-based application.  For the agent99 gem in the examples directory is a Sinatra implementation in the file `registry.rb`  Its primary purpose is to support a data store of currently registered agents.

It provides three main operations:

#### Register

![Central Registry Process](/assets/images/agent_registry_process.png)

Agents register by providing their information (_e.g._ name and capabilities) to the registry service. Upon successful registration, they receive a universally unique ID (UUID) that identifies them in the system. Registration is handled via HTTP POST to the registry service.

#### Discover

Other agents can discover capabilities by querying the registry. The discover operation takes a capability name and returns information about agents that provide that capability. This is done through an HTTP GET request with the capability as a query parameter.

#### Withdraw

When an agent needs to leave the system, it withdraws its registration using its UUID. This removes it from the available agents list via an HTTP DELETE request.

### Message Network

The Ruby implementation of the framework supports both AMQP and NATS messaging systems. So far work on the Ruby implementation has been focused on the AMQP implementation.

Messages within the Agent99 framework are JSON structures that have defined schemas such that the **MessageClient** process can provide transparent validation of messages to the agent.  Invalidate messages are returned to sender without invoking agent specific processes.

The configuration of the messaging network is not necessarily important so long as one agent has the ability to communicate with another agent.  For example, if we chose to impplement a peer-to-peer network using IP/port TCP operations that kind of implementation would still fit within the overall concept of the Agent99 framework.

There are three kinds of messages: request, response, control.

#### Request Messages

Request messages are validated against a schema defined by each agent. They contain:

- A header with routing information
- A payload with the request details
- Type-specific validation rules Requests are processed by the receive_request handler in target agents.


#### Response Messages

Responses are sent back to the requesting agent using the return address from the original request. They include:

- The original message header (reversed for routing)
- Response payload
- Status information Responses are handled by the receive_response method.

                                        
#### Control Messages

Control messages manage agent lifecycle and configuration:

- shutdown - Stop the agent
- pause/resume - Temporarily suspend/resume processing
- update_config - Modify agent configuration
- status - Query agent state
- response - Handle control operation results

Messages are delivered through queues with a 60-second TTL (Time To Live) to prevent queue buildup from inactive agents.

## Additional Reading

Check out the documentation of the current implementation at https::/github.com/MadBomber/agent99

Contributions to this initial reference implementation in Ruby are welcome and encouraged.  Additional language implementations would be a neat thing to get underway.  Anyone want to do the APL, PL-1 or Ada versions?

---
title: "Getting Smart with Agent99"
categories:
  - Engineering
tags:
  - Agents
  - Tools
---

# Getting Smart with Agent99

Welcome to the first article in a series that introduces the Ruby gem [**agent99**](https://rubygems.org/gems/agent99), which provides a reference implementation framework for the execution and management of software agents. If you're eager to jump right in, feel free to visit the [software repository](https://github.com/MadBomber/agent99).

> **NOTE:** The Ruby agent code shown in this article is based on working examples. For the most up-to-date and complete examples, please refer to the [agent99 repo](https://github.com/MadBomber/agent99/tree/main/examples).

## Background

I've been developing software systems since I wrote my first computer program in the fall of 1970. It was a one-liner in APL - A Programming Language. Of course, all programs in APL are essentially one-liners. I'm very happy that I've been using Ruby since 2005.

With this long history, I've accumulated some references that sometimes puzzle newer developers. For instance, when I mentioned that Agent 99 is smarter than Agent 86, my team had no idea what I was talking about.

One of my favorite spy shows from the 1960s is [**Get Smart**](https://www.youtube.com/watch?v=16_6XrPlV-w), a title that resonates with today's growing interest in integrating artificial intelligence (AI) into every software project, which is not always for the better.

In Get Smart, Agent 99, portrayed by Barbara Feldon, is a skilled and intelligent agent who excels through quick thinking and resourcefulness. In a male-dominated world, she stands as a symbol of capability and independence.

Here's how the Ruby programming language, like Agent 99, offers unique benefits in a Python-dominated AI landscape:

- **Refreshing Alternative:** Ruby allows developers to create elegant and expressive AI applications.
- **Encourages Innovation:** It promotes paradigm shifts, challenging the Python status quo in AI development.
- **Readable and Maintainable:** Developers who value clarity can find their niche in Ruby's design.
- **Supports Unique Projects:** Ruby's metaprogramming capabilities pave the way for creative AI solutions.
- **Integration-friendly:** Greater integration of Ruby-based frameworks makes them viable choices in the AI landscape.
- **Object-Oriented Strengths:** Ruby's object-oriented style leads to more modular and testable code.
- **Engaged Community:** Ruby inspires a community that often prefers its syntax over Python's.

Now, let's dive into the fundamental concepts behind the [Agent99 Framework](https://github.com/MadBomber/agent99) as implemented in the Ruby gem.

## Getting Started with Agent99

The `agent99` is a Ruby gem that serves as a framework for developing and managing software agents, allowing for the execution and communication of these agents in a distributed environment. It implements a reference protocol that supports agent registration, discovery, and messaging through a centralized registry and a messaging system like AMQP or NATS. Each agent, derived from the `Agent99::Base` class, is designed to perform specific tasks as defined by its capabilities, adhering to the Single Responsibility Principle (SRP) for enhanced maintainability and testability. The framework facilitates modular agent interactions, enabling developers to build innovative applications while taking advantage of Ruby’s expressive syntax and metaprogramming features. The library emphasizes best practices in software design, including error handling and lifecycle management for robust agent operations.

To install the Agent99 gem, simply run:

```bash
gem install agent99
```

The [documentation](https://github.com/MadBomber/agent99/blob/main/docs/README.md) provides a comprehensive overview of the framework, but here, we will explore definitions of software agents and the Single Responsibility Principle (SRP), along with how Agent99 distinguishes itself in agent management and description.

## What Is a Reference Implementation?

The Agent99 gem implements a protocol in Ruby that can be replicated in other programming languages. This interoperability allows software agents, given they support the Agent99 protocol, to mix and match regardless of the language they were built in.

## Understanding Software Agents and the Single Responsibility Principle

Software agents and the Single Responsibility Principle (SRP) are crucial in contemporary software development. They decompose complex systems into manageable, autonomous components, while SRP promotes the creation of maintainable, testable, and adaptable systems. Utilizing both can boost code quality and nurture agility in development teams, particularly in AI, automation, and microservices contexts.

## What Are Software Agents?

In simple terms, a software agent is a designated piece of code that performs a single function effectively. Within the Agent99 framework, agents are instances of subclasses derived from the **Agent99::Base** class. These instances can be running in their own separate process or groups of instances of different Agent99 instances can run within separate Threads in a single process.

Here's a simple example of an Agent99 agent class running in an independent process:

```ruby
# File: example_agent.rb

require 'agent99'

class ExampleAgent < Agent99::Base
  TYPE = :server

  def capabilities
    %w[ rubber_stamp yes_man example noop always_succeed ]
  end

  def receive_request
    logger.info "Example agent received request: #{payload}"
    send_response(status: 'success')
  end
end

ExampleAgent.new.run
```

```base
ruby example_agent.rb
```

Each agent subclass is responsible for specific methods that define its unique capabilities and how it handles requests. The capabilities method defines what things the agent can perform. Since we define a software agent below as code that does one thing, each entry in this Array of Strings is basically a synonym.

For a server type agent, the only methods that are required to be defined, as in the ExampleAgent class above, are its **capabilities** and its **receive_request** methods. Everything else from initialization, registration, message dispatching, and graceful shutdown are handled by the default methods within the **Agent99::Base** class.

More complex agents will require methods like **handle_response** and **handle_control** and potentially custom implementations of **init**, **initialize**, or **fini** may be necessary for managing state or resources.

> **RoadMap:** Currently, the Agent99 implementation defines the **capabilities** value as an `Array(String)`, with plans to enhance this functionality into descriptive unstructured text akin to defining tools for functional callbacks in LLM processing using semantic search.

## The Single Responsibility Principle (SRP)

The Single Responsibility Principle, part of the SOLID principles of object-oriented design, asserts that a class or module should have only one reason to change. This means it should fulfill a single job or responsibility effectively.

### Why SRP Matters

1. **Maintainability:** Code is easier to read and modify, leading to more maintainable systems.
2. **Testability:** Isolated responsibilities facilitate independent unit testing.
3. **Flexibility:** Minimal impact on other parts of the system when modifying one responsibility, reducing the risk of bugs.

### Applying SRP in Software Development

Implementing SRP involves:

- **Identifying Responsibilities:** Break down functionalities into specific tasks; each class or module should focus on a particular duty.
- **Modular Design:** Create a loosely coupled system to enhance separation of concerns.
- **Utilizing Design Patterns:** Harness design patterns like Observer, Strategy, and Factory to ensure clear interfaces and responsibilities.

## Alignment of Agents and SRP

The notion of software agents naturally corresponds with the SRP. Each agent can be a distinct class or module that encapsulates a specific functionality. For instance:

- An order processing agent focuses solely on order management.
- A notification agent manages the sending of alerts or messages without getting involved in order processing logic.

Designing agents with SRP in mind fosters modularity and reusability, allowing changes to one agent without affecting others and supporting more robust architecture.

## Agent99 as a Reference Framework

In its current iteration, the Agent99 Framework does not differ conceptually from other microservice architecture implementations. It enables centralized registration where agents list their capabilities for other agents or applications to discover. Agent communications occur via a distributed messaging system. The agent99 Ruby gem currently uses AMQP (via the Bunny gem and the RabbitMQ broker) and the NATS-server.

### Agent Structure

Agents in Agent99 inherit from **Agent99::Base**, which offers core functionality through crucial modules:

- **HeaderManagement:** Handles message header processing.
- **AgentDiscovery:** Facilitates capability advertisement and discovery.
- **ControlActions:** Manages control messages.
- **AgentLifecycle:** Oversees agent startup and shutdown functionality.
- **MessageProcessing:** Manages message dispatch and handling.

Every agent must define its type (server, client, or hybrid) and capabilities. The framework supports three message types: requests, responses, and control messages.

```ruby
class Agent99::Base
  include Agent99::HeaderManagement
  include Agent99::AgentDiscovery
  include Agent99::ControlActions
  include Agent99::AgentLifecycle
  include Agent99::MessageProcessing

  MESSAGE_TYPES = %w[request response control]

  attr_reader :id, :capabilities, :name, :payload, :header, :logger, :queue
  attr_accessor :registry_client, :message_client

  ###################################################
  private

  def handle_error(message, error)
    logger.error "#{message}: #{error.message}"
    logger.debug error.backtrace.join("\n")
  end

  # the final rescue block
  rescue StandardError => e
    handle_error("Unhandled error in Agent99::Base", e)
    exit(2)
end
```

### Centralized Registry

The registry service tracks agent availability and capabilities through a **RegistryClient**. A web-based application serves as the central registry, with a Sinatra implementation found in the `examples/registry.rb` file. Its primary function is to maintain a data store of registered agents.

It supports three core operations:

#### 1. Register

![Central Registry Process](/assets/images/agent_registry_process.png)

Agents register by providing their information (e.g., name and capabilities) to the registry service. Here's how registration works in practice:

```ruby
class WeatherAgent < Agent99::Base
  TYPE = :server

  def capabilities
    %w[get_temperature get_forecast]
  end

  def receive_request(message)
    case message.payload[:action]
    when 'get_temperature'
      send_response({ temperature: 72, unit: 'F' })
    when 'get_forecast'
      send_response({ forecast: 'Sunny with a chance of rain' })
    end
  end
end

# Start the agent
WeatherAgent.new.run
```

Upon successful registration, agents receive a universally unique ID (UUID) that identifies them in the system. The registration process is handled automatically by the Agent99 framework when you call `run`.

#### 2. Discover

Agents can query the registry to discover capabilities. The discovery operation retrieves information about agents offering specific capabilities via an HTTP GET request.

#### 3. Withdraw

When an agent needs to exit the system, it withdraws its registration using its UUID, removing it from the available agents list through an HTTP DELETE request.

### Messaging Network

The Ruby implementation of Agent99 currently focuses on AMQP messaging systems. Messages are formatted as JSON structures that adhere to defined schemas, allowing the **MessageClient** to validate messages effortlessly.

Messages are validated against defined schemas, and invalid messages return to the sender without invoking agent-specific processes.

Message types within the framework include:

#### Request Messages

These messages are validated against an agent-defined schema and include:

- A header with routing information.
- Agent-specific elements with their types and examples.

Requests are handled by the `receive_request` handler in target agents.

Here's an example of a request message schema using the [SimpleJsonSchemaBuilder gem](https://github.com/mooktakim/simple_json_schema_builder)

```ruby
# examples/maxwell_request.rb

require 'agent99/header_schema'

class MaxwellRequest < SimpleJsonSchemaBuilder::Base
  object do
    object :header, schema: Agent99::HeaderSchema

    string :greeting, required: false, examples: ["Hello"]
    string :name,     required: true,  examples: ["World"]
  end
end
```

This schema defines a `MaxwellRequest` with a header (using the `Agent99::HeaderSchema`), an optional greeting, and a required name. A valid JSON message conforming to this schema might look like:

```json
{
  "header": {
    "from_uuid": "123e4567-e89b-12d3-a456-426614174000",
    "to_uuid": "987e6543-e21b-12d3-a456-426614174000",
    "message_id": "msg-001",
    "correlation_id": "corr-001",
    "timestamp": "2023-04-01T12:00:00Z"
  },
  "greeting": "Hello",
  "name": "Agent99"
}
```

Using such schemas ensures that messages are well-structured and contain all necessary information before being processed by agents.

Here is how the **MaxwellAgent** associates itself with its specific request schema:

```ruby
# examples/maxwell_agent86.rb

require 'agent99'
require_relative 'maxwell_request'

class MaxwellAgent86 < Agent99::Base
  REQUEST_SCHEMA = MaxwellRequest.schema
  # ...
end
```

When an agent subclass defines a **REQUEST_SCHEMA**, the message processing of the **Agent99::Base** validates all incoming requests against the schema. If there are errors, those errors are returned to the sender without presenting the request message to the agent's custom **receive_request** method.

#### Response Messages

Responses are routed back to the requesting agent and include:

- The original message header in reverse.
- The response payload.
- Status information.

Responses are processed by the `receive_response` method.

#### Control Messages

Control messages manage agent lifecycles and configurations and include commands such as:

- **shutdown:** Stop the agent.
- **pause/resume:** Temporarily suspend or resume operations.
- **update_config:** Modify agent configurations.
- **status:** Query agent state.
- **response:** Handle control operation results.

Messages are queued with a 60-second TTL (Time To Live) to prevent buildup from inactive agents.

## Additional Resources

For further exploration, check out the documentation of the current Ruby implementation at [GitHub](https://github.com/MadBomber/agent99).

Contributions to this initial Ruby reference implementation are welcome! It would be exciting to see additional language implementations.

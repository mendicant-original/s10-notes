# Code Structure

Similar to how project structure matters for making your code play nice with other people's environments, your code structure also is an important part of being a good Ruby citizen. The tips below will help prevent weird things from happening when people are working on your project.

## Namespacing your code

Debugging name collisions can be very frustrating and confusing. Because the top-level namespace is a shared space that your code needs to share with all its dependencies as well as Ruby itself, it is best to be as frugal as possible about what top-level constants you define. Typically, the best approach is to wrap your entire project within a single uniquely-named module, to keep it as isolated as possible from the rest of the runtime system.

That means if we were creating a gem called mail_server which implemented a `Queue` class, we would not simply define the class as shown below:

```ruby
class Queue
  # ...
end
```

Instead, we would wrap the `Queue` class (and all other classes our gem provided) in a `MailServer` module, as shown below:

```ruby
module MailServer
  class Queue
    # ...
  end
end
```

## Avoiding empty module definitions

Some projects try to do the right thing by using namespaces, but end up doing so in a somewhat brittle way. The typical code smell starts in the entry point to the library, where a bare module is defined:

```ruby
require_relative "mail_server/queue"

module MailServer
end
```

The reason why people provide these blank module definitions is so that they can facilitate the condensed class definition syntax shown below:

```ruby
class MailServer::Queue
  # ...
end
```

However, outside of Rails, this style of class definition is often considered an anti-pattern. It creates an artificial sense of load-order dependencies between classes in a system, making it harder to require bits of functionality individually when needed. To avoid this issue, it's better to explicitly nest your modules, as shown below:

```ruby
module MailServer
  class Queue
    # ...
  end
end
```

## Avoiding dynamic code loading

While it may seem convenient to use `Dir.glob` or other means of dynamically loading your Ruby files, this makes it much harder to reason about the order in which the files in your project get loaded. Because the amount of time we spend creating files is trivial compared to the time we spend working on the code within them, adding an extra require line whenever a new file is created takes a negligible amount of effort. The tradeoffs of using dynamic file loading are simply not worth the cost of the ambiguity they introduce, except in rare cases where the exact files to be loaded cannot be known at runtime.

A general note related to this is that while dynamic techniques are powerful, they are much more complicated to reason about than their explicit counterparts. Ruby's strength lies in its ability to be as dynamic as we want it to be, but we need to constantly be aware of the tradeoffs involved in the decisions we make about whether to do something a slightly tedious but predictable way vs. a clever but complicated way.

<hr/>

[Go back to the
README](https://github.com/mendicant-university/s10-notes/blob/master/README.md)
for an outline of our notes across all topics.

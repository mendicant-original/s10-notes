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

When requiring files we don't need to use `Dir.glob`. We wrote our files and can take the few seconds to require them by name. Requiring them this way also ensures any files that need to be required in a certain order are done so. This will also prevent us from accidentally requiring junk we don't want, such as Vim swap files.

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

Often we'll see an empty module defined in the root file of a library. For example, `lib/mail_server.rb` may look like this.

```ruby
require_relative "mail_server/queue"

module MailServer
end
```

This is often done for one of two reasons. Cargo Culting and improper Class/Module definition in your other files. If you don't know why you're doing this, it's Cargo Culting and you should stop. Remember to examine everything you write in your code and make sure it's necessary. Now if you need this because your other code won't work, it's most likely because you've written something like `lib/mail_server/queue.rb` below.

```ruby
class MailServer::Queue
  # ...
end
```

Don't do this. The correct way to define your class is shown below. It allows you to remove the empty method definition from your root file. And remove a dependency on the order your code is required.

```ruby
module MailServer
  class Queue
    # ...
  end
end
```

## Avoiding dynamic code loading

When requiring files we don't need to use `Dir.glob`. We wrote our files and can take the few seconds to require them by name. Requiring them this way also ensures any files that need to be required in a certain order are done so. This will also prevent us from accidentally requiring junk we don't want, such as Vim swap files.

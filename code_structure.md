# Code Structure

Similar to how project structure matters for making your code play nice with other people's environments, your code structure also is an important part of being a good Ruby citizen. The tips below will help prevent weird things from happening when people are working on your project.

## Namespacing your code

Debugging name collisions can be very difficult. If your library defines everything on the root namespace have two potential name collisions paths. You could overwrite top level namespace Classes, Methods, etc. that you rely on, or you someone requiring your library in their code could have their code overwritten by you. To avoid this problem we wrap our code in a namespace via a Module. As shown below, we simply take the name of our library (`MailServer` in this case) and use it as the namespace.

```ruby
# Don't do this
class Queue
  # ...
end

def run
  # ...
end

# Do this instead
module MailServer
  class Queue
    # ...
  end

  def self.run
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

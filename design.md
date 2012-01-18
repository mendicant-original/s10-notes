# Design

Figuring out the right design for your code is highly context dependent and can be tricky to get right for even simple projects. However, there are certain problems that seem to have come up in several submissions in this session which can easily be avoided. We've tried to outline a few solutions to some of the most common problems here.

## Avoiding excess global state

I'm seeing a lot of over usage of the Singleton pattern. There's two problems with this. First is Ruby doesn't really have a good way to properly create Singleton's. Greg has written [a Practicing Ruby article](http://practicingruby.com/articles/shared/zmrfnxqpahmf) that discusses this topic in length. The second problem is that often we're reaching for a singleton because an object should only be created once not because it can only be created once.

Often times I'll see a configuration object being created as a singleton. Why? Is there a problem if someone creates multiple configuration objects? Take the example of our mail server. Perhaps they want to spawn multiple servers up to listen to different incoming queues. If we limit our configuration object to a singleton this can no longer be the case.

A singleton is a lot like a global object. It's created once upon first access and then passed back to whomever requests it. I try and void global state. So, I will often pass around my config object, logger object, etc. Instead of relying on a global singleton.

Another potential issue with singletons come during testing. You cannot test with different initial parameters for your singleton. Once you instantiate it in your first test, it will be the same for all other tests. This will lead you to either attempt to monkey patch your singleton as needed, or perform other tricks to ensure your tests are covering all possible inputs. Jumping through hoops in your tests is a design smell. Simplify your code.

## Avoiding class variables

`@@my_variable` this extra `@` throws a whole heap of gotcha's and problems at you. Greg wrote [an article](http://www.oreillynet.com/ruby/blog/2007/01/nubygems_dont_use_class_variab_1.html) on this over five years ago. Please read it an educate other's on why class variables are so bad.

## Using modules effectively

Bottom line, modules give us immensely more flexibility than subclasses when it comes to inheritance. Take for example this code:

```ruby
module Maildo
  module Message
    class SenderAwareMessage
      # ...
    end

    class SubscriberAwareMessage < SenderAwareMessage
      # ...
    end

    class TaskAwareMessage < SubscriberAwareMessage
      # ...
    end

    class List < TaskAwareMessage
      # ...
    end
  end
end
```

Now imagine that each of these classes is defined in it's own file. You'd have to navigate four files to find out everything that List does. Now take a look what happens is we use modules instead.

```ruby
module Maildo
  module Message
    module SenderAwareMessage
      # ...
    end

    module SubscriberAwareMessage
      # ...
    end

    module TaskAwareMessage
      # ...
    end

    class List
      include SenderAwareMessage
      include SubscriberAwareMessage
      include TaskAwareMessage
    end
  end
end
```

Now looking at `List`, we can see all of the modules it inherits from. This also opens up the opportunity to have classes that don't follow our previous subclass chain. Perhaps we want something to be a `SubscriberAwareMessage` and `TaskAwareMessage`. Using subclassing inheritance this is not possible. But with modules we can mix and match as needed. Using modules like this is one of the largest powers of Ruby and really separates the beginner from the intermediate programmer. Greg wrote an excellent series during his first run of Practicing Ruby on modules. I'd urge you to read them and see how you can write better code. Here are the links.

1. [Issue 1.8: Uses for modules (1 of 4)](http://blog.rubybestpractices.com/posts/gregory/037-issue-8-uses-for-modules.html)
2. [Issue 1.9: Uses for modules (2 of 4)](http://blog.rubybestpractices.com/posts/gregory/038-issue-9-uses-for-modules.html)
3. [Issue 1.10: Uses for modules (3 of 4)](http://blog.rubybestpractices.com/posts/gregory/040-issue-10-uses-for-modules.html)
4. [Issue 1.10.5: Addendum to 'Uses for modules, part 3'](http://blog.rubybestpractices.com/posts/gregory/041-issue-10.5-uses-for-modules.html)
5. [Issue 1.11: Uses for modules (4 of 4)](http://blog.rubybestpractices.com/posts/gregory/043-issue-11-uses-for-modules.html)

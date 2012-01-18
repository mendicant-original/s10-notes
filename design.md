# Design

Figuring out the right design for your code is highly context dependent and can be tricky to get right for even simple projects. However, there are certain problems that seem to have come up in several submissions in this session which can easily be avoided. We've tried to outline a few solutions to some of the most common problems here.

## Avoiding excess global state

While most folks know to avoid global variables, they tend to treat singleton objects as somehow being different things entirely. However, since the entire purpose of a singleton object is to be a singular and global representation of an object in your system, it is pretty much the object-oriented equivalent of storing a value in a global variable.

Using the singleton pattern has become commonplace for things like configuration objects, but we can probably build systems which are just as easy to use without them, and not sacrifice testability or maintainability along the way. Systems which do not rely on singletons also are naturally more capable of handling situations in which you want to have many of something that you originally thought you may need only one of. For example, in an object-relational mapper, using a singleton to manage the database connection information will limit the ORM to working with a single database at a time, but allowing configuration objects to be passed in instead makes it possible to instantiate multiple connection objects with different configuration details. With that in mind, it's not hard to see that similar scenarios apply to our mail server exercise in fairly natural ways.

There is an example of building a server which accepts an ordinary instance of a configuration object in the [Configuration Notes](https://github.com/mendicant-university/s10-notes/blob/master/configuration.md) we've provided, and that may provide a good starting point for how to remove singletons from your system wherever they're not clearly providing a major advantage. On top of that, we recommend reading [this Practicing Ruby article](http://practicingruby.com/articles/shared/zmrfnxqpahmf) which explains how awkward it is to implement singleton objects in Ruby in the first place.

This is one area in which what has become the convention might not be as good an idea as we once thought it was, and so we'd like to encourage you to use this course to experiment with more flexible designs that do not depend on singleton objects. We can discuss the tradeoffs as we go, since there are bound to be some.

## Avoiding class variables

While it's hard to say that any language construct is universally evil (even `eval` with strings has its uses!), the closest thing to being a universally bad idea in Ruby is to use class variables. Please read [Gregory's article from 2007](http://www.oreillynet.com/ruby/blog/2007/01/nubygems_dont_use_class_variab_1.html) on this issue, and let us know if you have any questions about it or aren't convinced by it.

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

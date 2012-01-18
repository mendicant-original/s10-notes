# Design Notes

Figuring out the right design for your code is highly context dependent and can be tricky to get right for even simple projects. However, there are certain problems that seem to have come up in several submissions in this session which can easily be avoided. We've tried to outline a few solutions to some of the most common problems here.

## Avoiding excess global state

While most folks know to avoid global variables, they tend to treat singleton objects as somehow being different things entirely. However, since the entire purpose of a singleton object is to be a singular and global representation of an object in your system, it is pretty much the object-oriented equivalent of storing a value in a global variable.

Using the singleton pattern has become commonplace for things like configuration objects, but we can probably build systems which are just as easy to use without them, and not sacrifice testability or maintainability along the way. Systems which do not rely on singletons also are naturally more capable of handling situations in which you want to have many of something that you originally thought you may need only one of. For example, in an object-relational mapper, using a singleton to manage the database connection information will limit the ORM to working with a single database at a time, but allowing configuration objects to be passed in instead makes it possible to instantiate multiple connection objects with different configuration details. With that in mind, it's not hard to see that similar scenarios apply to our mail server exercise in fairly natural ways.

There is an example of building a server which accepts an ordinary instance of a configuration object in the [Configuration Notes](https://github.com/mendicant-university/s10-notes/blob/master/configuration.md) we've provided, and that may provide a good starting point for how to remove singletons from your system wherever they're not clearly providing a major advantage. On top of that, we recommend reading [this Practicing Ruby article](http://practicingruby.com/articles/shared/zmrfnxqpahmf) which explains how awkward it is to implement singleton objects in Ruby in the first place.

This is one area in which what has become the convention might not be as good an idea as we once thought it was, and so we'd like to encourage you to use this course to experiment with more flexible designs that do not depend on singleton objects. We can discuss the tradeoffs as we go, since there are bound to be some.

## Avoiding class variables

While it's hard to say that any language construct is universally evil (even `eval` with strings has its uses!), the closest thing to being a universally bad idea in Ruby is to use class variables. Please read [Gregory's article from 2007](http://www.oreillynet.com/ruby/blog/2007/01/nubygems_dont_use_class_variab_1.html) on this issue, and let us know if you have any questions about it or aren't convinced by it.

## Using modules effectively

While class inheritance is the golden hammer in many object oriented programming languages, Ruby's mixin capabilities provide a solid alternative to the traditional inheritance model that apply in a wide range of situations. As an example of their strengths, consider the code below:

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

If each of these classes are actually building upon one another's functionality, and each child is actually a specialized superset of its parent class, it makes sense to use class inheritance. However, it is likely to be the case in a scenario like this that the classes mentioned are simply a set of independent handlers which should all be run by the `List` object. If that is the case, it makes sense to flatten the structure by using modules instead of subclassing:

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

In the structure above, all of the modules are independent from one another, but the method resolution order for calls on the `List` object remains similar to our inheritance example. The key difference is that we don't need to place an artificial hierarchy on the relationship between the modules just to get the behavior we want to see in `List`. This is a subtle but important difference: whenever it's as convenient to use modules as it is to subclass, the former should be chosen because it reduces the amount of hard dependencies in the system.

For more than you could possibly ever want to know about uses for modules, see the Practicing Ruby series on the topic. Some of the materials are a bit dated, but for the most part they should give you a good sense of what the possibilities are:

1. [Issue 1.8: Uses for modules (1 of 4)](http://blog.rubybestpractices.com/posts/gregory/037-issue-8-uses-for-modules.html)
2. [Issue 1.9: Uses for modules (2 of 4)](http://blog.rubybestpractices.com/posts/gregory/038-issue-9-uses-for-modules.html)
3. [Issue 1.10: Uses for modules (3 of 4)](http://blog.rubybestpractices.com/posts/gregory/040-issue-10-uses-for-modules.html)
4. [Issue 1.10.5: Addendum to 'Uses for modules, part 3'](http://blog.rubybestpractices.com/posts/gregory/041-issue-10.5-uses-for-modules.html)
5. [Issue 1.11: Uses for modules (4 of 4)](http://blog.rubybestpractices.com/posts/gregory/043-issue-11-uses-for-modules.html)

During this session, we'd like to encourage you to experiment with modules whenever it makes sense to do so. There is a such thing as module overuse, but if you're not using them often at all, chances are you're missing out on some good opportunities to improve your design. The key thing to remember is that modules are still a form of inheritance: they're just a non-hierarchical, more flexible type of inheritance. Your first question should be whether inheritance is appropriate at all, and then once you've decided that it is, it's worth trying to see if your problem can be modeled using module mixins before resorting to class inheritance.

<hr/>

[Go back to the
README](https://github.com/mendicant-university/s10-notes/blob/master/README.md)
for an outline of our notes across all topics.

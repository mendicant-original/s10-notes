# Style Notes

Throughout the life of Mendicant University, we have been compiling a [Style Guide](https://github.com/mendicant-university/wiki/wiki/Style-Guide) to help others get acquainted with the standard coding style in the greater Ruby ecosystem. This guide was made with two goals in mind: To show students what clean Ruby code looks like, and to make it easier for others reading your code (other students, mentors and instructors) to evaluate it. By using a standard style we can more easily communicate with one another through our code.

Please take the time to carefully read the style guide and apply its recommendations to your projects. In addition to the points in that guide, there are a few more things listed below that we thought were worth sharing with this session.

## Keeping things tidy

Reading code that is strewn with debugging code or unused commented code is akin to walking through a park covered with litter. The park may be beautiful, but the mess gets in the way of appreciating its charms. Because we use revision control, it is possible to both recover old code from previous commits when we need it, and also to conduct experiments or extensive debugging sessions on branches that can be cleaned up before they get merged back into your master branch. By keeping your master branch clean, it will be possible for others to review it and work with it at any point in time. 

## Understanding why we do things

Even though style is to some extent about aesthetics, most coding conventions have at least some substantial reason for existing. A solid reason for doing something a certain way is a sign that it might be a good idiom. Conversely, unidiomatic coding style typically does not have a clear justification, but instead often boils down to a couple common faulty arguments.

### I saw Programmer X do it (Argument based on authority)

Often this is cargo culting. We see someone we work with or out in the community whom we admire do something and we assume they must be doing it for a reason, so we copy them. A few years ago Aaron Patterson, when asked why he doesn't use parens in method definitions he said, "the parser skips a step, so it's faster". What he admitted recently was he was just trolling the community. He just liked the way it looked, there was no performance difference. 

The lesson here is that it's important to know why you're doing something the way you are. If someone else is doing something you want to copy, investigate why and test their claims yourself. You can also discuss those claims with others to get a deeper understanding of the tradeoffs involved. Code reviews are an excellent opportunity to get this sort of feedback, so don't hesitate to ask us if any of our own standards are unclear or sound like bad ideas to you.


### It helps me remember Y. (Argument based on convenience)

The following example is the conventional way of separating public method definitions from private definitions, under normal circumstances.

```ruby
class MyClass
  def initialize
    # ...
  end
  
  private
  
  def method1
    # ...
  end
  
  def method2
    # ...
  end
end
```

However, occasionally you'll see people writing their code in the manner shown below, because they feel it makes it easier to see which methods are public and which are public:

```ruby
class MyClass
  def initialize
    # ...
  end
  
  def method1
    # ...
  end
  private :method1
  
  def method2
    # ...
  end
  private :method2
end
```

Whenever we end up writing extra code like this for the sake of clarity, we need to step back and ask ourselves, can our tools do this work? For example, Vim users have at least three different ways to know whether they are working with private methods without having to write extra code. In particular, it is possible to: 

 * Use "%" to match def/end and jump up the code tree quickly. 
 * Enable code folding so that method definitions are collapsed by default.
 * Create a highlight group that changes the background for private methods.
 
Most decent text editors have similar functionality available to them as well. Relying on tools rather than conventions for things like this allows for each developer to customize their own experience rather than dealing with noisy source files that use non-standard conventions.

On a related note, we've also seen some students labeling their `end` statements on occasion. In addition to being something that introduces a potential maintenance headache, this convention is another clear example in which using your tools effectively to traverse your code structure can eliminate the need for these sorts of comments.

```ruby
class MyClass
  def initialize
  end # initialize
end # MyClass
```

While these kinds of mistakes are often born out of good intentions (making things easier to understand / remember), it is usually the case that if you're writing code in a non-conventional way, there is a better way to solve the problem that's causing you to break from standard practices. This course is a great opportunity to discuss those sorts of points, so don't be afraid to ask about anything that comes to mind!

<hr/>

[Go back to the
README](https://github.com/mendicant-university/s10-notes/blob/master/README.md)
for an outline of our notes across all topics.

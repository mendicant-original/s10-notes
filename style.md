Style
=====

MU Style Guide
--------------

Throughout the life of Mendicant University, we have been compiling a [Style Guide](https://github.com/mendicant-university/wiki/wiki/Style-Guide) to help others get aquainted with the standard coding style in the greater Ruby community. As with all matters of style, there are going to be disgreements. This guide was made with two goals. To show students what clean Ruby code looks like, and to make it easier for others reading your code (other students, methods and instructrs) to evaluate it. By using a standard style we can more easily communicate with one another though our code.

Please take the time to careful read the Style Guide and apply the style to your project. This is a very important part of having a quality exercise.

Don't litter
------------

Reading code that is strewn with debugging code or unused commented code is akin walking through a litter covered park. The park may be beautiful, but all this litter is just distracting. Your debugging code is just that, debugging code. If your working on debugging a problem, you shouldn't be checking anything in until you've uncovered the problem and fixed it. Version control allows us to go back into history to rescue any old code we may have accidentally deleted.

Please, be a good programmer and treat your code as a garden. Pull the weeds and compost them.

Why Do We Do Things?
--------------------

We often see strange bits of style. When asked why, the programmer usually has one of two answers.

  1. I saw Programmer X do it.
  2. It helps me remember Y.

I'll address these each individually and give an example.

### I saw Programmer X do it

Often this is cargo culting. We see someone we work with or out in the community that we admire do something and we assume they must be doing it for a reason, so we copy them. A few years ago Aaron Patterson, when asked why he doesn't use parens in method definitions he said, "the parser skips a step, so it's faster". What he admitted recently was he was just trolling the community. He just liked the way it looked, there was no performance difference. Know why you're doing something, and if someone else is doing something you want to copy, investigate why and test their claims yourself.

### It helps me remember Y.

Recently I saw some code where programmers couldn't remember if they had reached the private section of their instance methods, so instead of doing this:

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

They'd write their code like this:

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

Anytime we have to write extra code like this we need to step back and ask ourselves, can our tools do this work. I'm a Vim user, and I can think of at least three different ways to know if I'm working with private methods without having to write extra code. I can use "%" to match def/end and jump up the code tree quickly. I can enable code folding so that I only see the method I'm working on and the def's of others. Or I can write create a highlight group that changes the background for private methods. What does this tell you? It tells you that the better we know our tools, the less code we have to write.

I saw a couple other things along these lines this week.

```ruby
class MyClass
  def initialize
  end # initialize
end # MyClass
```

We shouldn't have to write a comment each time we end a block. Folding or Paren matching will do this for us.

```ruby
class MyClass
  def initialize
    @name = "bob"
  end
  
  def greet
    puts "hello #{name}"
  end
  
  def change_name(val)
    name = val
  end
  
  private
  
  attr_accessor :name
end
```

Here we're making a private `attr_accessor` to give use `#name` and `#name=`. Why? It's our instance variable, let's use it directly `puts "hello #{@name}"`. We're not gaining anything by having an `attr_accessor` and it's not going to save us any keystrokes until we use it 19 times.
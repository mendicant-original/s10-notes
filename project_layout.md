# Project Layout Notes

Without a specific effort to make your project designed for packaging and re-use, lots of small things that may seem innocuous will end up biting your users and contributors. We've provided a few tips here based on problems we've seen in the submissions so far in this session to help make your projects easier for others to work with.

## Using Bundler

Introducing a `Gemfile` into your project can make it easier for contributors to install your projects dependencies and also makes it easier for them to test out any executables your project ships with. Whenever your project depends on any gems (both at runtime and in development), it makes sense to build a minimal `Gemfile` which reads the dependencies directly from your `gemspec`. This makes it possible for Bundler users to work with your project in a way that is familiar to them without requiring the use of Bundler for those who'd rather not install it.

```ruby
source :rubygems

gemspec
```

Assuming you have an ordinary gemspec in your project, this will make it possible to use Bundler to install your dependencies via `bundle install`, and to execute your project's scripts via `bundle exec`. This really helps us when we're reviewing your projects, as it makes it so we don't need to spend too much effort setting up your project in order to evaluate it.

While Bundler has significant advantages for development, you should not depend on Bundler at all at runtime (i.e. don't `require 'bundler/setup'` or reference the `Bundler` constant anywhere) unless you are specifically using Bundler due to git-based gem dependencies or have problems with gem conflicts that can't easily be resolved by ordinary gem locking. Introducing Bundler at runtime results in a lot of additional complexity and an extra dependency for end users, so steps should be taken to avoid this when possible.


## Organizing your files

The general directory layout of Ruby libraries and applications has been fairly well agreed upon for several years now. While the Ruby Best Practices book covered this back in 2008, [Steve Klabnik has a more recent article](http://timelessrepo.com/making-ruby-gems) about structuring your files for gem packaging that is somewhat more up to date. Be sure to read his post and apply its recommendations to your projects as best as you can.

## Managing your environment

Tools like RVM and RBENV make life a whole lot easier when it comes to managing your Ruby environment. However, if you check in your `.rvmrc` file or `.rbenv-local` files, you may end up forcing a contributor/reviewer to either use your preferred way of configuring things or jump through hoops to override your configuration file in their own environment. An alternative approach is to treat these files the same you would any other configuration file: add them to your `.gitignore` and provide an example configuration file. It makes sense to get in the habit of doing this for anything that might affect the user's environment, including things like your `Guardfile` if you're using [guard](https://github.com/guard/guard).

## Use `require_relative` whenever possible

`require_relative` dynamically generates the correct path to a referenced file relative to the working directory. This allows you to simply reference other files relative to the location of the file in which the `require_relative` statement is mentioned. The `require` method works altogether differently in that it searches through the directories listed in the `$LOAD_PATH` array.

Before `require_relative` existed, most people either had to resort to ugly `File.join` hacks, or manipulate the load path in some way to get `require` to work as you would intuitively expect. As a result, many tools including Rake, Rubygems, Rspec, etc. added `lib/` to the load path by default so that these workarounds would not be necessary. Even in these special cases where `require` would work just as well, it's still better to use `require_relative`.

As rule of thumb, when faced with two options that both accomplish the same thing, you should chose the one that has less potentially harmful side effects. In this case, `require_relative` is the better choice because it provides a clean syntax to do requires that are decoupled from the load path.

For more in-depth information on the differences between `require` and `require_relative` and other ways to load code, you should read the Practicing Ruby article [Ways to load code](http://practicingruby.com/articles/shared/tmxmprhfrpwq).
<hr/>

[Go back to the
README](https://github.com/mendicant-university/s10-notes/blob/master/README.md)
for an outline of our notes across all topics.

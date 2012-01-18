# Project Layout Notes

Without a specific effort to make your project designed for packaging and re-use, lots of small things that may seem innocuous will end up biting your users and contributors. We've provided a few tips here based on problems we've seen in the submissions so far in this session to help make your projects easier for others to work with.

## Using Bundler

Introducing a `Gemfile` into your project can make it easier for contributors to install your projects dependencies and also makes it easier for them to test out any executables your project ships with. Whenever your project depends on any gems (both at runtime and in development), it makes sense to build a minimal Gemfile which reads the dependencies directly from your gemspec. This makes it possible for Bundler users to work with your project in a way that is familiar to them without requiring the use of Bundler for those who'd rather not install it.

```ruby
source :rubygems

gemspec
```

Assuming you have an ordinary gemspec in your project, this will make it possible to use Bundler to install your dependencies via `bundle install`, and to execute your project's scripts via `bundle exec`. This really helps us when we're reviewing your projects, as it makes it so we don't need to spend too much effort setting up your project in order to evaluate it.

While Bundler has significant advantages for development, you should not depend on Bundler at all at runtime (i.e. don't `require 'bundler/setup'` or reference the `Bundler` constant anywhere) unless you are specifically using Bundler due to git-based gem dependencies or have problems with gem conflicts that can't easily be resolved by ordinary gem locking. Introducing Bundler at runtime results in a lot of additional complexity and an extra dependency for end users, so steps should be taken to avoid this when possible.


## Organizing your files

The general directory layout of Ruby libraries and applications has been fairly well agreed upon for several years now. While the Ruby Best Practices book covered this back in 2008, [Steve Klabnik has a more recent article](http://timelessrepo.com/making-ruby-gems) about structuring your files for gem packaging that is somewhat more up to date. Be sure to read his post and apply its recommendations to your projects as best as you can.

## Managing your environment

Tools like RVM and RBENV make life a whole lot easier when it comes to managing your Ruby environment. However, if you check in your `.rvmrc` file or `.rbenv-local` files, you may end up forcing a contributor/reviewer to either use your preferred way of configuring things or jump through hoops to override your configuration file in their own environment. An alternative approach is to treat these files the same you would any other configuration file: add them to your .gitignore and provide an example configuration file. It makes sense to get in the habit of doing this for anything that might affect the user's environment, including things like your `Guardfile` if you're using [guard](https://github.com/guard/guard).

<hr/>

[Go back to the
README](https://github.com/mendicant-university/s10-notes/blob/master/README.md)
for an outline of our notes across all topics.

Project Layout
==============

The Gemfile
-----------

Make sure your project has a Gemfile if it's using anything outside of stdlib. If you're writing a gem, let your gemspec specify your dependencies and have you Gemfile reference as shown below.

```ruby
source :rubygems

gemspec
```

It's really that simple. There's some debate as to whether you should include your Gemfile.lock with your code. The best rule right now is to only include it with Applications where they will not function without the Gemfile.lock specific versions. But if that's the case, those versions should be specified exactly in your Gemfile already.

Physical Structure
------------------

Steve Klabnik [wrote an article](http://timelessrepo.com/making-ruby-gems) last year that lays down exactly how you should structure any ruby library. Please read this and apply it to your projects. This is widely agreed upon by the community as the standard.

Don't Force Your Environment on Others
--------------------------------------

Some programmers my use RVM, some may use RBENV. Some may use Gemsets, some may not. Don't force others to use your environment by including your `.rvmrc` or `.rbenv-local` files. Instead, as you do with configuration, add those to `.gitignore` and provide `.rvmrc.example` or `.rbenv-local.example` files. Apply this to any files that may force your environment on others, even things like your `Guardfile`.
# Configuration Notes

Making our applications and libraries easy to configure is an important part of
overall usability. While the topic is covered in great detail by a pair of Practicing Ruby articles ([Issue 1.3](http://blog.rubybestpractices.com/posts/gregory/032-issue-3-configurable.html) and [Issue 1.4](http://blog.rubybestpractices.com/posts/gregory/033-issue-4-configurable.html)), there several configuration-specific issues that we've seen in this session that we'd like to address directly.

## Keeping sensitive information private

One of the most common mistakes when sharing code is to embed usernames, passwords, credentials, API keys, and other private information directly in your implementation code. This is a bad idea for a number of reasons, apart from the obvious security issues. Hardcoding credentials also can lead to weird side effects when multiple people are using the same account to test something, and the general brittleness of the approach can make projects harder to maintain.

If you've checked in credentials in your project, simply deleting this information from your code and introducing a more flexible configuration approach will help put you on the right track. But because we use git for version control, the sensitive information will still be available in early commits if you don't specifically remove those as well. To learn how to rewrite your git history check out [this article](http://help.github.com/remove-sensitive-data/) from Github.

The reason why folks often check in credentials is that it seems more convenient to hardcode those values in the early stages of a project than it would be to think about configuration problems upfront. However, there are some simple tricks for building configurable applications that make it easy to make them more secure and flexible from the start, and those practices are worth learning.

## Providing example configurations 

Let's start of with an example configuration file from s10-int. For this exercise we need to configure a mail server. We're most likely going to need a handful of variables describing our email account, polling time, and how to send/receive messages. Our configuration file currently resides in `lib/mail_server/configuration.rb` and looks something like this.

```ruby
module MailServer
  module Configuration
    USERNAME     = "user@example.com"
    PASSWORD     = "password"
    POLLING_TIME = 10
    
    Mail.defaults do
      retriever_method(
        :pop3,
        address:    "pop.gmail.com",
        port:       995,
        user_name:  USERNAME,
        password:   PASSWORD,
        enable_ssl: true
      )
      
      delivery_method(
        address:   "smtp.gmail.com",
        port:      587,
        domain:    "example.com",
        user_name: USERNAME,
        password:  PASSWORD,
        authentication: true,
        enable_starttls_auto: true
      )
    end
  end
end
```

Now, we'd like to have users provide their own username and password along with the mail server configuration, but we don't want them to be forced to write this entire file for themselves. What we can do is create a new file under `config/environment.rb.example` which provides an example configuration for the users to complete. Be sure to also place `config/environment.rb` into your `.gitignore`. Now, we can write `config/environment.rb.example` as below:

```ruby
module MailServer
  module Environment
    USERNAME = "YOUR USERNAME"
    PASSWORD = "YOUR PASSWORD"
    
    POP3_SERVER = "YOUR POP3 SERVER" # example: pop.gmail.com
    POP3_PORT   = 995
    
    SMTP_SERVER = "YOUR SMTP SERVER" # example: smtp.gmail.com
    SMTP_PORT   = 587
    SMTP_DOMAIN = "YOUR SMTP DOMAIN" # example: example.com
  end
end
```

Then we can update `lib/mail_server/configuration.rb` to require this new file.

```ruby
require_relative "../../config/environment.rb"

module MailServer
  module Configuration
    POLLING_TIME = 10
    
    Mail.defaults do
      retriever_method(
        :pop3,
        address:    Environment::POP3_SERVER,
        port:       Environment::POP3_PORT,
        user_name:  Environment::USERNAME,
        password:   Environment::PASSWORD,
        enable_ssl: true
      )
      
      delivery_method(
        address:   Environment::SMTP_SERVER,
        port:      Environment::SMTP_PORT,
        domain:    Environment::SMTP_DOMAIN,
        user_name: Environment::USERNAME,
        password:  Environment::PASSWORD,
        authentication: true,
        enable_starttls_auto: true
      )
    end
  end
end
```

Providing the example configuration makes it easier for a user to start working with your application. It also helps seperate your environmental settings from your actual configuration. This is all very nice when working with applications, but if we were building a library, we could not reasonably assume that a user will create a `config/environment.rb` file. For this reason, libraries often provide dynamic configuration objects.

## Building configuration objects

A configuration object is an ordinary Ruby object that is responsible for representing and processing configuration data within your codebase. The `Mail.defaults` call in the previous section hints at one form of configuration object, and the example below tackles the same problem in a slightly different way:

```ruby
module MailServer
  class Configuration
    attr_accessor :username, :password,
      :pop3_server, :pop3_port,
      :smtp_server, :smtp_port, :smtp_domain

    def valid?
      # validate configuration data is as expected
    end

    def errors
      # after valid? is called, references an array of
      # errors like ["username is missing", "pop3_server address malformed"]
      # etc... or return an empty array if configuration is valid
    end
  end
end
```

With a configuration object such as the one above, a user would be able to work with our library in the following way:

```ruby
require "mail_server"

config = MailServer::Configuration.new           
config.username = "user@example.com"
# ...

# start may call config.valid? and possibly report on config.errors
MailServer.start(config)
```

Both the `mail` gem configuration system and the imaginary example shown above have something important in common: They both use ordinary object oriented programming techniques to treat configuration data as just another object in the system. This provides users with a great deal of flexibility while keeping things clean and easy to work with.

## Using shell environment variables

Another option for configuring your libraries and applications is the use of environment variables. This approach has the benefit of making it possible to vary configuration data at runtime without the user relying on access to the filesystem to update configuration data, and is commonly used in restricted environments such as Heroku.

While this approach is not recommended if building a Ruby based configuration system is feasible, it is sometimes the only practical way to configure your application. For more details on this approach, see [Practicing Ruby Issue 1.4](http://blog.rubybestpractices.com/posts/gregory/033-issue-4-configurable.html), particularly the "Using the Shell Environment for Configuration" section.

<hr/>

[Go back to the
README](https://github.com/mendicant-university/s10-notes/blob/master/README.md)
for an outline of our notes across all topics.

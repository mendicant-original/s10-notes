Configuration
=============

Don't Check in Passwords
------------------------

One of the most common mistakes when sharing code is to embed our usernames, passwords, credentials, other private information into the code. It's fair to say you shouldn't share this data with the world. You may catch that you've included information like this before releasing it and remove it. But, unless you go back and rewrite your version history it's still there.

To learn how to rewrite your Git history check out [this article](http://help.github.com/remove-sensitive-data/) from Github. Of course, what we really should practice not including this information in the first place, but it's so convinent to not type our credentials in each time we run. In the next section we'll take a look at a common solution to this problem, example configurations.

Providing Example Configurations
--------------------------------

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
        address:    ENVIRONMENT::POP3_SERVER
        port:       ENVIRONEMNT::POP3_PORT,
        user_name:  ENVIRONMENT::USERNAME,
        password:   ENVIRONMENT::PASSWORD,
        enable_ssl: true
      )
      
      delivery_method(
        address:   ENVIRONMENT::SMTP_SERVER,
        port:      ENVIRONMENT::SMTP_PORT,
        domain:    ENVIRONMENT::SMTP_DOMAIN,
        user_name: ENVIRONMENT::USERNAME,
        password:  ENVIRONMENT::PASSWORD,
        authentication: true,
        enable_starttls_auto: true
      )
    end
  end
end
```

Providing the example configuration makes it easier for a user to start working with your application. It also helps seperate your environmental settings from your actual configuration. This is all very nice when working with applications, but what if we're writing a library. We have no easy way to ensure that the user creates `config/environment.rb`. We need a new, more dynamic way of configuring our libraries. One such was is a configuration object which we'll discuss in the next section.

Configuration Objects
---------------------

What's a configuration object? Quite simply put it's an object that holds all our configuration information. What does one look like? Take a look in the previous sample. `Mail.defaults` is a configuration object in which we're setting up the configuration for the `receiver_method` and `delivery_method`. Below is an example of how to change our previous example into a configuration object.

```ruby
module MailServer
  class Configuration
    attr_accessor :username, :password,
      :pop3_server, :pop3_port,
      :smtp_server, :smtp_port, :smtp_domain
      
    def initialize(&block)
      instance_eval &block if block_given?
      
      # validate all required fields were given
    end
  end
end
```

Now a user of our library can config it like this:

```ruby
require "mail_server"

config = MailServer::Configuration.new do
           username = "user@example.com"
           # ...
         end
         
MailServer.start(config)
```

You could make other modifications to allow the user to perhaps setup the configuration by using the block form of `#start` or perhaps write `Configuration.setup` which initializes a new `Configuration` object and stores it as class instance variable. These are all valid options, but the key take away is to exploit Rubies biggest feature, "Everything's an Object". Use Objects as often as possible, don't be afraid to create new Classes/Objects. This leads us to our final point on configuration, Configuration: CONSTANTS vs. Object.

Configuration: CONSTANTS vs. Object
-----------------------------------

There's no true consensus in the Ruby community as which method of configuration is preferred. However here are some rules to follow.

  - An internal constant such as an email template, or a pooling length, that is never going to be configured by the user and will not change during testing can be a CONSTANT.
  - When writing a Gem, do not make your users set CONSTANTS. Instead provide them with configuration objects or block that are well documented.
  - When writing an application allow for either method, but ensure you provide an example configuration in the `config/` directory that only has the user entering needed information.
Configuration
=============

Don't Check in Password
-----------------------

One of the most common mistakes when sharing code is to embed our usernames, passwords, credentials, other private information into the code. It's fair to say you shouldn't share this data with the world. You may catch that you've included information like this before releasing it and remove it. But, unless you go back and rewrite your version history it's still there.

To learn how to rewrite your Git history check out [this article](http://help.github.com/remove-sensitive-data/) from Github. Of course, what we really should practice not including this information in the first place, but it's so convinent to not type our credentials in each time we run. In the next section we'll take a look at a common solution to this problem, example configurations.

Providing Example Configurations
--------------------------------

Let's start of with an example configuration file from s10-int. For this exercise we need to configure a mail server. We're most likely going to need a handful of variables describing our email account, polling time, and how to send/receive messages. Our configuration file may look something like this.

```ruby
module MailServer
  module Configuration
    USERNAME     = "user@example.com"
    PASSWORD     = "password"
    POLLING_TIME = 10
    
    Mail.defaults do
      retriever_method(
        :pop3,
        address:   "pop.gmail.com",
        port:      995,
        user_name: USERNAME,
        password:  PASSWORD,
        enable_ssl: true
      )
      
      delivery_method(
        address:   "smtp.gmail.com",
        port:      587,
        domain:    "example.com",
        user_name: USERNAME,
        password:  PASSWORD,
        authentication:       true,
        enable_starttls_auto: true
      )
    end
  end
end
```


  - Congfiguration Objects vs Configuration Globals
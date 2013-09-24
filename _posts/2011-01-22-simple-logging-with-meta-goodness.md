---
layout: post
title: Simple logging with meta goodness
---

### Syslog: a powerful creature from the Elder Days
When it comes to logging I’m a big fan of good old `syslog(3)`. Nowadays many programmers use something more or less inspired by Log4J, still the syslog seems to have at least that much options: just substitute severities for priorities and log targets for log facilities. Syslog has also in my opinion the strong advantage in that it has the whole infrastructure outside of the application – as a standard service at your OS disposal. Operating system should have a robust logging facility, shouldn’t it? That’s the case in the Unix world: virtually every system you encounter has some syslog implementation running out of the box. Standard C library has an API call to it, and if for some reason it’s impossible or inconvenient to use this interface, you can directly send messages using a well-known protocol to the UDP 514 port of your machine – as the client implementation for Node.js shows.

Not to mention the centralized logging machinery may be preferable by the admins in some serious production deployments and that is sometimes a prerequisite for your app.

Ruby’s inferface to syslog is very simple: you `require 'syslog'` which is in the stardard library, you initialize the logger with `Syslog.open` and then you have bunch of module functions err, info, debug etc for all the message priorities. The logs are sent to the files or servers defined in your system-wide configuration.

### Huh, system-wide logger? But I live in the REPL !!
Yeah well, production, but what about typical coding?

If you prefer this style of prototyping when you constantly load stuff into your IRB session and test it interactively, having all the logs available via `tail -f` seems an overkill, simple dump to the standard error would be enough. I crafted a simple wrapper module which redefines the interface calls using a simple define_method trick. It goes like this:

{% highlight ruby %}
require 'syslog'

module Log

  def self.set_stderr
    define_with_handler lambda{|any_priority, msg| $stderr.puts msg}
  end

  def self.set_syslog(appname)
    define_with_handler lambda do |priority, msg| 
      Syslog.send priority, msg
    end
    Syslog.open appname, 
      Syslog::LOG_PID | Syslog::LOG_NDELAY, Syslog::LOG_LOCAL1
  end

  private

  def self.define_with_handler handler
    %w/crit emerg alert err warning notice info debug/.each do 
      |priority|
      add_code do
        define_method priority, lambda do |msg| 
          handler.call(priority, msg)
        end
      end
    end
    add_code {define_method :log, handler}
  end

end

Log.set_stderr
{% endhighlight %}
Note `add_code` is a wrapper around `module_eval` called in the context of an eigenclass – as explained before.

Then I use `Log.info "all your base are belong to us"` :-]

That’s it folks!


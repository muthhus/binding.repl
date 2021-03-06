| Project         | binding.repl
|:----------------|:--------------------------------------------------
| Homepage        | https://github.com/robgleeson/binding.repl
| Documentation   | http://rubydoc.info/github/robgleeson/binding.repl
| Code Metrics    | [![Code Climate](https://codeclimate.com/github/robgleeson/binding.repl.png)](https://codeclimate.com/github/robgleeson/binding.repl)
| CI              | [![Build Status](https://travis-ci.org/robgleeson/ichannel.binding.repl.png)](https://travis-ci.org/robgleeson/binding.repl)
| Author          | Robert Gleeson

__DESCRIPTION__

__binding.repl__ can start a number of different ruby consoles at runtime.
IRB, ripl, and pry(of course!) can be invoked at runtime in any context
that an instance of `Binding` closes over.

I owe all credit to John Mair([banisterfiend](https://github.com/banister)),
the creator of Pry, for seeing the potential in a console that can be
started at runtime and bound to the context of a `Binding`. I don't
think a single ruby console has put as much emphasize on a console
that be invoked at runtime as Pry has.

the interface to invoke Pry at runtime is very accessible and easy to
use but if you look at other ruby console implementations you might find
the eval() of code is the same. It happens through `Binding#eval` which
closes over a context(maybe the context is "Foo", a class, or an
instance of "Foo").

__binding.repl__ provides the same `binding.pry` interface to all ruby
consoles (if I missed one, open an issue or send an e-note :)).

__EXAMPLES__

_Rails_

```ruby
class BlogsController < ApplicationController
  def show
    @blog = Blog.find(params[:id])
    binding.repl.{pry,irb,ripl,auto}
  end
end
```

_Auto discovery_

```ruby
class Bar
  # auto discover and start the first available console.
  # default order is: ripl, pry, or irb.
  binding.repl.auto

  # change default load order
  Binding.repl.auto_load_order = %w(pry ripl irb)

  # remove "irb" from auto discovery
  Binding.repl.auto_load_order -= ["irb"]
end
```

_Enable/disable at runtime_

```ruby
class Baz
  # invoke irb
  binding.repl.irb
  Binding.repl.disable!

  # no-op, returns :'binding.repl.disabled' and continues execution.
  binding.repl.irb
  binding.repl.ripl

  # invoke pry
  Binding.repl.enable!
  binding.repl.pry
end
```

__NOTES__

_CONFIGURATION_

ripl and pry can be customized by passing a set of key-value pairs but
IRB isn't as straight forward to configure and I haven't implemented
any customization options for it yet.

ripl can be invoked with options that are forwarded to `Ripl.start(…)`:

```ruby
class Foo
  binding.repl.ripl riplrc: false
end
```

pry can be configured in the same way. options are forwarded to `Pry.start(…)`:

```ruby
class Foo
  binding.repl.pry quiet: true
end
```

_WORKING WITH TEAMS_

people working in teams might have different choices for what ruby
console they like to use and __binding.repl__ tries to accomondate by
reading a `.binding.repl.rc` file from $HOME or by setting a shell
environment variable. `binding.repl.rc` is a simple JSON file.

let's say rob & amy are working on some code together. amy prefers
ripl, so her `$HOME/.binding.repl.rc` would look like this:
```javascript
{
  "auto_load_order": ["ripl", "irb"]
}
```

rob prefers pry, so his `$HOME/.binding.repl.rc` would look like this:
```ruby
{
  "auto_load_order": ["pry", "irb"]
}
```

when either rob or amy wants to start their favorite ruby console
they can use `binding.repl.auto`:

```ruby
class Foo
  # starts "irb" in the context of Foo for amy. fall back console is "irb".
  # starts "pry" in the context of Foo for rob. fall back console is "irb".
  binding.repl.auto
end
```

_SHELL ENVIRONMENT VARIABLES_

binding.repl can be configured with a couple of shell environment variables.

```bash
# disable trying to read $HOME/.binding.repl.rc
export BINDING_REPL_RC=0

# set the 'auto' load order
# takes precendence over $HOME/.binding.repl.rc when set
export BINDING_REPL_ORDER=pry,irb
```

_DEPENDENCIES_

binding.repl doesn't depend on anything. it's up to you to meet the
dependencies(pry, irb, and/or ripl). `binding.repl.{ripl,pry,irb}`
will try to load the appropiate console for you if it looks like it
hasn't been loaded yet.

__INSTALL__

ruby 1.9+ only.

```
gem install binding.repl
```

__CREDIT__

- [banisterfiend](https://github.com/banister) (John Mair)
  for pry

- [Kyrylo](https://github.com/kyrylo) (Kyrylo Silin)
  for his hard work on pry.

Rest of the Pry team(!!):

- [cirwin](https://github.com/conradirwin), Conrad Irwin.
- [ryanf](https://github.com/rf-), Ryan Fitzgerald.
- [rking](https://github.com/rking), "rking", on adventures outside programming now.

__LICENSE__

MIT.

__CONTRIBUTING__

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new E-Note

The Singleton Pattern
---------------------

1. A class that can have only one instance and that provides global
   access to that one instance.
2. The singleton presents rich opportunities for coupling in unfortunate ways.
3. It is best to limit the amount of code that is aware that a
   singleton is what it is.

### The Singleton Module.

```ruby
require 'singleton'

class SimpleLogger
  include Singleton

  attr_accessor :level

  ERROR = 1
  WARNING = 2
  INFO = 3

  def initialize
    @log = File.open('log.txt', 'w')
	@level = WARNING
  end

  def error(msg)
    @log.puts(msg)
	@log.flush
  end

  def warning(msg)
    @log.puts(msg) if @level >= WARNING
	@log.flush
  end

  def info(msg)
    @log.puts(msg) if @level >= INFO
	@log.flush
  end
end

logger1 = SimpleLogger.instance # Returns the logger
logger2 = SimpleLogger.instance # Returns exactly the same logger
```

### Modules as Singletons

```ruby
ModuleBasedSingleton
  ERROR = 1
  WARNING = 2
  INFO = 3

  @@log = File.open('log.txt', 'w')
  @@level = WARNING

  def self.error(msg)
    @@log.puts(msg)
	@@log.flush
  end

  #
  # And so on....
  #
end
```

### Testing Singletons

```ruby
require 'singleton'

class SimpleLogger
  # All of the logging functionality in this class...
end

class SingletonLogger < SimpleLogger
  include Singleton
end
```

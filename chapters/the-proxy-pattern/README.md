The Proxy Pattern
-----------------

1. Come in three main varieties: to control access to an object; to provde a location-independent way of getting at n object; and delaying the creation of an object.
2. The proxy has a reference to the real object, the subject, hidden inside.
3. The proxy delegates requests to the subject.

### The Protection Proxy

```ruby
class BankAccount
  attr_reader :balance

  def initialize(starting_balance = 0)
    @balance = starting_balance
  end

  def deposit(amount)
    @balance += amount
  end

  def withdraw(amount)
    @balance -= amount
  end
end

require 'etc'

class AccountProtectionProxy
  def initialize(real_account, owner_name)
    @subject = real_account
    @owner_name = owner_name
  end

  def method_missing(name, *args)
    check_access
    @subject.send(name, *args)
  end

  def check_access
    unless Etc.getlogin == @owner_name
      raise "Illegal access: #{ Etc.getlogin } cannot access account."
    end
  end
end

```

### Remote Proxies

```ruby
require 'soap/wsdlDriver'

wsdl_url = 'http://www.webservicex.net/WeatherForecast.asmx?WSDL'

proxy = SOAP::WSDLDriverFactory.new(wsdl_url).create_rpc_driver
weather_info = proxy.GetWeatherByZipCode('ZipCode' => '19128')
```

### Virtual Proxies

```ruby
class VirtualAccountProxy
  def initialize(&creation_block)
    @creation_block = creation_block
  end

  def method_missing(name, *args)
    subject.send(name, *args)
  end

  def subject
    @subject ||= @creation_block.call
  end
end

ap = VirtualAccountProxy.new { BankAccount.new(100) }
ap.deposit(25)
ap.withdraw(50)
puts "account balance is now: #{ ap.balance }"
# => account balance is now: 75
```

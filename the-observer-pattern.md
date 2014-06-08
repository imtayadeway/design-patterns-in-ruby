Also known as the publish/subscribe pattern or 'pubsub'.

```ruby
require 'observer'
  
class Employee
  include Observable
    
  attr_reader :name, :address
  attr_reader :salary
    
  def initialize(name, title, salary)
    @name = name
    @title = title
    @salary = salary
  end
    
  def salary=(new_salary)
    @salary = new_salary
    changed
    notify_observers(self)
  end
end

class Payroll
  def update(changed_employee)
    puts "Cut a new check for #{ changed_employee.name }!"
    puts "His salary is now #{ changed_employee.salary }!"
  end
end

class TaxMan
  def update(changed_employee)
    puts "Send #{ changed_employee.name } a new tax bill!"
  end
end

fred = Employee.new('Fred', 'Crane Operator', 30000.0)

payroll = Payroll.new
tax_man = Taxman.new

fred.add_observer(payroll)
fred.add_observer(tax_man)
fred.salary = 35000.0

# => Cut a new check for fred!
# => His salary is now 35000.0!
# => Send fred a new tax bill!
```

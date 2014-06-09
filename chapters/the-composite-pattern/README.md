The Composite Pattern
---------------------

1. The sum acts like one of the parts.
2. First, you need a common interface or base class for all your objects (the component).
3. Second, you need one or more leaf classes (the building blocks) the implement the component interface.
4. Third, you need at least one higher-level class (the composite). The composite is a component but it is built of subcomponents.

```ruby
class Task
  attr_reader :name, :parent
  
  def initialize(name)
    @name = name
    @parent = nil
  end
  
  def get_time_required
    0.0
  end
end

class CompositeTask < Task
  def initialize(name)
    super(name)
    @sub_tasks = []
  end

  def add_sub_task(task)
    @sub_tasks << task
    task.parent = self
  end

  def <<(task)
    add_sub_task(task)
  end

  def [](index)
    @sub_tasks[index]
  end

  def []=(index, new_value)
    @sub_tasks[index] = new_value
  end
  
  def remove_sub_task(task)
    @sub_tasks.delete(task)
    task.parent = nil
  end
  
  def get_time_required
    @sub_tasks.inject(0.0) { |time, task| time + task.get_time_required }
  end
end

class AddDryIngredientsTask < Task
  def initialize
    super('Add dry ingredients')
  end
  
  def get_time_required
    1.0
  end
end

class MixTask < Task
  def initialize
    super('Mix that batter up!')
  end
  
  def get_time_required
    3.0
  end
end

class MakeBatterTask < CompositeTask
  def initialize
    super('Make batter')
    add_sub_task(AddDryIngredientsTAsk.new)
    add_sub_task(AddLiquidsTask.new)
    add_sub_task(MixTask.new)
  end  
end

class MakeCakeTask < CompositeTask
  def initialize
    super('Make cake')
    add_sub_task(MakeBatterTask.new)
    add_sub_task(FillPanTask.new)
    add_sub_task(BakeTask.new)
    add_sub_task(FrostTask.new)
    add_sub_task(LickSpoonTask.new)
  end
end
```

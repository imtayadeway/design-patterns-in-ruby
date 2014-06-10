The Command Pattern
-------------------

1. Commands are objects that do nothing but wait to be executed and, when executed, go out and perform an applicaation-specific task.
2. Great for GUIs, or for queuing up commands (installers, database connections....).

```ruby
class SlickButton
  attr_accessor :command

  def initialize(command)
    @command = command
  end

  #
  # Lots of button drawing and management
  # code omitted...
  #

  def on_button_push
    @command.execute if @command
  end
end

class SaveCommand
  def execute
    #
    # Save the current document
    #
  end
end

save_button = SlickButton.new(SaveCommand.new)
```

### Code Blocks as Commands

```ruby
class SlickButton
  attr_accessor :command

  def initialize(&block)
    @command = block
  end

  #
  # Lots of button drawing and management
  # code omitted...
  #

  def on_button_push
    @command.call if @command
  end
end

new_button = SlickButton.new do
  #
  # Create a new document...
  #
end
```

### Commands that Record

```ruby
class Command
  attr_reader :description

  def initialize(description)
    @description = description
  end

  def execute
  end

  def unexecute
  end
end

class CreateFile < Command
  def initialize(path, contents)
    super("Create file: #{ path }")
    @path = path
    @contents = contents
  end

  def execute
    f = File.open(@path, 'w')
    f.write(@contents)
    f.close
  end

  def unexecute
    File.delete(@path)
  end
end

class DeleteFile < Command
  def initialize(path)
    super("Delete file: #{ path }")
    @path = path
  end

  def execute
    if File.exists?(@path)
      @contents = File.read(@path)
    end
    File.delete(@path)
  end

  def unexecute
    if @contents
      f = File.open(@path, 'w')
      f.write(@contents)
      f.close
    end
  end
end

class CopyFile < Command
  def initialize(source, target)
    super("Copy file: #{ source } to #{ target }")
    @source = source
    @target = target
  end

  def execute
    FileUtils.copy(@source, @target)
  end

  def unexecute
    # and so on....
  end
end

class CompositeCommand < Command
  def initialize
    @commands = []
  end

  def add_command(cmd)
    @commands << cmd
  end

  def execute
    @commands.each(&:execute)
  end

  def unexecute
    @commands.reverse.each { |cmd| cmd.unexecute }
  end

  def description
    @commands.inject('') { |str, cmd| str + cmd.description + "\n" }
  end
end

cmds = CompositeCommand.new

cmds.add_command(CreateFile.new('file1.txt', "hello world\n"))
cmds.add_command(CopyFile.new('file1.txt', 'file2.txt'))
cmds.add_command(DeleteFile.new('file1.txt'))

cmds.execute

puts cmds.description

# => Create file: file1.txt
# => Copy file: file1.txt to file2.txt
# => Delete file: file1.txt
```

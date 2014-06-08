The Strategy Pattern
--------------------

1. Pull the algorithm out into a separate object by defining a family of objects (the strategies) which all do the same thing. All of the objects support exactly the same interface.
2. The user of the strategy (the context) can treat the strategies like interchangeble parts.
3. The context can pass in arguments (low coupling), or if it is too complex can pass in itself as an argument (high coupling).

### Class-based Strategies:

```ruby
class HTMLFormatter
  def output_report(title, text)
    puts '<html>'
    puts '  <head>'
    puts "    <title>#{ title }</title>"
    puts '  </head>'
    puts '  <body>'
    text.each do |line|
      puts "    <p>#{ line }</p>"
    end
    puts '  </body>'
    puts '</html>'
  end
end

class PlainTextFormatter
  def output_report(title, text)
    puts "**** #{ title } ****"
    text.each do |line|
      puts line
    end
  end
end

class Report
  attr_reader :title, :text
  attr_accessor :formatter
  
  def initialize(formatter)
    @title = 'Monthly Report'
    @text = ['Things are going', 'really, really well']
    @formatter = formatter
  end
  
  def output_report
    formatter.output_report(title, text)
  end
end

report = Report.new(HTMLFormatter.new)
report.output_report

report.formatter = PlainTextFormatter.new
report.output_report
```

### Code blocks as strategies:

```ruby
class Report
  attr_reader :title, :text
  attr_accessor :formatter
  
  def initialize(&formatter)
    @title = 'Monthly Report'
    @text = ['Things are going', 'really, really well']
    @formatter = formatter
  end
  
  def output_report
    formatter.call(self)
  end
end

HTML_FORMATTER = lambda do |context|
  puts '<html>'
  puts '  <head>'
  puts "    <title>#{ context.title }</title>"
  puts '  </head>'
  puts '  <body>'
  context.text.each do |line|
    puts "    <p>#{ line }</p>"
  end
  puts '  </body>'
  puts '</html>'
end

report = Report.new(&HTML_FORMATTER)
report.output_report

report = Report.new do |context|
  puts "**** #{ context.title } ****"
  context.text.each do |line|
    puts line
  end
end

report.output_report
```

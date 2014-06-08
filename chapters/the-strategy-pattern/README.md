The Strategy Pattern
--------------------

1. Pull the algorithm out into a separate object by defining a family of objects (the strategies) which all do the same thing. All of the objects support exactly the same interface.
2. The user of the strategy (the context) can treat the strategies like interchangeble parts.

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

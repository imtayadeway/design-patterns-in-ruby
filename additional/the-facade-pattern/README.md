the facade pattern
------------------

1. Useful in Rails controllers to avoid instantiating more than one object.

```ruby
# app/facades/dashboard.rb
class Dashboard
  def initialize(user)
    @user = user
  end

  def new_status
    @new_status ||= Status.new
  end

  def statuses
    Status.for(user)
  end

  def notifications
    @notifications ||= user.notifications
  end

  private

  attr_reader :user
end
```
```ruby
# app/controllers/dashboards_controller.rb:
class DashboardsController < ApplicationController
  before_filter :authorize

  def show
    @dashboard = Dashboard.new(current_user)
  end
end
```
```erb
# app/views/dashboards/show.html.erb:
<%= render 'profile' %>
<%= render 'groups', groups: @dashboard.group %>

<%= render 'statuses/form', status: @dashboard.new_status %>
<%= render 'statuses', statuses: @dashboard.statuses %>
```

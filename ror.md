# RAILS

<https://guides.rubyonrails.org/>

## Rails Redirect

redirect_to(controller: 'main', action: 'index')

## Embedding Ruby

`<% code %>`  
`<%= code %>`

## Rails Links

`<%= link_to(text, target) %>`  
gdzie target można podać na dwa sposoby:  
`'main/index'`  
`{controller: 'main', action: 'index'}`

### Rails Links with Parameters

```ruby
<%= link_to('Link',
  {
    controller: 'main',
    action: 'index',
    id: 20,
    page: 5
  }
) %>
```

## Migrations

`rails generate migration MigrationName`  
`rails db:migrate`  
`rails db:migrate:status`  
`rails db:migrate VERSION=0`  
`rails db:migrate VERSION=20230306080918`  
`rake db:migrate`  
`rake db:rollback` - cofa ostatnią

## Models

`rails generate model Task name:string description:text position:integer completed:boolean`  
`rails console - e development`  
`reload!`

### Create records

```ruby
t=Task.new(name: 'Fold the laundry', position: 1)
t.description='Fold the laundry in the basket and put it in drawers'
t.completed=false
t.new_record?
t.save
t.new_record?
t2=Task.create(name: 'Sweep the porch', position: 2, completed: false)
t2.new_record?
```

### Update records

```ruby
task = Task.find(1)
task.name = 'Fold laundry'
task.save
task2 = Task.find(2)
task2.update(name: 'Sweep porch', description: 'Sweep dirt off the porch')
```

### Delete records

```ruby
task = Task.find(1)
task.destroy
```

### Find records

```ruby
tasks = Task.where(completed: false)
tasks = Task.where('completed = 0 AND position < 10')
tasks.count
tasks = Task.where(['name LIKE ?', '%porch%'])
tasks = Task.where('first_name = :name', name: params[:query])
task = tasks.first
task = Task.find_by(['name LIKE ?', '%porch%'])
tasks = Task.order(:position).limit(10).offset(10)
tasks = Task.order(:position => :desc)
tasks = Task.where(position: [2, 5])
tasks = Task.where(created_at: (Time.now.beginning_of_day..Time.now.end_of_day))
Task.group(:position).count
```

## CRUD: Create, Read, Update, Delete

### Standard CRUD Actions

|  CRUD  | Action  |         Description        |     Example URL    |
| ------ |:-------:| -------------------------- | ------------------ |
| Create |   new   | Display new record form    | /tasks/new         |
|        | create  | Process new record form    | /tasks/create      |
| Read   |  index  | List records               | /tasks             |
|        |  show   | Display a single record    | /tasks/show/:id    |
| Update |  edit   | Display edit record form   | /tasks/edit/:id    |
|        | update  | Process edit record form   | /tasks/update/:id  |
| Delete | delete  | Display delete record form | /tasks/delete/:id  |
|        | destroy | Process delete record form | /tasks/destroy/:id |

### REST: Resourceful Routes

| Group  | HTTP Verb |        URL        | Action  |           Description              |       URL Helper      |
| ------ | --------- | ----------------- | ------- | ---------------------------------- | --------------------- |
| Read   | GET       | /tasks            | index   | Show all items                     | tasks_path            |
|        | GET       | /tasks/:id        | show    | Show item with :id                 | task_path(:id)        |
| Create | GET       | /tasks/new        | new     | Show new form                      | new_task_path         |
|        | POST      | /tasks            | create  | Create an item                     | tasks_path            |
| Update | GET       | /tasks/:id/edit   | edit    | Show edit form                     | edit_task_path(:id)   |
|        | PATCH     | /tasks/:id        | update  | Update item with :id               | task_path(:id)        |
| Delete | GET       | /tasks/:id/delete | delete  | Show delete form for item with :id | delete_task_path(:id) |
|        | DELETE    | /tasks/:id        | destroy | Delete item with :id               | task_path(:id)        |

### Omit Resourceful Routes

```ruby
resources :users, except: [:show]
resources :products, only: [:index, :show]
```

### Add Resourceful Routes

```ruby
# config/routes.rb
resources :tasks do
  member do
    get :delete
  end
  
  collection do
    get :export
  end
end
```

## Rails helper: form_for

```erb
<%= form_for(@task) do |f| %>
    <%= f.text_field(:name) %>
    <%= f.text_field(:position) %>
    <%= f.check_box(:completed) %>
    <%= f.text_area(:description) %>
    <%= f.submit('Create Task') %>
<% end %>
```

## Cookies

```ruby
cookies[:username] = 'john smith'
cookies[:username] = {
 value: 'john smith',
 expires: 1.week.from_now
}
@username = cookies[:username]
```

## Session Data

```ruby
session[:language] = 'en'
@language = session[:language]
```

## Session Storage Strategies

- File storage (:file_store)
- Database storage (:active_record_store)
- Cache storage (:cache_storage)
- Cookie storage (:cookie_store)

### Session Cookie Storage

- Default in Rails from some time
- Stored in each user's browser
- Encrypted and digitally signed
- Fast; no lookup needed
- No file or database bloat
- 4 K maximum size

## Flash Hash

```ruby
flash[:notice] = 'Product created successfully.'
flash[:error] = 'Category could not be found.'
flash[:alert] = 'Not enough access privileges.'
```

```erb
<% if flash[:notice].present? %>
  <div id='flash-notice' style='border: 1px solid red; color: red; padding: 10px;'>
    <%= flash[:notice] %>
  </div>
<% end %>
<% if flash[:notice].present? %>
  <div class="alert alert-success">
  <%= flash[:notice]%>
  </div>
<% end %>
```

## Logging Information

`rails log:clear`

```ruby
logger.debug("The name is #{@subject.name}")
```

Logi:

- log/development.log
- log/production.log
- log/test.log

Log Level Configuration:

- config/environments/development.rb
- config/environments/production.rb
- config/environments/test.rb

`config.log_level = :debug`

### Log Levels

|                        | :debug | :info | :warn | :error | :fatal |
|----------------------- |:------:|:-----:|:-----:|:------:|:------:|
| Database queries       |    X   |       |       |        |        |
| Request/render details |    X   |   X   |       |        |        |
| Deprecations           |    X   |   X   |   X   |        |        |
| Standard errors        |    X   |   X   |   X   |   X    |        |
| Fatal errors           |    X   |   X   |   X   |   X    |   X    |

## Controller Filters

- before_action
- after_action
- around_action

specify which:

- only: [:method1, :method3]
- except: [:method2]

inherited filters can be skipped:

- skip_before_action
- skip_after_action
- skip_around_action

before_action :get_user_info_from_session

## Layouts

```ruby
class SomePublicController < ApplicationController
  layout 'public'
end
```

```ruby
class SomeAdminController < ApplicationController
  layout 'admin', except: [:access_log]
  layout 'report', only: [:access_log]

  def action
    render(layout: false)
  end
end
```

```erb
<div id="content" style="min-height: 400px;">
  <%= render(partial: 'layouts/flash') %>
  <%= yield %>
</div>
```

### Avoid double render errors

```ruby
if !logged_in?
  redirect_to(login_path) and return
end
render('menu')
```

### Render options

- ```ruby
  render('menu')
  ```

- ```ruby
  render(plain: 'Just render text.')
  ```

- ```ruby
  render(html: '<strong>HTML text</strong>')
  ```

- ```ruby
  render(json: Task.first)
  ```

- ```ruby
  render(xml: ['dog', 'cat', 'mouse'])
  ```

- ```ruby
  render(plain: 'OK', status: 500)
  ```

- ```ruby
  path_to_404 = Rails.root.join('public', '404.html')
  render(file: path_to_404)
  ```

- ```ruby
  send_file(path_to_404)
  ```

- ```ruby
  string = render_to_string(file: path_to_404)
  logger.debug(string)
  ```

## Precompile Assets for Production

- `export RAILS_ENV=production`
- `bundle exec rails assets:precompile`

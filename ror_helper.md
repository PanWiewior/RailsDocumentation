# RAILS HELPERS

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

## Image Helpers

```erb
<%= image_tag('task_manager_logo.png') %> 
<%= link_to(image_tag('task_manager_logo.png', height: 25, alt: 'Task Manager Logo'), menu_path) %>
<%= link_to(menu_path) do %>
  <%= image_tag('task_manager_logo.png', height: 25, alt: 'Task Manager Logo') %>
  by Wiewiór
<% end %>
```

- ```erb
  <div style="background-image: url('<%= image_path('gradient.png') %>');" >
  ```

- or uncomment `gem sassc-rails` in *Gemfile* and run `bundle install` and add:

  ```css
  header {
    background-image: image_url('gradient.png');
  }
  ```

- or rename `.css` to `.css.erb` and add:

  ```css
  header {
    background-image: url('<%= image_path('gradient.png') %>');
  }
  ```

## Text Helpers

- ```ruby
  word_wrap(text, line_width: 30, break_sequence: "\n")
  ```

- ```ruby
  simple_format(text)
  ```

- ```ruby
  truncate(text, {length: 28})
  ```

- ```ruby
  pluralize(n, 'product')
  ```

- ```ruby
  truncate_words
  ```

- ```ruby
  highlight
  ```

- ```ruby
  excerpt
  ```

## Sanitization Helpers

- ```ruby
  html_escape(), h()
  ```
  
  - default in Rails

- ```ruby
  raw(msg)
  ```

  - one time unescaped output

- ```ruby
  msg.html_safe
  ```

  - mark content as safe, never need to escape, but modifying string makes it unsafe again

- ```ruby
  strip_links(text)
  ```

  - remove link tags

- ```ruby
  strip_tags(text)
  ```

  - remove all HTML tags

- ```ruby
  escape_javascript(), j()
  ```

  - not default!

## Number Helpers

- ```ruby
  number_to_currency(34.5)
  number_to_currency(34.5, precision: 0, unit: 'PLN', format: '%n %u', separator: ',', delimiter: ' ')
  ```

- ```ruby
  number_to_percentage(34.5)
  number_to_percentage(34.5, precision: 1, separator: ',')
  ```

- ```ruby
  number_to_rounded(34.56789)
  number_to_rounded(34.56789, precision: 6)
  number_with_precision
  ```

- ```ruby
  number_to_delimited(3456789)
  number_to_delimited(3456789, delimiter: ' ')
  number_with_delimiter
  ```

- ```ruby
  number_to_human(123456789)
  number_to_human(123456789, precision: 5)
  ```

- ```ruby
  number_to_human_size(1234567)
  number_to_human_size(1234567, precision: 2)
  ```

- ```ruby
  number_to_phone(1234567890)
  number_to_phone(1234567890, area_code: true, delimiter: ' ', country_code: 48, extension: '321')
  ```

## Date and Time Helpers

- ```ruby
  Time.now
  ```

- ```ruby
  from_now
  30.days.from_now
  ```

- ```ruby
  ago
  2.months.ago
  ```

- ```ruby
  beginning_of_day
  beginning_of_week
  beginning_of_month
  beginning_of_year
  ```

- ```ruby
  end_of_day
  end_of_week
  end_of_month
  end_of_year
  ```

- ```ruby
  yesterday
  tomorrow
  ```

- ```ruby
  last_week
  last_month
  last_year
  ```

- ```ruby
  next_week
  next_month
  next_year
  ```

- ```ruby
  datetime.strftime(format_string)
  ```

  - Day
    - %a - abbreviated weekday name ("Sun")
    - %A - full weekday name ("Sunday")
  - Date
    - %d - day of the month (01..31)
  - Month
    - %b - abbreviated month name ("Jan")
    - %B - full month name ("January")
    - %m - month of the year (01..12)
  - Year
    - %y - year without a century (00..99)
    - %Y - year with century
  - Time
    - %H - hour of the day, 24-hour clock (00..23)
    - %I - hour of the day, 12-hour clock (01..12)
    - %M - minute of the hour (00..59)
    - %S - second of the minute (00..60)
    - %p - meridian indicator ("AM" or "PM")
    - %Z - time zone name

- ```ruby
  datetime.to_formatted_s(format_symbol)
  datetime.to_fs(format_symbol)
  ```

  ```ruby
  :db              "2022-01-09 13:36:29"
  :number          "20220109133629"
  :time            "13:36"
  :short           "09 Jan 13:36"
  :long            "January 09, 2022 13:36"
  :long_ordinal    "January 09th, 2022 13:36"
  :rfc822          "Sun, 09 Jan 2022 13:36:29 +0000"
  :iso8601         "2022-01-09T13:36:29+00:00"
  ```

## Form Helper Types

```ruby
form_tag(url, options) do
  # ...
  text_field_tag('name', params[:name])

  # Only necessary with select_tag
  opts = options_for_select(['text', 'HTML'], 'HTML') 
  select_tag(:content_type, opts)
end
```

```ruby
form_for(object, options) do |f|
  # ... use it if a form is providing information for a model
  text_field(:object, :name)
  f.text_field(:name)

  # Range
  f.select(:position, 1..5)
  # Array
  f.select(:content_type, ['text', 'HTML'])
  # Hash
  f.select(:visible, {'Visible' => 1, 'Hidden' => 2})
  # Array of arrays
  f.select(:category_id, Category.all.map {|p| [p.name, p.id|})
end
```

- text_field
- password_field
- text_area
- radio_button
- check_box
- select
- hidden_field
- date_field
- time_field
- datetime_field
- label
- submit
- and more

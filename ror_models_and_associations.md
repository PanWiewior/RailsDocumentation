# Models and associations

--------------------------------------------
Smart Model by Design
"Fat model, skinny controller"

## Select Data from a Query

```ruby
Task.all
Task.limit(5)
Task.first(5)
Task.last(5)
Task.count
Task.any?
Task.none?
Task.many?
Task.one?

Task.select(:id, :name).first
Task.pluck(:id, :name) - returns an array of the values, not objects
Task.ids
```

## Named Scopes

```ruby
class Product < ApplicationRecord
  scope :active, -> { where(active: true) }
  scope :search, lambda {|kw|
    keyword = (kw || '').downcase
    where ('LOWER(name) LIKE ?', "%#{keyword}%")
  }
  scope :with_style, -> (sid) { where(style_id: sid) }
  scope :created_before, -> (time) { where('created_at < ?', time) }
  scope :ordered, -> { order(created_at: :desc) }
end
```

```ruby
Product.active
# same as
Product.where(active: true)

Product.search('mug').with_style(34)
Product.created_before(1.day.ago)
```

## Validation Methods

- validates_presence_of - attribute must not be blank (nil, false, "", "  ", [], {})
- validates_length_of
- validates_numericality_of
- validates_inclusion_of
- validates_exclusion_of
- validates_format_of
- validates_uniqueness_of
- validates_acceptance_of
- validates_confirmation_of
- validates_associated

Or all in one:

```ruby
validates :attribute, presence: true,
            presence: { message: 'obiekt jest pusty' },
                      numericality: true, # :equal_to, :greater_than, :less_than, :greater_than_or_equal_to, :less_than_or_equal_to
            length: options_hash, # :is, :minimum, :maximum, :within
            format: { with: regex },
            inclusion: { in: array_or_range },
            exclusion: { in: array_or_range },
            acceptance: true, # :accept
            uniqueness: true, # :case_sensitive, :scope
            confirmation: true
```

--------------------------------------------
Custom Validation

```ruby
class MyModel < ApplicationRecord
  validate :custom_method

  private

  def custom_method
    if test_case
      errors.add(:attribute, 'message')
    end
  end
end
```

## Callbacks

|      Create       |       Update      |     Destroy    |
| ----------------- | ----------------- | -------------- |
|                   |  **Validation**   |                |
| before_validation | before_validation |                |
| after_validation  | after_validation  |                |
|                | **Database Interaction** |            |
| before_save       | before_save       |                |
| before_create     | before_update     | before_destroy |
| after_create      | after_update      | after_destroy  |
| after_save        | after_save        |                |
| after_commit      | after_commit      | after_commit   |

### Execute Callbacks conditionally

```ruby
class Task < ApplicationRecord

  attr_accessor :skip_titleize_name

  before_validation :titleize_name, unless: :skip_titleize_name
  before_validation :set_default_position,
    if: Proc.new {|t| t.position.blank? || t.position < 1 }
  after_commit :cleaning_reminder, if: :too_many_records?

  private

  def titleize_name
    self.name = name.titleize
  end

  def set_default_position
      max = Task.maximum(:position) || 0
      self.position = max + 1
  end

  def cleaning_reminder
    # This could be a placeholder for sending an email to an admin
    logger.debug('Remember to prune old tasks')
  end

  def too_many_records?
    Task.count > 4
  end
end
```

## Associations Methods

- has_one, belongs_to
- has_many, belongs_to
- has_and_belongs_to_many

```ruby
has_many :tasks, dependent: :destroy
has_many :tasks, dependent: :nullify
```

```ruby
category.tasks = [task1, task2]
category.tasks << task3
category.tasks.create name: 'Task name'
```

```ruby
new_task = category.tasks.build
```

### Has And Belongs To Many (HABTM) Associations

- requires a join table
- two foreign keys; index both keys together
- no model for join table, no primary key column, no timestamp columns
- there are a number of user interfaces that controller and viewers can use (ie. collection check boxes helper method)
- join table naming: table1 + _ table2 (alphabetical order and plural table names)
- `rails generate migration CreateJoinTableModel1Model2 table1 table2`

Example:  
`rails generate migration CreateJoinTableTagTask tags tasks`

```ruby
class Task < ApplicationRecord
  has_and_belongs_to_many :tags
end
```

```ruby
class Tag < ApplicationRecord
  has_and_belongs_to_many :tasks
end
```

```erb
<%= form_for(@task) do |f| %>
  <%= f.collection_check_boxes(:tag_ids, Tag.all, :id, :name) %>
<% end %>
```

## Rich Join Associations

- join table with two indexed foreign keys (like HABTM)
- requires a primary key column (:id)
- join table has its own model
- no table name conventions to follow
- tip: names ending in '-ments' or '-ships' work well
  (assignments, engagements, authorships, memberships)

Example:  
`rails generate model TaskAssignment task:references user:references role:string`

```ruby
class TaskAssignment < ApplicationRecord
  belongs_to :task
  belongs_to :user
end
```

```ruby
class Task < ApplicationRecord
  has_many :task_assignments
end
```

```ruby
class User < ApplicationRecord
  has_many :task_assignments
end
```

- has_many :through
  - allows reaching across a rich join
  - treats rich join like a HABTM join
  - create a functional rich join first

Example:

```ruby
class Task < ApplicationRecord
  has_many :task_assignments
  has_many :users, through: :task_assignments
end
```

```ruby
class User < ApplicationRecord
  has_many :task_assignments
  has_many :tasks, through: :task_assignments
end
```

## Join Tables During Queries

- includes - produces LEFT OUTER JOIN
- (only for includes) if use a string or an array for the condition (or for another clause like order), then must use references
- joins    - produces INNER JOIN

```ruby
Task.includes(:category).all
Task.includes(:category).where(completed: false, category: {id: 1..50})
Task.includes(:category).references(:categories).where("categories.name LIKE '%Week%'")
Task.joins(:category).all
Task.joins(:category).where(completed: false, category: {id: 1..50})
Task.includes(:category).where("categories.name LIKE '%Week%'")
```

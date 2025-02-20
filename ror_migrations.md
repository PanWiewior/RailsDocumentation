- [Migracje](#migracje)
  - [Przydatne linki](#przydatne-linki)
  - [Schema statements methods](#schema-statements-methods)
  - [Data types mapping](#data-types-mapping)
  - [Generowanie migracji](#generowanie-migracji)
    - [Adding New Columns](#adding-new-columns)
    - [Creating associations using references](#creating-associations-using-references)
  - [Models](#models)
    - [Has And Belongs To Many (HABTM) Associations](#has-and-belongs-to-many-habtm-associations)
    - [Rich Join Associations](#rich-join-associations)
  - [Przykład wywołań dla nowego projektu](#przykład-wywołań-dla-nowego-projektu)

# Migracje

## Przydatne linki

- [Rails API: Schema Statements](https://api.rubyonrails.org/v8.0.1/classes/ActiveRecord/ConnectionAdapters/SchemaStatements.html)
- [Active Record Connection Adapters Table Definition](https://api.rubyonrails.org/v8.0.1/classes/ActiveRecord/ConnectionAdapters/TableDefinition.html)
- [Rails Guides: Active Record Migrations](https://guides.rubyonrails.org/active_record_migrations.html)
- [Oracle guide z 2007](https://www.oracle.com/technical-resources/articles/kern-rails-migrations.html)
- [List of Rails data types for ActiveRecord migrations](https://www.jdeen.com/blog/list-of-rails-data-types-for-activerecord-migrations)
- [When you can’t add a uniqueness constraint because you already have duplicates](https://josh-works.medium.com/rails-migration-when-you-cant-add-a-uniqueness-constraint-because-you-already-have-duplicates-352a370e4b54)
- [PostgreSQL at Scale: Database Schema Changes Without Downtime](https://medium.com/paypal-tech/postgresql-at-scale-database-schema-changes-without-downtime-20d3749ed680)

## Schema statements methods

- ```ruby
  add_belongs_to(table_name, ref_name, **options)
  ```

  to samo co add_reference

- ```ruby
  add_check_constraint(table_name, expression, if_not_exists: false, **options)
  ```

  options hash keys: :name, :if_not_exists, :validate (PostgreSQL only)

- ```ruby
  add_column(table_name, column_name, type, **options)
  ```

  The type parameter is normally one of the migrations native types, which is one of the following: :primary_key, :string, :text, :integer, :bigint, :float, :decimal, :numeric, :datetime, :time, :date, :binary, :blob, :boolean.

  You may use a type not in this list as long as it is supported by your database (for example, “polygon” in MySQL), but this will not be database agnostic and should usually be avoided.

  Available options are (none of these exists by default):

  - :comment - Specifies the comment for the column. This option is ignored by some backends.

  - :collation - Specifies the collation for a :string or :text column. If not specified, the column will have the same collation as the table.

  - :default - The column’s default value. Use nil for NULL.

  - :limit - Requests a maximum column length. This is the number of characters for a :string column and number of bytes for :text, :binary, :blob, and :integer columns. This option is ignored by some backends.

  - :null - Allows or disallows NULL values in the column.

  - :precision - Specifies the precision for the :decimal, :numeric, :datetime, and :time columns.

  - :scale - Specifies the scale for the :decimal and :numeric columns.

  - :if_not_exists - Specifies if the column already exists to not try to re-add it. This will avoid duplicate column errors.

  Note: The precision is the total number of significant digits, and the scale is the number of digits that can be stored following the decimal point. For example, the number 123.45 has a precision of 5 and a scale of 2. A decimal with a precision of 5 and a scale of 2 can range from -999.99 to 999.99.

  Please be aware of different RDBMS implementations behavior with :decimal columns:

  - The SQL standard says the default scale should be 0, :scale <= :precision, and makes no comments about the requirements of :precision.

  - MySQL: :precision [1..65], :scale [0..30]. Default is (10,0).

  - PostgreSQL: :precision [1..infinity], :scale [0..infinity]. No default.

  - SQLite3: No restrictions on :precision and :scale, but the maximum supported :precision is 16. No default.

  - Oracle: :precision [1..38], :scale [-84..127]. Default is (38,0).

  - SqlServer: :precision [1..38], :scale [0..38]. Default (38,0).

- ```ruby
  add_foreign_key(from_table, to_table, **options)
  ```

  options: :column, :primary_key, :name, :on_delete, :on_update, :if_not_exists, :validate (PostgreSQL only), :deferrable (PostgreSQL only)

- ```ruby
  add_index(table_name, column_name, **options)
  ```

  `column_name` can be a single Symbol, or an Array of Symbols.

  options: :if_not_exists, :unique, :length, :order, :where (PostgreSQL and SQLite), :include (PostgreSQL), :nulls_not_distinct (PostgreSQL), :using (PostgreSQL and MySQL), :opclass (PostgreSQL), :type (MySQL), :algorithm(PostgreSQL)

- ```ruby
  add_reference(table_name, ref_name, **options)
  ```

  options: :type, :index, :foreign_key, :polymorphic, :null

- ```ruby
  add_timestamps(table_name, **options)
  ```

  Adds timestamps (created_at and updated_at) columns to table_name. Additional options (like :null) are forwarded to add_column.

- ```ruby
  assume_migrated_upto_version(version)
  ```

- ```ruby
  change_column(table_name, column_name, type, **options)
  ```

- ```ruby
  change_column_comment(table_name, column_name, comment_or_changes)
  ```

  Changes the comment for a column or removes it if nil.

  Passing a hash containing :from and :to will make this change reversible in migration:

  `change_column_comment(:posts, :state, from: "old_comment", to: "new_comment")`

- ```ruby
  change_column_default(table_name, column_name, default_or_changes)
  ```

  Setting the default to nil effectively drops the default.

  Passing a hash containing :from and :to will make this change reversible in migration.

  `change_column_default(:posts, :state, from: nil, to: "draft")`

- ```ruby
  change_column_null(table_name, column_name, null, default = nil)
  ```

  Sets or removes a NOT NULL constraint on a column. The null flag indicates whether the value can be NULL.

  The method accepts an optional fourth argument to replace existing NULLs with some other value. Use that one when enabling the constraint if needed, since otherwise those rows would not be valid.

  Please note the fourth argument does not set a column’s default.

- ```ruby
  change_table(table_name, base = self, **options)
  ```

- ```ruby
  change_table_comment(table_name, comment_or_changes)
  ```

  Changes the comment for a table or removes it if nil.

  Passing a hash containing :from and :to will make this change reversible in migration.

- ```ruby
  check_constraint_exists?(table_name, **options)
  ```

  Checks to see if a check constraint exists on a table for a given check constraint definition.

- ```ruby
  check_constraints(table_name)
  ```

  Returns an array of check constraints for the given table. The check constraints are represented as CheckConstraintDefinition objects.

- ```ruby
  column_exists?(table_name, column_name, type = nil, **options)
  ```

  Checks to see if a column exists in a given table.

- ```ruby
  columns(table_name)
  ```

  Returns an array of Column objects for the table specified by table_name.

- ```ruby
  create_join_table(table_1, table_2, column_options: {}, **options)
  ```

  Creates a new join table with the name created using the lexical order of the first two arguments. These arguments can be a String or a Symbol.

- ```ruby
  create_table(table_name, id: :primary_key, primary_key: nil, force: nil, **options, &block)
  ```

  Creates a new table with the name table_name. table_name may either be a String or a Symbol.

  options: :id, :primary_key, :options, :temporary, :force, :if_not_exists, :as

- ```ruby
  data_source_exists?(name)
  ```

  Checks to see if the data source name exists on the database.

- ```ruby
  data_sources()
  ```

  Returns the relation names usable to back Active Record models. For most adapters this means all tables and views.

- ```ruby
  drop_join_table(table_1, table_2, **options)
  ```

  Drops the join table specified by the given arguments. See create_join_table and drop_table for details.

- ```ruby
  drop_table(*table_names, **options)
  ```

  Drops a table or tables from the database.

- ```ruby
  foreign_key_exists?(from_table, to_table = nil, **options)
  ```

  Checks to see if a foreign key exists on a table for a given foreign key definition.

- ```ruby
  foreign_keys(table_name)
  ```

  Returns an array of foreign keys for the given table. The foreign keys are represented as ForeignKeyDefinition objects.

- ```ruby
  index_exists?(table_name, column_name, **options)
  ```

  Checks to see if an index exists on a table for a given index definition.

- ```ruby
  index_name_exists?(table_name, index_name)
  ```

  Verifies the existence of an index with a given name.

- ```ruby
  indexes(table_name)
  ```

  Returns an array of indexes for the given table.

- ```ruby
  max_index_name_size()
  ```

  Returns the maximum length of an index name in bytes.

- ```ruby
  native_database_types()
  ```

  Returns a hash of mappings from the abstract data types to the native database types. See TableDefinition#column for details on the recognized abstract data types.

- ```ruby
  options_include_default?(options)
  ```

- ```ruby
  primary_key(table_name)
  ```

  Returns just a table’s primary key

- ```ruby
  remove_belongs_to(table_name, ref_name, foreign_key: false, polymorphic: false, **options)
  ```

  Alias for: remove_reference

- ```ruby
  remove_check_constraint(table_name, expression = nil, if_exists: false, **options)
  ```

  Removes the given check constraint from the table. Removing a check constraint that does not exist will raise an error.

- ```ruby
  remove_column(table_name, column_name, type = nil, **options)
  ```

  Removes the column from the table definition.

- ```ruby
  remove_columns(table_name, *column_names, type: nil, **options)
  ```

  Removes the given columns from the table definition.
  
  type and other column options can be passed to make migration reversible.

- ```ruby
  remove_foreign_key(from_table, to_table = nil, **options)
  ```

  Removes the given foreign key from the table. Any option parameters provided will be used to re-add the foreign key in case of a migration rollback. It is recommended that you provide any options used when creating the foreign key so that the migration can be reverted properly.

- ```ruby
  remove_index(table_name, column_name = nil, **options)
  ```

  Removes the given index from the table.

- ```ruby
  remove_reference(table_name, ref_name, foreign_key: false, polymorphic: false, **options)
  ```

  Removes the reference(s). Also removes a type column if one exists.

  Also aliased as: remove_belongs_to

- ```ruby
  remove_timestamps(table_name, **options)
  ```

  Removes the timestamp columns (created_at and updated_at) from the table definition.

- ```ruby
  rename_column(table_name, column_name, new_column_name)
  ```

  Renames a column.

- ```ruby
  rename_index(table_name, old_name, new_name)
  ```

  Renames an index.

- ```ruby
  rename_table(table_name, new_name, **)
  ```

  Renames a table.

- ```ruby
  table_alias_for(table_name)
  ```

  Truncates a table alias according to the limits of the current adapter.

- ```ruby
  table_comment(table_name)
  ```

  Returns the table comment that’s stored in database metadata.

- ```ruby
  table_exists?(table_name)
  ```

  Checks to see if the table table_name exists on the database.

- ```ruby
  table_options(table_name)
  ```

- ```ruby
  tables()
  ```

  Returns an array of table names defined in the database.

- ```ruby
  use_foreign_keys?()
  ```

- ```ruby
  view_exists?(view_name)
  ```

  Checks to see if the view view_name exists on the database.

- ```ruby
  views()
  ```

  Returns an array of view names defined in the database.

## Data types mapping

Common Data Types ([source](https://www.jdeen.com/blog/list-of-rails-data-types-for-activerecord-migrations))

| Representation | PostgreSQL        | sqlite3      | MySQL         | Oracle        | SQLSerer     |
| -------------- | ----------------- | ------------ | ------------- | ------------- | -------------|
| :binary        | bytea             | blob         | blob          | blob          | image        |
| :boolean       | boolean           | boolean      | tinyint(1)    | number(1)     | bit          |
| :date          | date              | date         | date          | date          | date         |
| :datetime      | timestamp         | datetime     | datetime      | datetime      | datetime     |
| :decimal       | decimal           | decimal      | decimal       | decimal       | decimal      |
| :float         | float             | float        | float         | number        | float(8)     |
| :integer       | integer           | integer      | int(11)       | number(38)    | int          |
| :bigint        | bigint            |              |               |               |              |
| :string        | character varying | varchar(255) | var-char(255) | varchar2(255) | varchar(255) |
| :text          | text              | text         | text          | clob          | text         |
| :time          | datetime          | datetime     | time          | date          | time         |
| :timestamp     | timestamp         | datetime     | datetime      | date          | datetime     |


## Generowanie migracji

`rails generate migration MigrationName`

`rails db:migrate`

`rails db:migrate:status`

`rails db:migrate VERSION=0`

`rails db:migrate VERSION=20230306080918`

```rake db:migrate```

```rake db:rollback```

### Adding New Columns

   migration name "AddColumnToTable" or "RemoveColumnFromTable" and is followed by a list of column names and types (and index)

```rails generate migration AddPartNumberToProducts part_number:string```

```rails generate migration AddPartNumberToProducts part_number:string:index price:decimal```

```rails generate migration RemovePartNumberFromProducts part_number:string```

### Creating associations using references

rails generate migration AddUserRefToProducts user:references

## Models

```rails generate model Task name:string description:text position:integer completed:boolean```

```rails destroy model post```

### Has And Belongs To Many (HABTM) Associations

```rails generate migration CreateJoinTableModel1Model2 table1 table2```

```rails generate migration CreateJoinTableTagTask tags tasks```

### Rich Join Associations

```rails generate model TaskAssignment task:references user:references role:string```

## Przykład wywołań dla nowego projektu

```rails new Blog -j esbuild --css bootstrap```

```rails generate model User name:string{30}```

```rails generate scaffold Project title:string{60} description:text{2000} started_at:datetime user:references:index completed:boolean```

```rails generate scaffold DbChange type:string{60} object_name:string{60} object_alias:string{4} project:references:index object_module:string{10}```

```rails generate scaffold DbChangePart type:string{60} column_name:string{60} column_notnull:boolean db_change:references:index column_unique:boolean position:integer column_descr:string{250} column_ref_table_name:string{60} column_domain:string{60} column_name_new:string{60} column_data_type:string{10} column_data_length:integer column_data_precision:integer column_data_scale:integer```

```rails db:create```

```rails db:migrate```

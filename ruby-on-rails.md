# Ruby on Rails

## Table of Contents

[Create a new rails application](#create-a-new-rails-application)\
[Install Gems](#install-gems)\
[Run Rails Server](#run-rails-server)\
[Route Structure](#route-structure)\
[ERB Syntax](#erb-syntax)\
[How embedded ruby works with html](#how-embedded-ruby-works-with-html)\
[Rails Links](#rails-links)\
[Params](#params)\
[Databases](#databases)\
[Glossary](#glossary)

## Create a new rails application
The database default used is sqlite3.
Other databases can be chosen by adding the -d flag and naming the database to be used.
Some of the databases that can be used are mysql, postgresql, oracle, frontbase, ibm_db, sqlserver etc

```ruby
rails new application_name sqlite3
```

## Install Gems
If other gems are to be used other than the defaults ruby on rails has added the gems must be added to the Gemfile and then bundle install must be run in the command line within the app directory.
```ruby
bundle install
```

## Run Rails Server
To run the rails server
```ruby
rails server
```
or
```ruby
rails s
```

## Route Structure
__Simple Match Route__

If you only used match routes then every single page in your app would have to have its own route string.\
<br>
Long version
```ruby
match "demo/index", :to => "demo#index", :via => :get
```
Short version
```ruby
get "demo/index"
```

__Default Route__

If the action and id are not entered in the url at the domain request the defaults will be used.

For example, if the address required is demo/index and the user enters demo as the address, the route will default to index
```ruby
:controller/:action/:id
or
:controller(/:action(/:id))
```
Default routing may not be continued in the future.

__Root Route__

The home page of the application, where the browser goes when there is no url.

```ruby
root 'demo#index'
```

# Controller

The controller chooses how to handle the request from the browser.
The controller should contain a method for each action view in the controller.
The action doesn't need to be defined but it is best practice rather than letting rails sensible defaults taking over.
We can ensure the actions we require by adding the render instruction inside the method. We can pass any argument to the render instruction and it will render that view.
```ruby
def index
    render 'index'
end

# if we pass in another argument within the index method that argument will be rendered instead

def index
    render 'about'
end

# in the above case, when the browser requests index the route will point to index but the controller will return about.
```

# Redirects

Redirects like other controller actions can be set to any other controller and view.
You can even direct to an outside url like https://google.com if you wanted to.
```ruby
def other_page
    redirect_to(:action => 'index')
end
```

[Top of Page](#ruby-on-rails)

## ERB Syntax
To embed ERB
```erb
<% processes and executes code does not output code %>
<%= with the equals sign processes, executes and does output code %>
```

## How embedded ruby works with html
The following code in Ruby would ouput 012
In the browser the code will return 3 because the '=' will output what follows it. The erb syntax is not correct. Note that in the server terminal the output is correct 012.
```erb
<%=
3.times do |n|
    puts n
end
%>
```
The correct erb syntax will return 012 in the browser.
The first line will process and execute but not output as required as we haven't included the '='.
We want the second line to be output so we include the '='.
Then process but not output the end. 
```erb
<% 3.times do |n| %>
    <%= n %>
<% end %>
```

## Rails Links
Both links, html and erb shown below work in the browser.
```erb
<%= link_to(text, target) %>
or
<a href='/demo/hello'>Hello page 1</a>
or
<%= link_to('Hello page 2', {:action => 'hello'}) %>
or
<%= link_to('Hello page 2', 'hello') %>
```

#Instance Variables
Remember each controller is a class, rails creates an instance of that class everytime it is called and we can have instance variable inside them.
The only way to transfer data from the controller to the view is through instance variables.

So the instance variable set in the controller:
```ruby
def hello
    @array = [1,2,3,4,5]
    render 'hello'
end
```

Can be called and used in the view
```ruby
<% @array.each do |n| %>
    <%= n %><br>
<% end %>
```

[Top of Page](#ruby-on-rails)

## Params
Params can be referenced by either symbol or string.
We can add parameters as shown below which will appear in the url.
```erb
<%= link_to('Hello with params', {:action => 'hello', :page => 5, :id => 20}) %>
```
The url that is returned from the link above:
http://localhost:3000/demo/hello?id=20&page=5

Notice how the id 20 is placed after the question mark.
If we change the route in the route file to include the id:

Using route command
```ruby
get ':controller/:action/:id'
```
returned url:
http://localhost:3000/demo/hello/20?page=5

Notice now the id 20 is included as part of the url, before the question mark.\
<br>

__Using the params__

We now have access to these paramaters by turning them into instance variables.
Params can be accessed in either the controller or the view but generally they are used in the controller.
Note: Params are always strings.
```ruby
def hello
    @array = [1, 2, 3, 4, 5]
    @id = params['id']
    @page = params[:page]
    render('hello')
end
```
Params values can be used in our views or more likely to retrieve data from the database. 
Following is an example of how params can be used to display page numbers.

```erb
Page: <%= @page %><br>
Next page: <%= @page.to_i + 1 %><br>
```
[Top of Page](#ruby-on-rails)

## Databases 
__(Note: the following is specific to mysql as used in the Lynda tutorial)__

Database names are typically all lower case and typed in snake_case.

Basic database commands
```ruby
SHOW DATABASES;
CREATE DATABASE db_name;
USE db_name;
DROP DATABASE db_name;
```

To add a column to a database
```ruby
rails g migration AddTitleToProduct title
rails db:migrate
```

Database access usually setup when creating the database but can also add access later.
```ruby
GRANT ALL PRIVILEGES ON db_name.*
TO 'username'@'localhost
IDENTIFIED BY 'password';
```

To see current permissions
```ruby
SHOW GRANTS FOR 'username'@'localhost';
```

Configure rails access to the database via the database.yml file found in the config folder.

Migrations are used to update and change database tables.

To generate a new migration file.
```ruby
rails generate migration FileNameInCamelCase
```

The change method within the database class is used to update the database. The change method is short for the up method and the down method. When running the change method rails defaults to the up and uses rails magic to change down.

To generate a new model file.
Note the model name must be singular.
Rails will create the necessary files.
Note that rails will name the table file as a plural of the singular name we gave it.
This is because the table will contain many rows of the model.
When a model is generated rails will also generate the migration file.
```ruby
rails generate model ModelNameSingular
```

The change (or up) method is used to create the columns of the table.
We specify the column type, column name and include any other required options.
An examples of the up method and the long and short syntax for column creation:
```ruby
def up
    create_table :users do |t|
        t.column "first_name", :string
        # shorthand version of the above
        t.string "last_name"
        t.timestamps
    end
end
```

Table column options available are:
```ruby
:limit => size
:default => value
:null => true/false
for decimal
:precision => number
:scale => number
```

Two other common and helpful options below will be auto completed and populated by rails.
The shorthand is automatically included by rails.
```ruby
t.datetime "created_at"
t.datetime "updated_at"
# the shorthand version automatically added by rails
t.timestamps
```

Rails will automatically add id's to the table.
To turn off that function we add :id => false to the create_table loop:
```ruby
def up
    create_table :users, :id => false do |t|
        t.column "first_name", :string
        # shorthand version of the above
        t.string "last_name"
        t.timestamps
    end
end
```

When ever a foreign key is included or a column is used for looking up records an index should be added.
```ruby
def up
    create_table :pages do |t|
      t.integer "subject_id" # this is a foreign key from the subject_id table
      t.string "name"
      t.integer "permalink"

      t.timestamps
    end
    add_index("pages", "subject_id")
    add_index("pages", "permalink")
end
```


Use rails db:migrate to migrate the database which will run all migrations which have not yet been run.
```ruby
rails db:migrate
```

The tables can be viewed through the database used via terminal eg: mysql, sqlite3 etc.

To migrate back down to the initial database
```ruby
rails db:migrate VERSION=0
```

We can also migrate back down a specific amount of steps.

To see current status of the database
```ruby
rails db:migrate:status
```
Example of the terminal output from the above db status request
```
Status   Migration ID    Migration Name
--------------------------------------------------
  down    20180403103125  Do nothing yet
  down    20180403103817  Create users
```

From the status info we can then migrate to that specific version
```
rails db:migrate VERSION=20180403103125
```

When checking status again we can see that the first migration is now up.
```
Status   Migration ID    Migration Name
--------------------------------------------------
   up     20180403103125  Do nothing yet
  down    20180403103817  Create users
```

Other migration commands:
```ruby
rails db:migrate
rails db:migrate VERSION=0
rails db:migrate VERSION=20180101120045
rails db:migrate:status
rails db:migrate:up VERSION=20180101120045
rails db:migrate:down VERSION=20180101120045
rails db:migrate:redo VERSION=20180101120045
```

Table Migration Methods
```ruby
create_table(table, options) do |t|
drop_table(table)
rename_table(table, new_name)
```

Column Migration Methods
```ruby
add_column(table, column, type, options)
remove_column(table, column)
rename_column(table, column, new_name)
change_column(table, column, type, options)
```

Index Migration Methods
```ruby
add_index(table, column, options)
remove_index(table, column)
```

Index Migration Method Options
```ruby
:unique => true/false
:name => "your_custom_name" # not required unless a specific name is needed
```

If there have been migration errors and the database is stuck between an up and down migration the easiest way out is with the following steps:
- comment out the problem code in the migration file
- run the migration
- comment the code back in
- migrate back down to VERSION=0
- migrate back up again

__ActiveRecord__
In rails ActiveRecord allows the retrieval and manipulation of data as objects, not as static rows.
ActiveRecord objects are intelligent, they understand the structure of the table, contain data from table rows, can crud and can be manipulated as objects.
For example the following code = an SQL INSERT
```ruby
user = User.new
user.first_name = "Tom"
user.save
```
The following code = an SQL UPDATE
```ruby
user.last_name = "Jones"
user.save
```
And the following = an SQL DELETE
```ruby
user.delete
```

__ActiveRelation__
Simplifies the generation of complex database queries.
An example rails query
```ruby
users = User.where(:first_name => "Tom")
users = users.order("last_name ASC").limit(5)
```
And the SQL equivalent (not exact)
```ruby
SELECT users.* FROM users
WHERE users.first_name = 'KEVIN'
ORDER BY users.last_name ASC
LIMIT 5
```

__Rails Console__

The rails console is accessed in the terminal using one of the following commands (all do the same thing).
The rails console is basically irb with the rails environment.
Be aware that any changes made in the rails console will occur in your database unless running the rails console in sandbox mode which will discard any changes on exit.
```
rails console development
rails console
rails c
# To open the rails console in sandbox mode
rails c --sandbox
```

__Creating Records in the rails console__\
Long Way
```ruby
subject = Subject.new
subject.name = "First Subject"
subject.save
```
Short Way
```ruby
subject = Subject.new(:name => "First Subject", :position => 1, :visible => true)
subject.save
```

Create Technique
Even shorter than the above methods.
Saves immediately
```ruby
subject = Subject.create(:name => 'Second Subject', :position => 2)
```

__Updating Records__\
Long Way
```ruby
subject = subject.find(1)
subject.name = "Initial Subject"
subject.save
```

Short Way
```ruby
subject = subject.find(2)
subject.update_attributes(:name => 'Next Subject', :visible => true)
```

__Deleting Records__\
```ruby
subject = subject.find(2)
subject.destroy
```

__Find Records__\
Primary key finder
```ruby
Class.find(primaryKeyId)
Subject.find(2)
# Returns an object, raises an error if not there.
```

Dynamic finders. Find records by different attributes.
```ruby
Subject.find_by_id(record-id)
# Returns an object or nil if not there.
Subject.find_by_name("record_name")
```

Find all, first or last
```ruby
Subject.all
Subject.first
Subject.last
```

__Query Methods: Conditions__\
where(conditions)
The condition expression types can be strings, arrays or hashes.
There are several ways to do this but the safest way, to prevent hacking (SQL injection) use the following method, using a hash.
Simple values can be used or ranges or arrays.
```ruby
Subject.where(:name => "test", :visible => true)
```

These conditon queries can be chained.
```ruby
User.where(:last_name => 'Smith').where(:first_name => 'John')
```

__Query Methods: Order, Limit and Offset__\
These query methods are all chainable.
```ruby
order(string)
order(:position => :asc) # order is ascending
order(:position => :desc) # order is descending
```

```ruby
limit(integer)
# returns an array with the number of records specified in the limit argument
```

```ruby
offset(integer)
# skips the records as specified in the offset argument
```

__Named Scopes__
Are database queries defined in the model with ruby code.
They are defined using ActiveRelation query methods.
Requires lambda syntax.
In the named scope example below, when the scope active is called it will peform the query method where active is true.
Note the lambda can also be entered as -> which is the symbol for lambda, either will work.
```ruby
scope :active, lambda {where(:active => true)}
# to call the scope on a class
Class.active
```

Creating and calling a scope is the same thing as creating and calling a method on the class, however rails convention is when working with query methods, use scopes rather than methods.
```ruby
def self.active
    where(:active => true)
end
# to call the method on the class
Class.active
```

Scopes will also take arguments, again this scope could also be written as a method but following is the correct scope syntax.
```ruby
scope :with_content_type, lambda {|ctype| where(:content_type => ctype)}
# to call the scope
Class.with_content_type('html')
```

The reason we use lambda is because when we use lambda it's going to be evaluated when it is called, not at the time when it is defined.
Once the scopes are written, scopes can be chain called as shown in the following example:
```ruby
Class.recent.visible.newest_first
```




# Glossary

__Database__

Database = a set of tables

Table = a set of columns and rows that represents a concept such as products, customers or orders

Column = a set of data types such as first_name, last_name etc stored in specific data types such as strings, integers etc

Rows = is a single record of data

Field = intersection of a row and column, similar to a cell in a spreadsheet

Index = data structure to increase the look up speed of the data in the table

Foreign keys = a table column whose values reference rows in another table

Schema = the structural definition of a databse

CRUD = Create, Read, Update, Delete

MVC => Model View Controller

ORM => Object Relational Mapping

Rails Architecture<br>
![Rails Architecture](images/rails-architecture.png)

__How the rails file structure relates__
(in my own words)

db/migrate/dateTimeStampMigrationFile.rb - contains the methods to create a blank table with the required columns and the methods to delete the table columns or table




[Top of Page](#ruby-on-rails)
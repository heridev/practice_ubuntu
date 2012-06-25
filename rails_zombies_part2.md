The part two for course rails for zombies part 2

create a rails app

$ rails new "myaplication"
after create a aplication go into the directory

if write $ rails you will see the diferents commands in rails
that you can use

rails generate --- create
rails console ---start rail console
rails serever-- execute the server and open the browser

exaxmple scafoold

rails g scaffold zombie name:string bio:text age:integer

diferents variable types
string, text, integer,boolean, decimal,
float,binary,date,time,datetime

Migrations
one migration look like
class CreateZombies < ActiveRecord::Migrate

end

to run all misgin migrations

rake db:migrate

Debug our app and insert update and consult
rails console 

examples:
> Zombie.create(name:"jose", age: 35)
> z = Zombie.first
> z.name = "nuevo nombre"
> z.save

###Adding columns at database at table we need use the sintaxis
###AddToTableName columname:typevarible

rails g migration AddEmailToZombies email:string 

And after run migration with
rake db:migrate

if something go wrong then we can go before that migration:
rake db:rollback

Dump the current state Database
rake db:schema:dump

When you begin to work with an existing aplication
rake db:setup

Exists diferent comman to migration for example
rename_column, rename_table etcetera you can search more info about
"guides.rubyonrails.org" on topic migrations

----------------------------------------------

###Write the migration manually which creates the tweets table in the
##database with the status string column and zombie_id integer column

class CreateTweets < ActiveRecord::Migration
  def up
    create_table :tweets do |t|
      t.string :status
      t.integer :zombie_id      
      t.timestamp
    end
  end
  def down  
    drop_table :tweets    
  end
end
---------------------------------------------
##Create migration by hand that adds two fields to the tweets table: a
##location string field which has a limit of 30 and a boolean field called
##show_location which defaults to false. 

class AddLocationToTweets < ActiveRecord::Migration
  def change
    change_table :tweets do |t|
      t.boolean :show_location, :default => false
      t.string :location, :limit => 30
    end
  end 
  --tambien funciona esta es lo mismo pero con diferente sintaxis 
  def change
    add_column :tweets, :location, :string, limit: 30
    add_column :tweets, :show_location, :boolean, default: false
  end 
end

--------------------------------------------------

##Now that we've rolled back, add a category_name string field and use
##the rename command to rename the status column to message instead. 

class AddLocationToTweets < ActiveRecord::Migration
  def change
    add_column :tweets, :location, :string, limit: 30
    add_column :tweets, :show_location, :boolean, default: false
    add_column :tweets, :category_name, :string
    rename_column :tweets, :status, :message    
  end 
end

-------------------------------------------

#Seguimos con el segundo nivel en rails zombie part2

###Good code and bad code
example 

class RottinZombiesController < AplicationController

def index
@rottin_zombies = Zombie.where(rotting:true)
end

end

then de good code is change the funcionality to the model.rb

class Zombie < ActiveRecord::Base
scope :rotting, where(rotting:true)
scope :fresh. where("age < 20")
scope :recent, order("created_at desc").limit()
end

##Example 2

in controller update

def update
if @zombie.age > 20
@zombie.rotting = true
end
end

we change to model

before_save :make_rotting

def make_rotting
  if age > 20
    self.rotting = true
  end
end

-------------------------------------------------
#CALLBACKS

###be carefull when we use the callback and the function no return
##values

we can use diferents and multiple callbacks
before_sabe, before_destroy, after_create you can search more about
callbacks in guide rails

---------------------------------------------------
##relationships

add relaction in file migration

def change
  create_table :brains do |t|
  blablablabla
  end
  add_index :brains. :zombie_id

end

Relashionship in model

class Brain < ActiveRecord::Base
  belongs_to :zombie
end

class Zombie < ActiveRecord::Base
  has_one :brain, :dependent => :destroy
end

check the documentation for more

---------------------------------------
##query using a relationship
###bad code 

def index
@zombies = Zombie.all

view index.html.erb
@zombies.each do |zombie|
  zombie.name
  zombie.bran.flavor
end

that generate too many query from de next

###Good code
def index
  @zombies = Zombie.includes(:brain).all

in view
is the same that the before code
----------------------------------------
 add more relationship with the next tables: zombie, role,
assignment

zombien has many assigment and role has many assigment

now in the migration file of assignment

add_index :assignments, :zombie_id
add_index :assignments, :role_id

in the modelsi
assignment
belongs_to :zombie
belongs_to :role

find order and limit conditions

Model.find(:all, :limit => 5, :order=> 'created_at desc')

-------------------------------------------------------------------
##scope conditions
named_scope :published, :conditions => { :published => true }

------------------------------------------------

#Excercises in Rails for zombies part 2 level 3

###Write a scope on the Tweet model called recent which returns the 4 most recent tweets. Hint: You'll need an order AND a limit scope.

class Tweet < ActiveRecord::Base
  scope :recent, :order => "created_at desc", :limit => 4
or
scope :recent, order('created_at desc').limit(4)
end

----------------------------------------

###Write another scope called graveyard which only shows tweets where the show_location column is true and the location is "graveyard"

scope :graveyard , where(:show_location => true, :location =>"graveyard")
or
scope :graveyard, where(show_location: true, location: "graveyard")


----------------------------------------------------------------------
In this controller action create an instance variable called @graveyard_tweets which uses both of the two scopes recent and graveyard together.

class Tweet < ActiveRecord::Base
  scope :recent, order('created_at desc').limit(4)
  scope :graveyard, where(show_location: true, location: "graveyard")
end

In controller we use the both scope 
class TweetsController < ApplicationController
  def index
    @tweets = Tweet.all
    @graveyard_tweets = Tweet.recent.graveyard
  end
end

--------------------------------------------------------------------------
Create a before_save callback that checks to see if a tweet has a location, if it does have a location then set show_location to true.Tip: You can check to see if location exists with if self.location?

class Tweet < ActiveRecord::Base
  
  before_save :comprobe_location
  
  def comprobe_location        
    if self.location?
      self.show_location = true
    end
    
  end 
  
end

--------------------------------------------------------------------------
Relationship

create_table "locations" do |t|
    t.integer "name"
    t.integer "tweeter_id" # BRAINS!!!
  end
      
  create_table "tweets" do |t|
    t.string "message"
    t.boolean "show_location", :default => false
    t.integer "zombie_id"
        
    t.timestamps
  end

in the models i declare the next source code

class Tweet < ActiveRecord::Base
  has_one :location, :dependent => :destroy, :foreign_key =>
"tweeter_id"  
end

class Location < ActiveRecord::Base
  belongs_to :tweet , :foreign_key => "tweeter_id"
end

----------------------------------------------------------

A Tweet can belong to one or more Categories (e.g. eating flesh, walking
dead, searching for brains). Write a migration that creates two tables,
categories, and categorizations. Give categories one column named name
of type string; and give categorizations two integer columns: tweet_id
and category_id.

 create_table "categories" do |t|
    t.string "name" 
  end
      
  create_table "categorizations" do |t| 
    t.integer "tweet_id"
    t.integer "category_id"
  end


--------------------------------------------------------------------
Now that we have our new tables, it's time to define the relationships
between each of the models. Define the has_many through relationships in
the Tweet & Category model and the belongs_to relationships in the
Categorization model.

database migration
ActiveRecord::Schema.define(:version => 20110814152905) do

  create_table "categories" do |t|
    t.string "name" 
  end
      
  create_table "categorizations" do |t| 
    t.integer "tweet_id"
    t.integer "category_id"
  end
      
  create_table "tweets" do |t|
    t.string "message"
    t.string "location", :limit => 30
    t.boolean "show_location", :default => false
    t.integer "zombie_id"
        
    t.timestamps
  end

end


code in the model to define relationships

class Tweet < ActiveRecord::Base
  has_many :categorizations
  has_many :categories, :through => :categorizations
  
end

class Categorization < ActiveRecord::Base
  belongs_to :tweet
  belongs_to :category
  
end

class Category < ActiveRecord::Base
  has_many :categorizations
  has_many :tweets, :through => :categorizations
end



-------------------------------------------------------------------

fin








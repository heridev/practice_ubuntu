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

#REST representation estate transfer

##Resful links in rails 2

zombie = Zombie.find(1)
link_to 'show', zombie

link_to 'create', zombie, method: :post
link_to 'update, zombie, method: :put
link_to 'delete', zombie, metod: :delete
---------------------------------------------------------
##ROUTES BE RAKIN

###Please type the rake command you use to list out all the routes on the
###command line

rake routes

with this command we create all the routes in the file and later we can
modify that

----------------------------------------------------------------------
creating and using forms

<%= form_for(@zombie) do |f| %>
  <%= f.text_field :name %>
  <%= f.submit %>
<% end %>

when this form is submitted request params will be
:params => {:zombie => {:name=> " jose heriberto perez"}}

labels 
<%= f.label :name %>
f.text_area :bio

f.check_box :rotting

f.radio_button :decomp, 'value1', checked:true
f.radio_button :decomp, 'value2'

f.select :decomp, ['value 1','value 2', 'value 3']

f.select :decomp, [['fresh',1],['rotting',2],['value 2',3]]

alternate text input helpers

f.password_field :password
f.number_field :price
f.range_field :quantity
f.mail_field :email
f.url_field :website
f.telephone_field :mobile
-------------------------------------------------------------------

###USING FORMS
##Create the form for entering tweet status (text_area) and location
##(text_field) using the appropriate Rails view helpers. All you need is a
##form_for block, the input helpers, and a submit button.

<h1>New tweet</h1>
<%= form_for @tweet do |f| %>
  <%= f.label :status, 'Status' %>:
  <%= f.text_area :status %><br />
  <%= f.label :location, 'Location' %>:
  <%= f.text_field :location %><br />
  <%= f.submit %>
<% end %>
------------------------------------------------------------------

##Using inputs
###Look at the following database table and create the proper input fields
###for the columns listed here.


---------------------------------------------------------------------

using radiobutton

Rather than having a weapon that is broken or not, lets instead have a
condition field which is either "New", "Rusty", or "Broken". Add a set
of radio buttons where the user can select one of these states. Make
"New" be checked by default.

<%= form_for(@weapon) do |f| %>
<%= f.label :name , "Name" %>
<%= f.text_field :name %>
<%= f.label :ammo , "Ammo" %>
<%= f.number_field :ammo %>
<%= f.label :condition , "Condition" %>
<%= f.radio_button :condition, 'New', checked:true %>
<%= f.radio_button :condition, 'Rusty' %>
<%= f.radio_button :condition, 'Broken' %>
<% end %>
-----------------------------------------------------------------

the example anterior of radiobutton using select is the next
 code

<%= form_for(@weapon) do |f| %>
  <%=  f.select :condition,
[['New',"New"],['Rusty','Rusty'],['Broken','Broken']]
%>
<% end %>

--------------------------------------------------------------
##NESTED ROUTES

resources :zombies do
  resources :tweets
end

Write the nested route that will allow us to nest tweets and weapons
under the zombie resource. The idea here is that a zombie has many
tweets and zombie has many weapons.

RailsForZombies::Application.routes.draw do
  resources :zombies do
    resources :tweets
    resources :weapons
  end
end

later we can use the tweets and weapons with the zombies exaxmple
if we wan to search a tweet o weapon we can write
/zombies/4/tweets/2 el tweet number 2
/zombies/4/tweets/ search all the tweets for zombies

-----------------------------------------------------------------

NESTED PARAMS

we we call nested params use the next example
@zombie = Zombie.find(params[:zombie_id])

Now that we have the proper route, we need to make sure the weapons
controller properly looks up both the zombie and the weapon when we
request /zombies/2/weapons/1. Finish this controller:

# Here is the parameters hash sent into the controller
params = { zombie_id: 2, id: 1 }

class WeaponsController < ApplicationController  
  def show 
    @zombie =  Zombie.find(params[:zombie_id])
    @weapon =  @zombie.weapons.find(params[:id])
  end
end

-----------------------------------------------------------------
When we use nested rake routes(rutas anidadas) for relationship when we
write code in console like:

$ rake routes

then we will obtain some routes like the next examples:
zombies_tweets GET /zombies/:zombie_id/tweets

               POST /zombies/:zombie_id/tweets
new_zombie_tweet GET /zombies/:zombie_id/tweets
edit_zombie_tweet GET /zombies/:zombie_id/tweets/:id/edit
zombie_tweet GET /zombies/:zombie_id/tweets/:id
             DELETE /zombies/:zombie_id/tweets/:id

then if we want use that routes to link_to in the view we need write:

link_to "#{@zombie.name} 's Tweets", zombie_tweets_path(@zombie)
link_to 'New Tweet', new_zombie_tweet_path(@zombie)
link_to 'Edit', edit_zombie_tweet_path(@zombie,tweet)
link_to 'Destroy', [@zombie, tweet], method: :delete

and in the controller if we want to use that

def create
  @tweet = @zombie.tweets.new(params[:tweeet])
  
  respond_to do |format|
    if @tweet.save
      format.html{redirect_to [@zombie, @tweet],
                  notice: "Tweet was succesfully created"}
      ane format json looks like similar
end


Exercise in rail for zombies for link_to
Now create the proper link_to for when we view a zombie and want to list
out all their weapons, and when we want to create a new weapon for this
zombie.

<h2><%= @zombie.name %>'s weapons</h2>
<ul>
  <% @weapons.each do |w| %>
    <li><%= link_to w.name, [@zombie, w] %></li>
  <% end %>
</ul>

<%= link_to "New Weapon", new_zombie_weapon_path(@zombie)%>

-------------------------------------------------------------------------

NESTED FORM (Formularios anidados)
when we use forms anidados and they have a relationship we need to
declare with de next type:

<%= form_for([@zombie, @tweet])do |f| %>

in exercise in rails for zombies:
Change the form_for below to use the proper nesting for creating a new
weapon for a Zombie.

<%= form_for([@zombie, @weapon]) do |f| %>
  <%= f.text_field :name %>

  <%= f.submit %>
<% end %>

------------------------------------------------------------------------

PARTIAL FORMS

the partial form begin with underscore example
app/views/tweets/_form.html.erb

<%= form_for([@zombie, @tweet]) do |f| %>

and we can reuse that partial form using the next code

app/views/tweets/new.html.erb

<h1>New Tweet</h1>
<%= render 'form' %>

-------------------------------------------------------------------------

Aditional view helpers

<div id = "tweet_<%= tweet.id %>" class="tweet"
  <%= tweet.body %>
</div>

this is the same

<%= div_for tweet do %>
  <%= tweet.body %>
<% end %>

<%= truncate ("i need brain: ", :length => 10, :separator => '') %>
i seee <%= pluralize(Zombie.count, "Zombie") %> pone zombie or zombies
His name was <%= @zombie.name.titleize %> camelize

to use for convert and add commas and en example: uno, dos and tres
and <%= @roles_name.to_sentence %>

the excercise in the rails for zombies:
Modify the following code to make it more pretty, using titleize,
to_sentence, pluralize, and number_to_currency (in just that order)

<h2><%= @zombie.name.titleize %></h2>
<p>Weapons: <%= @zombie.weapon_list.to_sentence %></p>
<p><%= pluralize(@zombie.tweets.size , "Tweet") %></p>
<p>Money in Pocket <%= number_to_currency @zombie.money %></p>


this code display the next view
Gregg Pollack
Weapons: Torn off Leg, Chainsaw, and Ping Pong Paddle
2 Tweets
Money in Pocket $26.66

------------------------------------------------------------------

PARTIAL COLLECTION

use the next partial collection

Refactor the code below to use the _weapon.html.erb partial to render
the list of weapons.

the code original is all in the same file

<h1><%= @zombie.name %>'s Weapons</h1>
<% @weapons.each do |weapon| %> 
  <%= div_for weapon do %>
    <h2><%= weapon.name %></h2> 
    <p>
      Condition: <%= weapon.condition %>
      Ammo: <%= weapon.ammo %>
      Purchased <%= time_ago_in_words weapon.purchased_on %> ago
    </p> 
  <% end %>
<% end %>


and the solution is the next and the two files 

weapons/index.html.erb

<h1><%= @zombie.name %>'s Weapons</h1>
<% @weapons.each do |weapon| %> 
<% #render 'weapon'%>
<%= render :partial => "weapon", :locals => { :weapon => weapon } %>
<% end %>

weapons/_weapon.html.erb

<%= div_for (weapon) do %>
    <h2><%= weapon.name %></h2> 
    <p>
      Condition: <%= weapon.condition %>
      Ammo: <%= weapon.ammo %>
      Purchased <%= time_ago_in_words weapon.purchased_on %> ago
    </p> 
  <% end %>



-----------------------------------------------------------------

#LEVEL 4

###Creating and sending mails
create mailer
$rails g mailer ZombieMailer decomp_change lost_brain

this create diferent files that

create app/mailers/zombie_mailer.rb
invoke erb
create app/views/zombie_mailer
create app/views/zombie_mailer/decomp_change.text.erb
create app/views/zombie_mailer/lost_brain.text.erb

and the zombie_mailer.rb has the next codde

Class ZombieMailer < ActionMailer::Base
  
  default from:  "from@example.com"
  def decomp_change
    @gretting = "Hi" --la creamos para enviar a las vistas info
  end
end

to send a mail to zombie

we can use to for params  in the first part of this class:
from: my@gmail.com
cc: my@gmail.com
bcc: my@gmail.com
reply_to: my@gmail.com

def decomp_change(zombie)
  @zombie = zombie
  @last_tweet = @zombie.tweets.last
  attachments['z.pdf'] = File.read("#{Rails.root}/public/zombie.pdf")
  mail to: @zombie.email, subject: "your decomp stage has changed"
end

in the views if we want use html or text use the file or create it

app/views/zombie_mailer/decomp_change.text.erb
or
app/views/zombie_mailer/decomp_change.html.erb

<h1>Greetings <%= @zombie.name %></h1>
<p>You>r descomposition state is now <%= @zombie.decomp %></p
<p>And your last tweet was: <%= @last_tweet.body %></p>

to send that mail we declare for example in the model of zombie

class Zombie > ActiveRecord::Base
  after_save :decomp_change_notification, if : :decomp_changed?

  def decomp_change_notification
    ZombieMailer.decomp_change(self).deliver
  end
end

we can use diferent email services for use de mailing list or other for
example mad mimi or mailchimp to use it for example madmimi

in Gemfile add
gem 'madmimi'
then 
$ bundle install to update the gemfile as the new gem added

then we can use it for example

mimi = MadMimi.new('<email>','<api_key>')
mimi.add_to_list('gregg@envylabs.com','newletter')
mimi.remove_from_list('gregg@envylabs.com','newsletter')

----------------------------------------------------------------------
##the asset pipeline

--------------------------------------------------------------------
Excercise in rails for zombies
generate mailer
Enter the command for generating a mailer called WeaponMailer which has
the emails low_ammo and broken.

$ rails g mailer WeaponMailer low_ammo broken

Simple mailer
Code up the low_ammo mailer with the subject of "#{weapon.name} has low
ammo", the email should be sent to the zombie.email. Lastly, set the
default from address for all emails in WeaponMailer to admin@rfz.com

class WeaponMailer < ActionMailer::Base
  default :from => 'admin@rfz.com'
  def low_ammo(weapon, zombie)
    @zombie = zombie
    @weapon = weapon
    mail(:to => @zombie.email, :subject => "#{@weapon.name} has low
ammo")
end
end

-------------------------------------------------------------------------
##MAIL DELIVERY

Finish coding the check_ammo method on the Weapon model so when we have
exactly three ammo left, it will send out the low_ammo mailer we just
created. 

class Weapon < ActiveRecord::Base
  belongs_to :zombie
  before_save :check_ammo

  def check_ammo  
    if self.ammo == 3
      WeaponMailer.low_ammo(self,self.zombie).deliver
    end
  end

end

------------------------------------------------------------------------
Next exercise in rails for zombies
##ATTACHING A FILE

Change the low_ammo method to include a picture of the weapon that's low
on ammo as an attachment. You can name the file weapon.jpg and load the
file using weapon.picture_file. 

explication to solution

wen can observe what weapon.picture_file is the source where the image
is located and we use that image to send attached in the email

class WeaponMailer < ActionMailer::Base
  default from: "admin@rfz.com"

  def low_ammo(weapon, zombie)
    attachments['weapon.jpg'] = weapon.picture_file
    mail to: zombie.email, subject: "#{weapon.name} has low ammo"    
  end 
end

---------------------------------------------------------------------
ASSET TAG HELPER

<%= javascript_include_tag "custom"%>
<%= stylesheet_link_tag "style" %>
<%= image_tag "rails.png" %>

-----------------------------------------------------
##EXERCISE RAIL FOR ZOMBIES 
###Convert the following to their appropriate asset tags. 

<img src="/assets/weapon.png" />
<script src="/assets/weapon.js" />
<link href="/assets/weapon.css" media="screen" rel="stylesheet"
type="text/css" />

<%= javascript_include_tag "weapon" %>
<%= stylesheet_link_tag "weapon" %>
<%= image_tag "weapon.png" %>

----------------------------------------------------

in stylesheet routes we can use

app/assets/stylesheets/zombie.css

form.new_zombie input.submit{
  background-image: url(<% asset_path('button.png') %>)
}

this generate the next code:
assets/button-afaafasdfasdf.png

-----------------------------------------------------
###Excersise in the rails course

###Convert the following scss.erb file to properly reference the
###asset_path for the image listed in it. Also, try refactoring the
###scss to use nesting. 

h2#newUser a {
  height: 64px;
  width: 50px;
  display: block;
  background: url(/assets/rails.png) no-repeat;
}

the solution

h2#newUser {
  text-indent: -9999px; 
}

h2#newUser a {
  height: 64px;
  width: 50px;
  display: block;
  background: url(<%= asset_path('rails.png') %>) no-repeat;
}



SCSS - SASSY  css 
Extension para css3 and to use nested rules, variables,and more

example:
form.new_zombie{ 
  border: 1px dashaed gray;
}
form.new_zombie .field#bio{
  display: none;
}

lets do some nesting

form.new_zombie
{
  border: 1 px dashed gray;
  .field#bio{
    display:none;
  }  
}


##Cofeescript is really nice javascript compile

##in zpombie form we need to add some more field wen the user clic en bio
##field

in app/aasets/javascript/zombies.js.cofee

in jquery looks like
$(document).ready(function()){
  $('#show-bio').click(function(event)){
    event.preventDefault();
    $($this).hide();
    $('.field#bio').show();
  }
}

in cofeescript

$(document).ready ->
  $('#show-bio').click(event) ->
    event.preventDefault()
    $(this).hide()
    $('.field#bio').show()

---------------------------------------------------------
###EXERCISE IN RAILS FOR ZOMBIES COFFESCRIPTIN

Use CoffeeScript so when the New Weapon link is pressed it makes the newWeapon div visible and then hides the New Weapon link. Don't forget to call preventDefault(). 

we have the next view/index.html.erb

<a href='#' id='displayWeaponForm'>New Weapon</a>

<div id="newWeapon" style="display:none;">
  <%= form_for [@zombie, Weapon.new] do |f| %>
    <%= f.label :name %><br />
    <%= f.text_area :name %>
    <%= f.submit %>
  <% end %>
</div>

and the coffeescript script that give the function is the next code:

$(document).ready ->
  $("#displayWeaponForm").click (event) ->
    event.preventDefault()
    $('#newWeapon').show()
    $('#displayWeaponForm').hide()

----------------------------------------------------------

###SCAFFOLD CREATING A COFFEESCRIPT FILES

when we use the scaffold we generate diferents files and the js.coffe
too are created, then to use that js for example:

app/assets/javascripts/zombies.js.coffee
app/assets/javascripts/tweets.js.coffee
app/assets/javascripts/brains.js.coffee

and then if we wat add to the next type

<%= javascript_include_tag "zombies", "tweets", "brains"


if we use the app/assets/javascripts/application.js

we can declare in that file for use all our js
example:

//= require jquery
//= require jquery_ujs
//= require_tree .

to precompile all files into /public/assets/(on your server)

$rake assets:precompile
by default it ninifies all code

if we want to use other files in applicattion.js

example lib/assets/javascripts/shared.js.coffee

//= require shared

in the same type ###ASSETS STYLESHEETS

app/assets/stylesheets/application.css

/*
 *= require_self
 *= require_tree .
*/

in rails guide we can fin more information about ASSET PIPELINE
--------------------------------------------------

###LEVEL 5 in rails for zombies by envylab

RENDERING EXTREMITIES

class ZombiesController << ApplicationController 
  def show
    @zombie = Zombie.find(:params[:id])
    respond_to do |format|
      format.html 
      format.json {render json: @zombie}
    end
  end
end

in the def show function we invoke the file
app/views/zombies/show.html.erb by defaul by what if we need to use
another view render in that function?????

in that case if the zombie is dead we need to use other view

class ZombiesController << ApplicationController
  def show
    @zombie = Zombie.find(:params[:id])
    respond_to do |format|
      if @zombie.decomp == "Dead(again)"
        render :dead_again
      end
      format.json {render json: @zombie}
    end
  end
end

if we only need one option like json we can remove respond_to like the
next code:

  def show
    @zombie = Zombie.find(:params[:id])
    format.json {render json: @zombie}    
  end

http status codes

200: ok
201 :created
422 :unprocesable_entity
401 :unauthorized
102 :processing
404 :not found

examples for json
render json: @zombie.errors, status: :unprocesable_entity
render json: @zombie, status: :created, location: @zombie

CUSTOM CHALLENGE ROUTE

add a new member route
http://localhot:3000/zombies/4/decomp

config routes.rb
match 'zombies/:id/decomp' => 'Zombies#decomp', :as => :'decomp_zombie'

resources :zombies do
  resources :tweets
  get :decomp, on: :member
end

$rake routes
then that generate

decomp_zombie GET /zombies/:id/decomp {:action => "decomp", :controller
=> "zombies"}

we can use that in the view with the next code
<%= link_to "Get Descomposition Status" , decomp_zombie_path(@zombie) %>

types of custom routes
:member    acts on a single resource
:collection  acts on a collection resources

routes   |       URL    |       routes helper
get :decomp , on: :member | /zombies/:id/decomp |
decomp_zombie_path(@zombie)
put :decay, on: :member | /zombies/:id/decay | decay_zombie_path(@zombie)
get :fresh, on: :collection | /zombies/fresh | fresh_zombies_path
post :search, on: :collection | /zombies/search | search_zombies_path

examples in views
<&= link_to 'Fresh Zombies',  fresh_zombies_path %>
<%= form_tag(seach_zombies_path) do |f| %>

in the controller

class ZombiesController < ApplicationController
  def decomp
    @zombie = Zombie.find(:params[:id])
    if @zombie.decomp == "Dead (Again)"
      render json: @zombie, status: :unprocesable_entity
    else
      render json: @zombie, status:ok
    end
  end  
end
end

in the before example we return all the data from that zombie and we
only need de decomp variable then to configure the json is the next

using one field
@zombie.to_json(only: :name)

using 2 or more field
@zombie.to_json(only: [:name, :decomp])

using except
@zombie.to_json(except: [:created_at, :id, :email])

other example using relationship ademas de incluir sus datos incluye en
un array llamado brain todos los campos y su respectivo dato
@zombie.to_json(include: :brain, except: [:created_at, :updated_at, :id])

later that analized the params json we can write the code of the
controller better and optimized

class ZombiesController < ApplicationController
  def decomp
    @zombie = Zombie.find(:params[:id])
    if @zombie.decomp == "Dead (Again)"
      render json: @zombie.to_json(only: :decomp),
             status: :unprocesable_entity
    else
      render json: @zombie, status:ok
    end
  end
end
end

-------------------------------------------------------------------------------
### The part Fun AJAX LINKS

add ajax link delete

There are 3 steps to make an ajax link call
1. make the link a remote call
2. Allow the controller to accept javascript call
3. use the view and script configuration

now lets go to program the funcionality

app/views/zombies/index.html.erb

<div id="zombies" >
<% @zombies.each do |zombie| %>
  <%= render zombie %>
<%= end %>
</div>

<%= form_for(Zombie.new, remote:true) do |f| %>
  <div class= "field">
    <%= f.label :name %>
    <%= f.text_field :name %>
  </div>
  <div class = "actions">
    <%= f.submit %>
  </div>
<%= end %>

in the file app/views/zombies/_zombie.html.erb

<%= div_for zombie do %
    <%= link_to "Zombie #{zombie.name}"m zombie %>
    <div class="actions">
      <%= link_to 'edit', edit_zombie_path(zombie) %>
      <%= link_to 'delete', zombie, method: :delete, remote: true %>
   </div>
<%= end %>


create the javascript

app/views/create.js.erb

<% if @zombie.new_record? %>
  $('input#zombie_name').effect('highlight',{ color: 'red'});
<% else %>
 $('div#zombies').append("<%= escape_javascript(render @zombie) %>");
  $('div#<%= dom_id(@zombie) %>').effect('highlight');
<% end %>

dont forget add the js
app/assets/javascripts/application.js

//= require jquery_ui

and the controller

class ZombiesController < ApplicationController
  def destroy
    @zombie = Zombie.find(:params[:id])
    @zombie.destroy    
    respond_to do |format|
      format.js
    end
  end  
end
end


Write the javascript to send back to the cliente
app/views/zombies/destroy.js.erb

$('#<%= dom_id(@zombie) %>').fadeOut();

Now the part2 the form create an ajax call too

--------------------------------------------------------
Add ajaxfield custom decomp form to assign  a custom descomp to
one zombie in his detail

create new custom route for our action
config/routes.rb

resources :zombies do
  resources :tweets
  get :decomp, on: :member
  put :custom_decomp, on: :member
end

the form
/views/zombies/show.html.erb

<strong>Descomposition Phase: </strong>
<span id= "descomp"%><%= @zombie.decomp %></span>

<div id= "custom_phase">
<% form_for @zombie, url: custom_decomp_zombie_path(@zombie),
remote: true do |f| %>

<strong>Custom Phase </strong>
  <%= f.text_field :decomp %>
  <%= f.submit "Set" %>
<% end %>
</div>

in the controller zombies_controller.rb

def custom_decomp
  @zombie = Zombie.find(params[:id])
  @zombie.decomp = params[:zombie][:decomp]
  @zombie.save
end

and the javascript
app/views/zombies/advance_decomp.js.erb

$('#decomp').text('<%= @zombie.decomp
%>').effect('highlight',{},3000);
<% if @zombie.decomp == "Dead (again)" %>
  $('div#custom_phase').fadeOut();
<% end %>

Now we will do the same using JSON


app/views/zombies/show.html.erb

<div id="custom_phase2">
  <%= form_for @zombie, url: custom_decomp_zombie_path(@zombie) do |f| %>
  <strong>Custom phase via JSON</strong>
  <%= f.text_field :decomp %>
  <%= f.submit "Set" %>
<% end %>
</div>

in the controller
app/controllers/zombies_controller.rb

def custom_decomp
  @zombie = Zombie.find(params[:id])
  @zombie.decomp = params[:zombie][:decomp]
  @zombie.save
  
  respond_to do |format|
    format.js
    format.json {render json: @zombie.to_json(only: :decomp)}
  end
end

in the app/asset/javascripts/zombies.js.coffee

$(document).ready ->
 $('div#custom_phase2 form').submit (event) ->
  event.priventDefault()

  url = $(this).attr('action')
  custom_decomp = $('div#custom_phase2 #zombie_decomp').val()
  
  $.ajax
    type: 'put'
    url: url
    data: { zombie: {decomp: custom_decomp}}
    dataType: 'json'
    success: (json) ->
      $('#decomp').text(json.decomp).effect('highlight')
      $('div#custom_phase2').fadeOut() if json.decomp == "Dead (again)"

--------------------------------------------------------------------------
###EXERCISE IN RAILS INTERNET

###RENDER

Complete the method below so that if the ammo is low it will render the
fire_and_reload view, otherwise it should render the fire_weapon view.

class WeaponsController < ApplicationController
  def fire_weapon
    @weapon = Weapon.find(params[:id]) 
    @weapon.fire!
    if @weapon.low_ammo?
      render :fire_and_reload
    else
      render :fire_weapon
    end
  end
end
------------------------------------------------------------------------------

CUSTOME RESOURCE ROUTES

Create two custom member routes on the weapons resource, so you have a
put method called toggle_condition and a put method called reload.

The solution is add the two method and add the do in the resources
weapon because we use two method instead the resources

RailsForZombies::Application.routes.draw do
  resources :zombies do
    resources :weapons do
      put 'toggle_condition', :on => :member
      put :reload, on: :member
    end      
  end
end

------------------------------------------------------------------------------
###RENDER JSON
Complete the create method below. When @weapon.save is successful it
should render the @weapon object in JSON, have status :created, and set
the location to the @weapon's show url. When @weapon.save fails it
should return the @weapon.errors and have the status
:unprocessable_entity.

The solution is

class WeaponsController < ApplicationController 
  def create
    @weapon = Weapon.new(params[:weapon]) 
    if @weapon.save
      render json: @weapon, status: :created, location: @weapon
    else
      render json: @weapon.errors, :status => :unprocessable_entity
    end
  end 
end

------------------------------------------------------------------------------
###RENDER JSON WITH OPTIONS

Complete the controller so that it returns in JSON only the amount of
ammo which is left in the weapon. If the ammo has less than 30 bullets
it should return the status code :ok, and if not it should return the
status code :unprocessable_entity.

the solution is

class WeaponsController < ApplicationController
  def reload
    @weapon = Weapon.find(params[:id]) 
    if @weapon.ammo < 30
      @weapon.reload(params[:ammo_to_reload])
      render :json => @weapon.to_json(only: :ammo), :status => :ok
    else
      render :json => @weapon.to_json(only: :ammo), status:
:unprocessable_entity
    end
  end
end
-------------------------------------------------------------------------------
###MORE JSON OPTIONS

Modify the show action so that the JSON it renders includes the zombie
record the @weapon belongs to. Also make it exclude the :id,
:created_at, and :updated_at fields.

The solution is
class WeaponsController < ApplicationController
  def show
    @weapon = Weapon.find(params[:id])
    render :json => @weapon.to_json(include: :zombie, except:
[:created_at, :updated_at, :id])
  end
end
------------------------------------------------------------------------------

###AS JSON

###Change the Zombie class to return different json by editing the as_json
###method. Return only the zombie's name, along with the weapons it has
###(using include), only display the weapon's name and ammo.

####in this solution we must search about "as_json" is a method in the model
####and the next is the solution how to use that method in the class and how
####to include another model to use and except and only show

class Zombie < ActiveRecord::Base
  has_many :weapons

  def as_json(options=nil)
    super(:only => :name, :include =>[:weapons], only: [:name, :ammo])
  end 
end

-----------------------------------------------------------------------------
####LINK REMOTE AND FORM AJAX

Modify the show.html.erb view below so that both the Toggle link and the
Reload form use AJAX. All you need to do is add the option that makes
them ajaxified.

<ul>
  <li>
    <em>Name:</em> <%= @weapon.name %>
  </li> 
  <li>
    <em>Condition:</em>
    <span id="condition"><%= @weapon.condition %></span>
    <%= link_to "Toggle", toggle_condition_weapon_path(@weapon) ,
remote: true %>
  </li> 
  <li>
    <em>Ammo:</em>
    <span id="ammo"><%= @weapon.ammo %></span>
  </li>
</ul>

<%= form_for @weapon, url: reload_weapon_path(@weapon), :remote => true
do |f| %>
  <div class="field">
    Number of bullets to reload:
    <%= number_field_tag :ammo_to_reload, 30 %> <br /> <%= f.submit
"Reload" %>
  </div>
<% end %>
--------------------------------------------------------------------------

###AJAX RESPONSE

the problem is the next
Modify the toggle_condition action so that it responds to JavaScript,
and complete the toggle_condition.js.erb using jQuery to update the
condition span with the @weapon's changed condition and make it
highlight.

solution

file app/controllers/weapons_controller.rb

class WeaponsController < ApplicationController
  def toggle_condition
    @weapon = Weapon.find(params[:id]) 
    @weapon.toggle_condition 

    respond_to do |format|
      format.html { redirect_to @weapon, notice: 'Changed condition' }
      format.js
    end
  end
end

the file show.html.erb

<p id="notice"><%= notice %></p>
<ul>
  <li>
    <em>Name:</em>
    <%= @weapon.name %>
  </li>
  <li>
    <em>Condition:</em>
    <span id="condition"><%= @weapon.condition %></span> 
    <%= link_to "Toggle", toggle_condition_user_weapon_path(@user,
@weapon), remote: true %>
  </li>
</ul>

file 
app/views/weapons/toggle_condition.js.erb

$('#condition').text("<%= @weapon.condition %>").effect("highlight")

the explicaction is that we only to identify or to execute code
javascript we need to tell the controller that "format.js" and in the
view file we can write code to add to the div or id html element with
"text", "append" or "html" in that example we used ".text" and 

------------------------------------------------------------------------------

###AJAX RESPONSE 2
the next exercise do the next sentence

Now write the controller and JavaScript code needed to properly reload
the weapon using the ajaxified form. In the reload.js.erb use jQuery to
update the #ammo text to the current @weapon.ammo value and if the ammo
value is over or equal to 30, fadeOut the #reload_form div.

with the next files

weapons_controller.rb

class WeaponsController < ApplicationController
  def reload
    @weapon = Weapon.find(params[:id])
    respond_to do |format|
      if @weapon.ammo < 30
        @weapon.reload(params[:ammo_to_reload])      
        format.json { render json: @weapon.to_json(only: :ammo), status:
:ok }
        format.html { redirect_to @weapon, notice: 'Weapon ammo
reloaded' }
        format.js
      else
        format.json { render json: @weapon.to_json(only: :ammo), status:
:unprocessable_entity }
        format.html { redirect_to @weapon, notice: 'Weapon not reloaded'
}
        format.js
      end
    end
  end
end

the file show.html.erb

<p id="notice"><%= notice %></p>
<ul>
  <li>
    <em>Name:</em>
    <%= @weapon.name %>
  </li>
  <li>
    <em>Condition:</em>
    <span id="condition"><%= @weapon.condition %></span> 
    <%= link_to "Toggle", toggle_condition_user_weapon_path(@user,
@weapon), remote: true %>
  </li>
  <li>
    <em>Ammo:</em>
    <span id="ammo"><%= @weapon.ammo %></span>
  </li>
</ul>

<div id="reload_form">
<%= form_for [@user, @weapon], url: reload_user_weapon_path(@user,
@weapon) do |f| %>
  <div class="field">
    Number of bullets to reload:
    <%= number_field_tag :ammo_to_reload, 30 %> <br />
    <%= f.submit "Reload" %>
  </div>
<% end %>
</div>

<%= link_to 'Edit', edit_weapon_path(@weapon) %> |
<%= link_to 'Back', weapons_path %>


the file reload.js.erb

$('#ammo').text('<%= @weapon.ammo %>');
<% if @weapon.ammo >= 30 %>
  $('#reload_form').fadeOut();
<% end %>
-----------------------------------------------------------------------------

final exercise in coffeescript

the exercise do the next sentence

Instead of returning jQuery which gets executed on the client-side, lets
write the ajax request in CoffeeScript communicating with JSON. It
should do the same thing as the last challenge, updating & highlighting
the ammo, and fading out the form if ammo is equal or above 30. 
Tip for your ajax form: data: {ammo_to_reload: ammo}.

in the next solution only use the coffeescript script to do the sama
like the before script

weapon.js.coffee

$(document).ready ->
  $('div#reload_form form').submit (event) ->
    event.preventDefault()
    url = $(this).attr('action')
    ammo = $('#ammo_to_reload').val()
    
    $.ajax
      type: 'put'
      url: url
      data: {ammo_to_reload: ammo}
      dataType: 'json'
      success: (json) ->
        $('#ammo').text(json.ammo).effect('highlight')
        $('#reload_form').fadeOut() if ammo <= 30

----------------------------------------------------------------------------





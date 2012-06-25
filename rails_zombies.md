Some codes in the level 1 what i remember

v = Zombie.find(3)
v.destroy

v = Zombie.find(3)
v.name = "jose heriberto"
v.save

Zombie.limit(10)

Zombie.all

Zombie.create(:name => "jose heriberto", :vayard => "algun menesaje")

i go in the level 2
http://railsforzombies.org/levels/2

insert the validate in the model example
class Tweet < ActiveRecord::Base

  validates_presence_of :status
 validates :status, :presence => true

end

For enlazar tables use the same logic like cakephp belongs_to : zombie
and in the table "model_id"


validate bod in model presence and unique the name
validates :name, :uniqueness => true, :presence => true

create a relashionship where zombies should have one weapon
class Weapon < ActiveRecord::Base
   belongs_to :zombie
end
class Zombie < ActiveRecord::Base
   has_one :weapon
end

using realtions chips good find all the weapon's ash
Weapon.find(:all, :conditions => { :zombie_id => 1 })

quantity of weapon's ash
Weapon.find(:count, :conditions => { :zombie_id => 1 })

Using views

<p>posted by <%= tweet.zombie.name %></p>

link_to tweet.zombie.name, zombie_path(zombie.name)

link_to @tweet.zombie.name, @tweet.zombie, :confirm => "are you sure"

Tweet.all.each do |tweet|
 tweet.status
end

or equals to
tweets = Tweet.all
tweets.each do |tweet|
  tweet.name
end

if tweets.empty?
 puts no found tweets
end

if zombie.tweets.count < =  2 then
end


##Edit and delete links
link_to tweet.zombie.name, tweet.zombie
link_to "edit", edit_tweet_path(tweet)

link_to "link text", <code>

<p>
link_to zombie.name, zombie , :style =>"color:#fff;"
</p>
exercises
truncate to display only 10 caracters
  truncate(zombie.graveyard, :length => 10)


#Imprime SMART ZOMBIE si tiene mas de dos tweets
<% zombies = Zombie.all %>

<ul>
  <% zombies.each do |zombie| %>
    <li>
      <%= zombie.name %>
      <% if zombie.tweets.count >= 2 then %>
      SMART ZOMBIE
      <% end%>
    </li>
  <% end %>
</ul>

###link to edit zombie
<% zombies = Zombie.all %>

<ul>
  <% zombies.each do |zombie| %>
    <li>
      <%= link_to zombie.name, { :controller => 'zombies', :action =>
'edit', :id => zombie.id } %>
    </li>
  <% end %>
</ul>


###Using controllers
into def we can call a diferent view

example 
def show
 @tweet =  Tweet.find(params[:id])
 render :action => 'status'
end

status.html.erb
<%= @tweet.name %>

use xml or json or html

inside de controller

def show
 @tweet = Tweet.fin(params[:id])
 respond_to do |format|
  format.html
  format.xml {render :xml =>@tweet}
  format.json {render :json =>@tweet}
 end
end

add some autorizacion for the person has tweet
class tweet
 before_filter :get_tweet, only => [:edit, update, :destroy]
 before_filter :check_auth, onlye => 
 [:edit, update, :destroy]
def check_auth
 if session[:zombie_id]!= @tweet.zombie_id
  flash[:notice] = "sorry, you can't edit this tweet"
  redirect_to tweets_path
 end 
end


def get_tweet
 @tweet =  Tweet.find(params[:id])
end

def destroy
end

def edit
end

def update
end

end


##In the layout a notice for show message to user
<% if flash[:notice] %>
 <div id = "notice"><%= flash[:notice] %></div>
<% end %>
--------------------------------------------------------------------
##Add a before filter which calls a method to check if a zombie has no
##tweets. Redirect to zombies_path if they dont have tweets. Only on show.

class ZombiesController < ApplicationController
  before_filter :find_zombie
  
  def show
    render :action => :show
  end

  def find_zombie
    @zombie = Zombie.find params[:id]
    @tweets =  Tweet.find(:all, :conditions => { :zombie_id =>
@zombie.id })
    if(@tweets.empty?)
      redirect_to zombies_path
    end
  end
end

------------------------------------------------------
##Routing in rails

llamdo resfull resources
we need define routes in config/routes.rb

for example localhost/tweets/new

in routes.rb
resources:tweets
match 'new_tweet' => "Tweets#new"
el primer de match es el path rapido y el segundo es a donde realmente
esta apuntado que es el controller#action

now if you run localhost/new_tweet will show the form to create a nuew

if we wan redirect some action for example the before action we want redirect to list of tweets
we need to write in routes.rb
match 'new_tweet' => redirect ('/tweets')

if we want show an action from localhost/ we need
root :to => "Tweets#index"
------------------------------------------
#routes params
if params[:zipcode]
  @tweets = Tweet.where(:zipcode => params[:zipcode])
else
  @tweets = Tweet.all
end

In routes for send params we will call

match 'local_tweets/:zipcode' => 'Tweets#index'
and to we can specify other name

match 'loca_tweets/:zipcode'=> 'Tweets#index', :as =>'local_tweets'

y the link to call that action will
 <%= link_to "Tweets in 32828",
local_tweets_path(32828) %>

That seem to call link is the match +underscore+ path
------------------------------------------------
find all tweet from one zombie

if params[:name]
  @zombie = zombie.where(:name=>params[:name]).first
  @tweets = @zombie.tweets
end

--------------------------------------------

Create a named route. It should generate a path like
'/zombies/:name' where :name is a parameter, and points to the index
action in ZombiesController. Name the route 'graveyard'

RailsForZombies::Application.routes.draw do
  match 'zombies/:name' => 'Zombies#index' , :as => "graveyard"
  #match ':name' => 'Zombie#index', :as => 'graveyard'
  #match 'zombies/:name',:to=> 'Zombies#index', :as => 'graveyard'
end

---------------------------------------------

finished the course thanks


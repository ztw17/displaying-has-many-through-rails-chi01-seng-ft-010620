
# Displaying Has Many Through Rails

## Objectives

1. construct a bi-directional has many through.
2. query for associations via the belongs_to, has_many, and has_many through associations.
3. iterate over associations in a view and display associated data for a primary instance.
4. identify the join model in a has many through.

## Overview

We've seen how we can use simple associations to display data to our users in Rails, but what about more complex relationships? Lucky for us, the interface for displaying this type of data is just as easy, thanks to ActiveRecord and `has_many through`.

## Lesson

### has_many, through

Let's say you're making a blog and you want to give users the ability to sign up and comment on your posts. What's the relationship between a post and a comment?  If you said, "a comment belongs to a post, and a post has many comments," give yourself a pat on the back!

What about the relationship between a user and a comment? Again, the user has many comments and the comment belongs to the user. So far, this is pretty straight forward.

Things get slightly more complicated when we talk about the relationship between a user and posts that the user has commented on. How would you describe that relationship? Well, a user obviously can comment on many posts, and a post has comments by many users. Yep, this is a many to many relationship. We can setup a many-to-many relationship using a join table. You probably remember seeing this in previous READMEs - in this case, `comments` will act as our Join table. Any table that contains two foreign keys can be thought of as a join table. In this case, our `comments` table will relate our posts with our users. A row in our comments table will look something like this:

 |id | content     | post_id     | user_id |
| :------------- | :------------- | :-------------|
|1|"I loved this post!"      | 5       | 3

For this instance, we know that comment with the id of `1` was created by the user with id `3` for the post with id `5`. We have all of the information we need to determine all of the posts that a particular user has commented on as well as all of the users who commented on any post. When we're done, we'll be able to simply call `@user.posts` to get a collection of all of those posts.

Let's set this up. First, we'll need migrations for a comments table, posts table, and users table. We've included migrations and models in this repo so you can follow along.

```ruby
# db/migrate/xxx_create_posts
class CreatePosts < ActiveRecord::Migration
  def change
    create_table :posts do |t|
      t.string :title
      t.string :content
      t.timestamps null: false
    end
  end
end
```

```ruby
# db/migrate/xxx_create_users
class CreateUsers < ActiveRecord::Migration
   def change
    create_table :users do |t|
      t.string :username
      t.string :email
      t.timestamps null: false
    end
  end
end
```

```ruby
# db/migrate/xxx_create_comments
class CreateComments < ActiveRecord::Migration
  def change
    create_table :comments do |t|
      t.string :content
      t.belongs_to :user
      t.belongs_to :post
      t.timestamps null: false
    end
  end
end
end
```

In our models, we have the following:

```ruby
#app/models/post.rb
class Post
  has_many :comments
  has_many :users, through: :comments
end
```

```ruby
#app/models/user.rb
class User
  has_many :comments
  has_many :posts, through: :comments
end
```

```ruby
#app/models/comment.rb
class Comment
  belongs_to :user
  belongs_to :post
end
```

Instances of our `User` model now respond to a method called `posts`. This will return a collection of posts that share a comment with the user.

### Displaying Comments on Our Posts

First, let's setup our posts show page to display all of the comments in our particular post. We'll include the username of the user who created the comment, as well as a link to their show page.

In `app/controllers/posts_controller.rb`, define a `show` action that finds a have it load a particular post.

```ruby
# app/controllers/posts_controller.rb
class PostsController < ApplicationController

  def show
    @post = Post.find(params[:id])
  end
end
```

In our posts show page, we'll display, the title and content information for the post, as well as the information of each comment associated with the post.

```erb
#app/views/posts/show.html.erb
<h2><%= @post.title %></h2>
<p>
  Content: <%= @post.content %>
</p>
Comments:
  <% @post.comments.each do |comment| %>
    <%= link_to comment.user.username, user_path(comment.user) %> said
    <%= comment.content %>
  <% end %>
```




## Notes

As relationships become more complex and require join models with has many though associations some additional considerations must be taken into account within our rails app.

Let's look at a blog domain with posts that have many comments but each comment is by a unique user.

post table schema / migration
user table schema / migration
comments table schema / migration

comments model with associations
  belongs_to user
  belongs_to post

post model with associations
  has_many comments
  has_many users, through comments

user model with associations
  has_many comments
  has_many posts, through comments

you might not think of something like a comment functiononing as a join model but it does. anything with 2 foreign keys that is wired with a through relationship is a join model.

on a post show page we want to list all the comments. easy.

@post.comments.each do |comment|
  comment.content
  comment.user.name
end

we're not using the through relationship and are just using on the comment association.

Now in each comment, we're linking to the user#show page. On that page we want to show posts that the user has commented on.

@user.posts.each do |post|
  post.title
end

we're hitting the through relationship but nothing really changes because the entire join model is abstracted away.

conclusion - displaying data on a has many through isn't too different than a belongs to and a has many. that's the point of the abstraction. you shouldn't worry about how activerecord got the data, whether through a join query or not. Just make sure the instance or relation you're using in your code actually has the data you want.


## Resources

https://robots.thoughtbot.com/accepts-nested-attributes-for-with-has-many-through

<a href='https://learn.co/lessons/displaying-has-many-through-rails' data-visibility='hidden'>View this lesson on Learn.co</a>


# Displaying Has Many Through Rails

## Objectives

1. construct a bi-directional has many through.
2. query for associations via the belongs_to, has_many, and has_many through associations.
3. iterate over associations in a view and display associated data for a primary instance.
4. identify the join model in a has many through.

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

you might not think of something like a comment functiononing as a join model but it does. anything with 2 fks that is wired with a through relationship is a join model.

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

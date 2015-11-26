
# Displaying Has Many Through Rails

## Objectives

1. Objective 1
2. Objective 2.

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

you might not think of something like a comment functiononing as a join model but it does. anything with 2 fks that is wired with a through relationship is a join model. Sometimes they are real things, like in this example, but soon we'll be dealing with something called a PostTag

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

That's still pretty simple, we're hitting the through relationship but nothing really changes because the entire join model is abstracted away.

But what if we wanted to show the comments the user has left on each post?

@user.comments.group(:post) do |p|



## Resources

https://robots.thoughtbot.com/accepts-nested-attributes-for-with-has-many-through

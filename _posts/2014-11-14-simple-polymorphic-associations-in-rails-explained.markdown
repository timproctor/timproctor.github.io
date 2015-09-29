---
layout: post
title: "Simple Polymorphic Associations in Rails Explained"
date: 2014-11-14 11:40:53 -0700
categories: rails ruby data-associations
---

## Me vs. Rails Guide

Yes, everything I'm going to blog about today could be easily found in the Rails guide. They might
even do a better job of explaining things than I would. Selfishly, this blog is actually not
intended for any reader, except future me. If someone does read any of my blog and you benefit,
that is an unintentional bonus. The other purpose of this blog is to engage another region of my
brain to help solidify abstract concepts. This is a technique talked about in Andy Hunt's book,
"Pragmatic Thinking and Learning: Refactor Your Wetware."

## An Article has a Comment and a Comment belongs to an Article

To keep it simple, consider an Article model. The article has a title and a body. The article will
automatically have a primary key, that Rails auto-increments as more articles are created.
For this example, the user is not even part of the data structure, so this is a simplification
and probably not realistic. A Comment model will have an article_id, which serves as the foreign
key to the Article model. The foreign key is just a way to link or establish a relationship between
articles and comments. An article knows about a comment because a comment has that article_id that
correlates to any specific article.

![article-comment]({{ site.url}}/assets/images/article_comment.png)

## Add a Photo model that also allows for Comments

So far so good, right? If we create our first article, it will have an id of 1 in both the Article
and Comment model. Now, suppose we add a Photo model and the photos can take in comments too, like
the articles. Below, is another model of this scenario. Do you see any conflicts?

![id-clash]({{ site.url }}/assets/images/article_comment_photo_clash.png)

## id's clash

Both the photo and article have the same id of 1. This is an issue that needs to be resolved as
there is no way to know which model `id: 1` is referring to. Is `1` referring to the photo_id or
the article_id? So this is why we would use a polymorphic relationship in our Rails application.
Active Record will help us settle this id clash.
It could look like the following:

![polytime]({{ site.url }}/assets/images/polymorphic.png)

Notice that in the Comment model, the photo_id and article_id have been replaced with `commentable_type:string`.
When a comment is created, it will be `commentable_type: "Article"` or `commentable_type: "Photo"` and the
commentable_id will auto-increment. This solves the problem of photo_id and article_id both being 1.
Also, notice that this polymorphic relationship is explicitly stated in the Comment model with
`belongs_to :commentable, polymorphic: true`. In the Article and Photo model it is necessary to
add `as: :commentable` to the `has_many :comments`. The entire line becomes: `has_many :comments, as: :commentable`.

## Not talked about: migrations.

I did not talk about the steps needed to make the migration changes for the Comment, Article, and Photo
model, but like I said at the start, the Rails guides do an excellent job of explaining the whole
polymorphic problem.

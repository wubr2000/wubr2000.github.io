---
layout: page
title: "Expert-sourced, bite-sized restaurant reviews"
date: 2013-12-26 07:21
comments: false
sharing: true
footer: true
---

## Problem

There are many crowdsourced restaurant review sites already. For example, in Hong Kong, we have [Openrice](http://www.openrice.com). In the US, there is Yelp. The reviews on these sites are usually ranked by popularity or the strength of social media connections. But the quality, reliability, credibility of these reviews are difficult to verify. At the other end of the spectrum, there are proprietary restaraunt review companies such as Michelin, Zagat, Timeout, etc. These are written and edited by very credible sources but they represent the view of a particular company or individual which could be biased.

## Solution

**I envisioned [Bites](http://bitesize.herokuapp.com) to be the RottenTomatoes of restaurant reviews.** It is a review site that is written/curated by dining experts only. While other users can comment and vote on each review, they can never start a review unless they have been verified as an expert by us. Bites will also be Twitter-like in that each review is only limited to 140 words. There will be upvotes/downvotes like Quora and a rigourous expertise verification system as well.

The incentive for experts to share their bite-sized review on this website is to promote their work or to enhance their reputation. This is the reason that [Quora](http://www.quora.com) is able to attract experts to be top writers for their website.

## Lessons

**The most important thing that I learned in this project is how to set up a "following/followers" data model - the backbone of any social network.** This is done by creating a Relationship "through-table" that is also reversible.

This is how to set up the "Relationship" table in the User model:

```ruby
class User < ActiveRecord::Base
  #Relationship table for following:
  has_many :relationships, foreign_key: "follower_id", dependent: :destroy
  has_many :followed_users, through: :relationships, source: :followed
```

By default, in a has_many through association Rails looks for a foreign key corresponding to the singular version of the association; in other words, code like "has_many :followeds, through: :relationships" would assemble an array using the followed_id in the relationships table. But, user.followeds is rather awkward; far more natural is to use "followed users" as a plural of "followed", and write instead user.followed_users for the array of followed users. Naturally, Rails allows us to override the default, in this case using the :source parameter, which explicitly tells Rails that the source of the followed_users array is the set of followed ids.

This is how to set up the "Reverse Relationship" table for followers in the User model.

```ruby
  #Reverse relationship table for followers
  has_many :reverse_relationships,  foreign_key: "followed_id", 
                                    class_name: "Relationship",
                                    dependent: :destroy
  has_many :followers, through: :reverse_relationships, source: :follower
```

It is not necessary to make a whole database table just to hold reverse relationships. Instead, take advantage of the underlying symmetry between followers and followed users to simulate a reverse_relationships table by passing followed_id as the primary key. Note that it is necessary to include the class name for this association:
```ruby
  has_many :reverse_relationships, foreign_key: "followed_id", class_name: "Relationship"
```
because otherwise Rails would look for a ReverseRelationship class, which doesn't exist.

In the case of a :followers attribute, Rails will singularize "followers" and automatically look for the foreign key followerid in this case. I've kept the :source key to emphasize the parallel structure with the has_many :followed_users association, but you are free to leave it off.

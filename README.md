Copyright (C) 2008-2012      Ben J Woodcroft <donttrustben somewhere near gmail.com>

This software is BETA! Use at your own risk.

[![Build Status](https://secure.travis-ci.org/wwood/bioruby-krona.png)](http://travis-ci.org/wwood/bioruby-krona)

= Reachable

Reachable is a small Ruby liby that extends the Array class so that Arrays are more transparent
to methods. For instance, a ReachableArray of Book objects can not only take normal Array methods such as
collect and sum, but also methods that operate Book objects, such as author and title. 

== Example

Say I have an regular ActiveRecord driven database, that has Bookshelf, Book, and Author classes. Bookshelves have many books, and books have many authors.

Say I want all the books.

```ruby
Book.all
```

Then I want to find all the bookshelves from those that contain those books.

```ruby
Book.all.collect{|book| book.bookshelf} #=> array of bookshelves.
```

From personal experience, running a single method on each array element and collecting the results is quite common, especially in Rails. The idea, then, is to make the arrays not just responsive to the methods that apply to the Array itself, but to methods that apply to the +members+ of that array. That is, make the array reachable (caution: made up word). In the case above we want to run the 'bookshelf' method on each of the book objects in the array.

To make an array reachable, convert it into a ReachableArray object, by calling the reach method of Array, which is added by the inclusion of the reach library. 

```ruby
require 'reachable'
Book.all.reach #=> ReachableArray of books
```

Now getting the corresponding array of bookshelves requires less typing:

```
require 'reachable'
Book.all.reach.bookshelf #=> ReachableArray of Bookshelf objects
```

Notice that a ReachableArray is returned by the bookshelf method, not an Array. This means you can chain reaches together:

```ruby
require 'reachable'
Author.all.reach.book.bookshelf #=> ReachableArray of Bookshelf objects
```

Removing reachability from the array, or retracting, is equally as simple - just use the retract method:

```ruby
require 'reachable'
Book.all.reach.bookshelf.retract #=> Array of Bookshelf objects
```

=== Slap

Like reach, the slap method is another method added to the Array class to make it mroe transparent. The difference is that reach looks to see if the given method operates on the Array class, and if not then applies it to each member of that array. The slap method bypasses the first step, and just applies the given method to each member. For instance

```ruby
require 'reachable'
[[1,2,3],[4]].slap.length.retract #=> [1,3] 
```

== Caution

When operating on a ReachableArray, methods that operate on the Array itself take precedence over methods that apply to array members.

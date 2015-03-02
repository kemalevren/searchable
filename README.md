# Searchable trait for Laravel's Eloquent models

This package adds search/filtering functionality to Eloquent models in Laravel 4/5.

## Composer install

Add the following line to `composer.json` file in your project:

    "jedrzej/searchable": "dev-master"
	
or run the following in the commandline in your project's root folder:	


    composer require "jedrzej/searchable" "dev-master"

## Setting up searchable models

In order to make an Eloquent model searchable, add the trait to the model and define a list of fields that the model can be filtered by:

    use Jedrzej\Searchable\SearchableTrait;
    
    class Post extends Eloquent
    {
        use SearchableTrait;
        
        public function getSearchableAttributes()
        {
            return ['title', 'forum_id', 'user_id', 'created_at'];
        }
    }

`SearchableTrait` adds a `filtered()` scope to the model - you can pass it a query being an array of filter conditions:
 
    // return all posts with forum_id equal to $forum_id
    Post::filtered(['forum_id' => $forum_id])->get();
    
    // return all posts with with <operator> applied to forum_id
    Post::filtered(['forum_id' => <operator>])->get();
 
 or it will use `Input::all()` as default:
    
    // if you append ?forum_id=<operator> to the URL, you'll get all Posts with <operator> applied to forum_id
    Post::filtered()->get();
 
## Building a query

The `SearchableTrait` supports the following operators:
    
### Comparison operators
Comparison operators allow filtering based on result of comparison of model's attribute and query value. They work for strings, numbers and dates. They have the following for:
    
    (<operator>)<value>

The following comparison operators are available:

* `gt` for `greater than` comparison
* `ge` for `greater than or equal` comparison
* `lt` for `less than` comparison, e.g
* `le` for `les than or equal` comparison

In order to filter posts from 2015 and newer, the following query should be used:

    ?created_at=(ge)2015-01-01
    
### Equals/In operators
Searchable trait allows filtering by exact value of an attribute or by a set of values, depending on the type of value passed as query parameter. 
If the value contains commas, the parameter is split on commas and used as array input for `IN` filtering, otherwise exact match is applied.
    
In order to filter posts from user with id 42, the following query should be used:

    ?user_id=42
    
In order to filter posts from forums with id 7 or 8, the following query should be used:

    ?forum_id=7,8
    
### Like operators
Like operators allow filtering using `LIKE` query. This operator is triggered if exact match operator is used, but value contains `%` sign as first or last character.

In order to filter posts that start with `How`, the following query should be used:

    ?title=How%
    
### Negation operator
It is possible to get negated results of a query by prepending the operator with `!`.
    
Some examples:
    
    //filter posts from all forums except those with id 7 or 8
    ?forum_id=!7,8
    
    //filter posts older than 2015
    ?created_at=!(ge)2015
    
#  mustache.js with added dot notation and filters

For the original mustache.js implementation visit <http://github.com/janl/mustache.js>.

## Why use this version?

In my opinion the original moustache.js implementation lacks two features: convenient access to value's properties (the dot notation) and value filters (which are available for instance in Django template engine or Smarty).

And I am not convinced about reasons for not doing this, which were discussed in <http://github.com/defunkt/mustache/issues/6>.

## The dot notation

Tag in dot notation consists of a tag's name and optionally its properties separated by dots, followed by filters. Filters are separated from the properties and each other by vertical line characters (|). Filters are essentially one-parameter functions declared in the global scope.

Long story short, tag name `name1.name2.name3 | filter1 | filter2` will be processed the following way:

1. search in template context for value named `name1`
2. try to access `name2` property in the value from (1), if the property is not found return an empty string
3. do the same as above but search for `name3` property in value from (2)
4. apply filter `filter1` to the value from (3)
5. apply filter `filter2` to the value from (4)
6. return the value from (5), so it may be used in a template

Filters are only applied if value specified by names is found within context.

## Example

For example:

    // declare filter
    function uc(str) {
      return ('' + str).toUpperCase();
    }

    var view = {
      user: {
        first_name: 'John',
        last_name: 'Smith'
      }
    }

    var template = "Hello {{ user.first_name }} {{ user.last_name | uc }}!";

    var html = Mustache.to_html(template, view);

After execution the above code the value of `html` variable will be:

    Hello John SMITH!

## About the modifications

This version differs from the original one only in the implementation of `Renderer.prototype.find` function.

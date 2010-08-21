#ICanHaz.js
A simple/powerful approach for doing client-side templating with Mustache.js and jQuery.

##The Quick Demo:

###Step 1. - Define your template

    <script id="user" type="text/html">
        <li>
            <span class"name">Hello I'm {{ name }}</span>
            <span class="twitter"><a href="http://twitter.com/{{ twitter }}">@{{ twitter }}</a></span>
        </li>
    </script>

###Step 2. - Retrieve your populated template:
    
    // I Can Haz User?
    var user = ich.user(user_data_object)

###Step 3. - There is no step 3!

##Why would we need this?
Because building html elements using javascript or jQuery is ugly:
    
    // obviously there's several ways to do this, but point is... it's ugly.
    
    // vanilla JS
    hello_div = document.createElement('div');
    hello_div.setAttribute('class', 'hello');
    my_list = document.createElement('ul');
    hello_div.appendChild(my_list);
    list_item = document.createElement('li');
    list_item.innerHTML = 'My list item';
    my_list.appendChild(list_item);
    
    // jQuery
    hello_div = $('<div class="hello"><ul></ul></div>');
    hello_div.children('ul').append('<li>My list<li>');
    

It gets really problematic if what you're building is a lot longer or more complex than this example. Not to mention, it's also not a clean separation of concerns to write html in javascript.

Mustache.js gives us an awesome templating solution, here's a snippet from their docs:
    
    var view = {
      title: "Joe",
      calc: function() {
        return 2 + 4;
      }
    }
    
    var template = "{{title}} spends {{calc}}";
    
    var html = Mustache.to_html(template, view);

But the beauty fades when we're dealing with multi-line html in the browser because strings in JS can't include new-lines so everything has to be escaped. Then there's the problem of double vs. single quotes and before you know it... we're back in ugly land:
    
    var template = '<div class="hello">\
        <span class="title">{{ title }}</span>\
        <ul></ul>\
    </div>'

##I Can Haz Better Solution?
YES!

With ICanHaz.js you define your Mustache.js template snippets in script blocks of type="text/html" and give them an "id" as a title for your snippet (Which validates, btw). This approach was suggested by jQuery developer [@jeresig](http://twitter.com/jeresig) [on his blog](http://ejohn.org/blog/javascript-micro-templating/).Then, on document ready ICanHaz.js builds a cache of all the templates and creates a function for each snippet. All you have to do is say to yourself for example "I can haz user?":

    var data = {
        first_name: "Henrik",
        last_name: "Joreteg"
    }
    
    // I can has user??
    html = ich.user(data)

At this point 'html' is jQuery object containing your complete html with your data injected. 

For each template you define, ICanHaz builds a retrieval function with the same name. 
If you don't want a jQuery object but just want the populated string you can just pass in `true` as the second argument to get the raw string. This is useful if your template isn't producing html.

##Full Working Example
    <!DOCTYPE html>
    <html>
        <head>
            <title>ICanHaz.js Demo</title>
            <script src="jquery-1.4.2.min.js" type="text/javascript"></script>
            <script src="mustache.js" type="text/javascript"></script>
            <script src="ICanHaz.js" type="text/javascript"></script>
            
            <script id="user" type="text/html">
                <li>
                    <span class"name">Hello I'm {{ name }}</span>
                    <span class="twitter"><a href="http://twitter.com/{{ twitter }}">@{{ twitter }}</a></span>
                    <span class="job">I work for the awesome {{ employer }} as a {{ job_title }}.</span>
                    <span class="twitter">You should follow <a href="http://twitter.com/{{ other_twitter }}">@{{ other_twitter }}</a> too.</span>
                </li>
            </script>
            
            <script type="text/javascript">
                // when the dom's ready
                $(document).ready(function () {
                    // add a simple click handler for the "add user" button.
                    $('#add_user').click(function () {
                        var user_data, user;
                        
                        // build a simple user object, in a real app this would probably come from a server
                        // somewhere. Otherwise hardcoding here is just silly.
                        user_data = {
                            name: "Henrik Joreteg",
                            twitter: "HenrikJoreteg",
                            employer: "&yet",
                            job_title: "JS nerd",
                            other_twitter: "andyet"
                        };
                        
                        // Here's all the magic.
                        user = ich.user(user_data);
                        
                        // append it to the list, tada! Now go do something more useful with this.
                        $('#user_list').append(user);
                    });
                });
            </script>
        </head>
        <body>
            <h1>ICanHaz.js Demo</h1>
            <h3>User List</h3>
            <button id="add_user">Add User</button>
            <ul id="user_list"></ul>
        </body>
    </html>
# Request Path

* This will become a table of contents (this text will be scraped).
{:toc}

All web applications begin with an HTTP request by a client.
They all end with a response from our server.
In this chapter, we are going to follow the program flow
from the client request through to the response.

## From the Client to Our Doorstep

The inner working of the Internet are beyond the scope of this book.
It is however helpful to remember that there is a client (web browser) out
there
and that the request it generates passes through a number of steps to reach
our servers.
Along the way proxies, caches and firewalls may look at the request, modify it,
and possibly store what we send back.
If all goes well this will be transparent to both our server and the user.
There are times, particularly with dynamic sites, where these things will need
to be taken into account.
There are also ways to take advantage of the infrastructure of the internet
that reduce the load on the server
while can save both bandwidth and equipment costs.

## Reverse Proxy (optional)

The first stop for a request in a live application is usually a reverse proxy like 
[NginX][] or [Apache mod\_proxy][].
Depending on the setup, these proxies may serve static files like images,
CSS style sheets, JavaScript, videos etc.
Often, we will even let it serve cached pages without ever interacting
with our application.

[Phusion Passenger][] is not a reverse proxy per say. However, for the sake
of the argument let's pretend it is one as it will receive the request via Apache
and send a rack request to the server application.

## Web server

If the request was not already returned by by the reverse proxy.
Or if no reverse proxy is being used, the request is being forwarded on to a Web Server.
[Mongrel][], [Thin][], [Ebb][] and a number of lesser-know programs fill this
role.

Once the response is received by the webserver, its being parsed and sent to [Rack][]
via a handler.


## Rack and Merb

Merb receives the request as a [Rack][] environment. A Rack environment wraps
all the cgi style headers for the request.
Because Merb uses Rack as an abstraction layer, it becomes really easy to change
web servers.
You might hear people refer to these web servers as adapters.

Before Merb dispatches the request through the stack, one can add "Rack middlewares".
Rack middlewares can process the request before it's sent through Merb or even wrap it
allowing custom code to be execute before and after going through the stack.
Rack Middlewares end up being very powerful tools allowing you to do
custom logging, caching, profiling etc...

We will not cover the use of Rack extensively here,
but you can find Merb's Rack setup in 'config/rack.rb' (full stack app).

## Router

Once the request is being passed to the stack, the router is asked where to go
by the request.
The request then sends itself to the controller.

The Router's job is to set up parameters extracted from the request
and to tell the request where to go. 
In most cases, the router maps a request to a controller method (action) and also
sets up parameters extracted from the request.

Behind the scenes the Merb framework has already done some work for you.
By the time a request reaches the controller, Merb has:

    * Extracted the request URI Path.
    * Parses any Post or Query data sent by the client into the params hash.
    * Setup access to request cookies and the current session.

The developer can use any of this information to route the request.
Please see the chapter on routing for more information.

## Controller Action

The request is now being dispatched to a controller method.
For instance: the **show** method/action from the **Articles** controller.
The action can render a view or simply return a string.
The returned value from the method will be used to build a Rack response.
A Rack response is just a simple tuple containing [status, headers, body].

## Out the Door

Finally Merb sends a well formed rack response back to the web server.
The web server in turn formats an HTTP response which is put out over
the wire.
The client receives the response and interprets by, for example, rendering a web page.


## Caching

If caching is enabled, the request might not go through the entire process
described above.
Caching may happen within Merb, by a Rack Middleware, a Proxy server,
or by the client web browser.


[Apache mod\_proxy]:  http://httpd.apache.org/docs/2.0/mod/mod_proxy.html
[GlassFish]:          /deployment/jruby
[Mongrel]:            http://mongrel.rubyforge.org/
[MVC]:                /getting-started/mvc
[NginX]:              /deployment/nginx
[Phusion Passenger]:  /deployment/passenger
[Rack]:               http://rack.rubyforge.org/
[Thin]:               http://code.macournoyer.com/thin/

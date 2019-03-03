Apache isn’t the fastest web server, or the easiest to configure, or the most secure, but it’s good enough to dominate all others.

Apache powers more than 60 percent of all public web sites.

Apache runs on Linux, Mac OS X, and all other Unix-like systems, as well as the many incarnations of Microsoft Windows.

Apache can be built with all of its modules combined into one big program (static linking), or with modules that are loaded into memory as needed (dynamic shared objects, or DSOs). 

The DSO method is easier and more flexi-ble, since it allows you to add modules to Apache after you’ve built it.



### Static and Dynamic Files:

* A basic web site consists of files: HTML, graphics, JavaScript, stylesheets, and other types. 

* The contents of these files are static—they don’t change on the server, and the only job of the web server is to return them to the browser on request.

* A web server needs only a little configuration to serve static files.

* The simplest way to make static HTML files dynamic is with server-side includes (SSI).

* SSI has its limits, though, and most dynamic sites use the far more powerful Common Gateway Interface (CGI) programs.

* CGI is a proto-col that specifies how web clients and servers should exchange requests and responses.

* One faster method is FastCGI, which starts up the CGI program as a separate long- running process and manages two-way communications between it and the web server. 

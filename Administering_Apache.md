- Apache isn’t the fastest web server, or the easiest to configure, or the most secure, but it’s good enough to dominate all others.

- Apache powers more than 60 percent of all public web sites.

- Apache runs on Linux, Mac OS X, and all other Unix-like systems, as well as the many incarnations of Microsoft Windows.

- Apache can be built with all of its modules combined into one big program (static linking), or with modules that are loaded into memory as needed (dynamic shared objects, or DSOs). 

- The DSO method is easier and more flexi-ble, since it allows you to add modules to Apache after you’ve built it.

### Static and Dynamic Files:

* A basic web site consists of files: HTML, graphics, JavaScript, stylesheets, and other types. 

* The contents of these files are static—they don’t change on the server, and the only job of the web server is to return them to the browser on request.

* A web server needs only a little configuration to serve static files.

* The simplest way to make static HTML files dynamic is with server-side includes (SSI).

* SSI has its limits, though, and most dynamic sites use the far more powerful Common Gateway Interface (CGI) programs.

* CGI is a proto-col that specifies how web clients and servers should exchange requests and responses.

* One faster method is FastCGI, which starts up the CGI program as a separate long- running process and manages two-way communications between it and the web server.

### Apache Configuration Files

Apache uses plain ASCII configuration files.
 
 
| File/Directory under /etc/apache2 | Uses |
| --- | --- |
| apache2.conf | ```shell Main configuration file. Includes other files through the following directives: # Include module configuration: Include /etc/apache2/mods-enabled/*.load Include /etc/apache2/mods-enabled/*.conf # Include all user configurations: Include /etc/apache2/httpd.conf # Include ports listing Include /etc/apache2/ports.conf # Include generic snippets of statements Include /etc/apache2/conf.d/[^.#]*```|
| conf.d/* | Anything you like can go here. By default, it’s empty.|
| mods-enabled/*.conf | Definitions for each enabled module. Debian includes the programs a2enmod to enable a module and a2dismod to disable one. The effect is to move xyz.conf and xyz. load files between /etc/apache/mods-available and /etc/apache2/mods-enabled for a module named xyz. The apache2.conf file uses the files under mods-enabled. |
| sites-enabled/* | Definitions for each web site. The default is 000-default, but there’s nothing magic about that name. You can have as many files here as you like.|
| .htaccess | Definitions for a directory, contained in that directory. Overrides other configuration files because it’s read last. Permitted only if AllowOverride is not set to none. Can be changed without reloading Apache. This is how many webmasters allow their clients to customize their sites without touching the main Apache configuration files.|

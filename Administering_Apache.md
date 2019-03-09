## `Administering_Apache`

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


Configuration File Directives:

Following are the contents of the default Apache configuration file, /etc/apache2/sites-enabled/ 000-default. Sections begin and end with HTML-style tags, such as:
```shell
<VirtualHost *>
    ...
 </VirtualHost>
 ```
 
 ```shell
 # Answer to any name or IP address:
    NameVirtualHost *
    # For any virtual host at any address, any port:
    <VirtualHost *>
# If Apache has problems, whom should it contact? ServerAdmin webmaster@localhost
        # Our web site files will be under this directory:
        DocumentRoot /var/www/
        # Overall directives, in case we move DocumentRoot
        # or forget to specify something later:
        <Directory />
            # Lets Apache follow symbolic links:
            Options FollowSymLinks
            # Disables .htaccess files in subdirectories:
            AllowOverride None
        </Directory>
        # DocumentRoot itself:
        <Directory /var/www/>
            Options Indexes FollowSymLinks MultiViews
            # Forbids .htaccess files:
            AllowOverride None
            Order allow,deny
            allow from all
            # Maps / to /apache2-default, the initial welcome
         # page that says "If you can see this...":
             RedirectMatch ^/$ /apache2-default/
         </Directory>
         # Permits CGI scripts:
         ScriptAlias /cgi-bin/ /usr/lib/cgi-bin/
         <Directory "/usr/lib/cgi-bin">
             AllowOverride None
             Options ExecCGI -MultiViews +SymLinksIfOwnerMatch
             Order allow,deny
             Allow from all
         </Directory>
         # Error log for a single site:
         ErrorLog /var/log/apache2/error.log
         # Possible values include: debug, info, notice,
         # warn, error, crit, alert, and emerg:
         LogLevel warn
         # Access log for a single site:
         CustomLog /var/log/apache2/access.log combined
         # Sends Apache and PHP version information to browsers;
         # Set to Off if you're paranoid, or have reason to be:
         ServerSignature On
         # Shows Apache docs (only to local users)
         # if you installed apache2-docs;
         # to suppress showing the documents,
         # you can comment these lines or delete them:
         Alias /doc/ "/usr/share/doc/"
         <Directory "/usr/share/doc/">
             Options Indexes MultiViews FollowSymLinks
             AllowOverride None
             Order deny,allow
             Deny from all
             Allow from 127.0.0.0/255.0.0.0 ::1/128
         </Directory>
     </VirtualHost>
 ```

### User and Group directives

These important settings tell Apache to run with a particular user ID and group ID. The Debian default in /etc/apache2/apache2.conf is:
```shell
 User www-data
 Group www-data
 ```
 
 
### Authentication and Authorization

Authentication determines who a visitor is.
Authoriza- tion determines what that visitor can do.

• Read a file
• Use server-side includes
• Run a CGI program
• Generate an index page for a directory lacking one

In Apache, the usual place to store authentication information is in a plain-text user file (often called an .htpasswd file, after the program that modifies it). The user file contains user IDs and encrypted passwords. The optional group file contains plain- text group IDs and user IDs; it’s useful for larger sites because it lets you specify per- missions for a group as a whole rather than for each of the individual users.

#### User files

create a password-protected directory and place a small text file in it.
```shell
# cd /var/www 
# mkdir secret
# cd secret
# echo "now you see it" > file.html
```
Now make a user file:
```shell
# cd /tmp
# htpasswd -c /tmp/users jack
New password: black_pearl
Re-type new password: black_pearl Adding password for user jack
```
If you want to change jack’s password later, enter:
```shell
# htpasswd /tmp/users jack
New password: kraken
Re-type new password: kraken Updating password for user jack
```
### Group files
Another way to handle multiple users is to use a group file.

### BenchMarking

Let’s make 1,000 separate requests for the same file, with a concurrency (simultaneous requests) of 5:
```shell
# ab -n 1000 -c 5 http://XXXX.XXXX.org/testing.html
This is ApacheBench, Version 2.0.41-dev <$Revision$> apache-2.0
Copyright (c) 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/ Copyright (c) 1998-2002 The Apache Software Foundation, http://www.apache.org/
Benchmarking server1.centralsoft.org (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Finished 1000 requests
Server Software:Apache/2.0.54
Server Hostname:server1.centralsoft.org
Server Port:80
Document Path:/bench.html
Document Length:1090 bytes
Concurrency Level:5
Time taken for tests:2.799386 seconds
Complete requests:1000
Failed requests:0
Write errors:0
Non-2xx responses:1000
Total transferred:1425000 bytes
HTML transferred:1090000 bytes
Requests per second:357.22 [#/sec] (mean)
Time per request:13.997 [ms] (mean)
Time per request:2.799 [ms] (mean, across all concurrent requests)
Transfer rate:496.89 [Kbytes/sec] received
Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.1      0       3
Processing:     6 
Waiting:        5
Total:          6
Percentage of the requests served within a certain time (ms)
50%     11
66%     12
75%     13
80%     13
90%     14
95%     14
98%     15
99%     16
100%    22 (longest request)
```

### Troubleshooting
If you like diagnosing problems, you’ll love the Web. There are so many things to break, in so many places and in so many ways, that you’ll be kept busy for ages.
Let’s look at some classic web problems. (The browser error messages are those used by the Firefox browser, but Internet Explorer’s messages are similar.)
Web Page Doesn’t Appear in Browser
Let’s assume your document root is /var/www, your file is test.html, and your server is server1.centralsoft.org. When you use an external web browser to access http://XXX.XXX.org/test.html, you get an error page in your browser window.
A browser error message like “Server Not Found” implies a DNS problem. First, ensure that server1.centralsoft.org has DNS entries in a public nameserver:
```shell
# dig server1.centralsoft.org
...
;; ANSWER SECTION:
server1.centralsoft.org. 106489 IN A 192.0.34.166 ...
```
Then see whether the server can be reached from the Internet. If your firewall allows pings, poke the server from the outside to see if it’s alive:
```shell
# ping server1.centralsoft.org
PING server1.centralsoft.org (192.0.34.166) 56(84) bytes of data.
64 bytes from server1.centralsoft.org (192.0.34.166): icmp_seq=1 ttl=49 time=81.6 ms
```
Check that port 80 is open and not blocked. From an external machine, try nmap:
```shell
# nmap -P0 -p 80 server1.centralsoft.org
    Starting nmap 3.81 ( http://www.insecure.org/nmap/ ) at 2006-07-25 23:50 CDT
    Interesting ports on server1.centralsoft.org (192.0.34.166):
    PORT   STATE SERVICE
    80/tcp open  http
    Nmap finished: 1 IP address (1 host up) scanned in 0.186 seconds
```
If you don’t have nmap, pretend to be a web browser. Use telnet to connect to the standard web port (80) and make the simplest HTTP request possible:
```shell
# telnet server1.centralsoft.org 80 Trying 192.0.34.166...
Connected to server1.centralsoft.org. Escape character is '^]'.
    HEAD / HTTP/1.0
    HTTP/1.1 200 OK
    Date: Wed, 26 Jul 2006 04:52:13 GMT
    Server: Apache/2.0.54 (Fedora)
    Last-Modified: Tue, 15 Nov 2005 13:24:10 GMT
    ETag: "63ffd-1b6-80bfd280"
    Accept-Ranges: bytes
    Content-Length: 438
    Connection: close
    Content-Type: text/html; charset=UTF-8
    Connection closed by foreign host.
```
If that doesn’t work, make sure this line is in /etc/apache2/ports.conf: 
```shell
Listen 80
```
and see whether anything else is hogging port 80:
```shell
# lsof -i :80
```
If you don’t see apache2 in this output, find out whether Apache is running: 
```shell
# ps -efl | grep apache2
```
If the output contains lines like this:
```shell
     5 S root      7692     1  0  76   0 -  2991 415244 Jul16 ?        00:00:00
     /usr/sbin/apache2 -k start -DSSL
```
Apache is running. If it isn’t, kick it in the pants:
```shell
# /etc/init.d/apache2 start
```
Then run the ps command again. If Apache still does not appear, look at the error log:
```shell
# tail -f /var/log/apache2/error.log
```
If you don’t have permission to view this file, you’re definitely having a hard day. If the error log is empty, it may also have the wrong permissions. Confirm that the /var/ log/apache2 directory and the /var/log/apache2/error.logfile exist:
```shell
# ls -l /var/log/apache2
total 84
```
If the tail of the error log showed old information, you may be out of disk space. It’s surprising how often we forget to check this before investigating more esoteric sus- pects, such as firewalls. Type:
```shell
# df
```
If you used a different User or Group directive in your Apache configuration, check
that the user and group exist:
```shell
# id www-data
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```
If the browser returned an Apache error message, you have some more digging to do. If the display says:
```shell
     Not Found
     The requested URL /wrong.html was not found on this server.
 ```
the URL was probably mistyped. If you see:
```shell
     Forbidden
     You don't have permission to access /permissions.html on this server.
```
the file is there, but the Apache user can’t read it:
```shell
# cd /var/www
# ls -l permissions.html
-rw------- 1 root root 0 Jul 26 00:01 permissions.html
```
Permissions problems can be fixed by changing the owner of the file to the process running Apache.
### Virtual Hosts Don’t Work
Use
```shell
# apache2ctl -S
```
for a quick check of your virtual host directives.
### SSI Doesn’t Work
If you see lines like this in your error log (/var/log/apache2/error.log): 
```shell
[error] an unknown filter was not added: INCLUDES
```
you didn’t enable mod_include. Run the command:
```shell
# a2enmod include
```
### CGI Program Doesn’t Run
If you can’t get a CGI program to run, work through the following checklist:

• Has CGI been enabled, by one of the methods discussed earlier?

• Is the CGI program in a CGI directory like /var/cgi-bin, or does it have a suffix like .php?

• Is the file readable? If not, use chmod.

• What does the Apache error log say?

• How about the system error log, /var/log/messages?

### SSL Doesn’t Work
Check that you enabled the Apache SSL module (a2enmod ssl) and told Apache to listen to port 443 in /etc/apache2/ports.conf:
Listen 443
If the directive wasn’t there, add it and restart Apache. Then try to access this URL in your browser: https://XXX.XXX.org. If it still doesn’t work, port 443 may be blocked by a firewall. You can check this with nmap:
```shell
# nmap -P0 -p 443 server1.centralsoft.org
    Starting nmap 3.70 ( http://www.insecure.org/nmap/ ) at 2006-08-01 22:38 CDT
    Interesting ports on ... (...):
    PORT    STATE SERVICE
    443/tcp open  https
    Nmap run completed -- 1 IP address (1 host up) scanned in 0.254 seconds
```


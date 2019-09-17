# deploymentnotes

1 ## Followed these instructions for server setup:
https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04

- In order to name the server, had to add the server name to local file /etc/hosts

- Another way is to add it to ./ssh/confi but the above is probably enough
        Host duckalope
            HostName 159.65.251.230
            User teal


2 ## Followed these instructions for grav configuration:
https://learn.getgrav.org/16/webservers-hosting/vps/digitalocean

- In order for these to work, you have to allow 80 and 443 through the firewall:
    ufw allow 80
    ufw allow 443

- nginx -t will fail if using the grav user, will only work with the root user

- I made a spelling error and so info.php worked, but index.html didn't work (accidentally wrote index.hmtl)

- while troubleshooting that i decided to add the grav user to the www-data group, and then decided to just leave it as a member...

- In order to configure droplet for multiple grav sites:
In the above instructions, change "grav" to the site name. So the config file is sitename.conf, the user is sitename, sites-enabled is sitename and each site is in its own user's home directory called sitename.

--A. sitename.conf
```
[sitename]

user = sitename
group = sitename
...
```

--B. adduser sitename

--C. /etc/nginx/sites-available/# vi sitename
```server {
    #listen 80;
    index index.html index.php;

    ## Begin - Server Info
    root /home/sitename/www/html;
    server_name localhost;
    ## End - Server Info
...
```

--D. Everywhere it says `listen = fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;` change it to `listen = 127.0.0.1:9000` where 900x ends in a unique port number for that site.

----I. In sites-available/sitename:
            ## Begin - PHP
            location ~ \.php$ {
            # Choose either a socket or TCP/IP address
            # fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
            # fastcgi_pass unix:/var/run/php5-fpm.sock; #legacy
            fastcgi_pass 127.0.0.1:9001;

----II. In /etc/php/7.2/fpm/pool.d/sitename.conf:
            [sitename]

            user = sitename
            group = sitename

            listen = 127.0.0.1:9001

            listen.owner = www-data
            listen.group = www-data

3 ## Make sure that the files in the html directory and all its children are owned by sitename:sitename.

4 ## Edit the sites-available file to point the dns to the site

server {
    #listen 80;
    index index.html index.php;

    ## Begin - Server Info
    root /home/sitename/www/html;
    server_name sitednsname.com;
    ## End - Server Info
...
```
5 ### login to the domain name company and add the IP address to the A record.
6 ## Check on https://dnschecker.org that its been moved over and then do certbot shit to setup https: https://certbot.eff.org/lets-encrypt/ubuntubionic-nginx.html

===========================================
If server is already setup with grav sites:

1 ## Follow these instructions for grav configuration, starting with the pool configuration:
https://learn.getgrav.org/16/webservers-hosting/vps/digitalocean#configure-nginx-connection-pool

- In the above instructions, change "grav" to the site name. So the config file is sitename.conf, the user is sitename, sites-enabled is sitename and each site is in its own user's home directory called sitename.

- Instead of creating new files from scratch, just copy an existing file changing the sitenames and the ports to unique values and changing the servernames to the site servernames.
--1. sitename.conf
--2. /etc/nginx/sites-available/# vi sitename

- Make sure that the files in the html directory and all its children are owned by sitename:sitename.

2 ### Login to the domain name company and add the IP address to the A record.
3 ## Check on https://dnschecker.org that its been moved over and then use certbot to setup https: https://certbot.eff.org/lets-encrypt/ubuntubionic-nginx.html


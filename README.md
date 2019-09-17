## deploymentnotes

### SERVER SETUP
Followed these instructions for server setup:
https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-18-04

- In order to name the server, had to add the server name to local file /etc/hosts

- Another way is to add it to ./ssh/confi but the above is probably enough
        Host duckalope
            HostName 159.65.251.230
            User teal
- On the server, you have to allow 80 and 443 through the firewall:
    ufw allow 80
    ufw allow 443

### SETUP A GRAV SITE
https://learn.getgrav.org/16/webservers-hosting/vps/digitalocean

- In the above instructions, change "grav" to the site name. So the config file is sitename.conf, the user is sitename, sites-enabled is sitename and each site is in its own user's home directory called sitename.

1. In /etc/php/7.2/fpm/pool.d create `sitename.conf`
```
[sitename]

user = sitename
group = sitename
...
```

2. adduser sitename

3. In /etc/nginx/sites-available/# create `sitename`

```server {
    #listen 80;
    index index.html index.php;

    ## Begin - Server Info
    root /home/sitename/www/html;
    server_name localhost;
    ## End - Server Info
...
```

4. In both of the above files, where is says `listen = fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;` change it to `listen = 127.0.0.1:9000` where 900x ends in a unique port number for that site.

5. Restart ngnix and php using sudo command.

6. Edit the sites-available file to point the dns to the site

```
server {
    #listen 80;
    index index.html index.php;

    ## Begin - Server Info
    root /home/sitename/www/html;
    server_name sitednsname.com;
    ## End - Server Info

```

7. Login to the domain name company and add the IP address to the A record.
8. Check on https://dnschecker.org that its been moved over. 
9. using certbot (https: https://certbot.eff.org/lets-encrypt/ubuntubionic-nginx.html) run sudo certbot --nginx to add certificate to new site

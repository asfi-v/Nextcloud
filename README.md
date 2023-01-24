
## Nextcloud

Install NextCloud with OpenMediaVault and Docker


## Prerequisites

- You’ll need a domain name.

- You’ll need to a CloudFlare account with your domain name pointed to it.

- You’ll need to point a subdomain to your home’s IP address. Make sure that Proxy Status is “DNS Only”. Also make sure that you change SSL/TLS to “Full”.

- You’ll need to forward ports 80 and 443 to your Server Machine.

- You’ll need NGINX Proxy Manager installed.

- You’ll need to create a volume in OpenMediaVault for your NextCloud file storage

## Installation

Open Portainer on your Server Machine and create a new Stack

Paste the following into your new stack:

```bash
version: '2'

volumes:
  nextcloud:
  dbnc:

services:
  dbnc:
    image: mariadb
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    restart: always
    volumes:
      - dbnc:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=yourpasswordhere #Change This
      - MYSQL_PASSWORD=yourpasswordhere #Change This
      - MYSQL_DATABASE=nextclouddbname
      - MYSQL_USER=nextcloudusername

  app:
    image: nextcloud
    ports:
      - 8080:80
    links:
      - dbnc
    volumes:
      - NextCloud:/var/www/html #Change 'nextcloud' to your external drive volume if available
    restart: always
```

Change the few things as noted above

Click the “Deploy the Stack” button

## Go to your NGINX Proxy Manager Dashboard:
Create a new Proxy Host

- Fill in the domain name (or subdomain) you designated for your NextCloud install
- Scheme should be http
- Forward Hostname/IP should be your Server Machine's IP address
- Foward port should be 8080
- Click the “Block Common Exploits” toggle

Go to the SSL tab.

- Change “None” to “Request a New SSL Certificate”
- Click the “Force SSL” and “HTTP/2 Support” toggles
- You can click the HSTS toggles if you’ve enabled HSTS in CloudFlare
- Click on “Save” button

Give Some Time to do its thing, and after a moment, you should see that everything has been deployed correctly

Next, go to https://nextcloud.yourdomain.com (change this to your actual domain) and you can see the NextCloud install page


- Enter an Admin username and password.
- Change the database type to MySQL/MariaDB.
- Enter the username, password, and database name as you have them in your Portainer Stack.
- Change Database location to from “localhost” to “dbnc” (without quotes).
- Toggle the option for additional app installation to meet your needs.
- Click “Finish setup”

At this point, Give Sometime to do its thing and finish all the install process.

Once the install is complete, you’ll be taken to the NextCloud dashboard!

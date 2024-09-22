# certbot-certificate-for-nginx

This guide suppose that we are alreay have nginx container (nginx_server) in docker-compose:

```
  nginx:
    image: nginx:alpine
    container_name: nginx_server
    volumes:
      - static_volume_app1:/usr/share/nginx/html/static_app1
      - media_volume_app1:/usr/share/nginx/html/media_app1
      - static_volume_app2:/usr/share/nginx/html/static_app2
      - media_volume_app2:/usr/share/nginx/html/media_app2
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
    ports:
      - "80:80"
      - "443:443"
```

1. Add the following line in docker-compose file:

```
- /etc/letsencrypt:/etc/letsencrypt
```

the nginx part of docker-compose file should look like this:

```
  nginx:
    image: nginx:alpine
    container_name: nginx_server
    volumes:
      - static_volume_app1:/usr/share/nginx/html/static_app1
      - media_volume_app1:/usr/share/nginx/html/media_app1
      - static_volume_app2:/usr/share/nginx/html/static_app2
      - media_volume_app2:/usr/share/nginx/html/media_app2
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
      - /etc/letsencrypt:/etc/letsencrypt 
    ports:
      - "80:80"
      - "443:443"
```

2. Run the docker-compose:

```
docker-compose up --build
```

3. access the nginx_container and install certbot and then exit it:

```
docker exec -it nginx_server /bin/sh
# apk add certbot certbot-nginx
# exit
```

4. install the certificate in the nginx server:

```
docker exec -it nginx_server certbot --nginx
```

you will go through a prompt to install the certificate for all the domains:

```
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Enter email address (used for urgent renewal and security notices)
 (Enter 'c' to cancel): ******@gmail.com

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.4-April-3-2024.pdf. You must agree in
order to register with the ACME server. Do you agree?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: y

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Would you be willing, once your first certificate is successfully issued, to
share your email address with the Electronic Frontier Foundation, a founding
partner of the Let's Encrypt project and the non-profit organization that
develops Certbot? We'd like to send you email about our work encrypting the web,
EFF news, campaigns, and ways to support digital freedom.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: n
Account registered.

Which names would you like to activate HTTPS for?
We recommend selecting either all domains, or all domains in a VirtualHost/server block.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: ***.*****
2: ****.****.****
3: ****.****.****
4: www.****
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate numbers separated by commas and/or spaces, or leave input
blank to select all options shown (Enter 'c' to cancel):
Requesting a certificate for ***.***** and 3 more domains
Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/***.*****/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/***.*****/privkey.pem
These files will be updated when the certificate renews.

Deploying certificate
Successfully deployed certificate for ***.***** to /etc/nginx/conf.d/default.conf
Successfully deployed certificate for ***.***** to /etc/nginx/conf.d/default.conf
Successfully deployed certificate for ***.***** to /etc/nginx/conf.d/default.conf
Successfully deployed certificate for www.***.***** to /etc/nginx/conf.d/default.conf
Congratulations! You have successfully enabled HTTPS on https://***.*****, https://***.*****, https://***.*****, and https://www.***.*****

NEXT STEPS:
- The certificate will need to be renewed before it expires. Certbot can automatically renew the certificate in the background, but you may need to take steps to enable that functionality. See https://certbot.org/renewal-setup for instructions.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

What's next:
    Try Docker Debug for seamless, persistent debugging tools in any container or image → docker debug nginx_server
    Learn more at https://docs.docker.com/go/debug-cli/
```

5. if you are using cloudflare DNS, make sure to select SSL/TLS encryption as **Full** so the website redirects properly.

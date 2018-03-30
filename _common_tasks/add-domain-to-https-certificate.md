---
title: Add Domain To HTTPS Certificate
order: 2
---

To add one or more domains to an existing HTTPS 
certificate

- SSH into server
- Stop the Docker container `docker container stop homepage`
- Stop Nginx `sudo systemctl stop nginx`
- Check `ps -ef |grep nginx` and if necessary `kill -9 <pid>`
- Update certificate by specifying all domains (the ones you would like to 
add as well as the existing ones) `sudo certbot certonly -d <domain1> -d <domain2> ...`
- You should see something like
```
How would you like to authenticate with the ACME CA?
-------------------------------------------------------------------------------
1: Spin up a temporary webserver (standalone)
2: Place files in webroot directory (webroot)
-------------------------------------------------------------------------------
```
- Select option 1.
- You will be asked if you wish to replace the current certificate with a 
new one
```
Do you want to expand and replace this existing certificate with the new
certificate?
```
Select yes
- You should see something like
```
   Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/guyrking.com/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/guyrking.com/privkey.pem
   Your cert will expire on 2018-06-28. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
```
- Restart the Docker container `./deploy-homepage.sh`




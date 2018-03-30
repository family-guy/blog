---
title: Renew HTTPS Certificate
order: 1
---

- SSH into server
- Stop the Docker container `docker container stop homepage`
- Stop Nginx `sudo systemctl stop nginx`
- Check `ps -ef |grep nginx` and if necessary `kill -9 <pid>`
- Renew the certificate and restart Nginx `sudo certbot renew`
- You should see something like
```
-------------------------------------------------------------------------------
new certificate deployed with reload of nginx server; fullchain is
/etc/letsencrypt/live/guyrking.com/fullchain.pem
-------------------------------------------------------------------------------
Congratulations, all renewals succeeded. The following certs have been
renewed:
  /etc/letsencrypt/live/guyrking.com/fullchain.pem (success)
```
- Restart the Docker container `./deploy-homepage.sh`

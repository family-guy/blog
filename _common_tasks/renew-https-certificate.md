---
title: Renew HTTPS Certificate
order: 1
---

- SSH into server
- Stop the Docker container `docker container stop homepage`
- Stop Nginx `sudo systemctl stop nginx`
- Check `ps -ef | grep nginx` and if necessary `kill -9 <pid>`
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

## Local alternatives

Sometimes, you might only want to manually obtain `fullchain.pem` and
`privkey.pem` on your local machine for a single domain (`certbot renew` 
acts on multiple certificates and checks if certificates expire in less than
30 days; it also has to be run on the target webserver), e.g.

- To avoid having to reinstall `certbot` if the target webserver instance dies
- To integrate the renewal process more seamlessly into the deployment process,
 e.g Kubernetes on Google Cloud Platform
 
There are several ways to do this, two of which are described below:

### A. HTTP challenge
 
On your local machine,

```
certbot -d <my-domain> --manual --logs-dir certbot --config-dir certbot \
--work-dir certbot --preferred-challenges http certonly
```

This will prompt you to upload a file with specific content to the target
webserver

```
Create a file containing just this data:

<http-challenge-file-contents>

And make it available on your web server at this URL:

http://<my-domain>/.well-known/acme-challenge/<http-challenge-filename>

-------------------------------------------------------------------------------
Press Enter to Continue
```

How you go about uploading the file depends on how the target webserver is
deployed.

#### i. Containerisation on virtual machine

Add the following to `Dockerfile`

```
RUN mkdir -p /usr/share/nginx/html/.well-known/acme-challenge
COPY <http-challenge-filename> /usr/share/nginx/html/.well-known/acme-challenge
```

On `master`, create a file in the root of the repository 
`<http-challenge-filename>` containing one line `<http-challenge-file-contents>`.

Push up on `master`, wait for image to build successfully in Docker Hub, 
then SSH into GCE instance and run `./deploy-homepage.sh`.

You should see
`<http-challenge-file-contents>` when navigating to
`http://<my-domain>/.well-known/acme-challenge/<http-challenge-filename>`

Return to the terminal window with the HTTP challenge prompt and press `Enter`
which should output something like

```
Waiting for verification...
Resetting dropped connection: acme-v01.api.letsencrypt.org
Cleaning up challenges
Non-standard path(s), might not work with crontab installed by your operating system package manager

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /home/guy/certbot/live/guyrking.com/fullchain.pem
   Your key file has been saved at:
   /home/guy/certbot/live/guyrking.com/privkey.pem
   Your cert will expire on 2018-09-25. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

and write the new key `privkey.pem` and certificate `fullchain.pem` to  
`~/certbot/live/<my-domain>` on the local machine.

Upload `privkey.pem` and `fullchain.pem` to `~`
on the target webserver 

```
gcloud compute scp ~/certbot/live/<my-domain>/fullchain.pem \
~/certbot/live/<my-domain>/privkey.pem <instance-name>:~
```

Ensuring `gcloud` is pointing the correct Google Cloud account and project.

SSH into the target webserver and move the files from `~` to 
`/etc/letsencrypt/live/<my-domain>`.

Remove `<http-challenge-filename>` from the repository root and the lines 
added to `Dockerfile`. Push up.

#### ii. Continuous deployment (CircleCI, Kubernetes, Google Cloud Platform)

To do

### B. DNS challenge
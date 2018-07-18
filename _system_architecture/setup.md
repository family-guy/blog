---
title: Setup
order: 2
---

#### i. Containerisation on virtual machine

- `cd /path/to/minima`
- `git checkout gce`
- `gcloud info`
- Update Google Cloud account if necessary `gcloud config set account <my-account-email>`
- Update Google Cloud project if necessary `gcloud config set project <my-project-id>`
- Create Google Compute Engine instance in Google Cloud (free tier micro 
instance, ensure HTTP and HTTPS traffic are allowed in firewall, 
Debian GNU/Linux 9 (stretch))
- Create a static IP address for the instance, **_VPC network->External IP
addresses_** in Google Cloud web console
- SSH into instance via Google Cloud web console
- [Install Docker](https://docs.docker.com/install/linux/docker-ce/debian/#install-docker-ce-1)
- [Allow non-privileged users to run Docker commands](https://docs.docker.com/install/linux/linux-postinstall/)
 (avoid prefixing `docker` with `sudo`)
- `gcloud compute scp /path/to/minima/deploy-homepage.sh <instance-name>:~`
- Make `deploy-homepage.sh` executable on the instance `sudo chmod u+x deploy-homepage.sh`
- Update SSL certificate and private key if necessary in `letsencrypt` 
on the local machine
- Upload `letsencrypt` to the instance
```
gcloud compute scp --recurse /path/to/letsencrypt <instance-name>:~
```
- Move `letsencrypt` to `/etc/letsencrypt` on the instance
- Install nginx on the instance `sudo apt-get install nginx`
- Check it has installed successfully and is running `systemctl status nginx`  
/ viewing `<instance-ip-address>` in a browser
- `gcloud compute scp /path/to/default /path/to/nginx.conf <instance-name>:~` 
ensuring `server_name` is set to `<my-domain>` in `default`
- On the instance, move `default` to `/etc/nginx/sites-enabled/` and 
`nginx.conf` to `/etc/nginx/`
- Stop `nginx` on the instance `sudo systemctl stop nginx`
- Comment out in `deploy-homepage.sh`
```bash
#!/bin/bash
CONTAINER=homepage
# docker container stop $CONTAINER
# docker rm $CONTAINER
docker pull guydocker/minima:gce
docker run --detach --name=$CONTAINER --restart=always --publish=80:80 \
	--publish=443:443 --volume=/etc/letsencrypt/:/etc/letsencrypt/ \
	--volume=/etc/nginx/:/etc/nginx/ guydocker/minima:gce
# docker rmi $(docker images --filter "dangling=true" --quiet)
```
- Run `./deploy-homepage.sh`
- Check `<instance-ip-address>` in the browser (it should give a warning 
about the SSL certificate not matching the domain)
- Comment back in lines commented out above
- Point the domain to `<instance-ip-address>`
- Viewing `<instance-ip-address>` in the browser should now work as expected

#### ii. Continuous deployment

- `cd /path/to/minima`
- `git checkout master`
- `gcloud info`
- Update Google Cloud account if necessary `gcloud config set account <my-account-email>`
- Update Google Cloud project if necessary `gcloud config set project <my-project-id>`
- Create Google Kubernetes Engine cluster in Google Cloud
- Create Google Container Registry in Google Cloud
- Create a service account in Google Cloud
- Add the following environment variables in CircleCI
    - `DOCKER_CONTAINER_REGISTRY`
    - `SSL_CERTIFICATE`
    - `SSL_PRIVATE_KEY`
    - `GCLOUD_PROJECT`
    - `GCLOUD_SERVICE_KEY`
    - `GCLOUD_COMPUTE_ZONE`
    - `GCLOUD_K8_CLUSTER`
    
    (See blog posts for more details on some of the above)
- `git commit --allow-empty`
- Push up on `master`
- Once builds pass in CircleCI, set `server_name` in `redirect.conf` to the 
IP address of the Kubernetes load balancer
- Push up on `master`
- Go to `<ip-address-k8-load-balancer>` in the browser. The HTTPS redirect 
should work, but there will be a warning about the SSL certificate not 
matching the domain
- Point the domain to `<ip-address-k8-load-balancer>`
- Viewing `<ip-address-k8-load-balancer>` in the browser should now work as 
expected


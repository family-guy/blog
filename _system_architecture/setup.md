---
title: Setup
order: 2
---

#### i. Containerisation on virtual machine

- `gcloud info` check account and project are correct
- To update the account, `gcloud config set account <my-account-email>`
- To update the project, `gcloud config set project <my-project-id>`
- Create Google Compute Engine instance in Google Cloud (free tier micro 
instance, ensure HTTP and HTTPS traffic are allowed in firewall, 
Debian GNU/Linux 9 (stretch))
- Create a static IP address for the instance **_VPC network->External IP
addresses_** in Google Cloud web console
- SSH into instance via Google Cloud web console
- [Install Docker](https://docs.docker.com/install/linux/docker-ce/debian/#install-docker-ce-1)
- [Allow non-privileged users to run Docker commands](https://docs.docker.com/install/linux/linux-postinstall/)
 (avoid prefixing `docker` with `sudo`)
- Upload `deploy-homepage.sh` from local machine to instance 
`gcloud compute scp /path/to/deploy-homepage.sh <instance-name>:~`
- Make it executable on the instance `sudo chmod u+x deploy-homepage.sh`
- Upload `letsencrypt` directory from the local machine to the instance
```
gcloud compute scp --recurse /path/to/letsencrypt <instance-name>:~
```
- Move `letsencrypt` to `/etc/letsencrypt` on the instance
- Install nginx `sudo apt-get install nginx`
- Check it has installed successfully and is running `systemctl status nginx` 
and by viewing `<instance-ip-address` in a browser
- Upload the nginx configuation file `default` to the instance 
`gcloud compute scp /path/to/default <instance-name>:~` from local machine, 
ensuring `server_name` is set to `<my-domain>`
- Move the nginx configuration file `default` on the instance to 
`/etc/nginx/sites-enabled/default`
- Repeat the above two steps for the nginx configuration file `nginx.conf`
- Stop `nginx` on the instance `sudo systemctl stop nginx`
- Comment out lines in `deploy-homepage.sh`
```bash
#!/bin/bash
CONTAINER=homepage
# docker container stop $CONTAINER
# docker rm $CONTAINER
docker pull guydocker/minima
docker run --detach --name=$CONTAINER --restart=always --publish=80:80 \
	--publish=443:443 --volume=/etc/letsencrypt/:/etc/letsencrypt/ \
	--volume=/etc/nginx/:/etc/nginx/ guydocker/minima
# docker rmi $(docker images --filter "dangling=true" --quiet)
```
- Run `./deploy-homepage.sh`
- Comment back in lines above
- Check `<instance-ip-address>` in the browser (it should give a warning 
about the certificate not matching the domain)
- Point the domain to `<instance-ip-address>`
- Check `<instance-ip-address>` in the browser (it should work as expected)

#### ii. Continuous deployment (CircleCI, Kubernetes, Google Cloud Platform)

To do.
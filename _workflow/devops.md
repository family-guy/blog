---
title: DevOps
order: 2
---

## Step 1

#### -- Test build locally

- Checkout `feature` branch
- Build docker image; from project root `sudo docker build --tag=<name>:<tag> .`
- Run docker image `sudo docker run --detach --publish=80:80 --name=<containername> <imagename:imagetag>`
- Check `localhost` or `<ipaddress>` in browser

#### -- If checks passed

- Stop container `sudo docker container stop <containername>`
- Delete container `sudo docker container rm <containername>`
- Merge `feature` branch into `master`

## Step 2

#### -- Deploy to production

- Push up `master` branch
- Wait for image in Docker Hub to build successfully
- SSH into GCE instance; from home directory `./deploy-homepage.sh`
- Check `<productiondomain>` in browser
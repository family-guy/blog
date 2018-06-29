---
title: Details
order: 2
---

#### -- Make changes locally

- Pull down `master` branch
- Create and checkout `feature` branch
- `jekyll serve` or `jekyll serve --host=0.0.0.0` if serving from guest machine
- Make changes
- Test changes by checking `localhost:4000` or `<ipaddress>:4000` in browser
- Commit changes
- Back to third step; repeat

#### -- Test Docker build locally

- Checkout `feature` branch
- Build docker image; from project root `sudo docker build --tag=<name>:<tag> .`
- Run docker image `sudo docker run --detach --publish=80:80 --name=<containername> <imagename:imagetag>`
- Check `localhost` or `<ipaddress>` in browser

#### -- If checks passed

- Stop container `sudo docker container stop <containername>`
- Delete container `sudo docker container rm <containername>`
- Merge `feature` branch into `master`

#### -- Deploy to production

- Push up `feature` branch
- Wait for image in Docker Hub to build successfully
- SSH into GCE instance
- In `deploy-homepage.sh`, replace `guydocker/minima` with
`guydocker/minima:<feature>`
- From home directory `./deploy-homepage.sh`
- Test changes by checking `<productiondomain>` in browser
- If test fails, roll back by changing `guydocker/minima:<feature>`
to `guydocker/minima` and running `./deploy-homepage.sh`
- If test passes, merge `feature` into `master` in Github
- Wait for image in Docker Hub to build successfully
- Change `guydocker/minima:<feature>` to `guydocker/minima` and run
`./deploy-homepage.sh`
- Delete `feature` branch locally and in Github
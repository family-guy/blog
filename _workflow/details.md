---
title: Details
order: 2
---

### i. Containerisation on virtual machine

#### -- Make changes locally

- Pull down `gce` branch
- Create and checkout `feature` branch
- `jekyll serve` or `jekyll serve --host=0.0.0.0` if serving from guest machine
- Make changes
- Test changes by checking `localhost:4000` or `<ipaddress>:4000` in browser
- Commit changes
- Back to fourth step; repeat

#### -- Test Docker build locally

- Checkout `feature` branch
- Build docker image; from project root `sudo docker build --tag=<name>:<tag> .`
- Run docker image `sudo docker run --detach --publish=80:80 --name=<containername> <imagename:imagetag>`
- Check `localhost` or `<ipaddress>` in browser

#### -- If checks pass

- Stop container `sudo docker container stop <containername>`
- Delete container `sudo docker container rm <containername>`

#### -- Deploy to production

- Push up `feature` branch
- Wait for image in Docker Hub to build successfully
- SSH into GCE instance
- In `deploy-homepage.sh`, replace `guydocker/minima:gce` with
`guydocker/minima:<feature>`
- From home directory `./deploy-homepage.sh`
- Test changes by checking `<production-domain>` in browser
- If test fails, roll back by changing `guydocker/minima:<feature>`
to `guydocker/minima:gce` and running `./deploy-homepage.sh`
- If test passes, merge `feature` into `gce` in Github
- Delete `feature` branch in Github
- Wait for image in Docker Hub to build successfully
- Change `guydocker/minima:<feature>` to `guydocker/minima:gce` and run
`./deploy-homepage.sh`
- Locally, on `master`, `git cherry-pick` changes
- Disable CircleCI webhook in Github
- Push up on `master`
- Locally, update `gce` and delete `feature`

### ii. Continuous deployment

- Disable Docker Hub webhook in Github
- Pull down `master` branch
- Create and checkout `feature` branch
- `jekyll serve` or `jekyll serve --host=0.0.0.0` if serving from guest machine
- Make changes
- Test changes by checking `localhost:4000` or `<ipaddress>:4000` in browser
- Commit changes
- Back to fourth step; repeat
- If changes ok, push up on `feature`
- Create pull request in Github
- Final check, merge into `master`, delete `feature`
- Wait for CircleCI builds to complete
- Check `<production-domain>` in browser
- If not ok, rollback by reverting on `master`
- Else locally, on `gce`, `git cherry-pick` changes
- Push up on `gce`
- Locally, update `master` and delete `feature`

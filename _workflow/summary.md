---
title: Summary
order: 1
---

Only one branch, `master`.

- Pull down from `master`.
- Edit files locally.
- `jekyll serve`
- Check `localhost:4000` in browser
- Build docker image, from the project root `sudo docker build --tag=<name>:<version> .`
- Run docker image `sudo docker run --detach --name=<container-name> <image-tag>
- Check output in browser `localhost`

Only one branch, master. Pull down from master, make changes, test locally by running jekyll serve, check output in browser. Then build docker image, run docker image, check in browser. If all ok, then push to master
---
title: Mac OS X
order: 1
---

#### Step 1

Install Vagrant with an Ubuntu box and fsnotify

#### Step 2

Clone the git repository onto the host machine and the guest machine (latter requires SSH-ing into the Vagrant box).

#### Step 3

Get the Vagrant box's IP address

{% highlight bash %}
grep -nrw 'ip' Vagrantfile
{% endhighlight %}

#### Step 4

Sync the newly-cloned git repositories on the guest and host machines by adding

```
config.vm.synced_folder "/path/to/host-machine/repo", "/path/to/guest-machine/repo", fsnotify: true
```

to `Vagrantfile`.

This allows editing source files with your editor of choice on the host machine and for changes to take effect without restarting the server (but with refreshing the web page). If your editor of choice is Vim (or another that runs in a shell) you can skip this step ;)

#### Step 5

Exit the SSH session, restart the Vagrant box

{% highlight bash %}
vagrant reload --provision <vagrantboxid>
{% endhighlight %}

and start a new SSH session. 

#### Step 6

Install Jekyll. From the project root,

{% highlight bash %}
jekyll serve --host=0.0.0.0
{% endhighlight %}

Go in the browser to 

```
<ipaddress>:4000
```

replacing <<ipaddress>> with the one obtained in Step 3. You should see the app load.

#### Step 7

Install Docker so that you can check locally the deployment process (hence the use of Vagrant, Docker being easier to run on Linux than Mac OS X).

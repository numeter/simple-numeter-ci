simple-numeter-ci
==================

How to setup a simple ci for numeter project in 15 min with jenkins and vagrant

Setup numeter CI
=================

    wget -q -O - http://pkg.jenkins-ci.org/debian/jenkins-ci.org.key | sudo apt-key add -
    echo 'deb http://pkg.jenkins-ci.org/debian binary/' >> /etc/apt/sources.list
    apt-get update
    apt-get install vagrant jenkins nginx

Add your vagrant box :

    vagrant box add debian-7 http://puppet-vagrant-boxes.puppetlabs.com/debian-70rc1-x64-vbox4210-nocm.box

Config nginx
-------------

    vim /etc/nginx/sites-enabled/default

```
server {
        location / {
        proxy_redirect     off;
        proxy_set_header   X-FORWARDED_PROTO http;
        proxy_set_header   Host              $host;
        proxy_set_header   X-Real-IP         $remote_addr;
        proxy_pass http://localhost:8080;
        }
}
```

Config jenkins
----------------

Go to : http://myjenkins

**Installation du plugin pipeline**

  * http://myjenkins/pluginManager/available : Delivery Pipeline Plugin

**Donner le droit en sudo pour jenkins :**

```
visudo
```

```
jenkins ALL = NOPASSWD: /usr/bin/vagrant
```

**Ajouter les jobs numeter :**

    cd /var/lib/jenkins/ && git clone https://github.com/shaftmx/simple-numeter-ci jobs

Go on your jenkins web interface and refresh config from disk :

  * http://myjenkins/manage#

**Exemple de job jenkins :**

Start
```
cd /home/vagrant/ && sudo /usr/bin/vagrant destroy -f from_source
cd /home/vagrant/ && sudo /usr/bin/vagrant up from_source
```

Task
```
cd /home/vagrant/ && sudo /usr/bin/vagrant ssh from_source -c "sudo apt-get update; sudo apt-get install -y git"
cd /home/vagrant/ && sudo /usr/bin/vagrant ssh from_source -c "git clone https://github.com/enovance/numeter"
cd /home/vagrant/ && sudo /usr/bin/vagrant ssh from_source -c "sudo numeter/extra/sources_test.sh --go"
```

Stop
```
cd /home/vagrant/ && sudo /usr/bin/vagrant destroy -f from_source
```

Vagrant memo
-------------

```
vagrant up boxname
vagrant halt boxname
vagrant ssh boxname
vagrant destroy boxname
```

**In case you want to use lxc insted virtualbox :**

    * https://github.com/fgrehm/vagrant-lxc
    * https://github.com/fgrehm/vagrant-lxc/wiki/Usage-on-debian-hosts

Add this in env vars of your jenkins user :

    export VAGRANT_DEFAULT_PROVIDER=lxc

In case you are using sudo to launch vagrant commandes :

    Defaults env_keep +="VAGRANT_DEFAULT_PROVIDER"



simple-numeter-ci
=================

How to setup a simple ci for numeter project in 15 min with jenkins and vagrant
#wiki

====== Setup numeter CI ======
  wget -q -O - http://pkg.jenkins-ci.org/debian/jenkins-ci.org.key | sudo apt-key add -
  echo 'deb http://pkg.jenkins-ci.org/debian binary/' >> /etc/apt/sources.list
  apt-get update
  apt-get install vagrant jenkins nginx

Config nginx
<code>
vim /etc/nginx/sites-enabled/default
server {
        location / {
        proxy_redirect     off;
        proxy_set_header   X-FORWARDED_PROTO http;
        proxy_set_header   Host              10.66.6.205:80;
        proxy_set_header   X-Real-IP         $remote_addr;
        proxy_pass http://localhost:8080;
        }
}
</code>

Go to : http://10.66.6.205

**Installation du plugin pipeline**

  * http://10.66.6.205/pluginManager/available : Delivery Pipeline Plugin

**Donner le droit en sudo pour jenkins :**
  usermod -a -G sudo jenkins
  jenkins ALL = NOPASSWD: /usr/bin/vagrant

**Ajouter les jobs numeter :**
  cd /var/lib/jenkins/ && git clone http://...../ jobs

**Exemple de job jenkins :**

Start
<code bash>
cd /home/vagrant/ && sudo /usr/bin/vagrant destroy -f from_source
cd /home/vagrant/ && sudo /usr/bin/vagrant up from_source
</code>

Task
<code bash>
cd /home/vagrant/ && sudo /usr/bin/vagrant ssh from_source -c "sudo apt-get update; sudo apt-get install -y git"
cd /home/vagrant/ && sudo /usr/bin/vagrant ssh from_source -c "git clone https://github.com/enovance/numeter"
cd /home/vagrant/ && sudo /usr/bin/vagrant ssh from_source -c "sudo numeter/extra/sources_test.sh --go"
</code>

Stop
<code bash>
cd /home/vagrant/ && sudo /usr/bin/vagrant destroy -f from_source
</code>

**Exemple de deploiement et test de box vagrant**

Mettre en place ma box vagrant :
  mkdir vagrant_config
  vagrant box add debian-7 http://puppet-vagrant-boxes.puppetlabs.com/debian-70rc1-x64-vbox4210-nocm.box
  vagrant init debian-7

Tester la box :
  vagrant up
  vagrant ssh
  vagrant destroy


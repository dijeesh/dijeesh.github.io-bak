---
layout: post
title: Install and Configure Gitlab CE Server in AWS
description: Install and Configure Private Gitlab CE Server in AWS
#image: /assets/media/Lamp.jpg
Categories: [DevOPS]
tags: [devops, sysops, gitlab-ce, git, centos7, devops, dijeeshpnair ]
comments: true
---

Introduction
-------------
Hey there, this is the first part of my blog series `Getting started with CI/CD on AWS platform using GitLab CE`


<br>

GitLab Community Edition (CE) is an open source end-to-end software development platform with built-in version control, issue tracking, code review, CI/CD and much more, check [official documentation](https://gitlab.com/gitlab-org/gitlab-ce) for more information.  Due to security considerations, we have provisioned our own private GitLab-CE server for many of our clients builds even though gitlab.com hosted solution and many pre-configured AMIs are available in AWS. Here are the steps to set-up a Gitlab-CE server.



<br>

Provision EC2 Instance
-------------------

- Provision a t2.medium instance [ CentOS 7 AMI ]

- Open HTTP, HTTPS and SSH connections in Security Group settings

- Enable basic monitoring for the EC2 instance ( StatusCheckFailed )

-  Assign an EIP



<br>

  Basic System setup
-------------------

- Install system updates
```
  yum update -y
```

- Disable SELinux
```
  sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
```

- Install basic system management tools
```
  yum install -y net-tools wget curl lsof screen ntp dstat
```

- Disable SSH Password Authentification
```
  sed -i 's/PasswordAuthentication\ yes/PasswordAuthentication\ no/g' /etc/ssh/sshd_config
```

- Set static system hostname
```
  hostnamectl set-hostname nr-oprs-db-002.dc4.sea.networkredux.net
  hostnamectl set-hostname "nr-oprs-db-002.dc4.sea.networkredux.net" --pretty
  hostnamectl set-hostname nr-oprs-db-002.dc4.sea.networkredux.net --static
  hostnamectl set-hostname nr-oprs-db-002.dc4.sea.networkredux.net --transient
```

- Update /etc/hosts
```
  grep $(hostname) /etc/hosts || echo -e "127.1.0.1\t$(hostname)\t$(hostname -s)" >> /etc/hosts
```

- Restart EC2 instance.
```
  reboot
```



<br>

  Install GitLab CE
-------------------

- Install dependencies
```
  yum install -y curl policycoreutils-python openssh-server
  systemctl enable sshd
  systemctl start sshd
```

- Install and configure Postfix MTA
```
  yum install postfix
  systemctl enable postfix
  systemctl start postfix
```

- Add the GitLab package repository
```
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
```

-  Install Gitlab CE
```
sudo EXTERNAL_URL="http://gitlab.yourdomain.com" yum install -y gitlab-ce
```

- Complete the installation

- Navigate your browser to http://gitlab.yourdomain.com and you will be redirected to reset root password for your installation.



<br>

GitLab CE Initial setup
-------------------
- Go to http://gitlab.yourdomain.com and login to GitLab using the root credentials you created.

- Go to profile settings and setup root user's Email account

- Go to Admin area -  Settings - Sign-up Restrictions and uncheck Sign-up enabled

- Go to Admin area - Overview: create groups, create users, add users into groups.





<br>

GitLab CE Secure using LetsEncrypt Certificates
-------------------
Let's secure our GitLab instance using Free LetsEncrypt SSL Certificates

- Install epel repository
```
  yum install epel-release -y
```

- Install Certbot
```
  yum install certbot -y
```

- Create directory for LetsEncrypt verification files
```
  mkdir -p /var/www/public/letsencrypt
```

- Update Gitlab Nginx Configuration
```
  Edit /etc/gitlab/gitlab.rb and in GitLab NGINX add following line

  nginx['custom_gitlab_server_config'] = "location ^~ /.well-known { root /var/www/public/letsencrypt; }"
```

- Reconfigure GitLab
```
  gitlab-ctl reconfigure
```

- Request SSL Certificates
```
  certbot certonly --webroot --webroot-path=/var/www/public/letsencrypt -d gitlab.yourdomain.com
```

- Update Gitlab Nginx Configuration
```
  Edit /etc/gitlab/gitlab.rb and
```

```
1 Update external_url to use https
  external_url 'https://gitlab.yourdomain.com'

2 Update redirect_http_to_https settings and set to true
  nginx['redirect_http_to_https'] = true

3 Specify SSL Certificates
  nginx['ssl_certificate'] = "/etc/letsencrypt/live/gitlab.yourdomain.com/fullchain.pem"
  nginx['ssl_certificate_key'] = "/etc/letsencrypt/live/gitlab.yourdomain.com/privkey.pem"
```


- Reconfigure GitLab
```
gitlab-ctl reconfigure
```

- Setup cronjob for SSL renewal
```
  crontab -e
  0 2 1 * * root /usr/bin/certbot renew --quiet --renew-hook "/usr/bin/gitlab-ctl restart nginx"
```


<br><br><br>
Alright, now we have our GitLab instance up and running.
<br>
- [x] Provisioned EC2 Instance
- [x] Installed GitLab CE
- [x] Configured Users and Groups
- [x] Secured GitLab using LetsEncrypt SSL Certificates
- [x] Enabled auto renewal for LetsEncrypt SSL Certificates
<br><br><br>




Happy Coding !!

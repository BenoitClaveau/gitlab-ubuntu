# GitLab installation on Ubuntu 16.04

#### Update ubuntu

sudo apt-get update && sudo apt-get upgrade

#### Install

[https://about.gitlab.com/downloads/#ubuntu1604](https://about.gitlab.com/downloads/#ubuntu1604)

* uninstall postfix

```shell
sudo apt-get purge postfix
```

* postfix command

```shell
sudo nano /etc/postfix/main.cf
sudo /etc/init.d/postfix restart
```

#### Configure

[https://docs.gitlab.com/ce/administration/troubleshooting/debug.html](https://docs.gitlab.com/ce/administration/troubleshooting/debug.html)

```shell
sudo nano /etc/gitlab/gitlab.rb
```

* Modify external_url to http:olmo.izzili.com
* Uncomment smtp [https://docs.gitlab.com/omnibus/settings/smtp.html](https://docs.gitlab.com/omnibus/settings/smtp.html)

```shell
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
```

#### Test configuration

```shell
sudo gitlab-rails console production
```

```shell
ActionMailer::Base.delivery_method
=> :smtp
```

```shell
ActionMailer::Base.smtp_settings
=> {:address=>"localhost", :port=>25, :domain=>"localhost.localdomain", :user_name=>nil, :password=>nil, :authentication=>nil, :enable_starttls_auto=>true}
```

Send a test message via the console.
```shell
Notify.test_email('youremail@email.com', 'Hello World', 'This is a test message').deliver_now
```

#### First connection

login: root
password: <new password>

### Test mail


```shell
sudo apt-get install mailutils
```

* Create user

login: root
password: <new password>

```shell
sudo useradd -m -s /bin/bash incoming
```

## GitLab Runner 

### Install

[https://docs.gitlab.com/runner/install/linux-repository.html](https://docs.gitlab.com/runner/install/linux-repository.html)

### Commands

[https://gitlab.com/gitlab-org/gitlab-ci-multi-runner/blob/master/docs/commands/README.md](https://gitlab.com/gitlab-org/gitlab-ci-multi-runner/blob/master/docs/commands/README.md)



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

* Create a runner

```shell
sudo gitlab-ci-multi-runner register
```

* Start as a service

```shell
sudo gitlab-ci-multi-runner install
sudo gitlab-ci-multi-runner start
```

### Commands

[https://gitlab.com/gitlab-org/gitlab-ci-multi-runner/blob/master/docs/commands/README.md](https://gitlab.com/gitlab-org/gitlab-ci-multi-runner/blob/master/docs/commands/README.md)

## Mattermost

* Uncomment mattermost_external_url

### Start & Stop

```shell
sudo gitlab-ctl stop mattermost
sudo gitlab-ctl start mattermost
```

### log

```shell
sudo tail /var/log/gitlab/mattermost/mattermost.log
```

### configuration

[https://docs.mattermost.com/administration/config-settings.html](https://docs.mattermost.com/administration/config-settings.html)

```shell
http://mattermost.exemple.com:8065

sudo -u mattermost /opt/gitlab/embedded/bin/mattermost --config=/var/opt/gitlab/mattermost/config.json
```

### 502 Bad Gateway

#### Create a batch to upgrate Mattermost

```shell
sudo nano upgrade_mattermost
```

```shell
# /bin/bash

declare -a versions=("1.0.0" "1.1.0" "1.2.1" "1.3.0" "1.4.0" "2.0.0" "2.1.0" "2.2.0")

for v in "${versions[@]}"
do
     wget https://releases.mattermost.com/${v}/mattermost-team-${v}-linux-amd64.tar.gz -O - | tar -xz
     gitlab-ctl stop mattermost
     rm /opt/gitlab/embedded/bin/mattermost
     cp mattermost/bin/platform /opt/gitlab/embedded/bin/mattermost
     rm /var/opt/gitlab/mattermost/config.json
     cp mattermost/config/config.json /var/opt/gitlab/mattermost/config.json
     gitlab-ctl reconfigure
     gitlab-ctl start mattermost
done
```


```shell
sudo chmod +x upgrade_mattermost
```

```shell
sudo bash upgrade_mattermost
```


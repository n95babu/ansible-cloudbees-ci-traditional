# start-here

## Prerequisites

* vagrant
* add the following entries to your local `/etc/hosts`:

```
192.168.32.11 oc
192.168.32.13 cm1
192.168.32.15 agent1
```

## Startup

* `vagrant up`

## Install Operations Center

* `vagrant ssh oc`
* `sudo su -`
* `yum -y install wget`
* create `/etc/security/limits.d/30-cloudbees.conf`
  * https://support.cloudbees.com/hc/en-us/articles/222446987-Prepare-Jenkins-for-Support

```
cloudbees-core-oc soft core unlimited
cloudbees-core-oc hard core unlimited
cloudbees-core-oc soft fsize unlimited
cloudbees-core-oc hard fsize unlimited
cloudbees-core-oc soft nofile 4096
cloudbees-core-oc hard nofile 8192
cloudbees-core-oc soft nproc 30654
cloudbees-core-oc hard nproc 30654
```

* setup firewalld
  * `systemctl enable firewalld`
  * `systemctl start firewalld`
  * `firewall-cmd --permanent --add-port=22/tcp`
  * `firewall-cmd --permanent --add-port=8888/tcp`
  * `firewall-cmd --permanent --add-port=50000/tcp`
  * `firewall-cmd --reload`
  
*  add the following entries to `/etc/hosts`:

```
192.168.32.11 oc
192.168.32.13 cm1
192.168.32.15 agent1
```


* https://adoptopenjdk.net/installation.html#linux-pkg

```
cat <<'EOF' > /etc/yum.repos.d/adoptopenjdk.repo
[AdoptOpenJDK]
name=AdoptOpenJDK
baseurl=http://adoptopenjdk.jfrog.io/adoptopenjdk/rpm/centos/$releasever/$basearch
enabled=1
gpgcheck=1
gpgkey=https://adoptopenjdk.jfrog.io/adoptopenjdk/api/gpg/key/public
EOF
```

* repo to get latest version Git
  * `yum -y install https://packages.endpoint.com/rhel/7/os/x86_64/endpoint-repo-1.7-1.x86_64.rpm`

* Operations Center repo
  * `wget -O /etc/yum.repos.d/cloudbees-core-oc.repo https://downloads.cloudbees.com/cloudbees-core/traditional/operations-center/rolling/rpm/cloudbees-core-oc.repo`
  * `rpm --import https://downloads.cloudbees.com/cloudbees-core/traditional/operations-center/rolling/rpm/cloudbees.com.key`

* create directories
  * `mkdir -p /var/cache/cloudbees-core-oc/tmp`
  * `mkdir -p /var/cache/cloudbees-core-oc/heapdumps`

* `yum remove git*`
* `yum -y install adoptopenjdk-8-hotspot git cloudbees-core-oc fontconfig`
* edit `/etc/sysconfig/cloudbees-core-oc`
  * `JENKINS_JAVA_OPTIONS="-Djava.awt.headless=true -Dcom.cloudbees.jenkins.ha=false -Djava.io.tmpdir=/var/cache/cloudbees-core-oc/tmp/ -Dcb.BeekeeperProp.noFullUpgrade=true -Dorg.apache.commons.jelly.tags.fmt.timeZone=America/New_York -Duser.timezone=America/New_York"`
  * `JENKINS_ARGS="--pluginroot=/var/cache/cloudbees-core-oc/plugins"`
* `chown -R cloudbees-core-oc:cloudbees-core-oc /var/cache/cloudbees-core-oc`
* `systemctl start cloudbees-core-oc`
* `tail -f /var/log/cloudbees-core-oc/cloudbees-core-oc.log`

## Setup Operations Center UI

* open http://oc:8888/
* vagrant ssh oc
  * `sudo cat /var/lib/cloudbees-core-oc/secrets/initialAdminPassword`
* Request a trial license
* Install suggested plugins
* Create First Admin User
* Jenkins URL: http://oc:8888/
* Click Start using CloudBees CI Operations Center
* http://oc:8888/restart

## Create cm1 Client Controller object on Operations Center

* `New Item`
  * Item Name: `controllers`
  * Type: `Folder`
* `New Item`
  * Item Name: `cm1`
  * Type: `Client Master`
* Stop once you see "Connection Details"

## Install Client Controller

* `vagrant ssh cm1`
* `sudo su -`
* `yum -y install wget`
* create `/etc/security/limits.d/30-cloudbees.conf`
  * https://support.cloudbees.com/hc/en-us/articles/222446987-Prepare-Jenkins-for-Support

```
cloudbees-core-cm soft core unlimited
cloudbees-core-cm hard core unlimited
cloudbees-core-cm soft fsize unlimited
cloudbees-core-cm hard fsize unlimited
cloudbees-core-cm soft nofile 4096
cloudbees-core-cm hard nofile 8192
cloudbees-core-cm soft nproc 30654
cloudbees-core-cm hard nproc 30654
```

* setup firewalld
  * `systemctl enable firewalld`
  * `systemctl start firewalld`
  * `firewall-cmd --permanent --add-port=22/tcp`
  * `firewall-cmd --permanent --add-port=8080/tcp`
  * `firewall-cmd --reload`
  
*  add the following entries to `/etc/hosts`:

```
192.168.32.11 oc
192.168.32.13 cm1
192.168.32.15 agent1
```


* https://adoptopenjdk.net/installation.html#linux-pkg

```
cat <<'EOF' > /etc/yum.repos.d/adoptopenjdk.repo
[AdoptOpenJDK]
name=AdoptOpenJDK
baseurl=http://adoptopenjdk.jfrog.io/adoptopenjdk/rpm/centos/$releasever/$basearch
enabled=1
gpgcheck=1
gpgkey=https://adoptopenjdk.jfrog.io/adoptopenjdk/api/gpg/key/public
EOF
```

* repo to get latest version Git
  * `yum -y install https://packages.endpoint.com/rhel/7/os/x86_64/endpoint-repo-1.7-1.x86_64.rpm`

* Client Controller repo
  * `wget -O /etc/yum.repos.d/cloudbees-core-cm.repo https://downloads.cloudbees.com/cloudbees-core/traditional/client-master/rolling/rpm/cloudbees-core-cm.repo`
  * `rpm --import https://downloads.cloudbees.com/cloudbees-core/traditional/client-master/rolling/rpm/cloudbees.com.key`

* create directories
  * `mkdir -p /var/cache/cloudbees-core-cm/tmp`
  * `mkdir -p /var/cache/cloudbees-core-cm/heapdumps`

* `yum remove git*`
* `yum -y install adoptopenjdk-8-hotspot git cloudbees-core-cm fontconfig`
* edit `/etc/sysconfig/cloudbees-core-cm`
  * `JENKINS_JAVA_OPTIONS="-Djava.awt.headless=true -Dcom.cloudbees.jenkins.ha=false -Djava.io.tmpdir=/var/cache/cloudbees-core-cm/tmp/ -Dcb.BeekeeperProp.noFullUpgrade=true -Dorg.apache.commons.jelly.tags.fmt.timeZone=America/New_York -Duser.timezone=America/New_York"`
  * `JENKINS_ARGS="--pluginroot=/var/cache/cloudbees-core-cm/plugins"`
* `chown -R cloudbees-core-cm:cloudbees-core-cm /var/cache/cloudbees-core-cm`
* `systemctl start cloudbees-core-cm`
* `tail -f /var/log/cloudbees-core-cm/cloudbees-core-cm.log`

## Setup Client Controller UI

* open http://cm1:8080/
* vagrant ssh cm1
  * `sudo cat /var/lib/cloudbees-core-cm/secrets/initialAdminPassword`
* Click `Join a CloudBees CI Operations Center`
  * Copy the Connection Details value from the Operations Center and paste in the Connection details field then click Submit
* Install suggested plugins
* Create First Admin User
* Click Start using CloudBees CI Client Master
* http://cm1:8080/restart

## Install Agent

* `vagrant ssh agent1`
* `sudo su -`
* setup firewalld
  * `systemctl enable firewalld`
  * `systemctl start firewalld`
  * `firewall-cmd --permanent --add-port=22/tcp`
  * `firewall-cmd --reload`

*  add the following entries to `/etc/hosts`:

```
192.168.32.11 oc
192.168.32.13 cm1
192.168.32.15 agent1
```
* https://adoptopenjdk.net/installation.html#linux-pkg

```
cat <<'EOF' > /etc/yum.repos.d/adoptopenjdk.repo
[AdoptOpenJDK]
name=AdoptOpenJDK
baseurl=http://adoptopenjdk.jfrog.io/adoptopenjdk/rpm/centos/$releasever/$basearch
enabled=1
gpgcheck=1
gpgkey=https://adoptopenjdk.jfrog.io/adoptopenjdk/api/gpg/key/public
EOF
```

* repo to get latest version Git
  * `yum -y install https://packages.endpoint.com/rhel/7/os/x86_64/endpoint-repo-1.7-1.x86_64.rpm`

* `yum remove git*`
* `yum -y install adoptopenjdk-8-hotspot git fontconfig wget`
* install Docker and unzip (https://docs.docker.com/engine/install/centos/)
  * `yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine`
  * `yum -y install yum-utils`
  * `yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo`
  * `yum -y install docker-ce docker-ce-cli containerd.io unzip`
  * `groupadd docker`
  * `systemctl enable docker`
  * `systemctl start docker`
  * `exit`
  * `sudo usermod -aG docker $USER`
  * `exit`
  * `vagrant ssh agent1`
  * `docker run hello-world`
  * `sudo su -`
* install Maven
  * `mkdir -p /opt/tools/maven`
  * `cd /opt/tools/maven`
  * `wget https://mirrors.sonic.net/apache/maven/maven-3/3.8.1/binaries/apache-maven-3.8.1-bin.tar.gz`
  * `tar zxvf apache-maven-3.8.1-bin.tar.gz`
  * `rm -f apache-maven-3.8.1-bin.tar.gz`
  * `ln -s apache-maven-3.8.1 latest`
* install Gradle
  * `mkdir -p /opt/tools/gradle`
  * `cd /opt/tools/gradle`
  * `wget https://services.gradle.org/distributions/gradle-6.7.1-bin.zip`
  * `unzip gradle-6.7.1-bin.zip`
  * `rm -f gradle-6.7.1-bin.zip`
  * `ln -s gradle-6.7.1 latest`
* `echo "PATH=/opt/tools/gradle/latest/bin:\$PATH" > /etc/profile.d/gradle.sh`
* `echo "PATH=/opt/tools/maven/latest/bin:\$PATH" > /etc/profile.d/maven.sh`
* `chown -R vagrant:vagrant /opt/tools`

## Configure Global Security settings on Operations Center

* `Manage Jenkins` -> `Security` -> `Configure Global Security`
* `Connected master on-master executors`
  * check `Enforce`
  * uncheck `Allow per-master overrides`
  * set `# of executors` to 0
* `Client master security`
  * select `Single Sign-On (security realm and authorization strategy)` for `Security Setting Enforcement`

## Connect agent to Client Controller

* connect agent

## Create test job on Client Controller

```
pipeline {
  agent {label "linux"}
  stages {
    stage("Hello") {
      steps {
        sh """
          mvn --version
          gradle --version
          docker info
        """
      }
    }
  }
}
```

## Cleanup

* `vagrant halt; vagrant destroy -f`
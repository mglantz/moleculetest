# moleculetest
Molecule / Jenkins demo on Red Hat Enterprise Linux 8

## Install Jenkins and prerequisites

Install required software / repos and configure firewall
```
# Setup repositories, we need EPEL to get daemonize, a Jenkins dependency
dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
wget http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo -O /etc/yum.repos.d/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat/jenkins.io.key

# Install Jenks and dependencies
dnf install -y java-11-openjdk-devel jenkins

# Enable firewall opening
firewall-cmd --permanent --add-port=8080/tcp
firewall-cmd --reload

# Setup Jenkins user for rootless containers
usermod --add-subuids 3000000-3200000 jenkins
usermod --add-subgids 3000000-3200000 jenkins

# Install Ansible, Podman and dependencies
dnf install -y podman ansible python3 python3-virtualenv gcc git
```

## Configure Jenkins
* Go to https://hostname:8080, install recommended plugins (and sometimes you need to restart jenkins after that).
* Add New Item, name-it-like-this or likethis not 'like this'. Type: Pipeline.  Scroll down to pipeline script and paste in the content of this file: https://raw.githubusercontent.com/mglantz/moleculetest/main/Jenkinsfile And run.
* Done
![Complete pipeline](jenkins-molecule.png)

## Installing a worker node
So, running jobs on your main Jenkins node is bad in general due to security concerns.
If you want to prep a worker to run on. Here's what you need to do to prep it.

```
dnf install -y java-11-openjdk-devel podman python3 python3-virtualenv gcc git
```

When you are done, setup credentials in Jenkins, add the node and change the agent section of the pipeline to:
```
  agent {
    label 'Molecule_WorkerNode'
  }
```

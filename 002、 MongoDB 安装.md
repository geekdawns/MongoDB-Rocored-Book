###  002、 MongoDB 安装

#### 环境

- CentOS 6.5

#### 官方手册

[Red Hat](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/)

Install MongoDB Community Edition on Red Hat Enterprise or CentOS Linux

On this page

- [Overview](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/#overview)
- [Packages](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/#packages)
- [Install MongoDB Community Edition](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/#install-mongodb-community-edition)
- [Run MongoDB Community Edition](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/#run-mongodb-community-edition)
- [Uninstall MongoDB Community Edition](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/#uninstall-mongodb-community-edition)

#### Overview

Use this tutorial to install MongoDB Community Edition on Red Hat Enterprise Linux or CentOS Linux versions 6 and 7 using .rpm packages. While Red Hat includes its own MongoDB packages, use the official MongoDB Community Edition packages to ensure that you have the latest release.

PLATFORM SUPPORT

This installation guide only supports 64-bit systems. See [*Platform Support*](https://docs.mongodb.com/manual/release-notes/3.0-compatibility/#compatibility-platform-support) for details.

MongoDB 3.2 deprecates support for Red Hat Enterprise Linux 5.

#### Packages

MongoDB provides officially supported packages in their own repository. This repository contains the following packages:

| mongodb-org        | A metapackage that will automatically install the four component packages listed below. |
| ------------------ | ---------------------------------------- |
| mongodb-org-server | Contains the [mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) daemon and associated configuration and init scripts. |
| mongodb-org-mongos | Contains the [mongos](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos) daemon. |
| mongodb-org-shell  | Contains the [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell. |
| mongodb-org-tools  | Contains the following MongoDB tools: [mongoimport](https://docs.mongodb.com/manual/reference/program/mongoimport/#bin.mongoimport) [bsondump](https://docs.mongodb.com/manual/reference/program/bsondump/#bin.bsondump), [mongodump](https://docs.mongodb.com/manual/reference/program/mongodump/#bin.mongodump),[mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/#bin.mongoexport), [mongofiles](https://docs.mongodb.com/manual/reference/program/mongofiles/#bin.mongofiles), [mongooplog](https://docs.mongodb.com/manual/reference/program/mongooplog/#bin.mongooplog), [mongoperf](https://docs.mongodb.com/manual/reference/program/mongoperf/#bin.mongoperf), [mongorestore](https://docs.mongodb.com/manual/reference/program/mongorestore/#bin.mongorestore),[mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/#bin.mongostat), and [mongotop](https://docs.mongodb.com/manual/reference/program/mongotop/#bin.mongotop). |

The mongodb-org-server package provides an initialization script that starts [mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) with the/etc/mongod.conf configuration file.

See [Run MongoDB Community Edition](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/#run-mongodb-community-edition) for details on using this initialization script.

The default /etc/mongod.conf configuration file supplied by the packages have bind_ip set to127.0.0.1 by default. Modify this setting as needed for your environment before initializing a [*replica set*](https://docs.mongodb.com/manual/reference/glossary/#term-replica-set).

#### Install MongoDB Community Edition

NOTE

To install a version of MongoDB prior to 3.2, please refer to that version’s documentation. For example, see version [3.0](https://docs.mongodb.com/v3.0/tutorial/install-mongodb-on-red-hat/).

This installation guide only supports 64-bit systems. See [*Platform Support*](https://docs.mongodb.com/manual/release-notes/3.0-compatibility/#compatibility-platform-support) for details.

##### Configure the package management system (yum).

Create a `/etc/yum.repos.d/mongodb-org-3.4.repo` file so that you can install MongoDB directly, using yum.

Changed in version 3.0: MongoDB Linux packages are in a new repository beginning with 3.0.

###### For the *latest* stable release of MongoDB

Use the following repository file:

```
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```

###### For versions of MongoDB *earlier* than 3.0

To install the packages from an earlier [*release series*](https://docs.mongodb.com/manual/release-notes/#release-version-numbers), such as 2.4 or 2.6, you can specify the release series in the repository configuration. For example, to restrict your system to the 2.6 release series, create a /etc/yum.repos.d/mongodb-org-2.6.repo file to hold the following configuration information for the MongoDB 2.6 repository:

```
[mongodb-org-2.6]
name=MongoDB 2.6 Repository
baseurl=http://downloads-distro.mongodb.org/repo/redhat/os/x86_64/
gpgcheck=0
enabled=1

```

You can find .repo files for each release [in the repository itself](https://repo.mongodb.org/yum/redhat/). Remember that odd-numbered minor release versions (e.g. 2.5) are development versions and are unsuitable for production use.

##### Install the MongoDB packages and associated tools.

To install the latest stable version of MongoDB, issue the following command:

```
sudo yum install -y mongodb-org

```

#### Run MongoDB Community Edition

###### 配置SELinux

IMPORTANT

If you are using SELinux, you must configure SELinux to allow MongoDB to start on Red Hat Linux-based systems (Red Hat Enterprise Linux or CentOS Linux).

To configure SELinux, administrators have three options:

- If SELinux is in enforcing mode, enable access to the relevant ports that the MongoDB deployment will use (e.g. 27017). See [*Default MongoDB Port*](https://docs.mongodb.com/manual/reference/default-mongodb-port/) for more information on MongoDB’s default ports. For default settings, this can be accomplished by running

  ```
  semanage port -a -t mongod_port_t -p tcp 27017

  ```

- Disable SELinux by setting the SELINUX setting to disabled in /etc/selinux/config.

  ```
  SELINUX=disabled

  ```

  You must reboot the system for the changes to take effect.

- Set SELinux to permissive mode in /etc/selinux/config by setting the SELINUX setting topermissive.

  ```
  SELINUX=permissive

  ```

  You must reboot the system for the changes to take effect.

  You can instead use setenforce to change to permissive mode. setenforce does not require a reboot but is **not** persistent.

Alternatively, you can choose not to install the SELinux packages when you are installing your Linux operating system, or choose to remove the relevant packages. This option is the most invasive and is not recommended.

##### Data Directories and Permissions

WARNING

On RHEL 7.0, if you change the data path, the *default* SELinux policies will prevent [mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) from having write access on the new data path if you do not change the security context.

The MongoDB instance stores its data files in /var/lib/mongo and its log files in /var/log/mongodbby default, and runs using the mongod user account. You can specify alternate log and data file directories in/etc/mongod.conf. See [systemLog.path](https://docs.mongodb.com/manual/reference/configuration-options/#systemLog.path) and [storage.dbPath](https://docs.mongodb.com/manual/reference/configuration-options/#storage.dbPath) for additional information.

If you change the user that runs the MongoDB process, you **must** modify the access control rights to the/var/lib/mongo and /var/log/mongodb directories to give this user access to these directories.

#### Procedure

##### 开启 MongoDB.

You can start the [mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) process by issuing the following command:

```
sudo service mongod start

```

##### 验证MongoDB是否已经成功被启动

You can verify that the [mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) process has started successfully by checking the contents of the log file at /var/log/mongodb/mongod.log for a line reading

```
[initandlisten] waiting for connections on port <port>

```

where <port> is the port configured in /etc/mongod.conf, 27017 by default.

You can optionally ensure that MongoDB will start following a system reboot by issuing the following command:

```
sudo chkconfig mongod on

```

##### 停止MongoDB.

As needed, you can stop the [mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) process by issuing the following command:

```
sudo service mongod stop

```

##### 重启MongoDB.

You can restart the [mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) process by issuing the following command:

```
sudo service mongod restart

```

You can follow the state of the process for errors or important messages by watching the output in the/var/log/mongodb/mongod.log file.

##### 开始使用MongoDB.

To help you start using MongoDB, MongoDB provides [*Getting Started Guides*](https://docs.mongodb.com/manual/#getting-started) in various driver editions. See [*Getting Started*](https://docs.mongodb.com/manual/#getting-started) for the available editions.

Before deploying MongoDB in a production environment, consider the [*Production Notes*](https://docs.mongodb.com/manual/administration/production-notes/) document.

Later, to stop MongoDB, press Control+C in the terminal where the [mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) instance is running.

#### 卸载MongoDB Community Edition

To completely remove MongoDB from a system, you must remove the MongoDB applications themselves, the configuration files, and any directories containing data and logs. The following section guides you through the necessary steps.

WARNING

This process will *completely* remove MongoDB, its configuration, and *all* databases. This process is not reversible, so ensure that all of your configuration and data is backed up before proceeding.

##### 停止MongoDB.

Stop the [mongod](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) process by issuing the following command:

```
sudo service mongod stop
```

##### 删除包

Remove any MongoDB packages that you had previously installed.

```
sudo yum erase $(rpm -qa | grep mongodb-org)
```

##### 移除数据目录

Remove MongoDB databases and log files.

```
sudo rm -r /var/log/mongodb
sudo rm -r /var/lib/mongo
```
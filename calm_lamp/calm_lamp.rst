***********************
Calm Blueprint (LAMP)
***********************


Overview
********

.. note::

  This lab should be completed **AFTER** the :ref:`calm_mysql_lab` lab.

  Estimated time to complete: **40 MINUTES**

In this exercise you will extend the MySQL Blueprint created previously into a basic LAMP Stack (Linux Apache MySQL PHP) deployment with a scalable web tier as shown below.

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab2/image1.png

Creating the Web Server
***********************

From **Prism Central > Apps**, select **Blueprints** from the sidebar and select your Blueprint from the previous exercise.

In **Application Overview > Services**, click :fa:`plus-circle`.

Note **Service1** appears in the **Workspace** and the **Configuration Pane** reflects the configuration of the selected Service. You can rearrange the Service icons on the Workspace by clicking and dragging them.

Fill out the following fields:

- **Service Name** - APACHE_PHP
- **Name** - APACHE_PHP_AHV
- **Cloud** - Nutanix
- **OS** - Linux
- **VM Name** - APACHE_PHP
- **Image** - CentOS7-For-Calm
- **Device Type** - Disk
- **Device Bus** - SCSI
- Select **Bootable**
- **vCPUs** - 2
- **Cores per vCPU** - 1
- **Memory (GiB)** - 4
- Select :fa:`plus-circle` under **Network Adapters (NICs)**
- **NIC** - vlan.0
- **Credential** - CENTOS

Scroll to the top of the **Configuration Panel**, click **Package**.

Fill out the following fields:

- **Name** - APACHE_PHP_PACKAGE
- **Install Script Type** - Shell
- **Credential** - CENTOS

Copy and paste the following script into the **Install Script** field:

.. code-block:: bash

   #!/bin/bash
   set -ex
   # -*- Install httpd and php
   sudo yum update -y
   sudo yum -y install epel-release
   sudo rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
   sudo yum install -y httpd php56w php56w-mysql

   echo "<IfModule mod_dir.c>
           DirectoryIndex index.php index.html index.cgi index.pl index.php index.xhtml index.htm
   </IfModule>" | sudo tee /etc/httpd/conf.modules.d/dir.conf

   echo "<?php
   phpinfo();
   ?>" | sudo tee /var/www/html/info.php
   sudo systemctl restart httpd
   sudo systemctl enable httpd

Fill out the following fields:

- **Uninstall Script Type** - Shell
- **Credential** - CENTOS

Copy and paste the following script into the **Uninstall Script** field:

.. code-block:: bash

  #!/bin/bash
  echo "Goodbye!"

Click **Save**.

Adding Dependencies
===================

As our application will require the database to be running before the web server starts, our Blueprint requires a dependency to enforce this ordering.

In the **Workspace**, select the **APACHE_PHP** Service and click the **Create Dependency** icon that appears above the Service icon.

Select the **MySQL** Service. This will hold the execution of **APACHE_PHP** installation script until the **MySQL** Service is running.

Click **Save**.

In **Application Overview > Actions**, select **Create** to see the flow of execution after the dependency is added.

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab2/image11.png

Adding Replicas
===============

Calm makes it simple to add multiple copies of a given Service, which is helpful for scale out workloads such as web servers.

In the **Workspace**, select the **APACHE_PHP** Service.

In the **Configuration Pane**, select the **Service** tab.

Under **Deployment Config**, change the **Number of replicas** from 1 to 2.

Creating the Load Balancer
**************************

To take advantage of a scale out web tier our application needs to be able to load balance connections across multiple web server VMs. HAProxy is a free, open source TCP/HTTP load balancer used to distribute workloads across multiple servers. It can be used in small, simple deployments and large web-scale environments such as GitHub, Instagram, and Twitter.

In **Application Overview > Services**, click :fa:`plus-circle`.

Select **Service1** and fill out the following fields in the **Configuration Pane**:

- **Service Name** - HAProxy
- **Name** - HAPROXYAHV
- **Cloud** - Nutanix
- **OS** - Linux
- **VM Name** - HAProxy
- **Image** - CentOS7-For-Calm
- **Device Type** - Disk
- **Device Bus** - SCSI
- Select **Bootable**
- **vCPUs** - 2
- **Cores per vCPU** - 1
- **Memory (GiB)** - 4
- Select :fa:`plus-circle` under **Network Adapters (NICs)**
- **NIC** - vlan.0
- **Credential** - CENTOS

Scroll to the top of the **Configuration Panel**, click **Package**.

Fill out the following fields:

- **Name** - HAPROXY_PACKAGE
- **Install Script Type** - Shell
- **Credential** - CENTOS

Copy and paste the following script into the **Install Script** field:

.. code-block:: bash

  #!/bin/bash
  set -ex

  sudo setenforce 0
  sudo sed -i 's/permissive/disabled/' /etc/sysconfig/selinux

  port=80
  sudo yum update -y
  sudo yum install -y haproxy

  echo "global
    log 127.0.0.1 local0
    log 127.0.0.1 local1 notice
    maxconn 4096
    quiet
    user haproxy
    group haproxy
  defaults
    log     global
    mode    http
    retries 3
    timeout client 50s
    timeout connect 5s
    timeout server 50s
    option dontlognull
    option httplog
    option redispatch
    balance  roundrobin
  # Set up application listeners here.
  listen stats 0.0.0.0:8080
    mode http
    log global
    stats enable
    stats hide-version
    stats refresh 30s
    stats show-node
    stats uri /stats
  listen admin
    bind 127.0.0.1:22002
    mode http
    stats uri /
  frontend http
    maxconn 2000
    bind 0.0.0.0:80
    default_backend servers-http
  backend servers-http" | sudo tee /etc/haproxy/haproxy.cfg

  sudo sed -i 's/server host-/#server host-/g' /etc/haproxy/haproxy.cfg

  hosts=$(echo "@@{APACHE_PHP.address}@@" | sed 's/^,//' | sed 's/,$//' | tr "," "\n")

  for host in $hosts
  do
     echo "  server host-${host} ${host}:${port} weight 1 maxconn 100 check" | sudo tee -a /etc/haproxy/haproxy.cfg
  done

  sudo systemctl daemon-reload
  sudo systemctl enable haproxy
  sudo systemctl restart haproxy

Fill out the following fields:

- **Uninstall Script Type** - Shell
- **Credential** - CENTOS

Copy and paste the following script into the **Uninstall Script** field:

.. code-block:: bash

  #!/bin/bash
  echo "Goodbye!"

Click **Save**.

In the **Workspace**, select the **HAProxy** Service and click the **Create Dependency** icon that appears above the Service icon.

Select the **Apache_PHP** Service. This will hold the execution of **HAProxy** installation script until the **APACHE_PHP** Service is running.

Click **Save**.

Click **Launch**. Specify a unique **Application Name** (e.g. CalmIntro*<INITIALS>*-2) and click **Create**.

Takeaways
***********
- Applications typically span across multiple VMs, each responsible for different services. Calm is capable of automated and orchestrating full applications.
- Dependencies between services can be easily modeled in the Blueprint Editor.
- Users can quickly provision entire application stacks for production or testing for repeatable results without time lost to manual configuration.

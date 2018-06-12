***********************
Calm Blueprint (LAMP)
***********************


Lab Overview
************

Welcome to the Calm Hands-On-Lab - Intermediate Blueprint What we’re
going to do here is extend a basic blueprint:  

.. figure:: http://s3.nutanixworkshops.com/calm/lab2/image1.png

This is a basic LAMP Stack (Linux Apache MySQL PHP). In
this lab we’ll build on the previous 1x simple service MySQL
blueprint and evolve this into the multi-stack application you see
above.

**Calm Glossary**

**Service**: One tier of a multiple tier application. This can be made
up of 1 more VMs (or existing machines) that all have the same config
and do the same thing

**Application (App):** A whole application with multiple parts that are
all working towards the same thing (for example, a Web Application might
be made up of an Apache Server, a MySQL database and a HAProxy Load
balancer. Alone each service doesn’t do much, but as a whole they do
what they’re supposed to).

**Macro:** A Calm construct that is evaluated and expanded before being
ran on the target machine. Macros and Variables are denoted in the
@@{[name]}@@ format in the scripts.

**Subtrate:** A Calm object used to encapsulate the VM(s) within a Blueprint 

Part 1: Accessing and Navigating Calm
*************************************

Getting Familiar with the Tools:

1. Connect to https://<PC-IPAddress:9440>

2. Login to Prism Central using the credentials specified above (use these credentials unless specified otherwise throughout this lab).

3. Click on the Apps tab across the top of Prism

4. Welcome to Calm! Upon accessing this page you will now notice a new ribbon along the left - this is used to navigate through Calm.

5. You are, by default, dropped into the Applications tab and can see all the instances of applications that have been launched from a blueprint.

For now, let’s step through each tab:

.. figure:: http://s3.nutanixworkshops.com/calm/lab2/image2.png

Part 2: Creating a Web Server
*****************************

In this step we’ll add a second tier and connect it to the MYSQL service created from Lab #1 MySQL Blueprint.

Create Service
===============

Create the Service as follows.

1. Click the + sign next to **Services** in the **Overview** pane.
2. Notice there are now 2 service block icons in the workspace.
3. Rearrange the icons to your liking, then click on the new Service 2.
4. Name your service **APACHE_PHP** in the *Service Name* field.
5. The Substrate section is the internal Calm name for this Service. Name this **APACHE_PHP_AHV**
6. Make sure that the Cloud is set to **Nutanix** and the OS set to **Linux**
7. Configure the VM as follows:

.. code-block:: bash

  VM Name .  : APACHE_PHP
  Image .    : CentOS
  Disk Type .: DISK
  Device Bus : SCSI
  vCPU .     : 2
  Core/vCPU .: 1
  Memory     : 4 GB

8. Scroll to the bottom and add the NIC **bootcamp** to the **APACHE_PHP** VM.
9. Configure the **Credentials** to use **CENTOS** created earlier.

Package Configuration
=====================

1. Scroll to the top of the Service Panel and click **Package**.
2. Here is where we specify the installation and uninstall scripts for this service.
3. Name install package **APACHE_PHP_PACKAGE**,
4. Set the install script to **shell** and select the credential **CENTOS** created earlier.
5. Copy the following script into the *script* field of the **install** window:

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

**Fill in the uninstall script:**

6. Set the uninstall script to **shell** and select the credential **CENTOS** created earlier.
7. Copy the following script into the *script* field of the **uninstall** window:

.. code-block:: bash

   #!/bin/bash
   echo "goodbye!"

Since we need the DB IP Address to bring up the AppServer, we need to add a **Dependency**.

8. Click on the **APACHE_PHP_PACKAGE** service,
9. Click on the Arrow icon that appears right above it,
10. Click on the **MYSQL** service.
11. This tells Calm to hold running the script until the **MYSQL** service is up.
12. **Save** the blueprint, then click on the **Create** action from the **Overview** pane to see this.

.. figure:: http://s3.nutanixworkshops.com/calm/lab2/image11.png

Scale-out AppService
====================

Here we'll complete the provisioning of the blueprint.  

1. Click on the **APACHE_PHP_PACKAGE** service. 
2. Click on the **Service** tab. 
3. Change **Number of replicas** under **Deployment Config** from 1 to 2.  
4. This service will now deploy 2 VMs with the same configuration rather than just 1

Part 3: - Create HA Proxy Load Balancer
***************************************

Now that we've added redundancy or load balancing capacity to the AppServer we need something to actually perform the load balancing.  Lets add another Service **HA Proxy**

Create Service
===============

1. Click the + sign next to **Services** in the **Overview** pane.
2. Notice there are now 3 service block icons in the workspace.
3. Rearrange the icons to your liking, then click on the new Service 3.
4. Name your service **HAProxy** in the *Service Name* field.
5. Name the *Substrate*  **HAPROXYAHV**
6. Make sure that the Cloud is set to **Nutanix** and the OS set to **Linux**
7. Configure the VM as follows:

.. code-block:: bash

  VM Name .  : HAProxy
  Image .    : CentOS
  Disk Type .: DISK
  Device Bus : SCSI
  vCPU .     : 2
  Core/vCPU .: 1
  Memory     : 4 GB


8. Scroll to the bottom and add the NIC **bootcamp** to the **HAProxy** VM.
9. Configure the **Credentials** to use **CENTOS** created earlier.

Package Configuration
=====================

1. Scroll to the top of the Service Panel and click **Package**.
2. Here is where we specify the installation and uninstall scripts for this service.
3. Name the package **HAPROXY_PACKAGE**,
4. Set the install script to **shell** and select the credential **CENTOS** created earlier.
5. Copy the following script into the *script* field of the **install** window:

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

**Fill in the uninstall script:**

6. Set the uninstall script to **shell** and select the credential **CENTOS** created earlier.
7. Copy the following script into the *script* field of the **uninstall** window:

.. code-block:: bash

   #!/bin/bash
   echo "goodbye!"

8. We need to add a **Dependency** between **HAProxy** and **APACHE_PHP_AHV**

9. Click on the **HAProxy** service,
10. Click on the Arrow icon that appears right above it,
11. Click on the **APACHE_PHP_AHV** service.
12. This tells Calm to hold running the script until the **APACHE_PHP_AHV** service is up.
13. Save the blueprint, and launch it.


.. |image0| image:: lab2/media/image1.png
.. |image1| image:: lab2/media/image2.png
.. |image2| image:: lab2/media/image3.png
.. |image3| image:: lab2/media/image4.png
.. |image4| image:: lab2/media/image5.png
.. |image5| image:: lab2/media/image6.png
.. |image6| image:: lab2/media/image7.png
.. |image7| image:: lab2/media/image4.png
.. |image8| image:: lab2/media/image8.png
.. |image9| image:: lab2/media/image9.png
.. |image10| image:: lab2/media/image10.png

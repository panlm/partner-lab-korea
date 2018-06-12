.. _calm_mysql_lab:

**********************
Calm Blueprint (MySQL)
**********************


Overview
********

.. note::

  This lab must be completed after SSP has been configured.  For the Taiwan Partner Lab, the SSP step has already been completed for you.

  Estimated time to complete: **40 MINUTES**

In this exercise you will explore the basics of Nutanix Calm by building and deploying a Blueprint that installs and configures a single service, MySQL, on a CentOS image.

Calm Basics
***********

Glossary
========
- **Service**: One tier of a multiple tier application. This can be made up of 1 more VMs (or existing machines) that all have the same config and do the same thing
- **Application (App):** A whole application with multiple parts that are all working towards the same thing (for example, a Web Application might be made up of an Apache Server, a MySQL database and a HAProxy Load balancer. Alone each service doesn’t do much, but as a whole they do what they’re supposed to).
- **Macro:** A Calm construct that is evaluated and expanded before being ran on the target machine. Macros and Variables are denoted in the @@{[name]}@@ format in the scripts.
- **Subtrate:** A Calm object used to encapsulate the VM(s) within a Blueprint

Accessing Calm
==============

Open \https://<*Prism-Central-IP*>:9440/ in a browser.

Log in as **admin**.

From the navigation bar, select **Apps**.

Calm will default to the **Applications** tab, showing all instances of applications that have been launched from a Blueprint.

Tabbed Navigation
=================

Calm introduces a new sidebar to Prism Central. Note the titles and description of each tab:

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab1/image2.png

Blueprint Editor
================

The Blueprint Editor is the UI within Prism Central used to easily, graphically construct applications that consist of multiple clouds, VMs, and services. Note the key sections:

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab1/image4.png

Note the additional buttons below as they can be helpful when actively editing a Blueprint:

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab1/image5.png

In general, the Blueprint creation workflow is as follows:

- Create Object in Application Overview or select existing Object from the Workspace or Application Overview panel.
- Configure the Object in the Configuration Pane.
- Repeat for each Object.
- Connect dependencies between Objects in the Workspace.

Building the Blueprint
**********************

Creating Blueprint
==================

From **Prism Central > Apps**, select **Blueprints** from the sidebar and click **+ Create Application Blueprint**.

If prompted, select **default** from the **Project** drop down menu and click **Proceed**.  If you are not prompted for a project name, continue as normal.  Calm won't prompt for a project if only a single project exists.

Specify **CalmIntro<INITIALS>** in the **Name this Blueprint** field.

Click **Credentials >** :fa:`plus-circle` and fill out the following fields:

- **Credential Name** - CENTOS
- **Username** - root
- **Secret** - Password
- **Password** - nutanix/4u

Click **Back**.

.. note::

  Credentials are unique to each Blueprint.

  Each Blueprint requires a minimum of 1 Credential.

Click **Save** to save your Blueprint.

Setting Variables
=================

Variables allow extensibility of Blueprints, meaning a single Blueprint can be used for multiple purposes and environments depending on the configuration of its variables. Variables can either be static values saved as part of the Blueprint or they can be specified at **Runtime** (when the Blueprint is launched). By default, variables are stored in plaintext and visible in the Configuration Pane. Setting a variable as **Secret** will mask the value and is ideal for variables such as passwords.

Variables can be used in scripts executed against objects using the **@@{variable_name}@@** construct. Calm will expand and replace the variable with the appropriate value before sending to the VM.

In the **Configuration Pane** under **Variable List**, fill out the following fields:

+----------------------+------------------------------------------------------+------------+
| **Variable Name**    | **Value**                                            | **Secret** |
+----------------------+------------------------------------------------------+------------+
| Mysql\_user          | root                                                 |            |
+----------------------+------------------------------------------------------+------------+
| Mysql\_password      | nutanix/4u                                           | X          |
+----------------------+------------------------------------------------------+------------+
| Database\_name       | homestead                                            |            |
+----------------------+------------------------------------------------------+------------+
| App\_git\_link       | https://github.com/ideadevice/quickstart-basic.git   |            |
+----------------------+------------------------------------------------------+------------+

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab1/image8.png

Click **Save**.

Adding DB Service
=================

In **Application Overview > Services**, click :fa:`plus-circle`.

Note **Service1** appears in the **Workspace** and the **Configuration Pane** reflects the configuration of the selected Service.

Fill out the following fields:

- **Service Name** - MySQL
- **Name** - MySQLAHV

  .. note:: This defines the name of the substrate within Calm. Names can only contain alphanumeric characters, spaces, and underscores.

- **Cloud** - Nutanix
- **OS** - Linux
- **VM Name** - MYSQL
- **Image** - CentOS7-ForCalm
- **Device Type** - Disk
- **Device Bus** - SCSI
- Select **Bootable**
- **vCPUs** - 2
- **Cores per vCPU** - 1
- **Memory (GiB)** - 4
- Select :fa:`plus-circle` under **Network Adapters (NICs)**
- **NIC** - vlan.0
- **Credential** - CENTOS

.. note::

  Ensure selecting the **Credential** is the final selection made before proceeding to the next step, selecting other fields can clear your **Credential** selection.

Scroll to the top of the **Configuration Panel**, click **Package**.

Fill out the following fields:

- **Name** - MYSQL_PACKAGE
- **Install Script Type** - Shell
- **Credential** - CENTOS

Copy and paste the following script into the **Install Script** field:

.. code-block:: bash

   #!/bin/bash
   set -ex

   yum install -y "http://repo.mysql.com/mysql-community-release-el7.rpm"
   yum update -y
   yum install -y mysql-community-server.x86_64

   /bin/systemctl start mysqld

   #Mysql secure installation
   mysql -u root<<-EOF

   #UPDATE mysql.user SET Password=PASSWORD('@@{Mysql_password}@@') WHERE User='@@{Mysql_user}@@';
   DELETE FROM mysql.user WHERE User='@@{Mysql_user}@@' AND Host NOT IN ('localhost', '127.0.0.1', '::1');
   DELETE FROM mysql.user WHERE User='';
   DELETE FROM mysql.db WHERE Db='test' OR Db='test\_%';

   FLUSH PRIVILEGES;
   EOF

   sudo yum install firewalld -y
   sudo service firewalld start
   sudo firewall-cmd --add-service=mysql --permanent
   sudo firewall-cmd --reload

   #mysql -u @@{Mysql_user}@@ -p@@{Mysql_password}@@ <<-EOF
   mysql -u @@{Mysql_user}@@ <<-EOF
   CREATE DATABASE @@{Database_name}@@;
   GRANT ALL PRIVILEGES ON homestead.* TO '@@{Database_name}@@'@'%' identified by 'secret';

   FLUSH PRIVILEGES;
   EOF

.. note::

  You can click the **Pop Out** icon on the script field for a larger window to view/edit scripts.

  Looking at the script you can see the package will install MySQL, configure the credentials and create a database based on the variables specified earlier in the exercise.

Fill out the following fields:

- **Uninstall Script Type** - Shell
- **Credential** - CENTOS

Copy and paste the following script into the **Uninstall Script** field:

.. code-block:: bash

   #!/bin/bash
   echo "Goodbye!"

.. note:: The uninstall script can be used for removing packages, updating network services like DHCP and DNS, removing entries from Active Directory, etc. It is not being used for this simple example.

Click **Save**. You will be prompted with specific errors if there are validation issues such as missing fields or unacceptable characters.

Launching the Blueprint
***********************

From the toolbar at the top of the Blueprint Editor, click **Launch**.

In the **Name of the Application** field, specify a unique name (e.g. CalmIntro*<INITIALS>*-1).

.. note::

  A single Blueprint can be launched multiple times within the same environment but each instance requires a unique **Application Name** in Calm.

Click **Create**.

You will be taken directly to the **Applications** page to monitor the provisioning of your Blueprint.

Select **Audit > Create** to view the progress of your application. After **MySQLAHV - Check Login** is complete, select **PackageInstallTask** to view the real time output of your installation script.

Note the status changes to **Running** after the Blueprint has been successfully provisioned.

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab1/image25.png

Takeaways
*********
- By using different projects assigned to different clusters and users, administrators can ensure that workloads are deployed the right way each time.  For example, a developer can be a Project Admin for a dev/test project, so they have full control to deploy to their development clusters or to a cloud, while having Read Only access to production projects, allowing them access to logs but no ability to alter production workloads.
- The Blueprint Editor provides a simple UI for modeling potentially complex applications.
- Blueprints are tied to SSP Projects which can be used to enforce quotas and role based access control.
- Having a Blueprint install and configure binaries means no longer creating specific images for individual applications. Instead the application can be modified through changes to the Blueprint or installation script, both of which can be stored in source code repositories.
- Variables allow another dimension of customizing an application without having to edit the underlying Blueprint.
- Application status can be monitored in real time.

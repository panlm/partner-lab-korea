.. _ssp_lab:

-------------------
Self-Service Portal
-------------------

Overview
++++++++

.. note::
  This lab is **NOT** required for the Taiwan Partner Lab.  It has been left in place for later reference, if required.

  Estimated time to complete: **45 Minutes**

  **This lab can only be completed once per cluster, unless previous changes are undone.**
  
  **When completing this lab on a cluster after today's event, please ensure you replace the cluster details with your own e.g. usernames, passwords, etc.

In this exercise you will enable Self Service Portal (SSP) in Prism Central. SSP provides fast and simple IaaS capabilities for your Nutanix cluster. In addition to enabling SSP, you will configure 3 separate projects mapping to different Active Directory groups and explore roles and permissions available to those groups.

Configuring Authentication
++++++++++++++++++++++++++

In **Prism Central**, click :fa:`cog` **> Authentication**.

Click **+ New Directory**.

Fill out the following fields and click **Save**:

- **Directory Type** - Active Directory
- **Name** - NTNXLAB
- **Domain** - ntnxlab.local
- **Directory URL** - ldaps://10.21.XX.40
- **Service Account Name** - administrator@ntnxlab.local
- **Service Account Password** - nutanix/4u

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp01.png

Mouse over the **!** icon and select **Click here** to define role mappings.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp28b.png

Click **+ New Mapping**.

Fill out the following fields and click **Save**:

- **Directory** - NTNXLAB
- **LDAP Type** - user
- **Role** - Cluster Admin
- **Values** - administrator

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp29.png

Click **Close > Close**.

The **DC** VM, in some lab environments, is pre-populated with the following groups and user accounts:

+-----------------+-----------------------+--------------------------------+
| **Group**       | **Usernames**         | **Password**                   |
+-----------------+-----------------------+--------------------------------+
| SSP Admins      | adminuser01-25        | nutanix/4u                     |
+-----------------+-----------------------+--------------------------------+
| SSP Developers  | devuser01-25          | nutanix/4u                     |
+-----------------+-----------------------+--------------------------------+
| SSP Power Users | poweruser01-25        | nutanix/4u                     |
+-----------------+-----------------------+--------------------------------+
| SSP Basic Users | basicuser01-25        | nutanix/4u                     |
+-----------------+-----------------------+--------------------------------+

Enabling Self Service Portal
++++++++++++++++++++++++++++

In **Prism Central**, click :fa:`cog` **> Self-Service Admin Management**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp02.png

Fill out the following fields and click **Next**:

- **Select Active Directory** - NTNXLAB (ntnxlab.local)
- **Username** - administrator@ntnxlab.local
- **Passord** - nutanix/4u

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp03.png

Click **+ Add Admins**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp04.png

Enter **SSP Admins** and click **Save**.

  .. note::

    The **Name** field will autocomplete with matching entries from Active Directory.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp05.png

Click **Save**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp06.png

Creating Projects
+++++++++++++++++

Developers
..........

In **Prism Central > Explore > Projects**, click **Create Project**.

Fill out the following fields:

- **Project Name** - Developers
- **Description** - SSP Developers
- **AHV Cluster** - *<Nutanix Cluster Name>*

Under **Users, Groups, and Roles**, click **+ User**.

Fill out the following fields and click **Save**:

- **Name** - SSP Developers
- **Role** - Developer

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp08.png

Under **Network**, select **vlan.0**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp09b.png

Select **Quotas** and fill out the following fields:

- **VCPUS** - 10 VCPUs
- **Storage** - 200 GiB
- **Memory** - 40 GiB

Click **Save**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp10b.png

Power Users
...........

In **Prism Central > Explore > Projects**, click **Create Project**.

Fill out the following fields:

- **Project Name** - Power Users
- **Description** - SSP Power Users
- **AHV Cluster** - *<Nutanix Cluster Name>*

Under **Users, Groups, and Roles**, click **+ User**.

Fill out the following fields and click **Save**:

- **Name** - SSP Power Users
- **Role** - Developer

Under **Network**, select **vlan.0**.

Select **Quotas** and fill out the following fields:

- **VCPUS** - 10 VCPUs
- **Storage** - 200 GiB
- **Memory** - 40 GiB

Click **Save**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp11b.png

Calm
....

In **Prism Central > Explore > Projects**, click **Create Project**.

Fill out the following fields:

- **Project Name** - Calm
- **Description** - Calm
- **AHV Cluster** - *<Nutanix Cluster Name>*

Under **Users, Groups, and Roles**, click **+ User**.

Fill out the following fields and click **Save**:

- **Name** - SSP Admins
- **Role** - Project Admin

Click **+ User**, fill out the following fields and click **Save**:

- **Name** - SSP Developers
- **Role** - Developer

Click **+ User**, fill out the following fields and click **Save**:

- **Name** - SSP Power Users
- **Role** - Consumer

Click **+ User**, fill out the following fields and click **Save**:

- **Name** - SSP Basic Users
- **Role** - Operator

Under **Network**, select **vlan.0**.

Click **Save**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp12b.png

Using Self Service Portal
+++++++++++++++++++++++++

In this exercise we will log in to Prism Central as different AD users to compare what entities and actions are available based on role assignment.

In the navigation bar, select **Admin > Sign Out** to log out of Prism Central.

Project Admin
.............

Log in to Prism Central with the following credentials:

- **Username** - adminuserXX@ntnxlab.local (replace XX with 01-05)
- **Password** - nutanix/4u

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp13.png

Note the only items available in the navigation bar are **Explore** and **Apps**.

Select **VMs** from the sidebar to see all VMs to which the user has access.

Select **Projects** to see all Projects to which the user belongs. Select a Project and note the **Action** menu. As a Project Admin, you can delete and make changes to Projects, such as assigning new users and modifying quotas.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp14.png

Select **Images** from the sidebar to see all Images available in the Image Service of clusters registered with Prism Central.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp15.png

Select **Windows2012**, and click **Actions > Add Image to Catalog**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/sp16.png

Fill out the following fields and click **Save**:

- **Name** - Windows2012 Image
- **Description** - Windows2012 Image

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp17.png

Repeat these steps for the CentOS Image.

Select **Catalog Items** from the sidebar and verify the 2 Images are available.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp18.png

Developer
.........

Log in to Prism Central with the following credentials:

- **Username** - devuserXX@ntnxlab.local (replace XX with 01-05)
- **Password** - nutanix/4u

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp19.png

Select **VMs** from the sidebar to see all VMs to which the user has access.

Select **Projects** to see all Projects to which the user belongs. Select a Project and note the **Action** menu isn't available to users assigned the Developer role.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp20.png

Select **VMs** from the sidebar and click **Create VM**.

Select **Disk Images** and click **Next**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp21.png

Select **CentOS Image** and click **Next**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp22.png

Fill out the following fields and click **Save**:

- **Name** - Developer VM 001
- **Target Project** - Developers
- **Disks** - Select **Boot From** scsi.0
- **Network** - vlan.0
- **Advance Settings** - Check **Manually Configure CPU & Memory**
- **CPU** - 1 VCPU
- **Memory** - 2 GB

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp23.png

Select **Developer VM 001** and note the VM has been automatically started. Click **Actions** and note your available options. As the owner of a VM you can delete, update, or transfer ownership of the VM, perform power management, and launch a console.

Power User
..........

Log in to Prism Central with the following credentials:

- **Username** - poweruserXX@ntnxlab.local (replace XX with 01-05)
- **Password** - nutanix/4u

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp24.png

Select **VMs** from the sidebar and note you do not see **Developer VM 001**, that is because **SSP Power Users** is not a memeber of the **Developer** project.

Select **VMs** from the sidebar and click **Create VM**.

Select **Disk Images** and click **Next**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp21.png

Select **CentOS Image** and click **Next**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp22.png

Fill out the following fields and click **Save**:

- **Name** - Calm VM 001
- **Target Project** - Calm
- **Disks** - Select **Boot From** scsi.0
- **Network** - vlan.0
- **Advance Settings** - Check **Manually Configure CPU & Memory**
- **CPU** - 1 VCPU
- **Memory** - 2 GB

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp25.png

Log out of Prism Central and log in with the following credentials:

- **Username** - devuserXX@ntnxlab.local (replace XX with 01-05)
- **Password** - nutanix/4u

You should see both **Developer VM 001** and **Calm VM 001**. That is because **SSP Developers** is a member of both Projects and collaboration has been enabled for the **Calm** project.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp26.png

Select **Projects** from the sidebar. Select the **Developers** project to monitor resource usage against the project quota.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp27.png

Enabling App Management
+++++++++++++++++++++++

In **Prism Central**, click :fa:`cog` **> Enable App Management**.

.. note:: You will need to log into Prism Central as a Cluster Admin user.

Select **Enable App Management**.

Verify **Enable Nutanix Seeded Blueprints** is selected.

Click **Save**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/ssp/ssp30.png

Monitor the **Enable app management** task until completed successfully.

In the navigation bar, click **Apps** and verify the Calm sidebar is displayed. Select **Projects** from the sidebar and verify your SSP projects are present.

.. note::

  If you receive **Oops - Server Error** when loading the **Apps** page for the first time, refresh your browser.

Takeaways
+++++++++++

- Nutanix provides a native service to seperate out resources for different groups, while giving them a Self-Service approach to using those resources.

- Easy to assign resources to different projects using directory groups

- Easy to assign a set of resources (quotas) to better manage cluster resources, or for show back

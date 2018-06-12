.. _xtractdb_lab:

--------------------
Xtract for Databases
--------------------

Overview
++++++++

.. note::

  Estimated time to complete: **90 MINUTES**

  **Due to limited resources, this lab should be completed as a group.**

  Refer to :ref:`cluster_details` to find the source SQL Server VM assigned to your team.

In this exercise you will deploy Xtract for DBs and migrate a MS SQL 2014 database from ESXi to AHV. Rather than migrating the entire VM, migrating the database instance allows for streamlined automation of Best Practice configuration.

Getting Engaged with the Product Team
.....................................

- **Slack** - #xtract
- **Product Manager** - Jeremy Launier, jeremy.launier@nutanix.com
- **Product Marketing Manager** - Marc Trouard-Riolle, marc.trouardriolle@nutanix.com
- **Technical Marketing Engineer** - Mike McGhee, michael.mcghee@nutanix.com

Deploying Xtract for DBs
++++++++++++++++++++++++

In **Prism Central > Explore > VMs**, click **Create VM**.

Fill out the following fields and click **Save**:

- **Name** - Xtract-DB
- **Description** - Xtract for DBs
- **vCPU(s)** - 2
- **Number of Cores per vCPU** - 1
- **Memory** - 4 GiB
- Select **+ Add New Disk**

  - **Operation** - Clone from Image Service
  - **Image** - Xtract-DB
  - Select **Add**
- Select **Add New NIC**

  - **VLAN Name** - Primary
  - **IP Address** - *10.21.XX.43*
  - Select **Add**

Select the **Xtract-DB** VM and click **Actions > Power on**.

Open \https://<*XTRACT-DB-VM-IP*>/ in a browser to access **Xtract for DBs**.

Log in with the following credentials:

- **Username** - nutanix
- **Password** - nutanix/4u

Fill in **Name**, **Company**, and **Job Title**, then **Accept** the EULA.

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb02.png

Click **OK** when prompted about the **Nutanix Customer Experience Program**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb03.png

Migrating a Database
++++++++++++++++++++

Creating a Migration Project
............................

Enter project name, and click **Create New Project**:

**Project Name** - Website DB.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb04.png

Fill out the following fields and click **Begin Scan**:

- **Scan Name** - Parts DB
- **Hostname (or IP Address)** - *<SQLSvrTeam-XX IP Address>*
- **Instance Name (or Port)** - 1433
- **Username** - NTNXLAB\\Administrator
- **Password** - nutanix/4u

If the Scan fails, you will need elevate the permissions of the Scan User.

Click the **Actions** dropdown, and select **Elevate Scan User Privileges**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb06.png

Fill out the following fields and click **Re-scan**:

- **Username** - sa
- **Password** - nutanix/4u

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb07.png

If prompted to setup the **XP Command Shell (xp_cmdshell) Proxy User**, fill out the following fields and click **Done**:

- **Username** - sa
- **Password** - nutanix/4u

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb36.png

After the scan completes successfully, select your **MSSQLSERVER** Instance to see the Overview.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb08.png

Generating a Design
...................

Select your **MSSQLSERVER** Instance and click **Generate Design**.

Click the :fa:`pencil` to change the Design name.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb09.png

Fill out the following fields and click **Save**:

- **Custom Design Name** - MSSQLSERVER-UPTICK-WebsiteDB

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb10.png

Click **MSSQLSERVER-UPTICK-WebsiteDB** to review the Design Details.

.. note::

  Alternating the **Target Hypervisor** you can see part of Xtract's Best Practices automation in action. When ESXi is selected the disks are appropriately spread across multiple PVSCSI controllers.

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb11.png

Click **< Back** to return to the **Design Templates** view.

Preparing Target Template
.........................

In order to migrate the database we need to create a master VM on the target cluster to which the database can be migrated. Xtract can use a single template VM on the target cluster to deploy VMs for multiple projects/instances.

In **Prism Central > Explore > VMs**, click **Create VM**.

Fill out the following fields and click **Save**:

- **Name** - Xtract-DB-2012r2-Master
- **Description** - Xtract-DB win2012r2 Master VM
- **vCPU(s)** - 2
- **Number of Cores per vCPU** - 1
- **Memory** - 8 GiB
- Select **+ Add New Disk**

  - **Operation** - Clone from Image Service
  - **Image** - Windows2012
  - Select **Add**
- Select **Add New NIC**

  - **VLAN Name** - Primary
  - Select **Add**

Select the **Xtract-VM** VM and click **Actions > Power on**.

Once the VM has started, click **Launch Console**.

Set the local Administrator password to **nutanix/4u**.

In **Prism > VM > Table**, select **Xtract-DB-2012r2-Master** and click **Manage Guest Tools**.

Select **Enable Nutanix Guest Tools** and **Mount Nutanix Guest Tools**, and click **Submit**.

Install Nutanix Guest Tools and restart the VM.

Log in and run Windows Update. Set Windows Updates to **Check for updates but let me choose whether to download and install them**. Restart the VM after updates have finished installing.

.. note::

  Microsoft SQL Server 2016 requires `KB2919355 <https://www.microsoft.com/en-us/download/details.aspx?id=42334>`_ to install correctly.

Disable the Windows Firewall Service.

Shutdown the VM.

.. note:: It is not necessary to sysprep the target template VM if the target VM will use DHCP to obtain an IP address. For a migration requiring a static IP address, the target template VM must be put in a sysprep state prior to deployment.

Verify the **SQL Server 2016** installation media is available in the **Image Service** of your target cluster.

Deploying Target VM
...................

In **Xtract for DBs**, click **Proceed to Deploy**.

Click **...** under **Actions**, and select **Deploy**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb12.png

Note the prerequisites and click **Proceed to Deploy**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb13.png

Fill out the following fields for **Prism Credentials**, and click **Connect**:

- **IP Address** - *<Nutanix Cluster Virtual IP>*
- **Port** - 9440
- **Username** - admin
- **Password** - *<Nutanix admin Password>*

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb14.png

After successful connection to your target Nutanix cluster, click **Configure VMs**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb15.png

Fill out the following fields and click **Next**:

- **Name** - UptickAppDB
- **Container Name** - Databases
- **Retain clone of master VM on the Container** - Unselected
- **Network** - Primary
- Select **DHCP**

.. note::

  If existing storage containers exist on the target Nutanix cluster that match the specifications from the Best Practices design, they will be available to select from the **Container Name** field.

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb16.png

Fill out the following fields and click **Next**:

- **Target VM Master Image** - Xtract-DB-2012r2-Master
- **Target VM Password** - nutanix/4u

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb17.png

Download the `SQL Server 2016 KB3210089 Service Pack <http://10.21.64.50/images/SQLServer2016-KB3210089-x64.exe>`_.

Fill out the following fields:

- **SQL Server Image** - MMSSQL-2016SP1-ISO
- **Service Pack (Optional)** - SQLServer2016-KB3210089-x64.exe
- Select **Upload**

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb18.png

Click **Enter Account Credentials**.

Fill out the following fields and click **Next**:

- **Domain Account Name** - ``ntnxlab\adminuser01``
- **Password** - nutanix/4u


  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb38.png

Fill out the following fields and click **Validate and Save**:

- **Domain Name** - ntnxlab.local
- **Domain User Name** - administrator@ntnxlab.local
- **Domain Password** - nutanix/4u

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb37.png

Click **Review**.

.. note:: You can safely ignore any errors regarding the failure to verify the domain credentials.

Review your configuration and click **Deploy**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb19.png

Monitor the status of your deployment. Select **# task(s) completed** and **# pending task(s)** to see a complete list of pending and completed tasks.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb20.png

Once complete, click **Proceed to Migrate**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb21.png

Migrating the Database
......................

Click **Create a Migration Plan**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb22.png

Click :fa:`pencil` to update the **New Sample Plan** Name.

- **Plan Name** - UptickDB Plan.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb23.png

Click :fa:`plus-circle` to select the **MSSQLSERVER** Instance, and click **Next**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb24.png

If prompted for a file share to store new Full and Transaction Log backups, use the following file share located on your source SQL Server VM, and click **Save and Start the Plan**.

- **Server File Path** - ``\\<SQLSvrTeam-XX-IP-Address>\xdb``

.. note::

  As backup creation can be resource intensive, best practice for migration would be to have Xtract for DBs use a share on a dedicated filer, such as AFS. Creating the share on the source VM is done solely out of convenience for this exercise.

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb25.png

Click **Proceed** to begin the migration.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb26.png

Ignore any warnings regarding SQL Server Version mismatch.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb27.png

When the **Status** changes to **Ready for Cutover**, click **Action > Cutover Databases**.

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb28.png

Click **Proceed** to launch the **Cutover**.

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb29.png

Ignore additional warning messages.

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb30.png

When the **Status** changes to **Ready for Re-balancing**, click **Action > Initiate Post Cutover Processing**.

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb31.png

Select **Re-balance Data in Databases** and click **Start**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb32.png

When the **Status** changes to **Ready for Final Processing**, click the **Action > Initiate Data Cleanup**.

 .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb33.png

Click **Proceed** to launch the **Cleanup**.

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb34.png

After successful Cleanup, the **Status** will change to **Completed**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-db/xtractdb35.png

Takeaways
+++++++++++

- Xtract facilitates the migration of existing database instances to a Nutanix Enterprise Cloud.

- Databases are transformed at the application level, where Xtract discovers all instances in an infrastructure, understands their configuration and performance characteristics, and applies Nutanix best practices to their design template for migration to the target.

- This approach enables businesses to migrate from any source platform (virtual, physical and public cloud) with ease, optimizing the database servers in the process and extracting maximum value from the Nutanix investment.

- Xtract eliminates human error and data inconsistency in migrations.

- Xtract optimizes database performance by automatically re-balancing data across database files during migration.

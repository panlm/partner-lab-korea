----
HYCU
----

Overview
++++++++

.. note::

  Estimated time to complete: **1 HOUR**

  **Due to limited resources, this lab should be completed as a group.**

HYCU is the only solution built from the ground up to deliver a full suite of backup capabilities for Nutanix AHV, eliminating a key barrier to entry for AHV prospects. Additionally, as pure software, HYCU can help grow Nutanix deals as additional nodes are positioned to act as a backup target for workloads.

In this exercise you will configure a HYCU appliance, configure the connection to your Nutanix cluster, configure a Nutanix Volume Group to act as a backup target, and execute backup and restore operations at the VM, file, and application level.

Getting Engaged with the Product Team
.....................................

- **Slack** - #_HYCU-support-ext
- **Nutanix Product Manager** - Mark Nijmeijer, mark.nijmeijer@nutanix.com
- **Nutanix Technical Marketing Engineer** - Dwayne Lessner, dwayne@nutanix.com
- **HYCU VP Products** - Subbiah Sundaram, subbiah.sundaram@hycu.com
- **HYCU Sales Engineer (US East Coast)** - Jason D'Alessio, jason.dalessio@hycusoftware.com, (813) 484-5651
- **HYCU Sales Engineer (US West Coast)** - Adam Clark, adam.clark@comtradesoftware.com, (512) 423-6335

Configuring HYCU Appliance
++++++++++++++++++++++++++

.. note::

  HYCU has been pre-staged from a QCOW2 image as a VM with 2 vCPU, 4GB RAM on the **Primary** network. A Prism service account, **hycu**, with Cluster Admin priveleges has already been created.

In **Prism > VM > Table**, select the **HYCU** VM and click **Launch Console**.

Fill out the following fields, highlight **OK** and press **Return**:

  - **Hostname** - HYCU
  - **IPv4 address** - *10.21.XX.44*
  - **Subnet mask** - 255.255.255.128
  - **Default gateway** - *10.21.XX.1*
  - **DNS server** - *10.21.XX.40*
  - **Search domain** - ntnxlab.local

.. note:: Switch fields by pressing the Tab key.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/1.png

Wait approximately 1 minute for the internal database to initialize and backup controller services to start.

Note the default credentials and press **Return**. Close the **HYCU** VM console.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/2.png

Adding A Nutanix Cluster
++++++++++++++++++++++++

Open \https://<*HYCU-VM-IP*>:8443/ in a browser. Log in to the **HYCU Web Interface** using the default credentials.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/3.png

From the toolbar, click :fa:`cog` **> Nutanix Clusters**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/4.png

Click **+ New** and fill out the following fields:

  - **Cluster Prism Element URL** - *https://10.21.XX.37:9440/*
  - **User** - hycu
  - **Password** - nutanix/4u

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/5.png

Click **Save**.

After the cluster is successfully added, click **Close**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/6.png

From the sidebar, click **Virtual Machines** and validate that your cluster's VMs are listed in the table.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/7.png

From the toolbar, click :fa:`cog` **> iSCSI Initiator**.

Highlight the **Initiator Name** and copy to your clipboard or an external text file. Click **Close**.

 .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/8.png

Adding A Backup Target
++++++++++++++++++++++

The target is used for storing backups coordinated by HYCU. HYCU supports S3, Azure, NFS, SMB, and iSCSI storage targets. The recommended target configuration for Nutanix is an iSCSI connection to a Nutanix Volume Group.

From **Prism > Storage > Table > Storage Container**, select **+ Storage Container**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/9.png

Fill out the following fields and click **Save**:

  - **Name** - Backup
  - Select **Advanced Settings**
  - Select **Compression**
  - **Delay (In Minutes)** - 0
  - Select **Erasure Coding**

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/10.png

.. note:: Erasure Coding is well suited to backup target use cases as retained snapshots will become write cold and not frequently overwritten.

From **Prism > Storage > Table > Volume Groups**, select **+ Volume Group**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/11.png

Fill out the following fields and click **Save**:

  - **Name** - HYCU-Target
  - **iSCSI Target Name Prefix** - HYCU-Target
  - **Description** - HYCU Target VG
  - Select **+ Add New Disk**
    - **Storage Container** - Backup
    - **Size (GiB)** - 1000
  - Select **Enable external client access**
  - Select **CHAP Authentication**
  - **Target Password** - nutanixnutanix
  - Select **+ Add New Client**
    - **Client IQN** - *<HYCU iSCSI Initiator IQN>*

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/12.png

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/13.png

.. note::

  By default, HYCU's recommendation is 1 disk per Volume Group. Customers can utilize > 1 disk per Volume Group today to increase throughput to support a greater number of concurrent backups. Currently, HYCU Support should be engaged to configure > 1 disk per Volume Group.

  CHAP passwords must be between 12 and 16 characters long.

Select **HYCU-Target** and note the **Target IQN Prefix** in the **Volume Group Details** table. Triple-click this value to fully select it. Copy the value to your clipboard.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/14.png

From **Prism >** :fa:`cog` **> Cluster Details**, note the **iSCSI Data Services IP**. Click **Cancel**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/15.png

From the **HYCU Web Interface**, select **Targets** from the sidebar.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/16.png

Click **+ New**, fill out the following fields, and click **Save**:

  - **Name** - NutanixVG
  - **Description** - *<Nutanix Cluster Name>* HYCU-Target VG
  - **Type** - iSCSI
  - **Target Portal** - *<Nutanix iSCSI Data Services IP>*
  - **Target Name** -
  - Select **CHAP**
  - **Target Secret** - nutanixnutanix

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/17.png

.. note:: Maximum concurrent backups is a factor of how much disk throughput the backup target is capable of providing. HYCU is currently developing guidance for concurrent backups based on Nutanix hardware configuration.

Configuring Backup Policies
+++++++++++++++++++++++++++

From the **HYCU Web Interface**, select **Policies** from the sidebar.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/18.png

By default HYCU is configured with 4 different Policies:

  - **Gold** - 4 Hour RPO, 4 Hour RTO
  - **Silver** - 12 Hour RPO, 12 Hour RTO
  - **Bronze** - 24 Hour RPO, 24 Hour RTO
  - Exclude - Backup not required

To create a custom policy, click **+ New**.

Fill out the following fields and click **Save**:

  - **Name** - Fast
  - **Description** - 1 Hour RPO/RTO, Fast Restore Enabled (1 Day)
  - **Enabled Options** - Fast Restore
  - **Backup Every** - 1 Hours
  - **Recover Within** - 1 Hours
  - **Retention** - 4 Weeks
  - **Targets** - NutanixVG
  - **Fast Restore Retention** - 1 Day

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/19.png

.. note::

  HYCU supports multiple advanced configurations for backup policies, including:

  - **Backup Windows** - Allows an administrator to define granular time of day and day of week schedules to enforce backup policy.
  - **Copy** - Asyncronously copies data from the primary backup target to a configurable secondary backup target during periods of non-peak utilization.
  - **Archiving** - Allows an administrator to target slower, cold storage for long term retention.
  - **Fast Restore** - Retains local snapshots on the Nutanix cluster for rapid restores.
  - **Backup from Replica** - For VMs that use native Nutanix replication from a primary cluster to a secondary cluster, this feature will backup VMs from the replicated snapshots on the secondary cluster. This functionality can significantly reduce data movement for scenarios such as Remote Office Branch Office.

  HYCU is also unique in its ability for administrators to define desired RTO. By specifying a desired **Recover Within** period and selecting **Automatic** target selection, HYCU will compute the right target to send the VM. The performance of the target is constantly monitored to ensure it can recover the data within the configured window.

Select the **Exclude** policy and click **Set Default > Yes**.

.. note:: This will set the default policy for VMs to not be backed up by HYCU. In a production environment you could choose the appropriate policy to minimally backup all VMs by default.

Backing Up A VM
+++++++++++++++

In **Prism > VM > Table**, click **+ Create VM**.

Fill out the following fields and click **Save**:

- **Name** - WS12-HYCUBackupTest
- **Description** - WS12-HYCUBackupTest
- **vCPU(s)** - 2
- **Number of Cores per vCPU** - 1
- **Memory** - 4 GiB
- Select **+ Add New Disk**

  - **Operation** - Clone from Image Service
  - **Image** - Windows2012
  - Select **Add**
- Select **Add New NIC**

  - **VLAN Name** - Primary
  - Select **Add**

Select the **WS12-HYCUBackupTest** VM and click **Power on**.

Once the VM has started, click **Launch Console**.

Complete the Sysprep process and provide a password for the local Administrator account.

Log in as the local Administrator and create multiple files on the desktop (e.g. documents, images, etc.).

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/20.png

From the **HYCU Web Interface**, select **Virtual Machines** from the sidebar.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/21.png

Select **WS12-HYCUBackupTest** and click **Policies**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/22.png

.. note::

  HYCU will automatically synchronize at regular intervals. If **WS12-HYCUBackupTest** does not appear in the list of available Virtual Machines, click **Synchronize** to pull the updated list from Prism.

Select **Fast** and click **Assign**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/23.png

Select **Jobs** from the sidebar and monitor the backup progress for **WS12-HYCUBackupTest**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/24.png

Upon completion of the first full backup, select **Dashboard** from the sidebar and confirm all policies are compliant and 100% of VM's have been protected.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/25.png

Select **Virtual Machines** from the sidebar and select **WS12-HYCUBackupTest**. Click **Backup** to manually trigger an incremental backup.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/26.png

Restoring A VM
++++++++++++++

Select **Virtual Machines** from the sidebar and select **WS12-HYCUBackupTest**.

In the **Details** table below, mouse over the **Compliancy** and **Backup Status** icons for additional information about each Restore Point.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/27.png

Select the most recent incremental snapshot and click **Restore VM**. Select **Clone VM** and click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/28.png

.. note:: In addition to restoring the original VM and cloning, HYCU also offers the ability to export the disk image for a given Restore Point to an SMB share or NFS mount. If multiple Nutanix clusters are configured, HYCU can also restore a VM to an alternate cluster.

Select the **Default** Storage Container and **Power Virtual Machine On**. Click **Restore**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/29.png

In **Prism > VM > Table**, note that the original VM has been powered off and the cloned VM is now available - congratulations, you have restored your first VM from backup.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/30.png

.. note:: Automatically powering off the original VM is important to prevent potential network or service conflicts.

Power off the cloned VM and power on the original VM.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/31.png

From **HYCU > Virtual Machines**, click **Synchronize**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/32.png

.. note:: The cloned VM inherits the default HYCU Policy, and not the Policy assigned to the original VM.

Select **WS12-HYCUBackupTest**. Select the most recent Restore Point and click **Restore VM**. Select **Restore VM** and click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/33.png

Click **Restore**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/34.png

.. note:: Unlike restoring to a cloned VM, restoring the original VM maintains the assigned HYCU Policy.

In **Prism > Tasks**, validate that the original VM was deleted, restored, and powered on.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/35.png

Restoring Files
+++++++++++++++

In **Prism > VM > Table**, select the **WS12-HYCUBackupTest** VM and click **Launch Console**.

Log in to the VM as **Administrator** and permanently delete the files previously created on the desktop. Close the console.

From **HYCU > Virtual Machines**, select **WS12-HYCUBackupTest**. Select **Credentials > + New**.

Fill out the following fields and click **Save**:

  - **Name** - WS12-HYCUBackupTest Credentials
  - **Username** - Administrator
  - **Password** - *<WS12-HYCUBackupTest Password>*

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/36.png

Click **Assign**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/37.png

.. note::

  Credentials are only required to restore files directly to the VM. Note the **Discovery** icon is now green for **WS12-HYCUBackupTest** after valid credentials are applied.

Select the original Full backup Restore Point (prior to deleting the files) and click **Restore Files**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/38.png

Navigate to ``C:\Users\Administrator\Desktop`` and select the deleted files. Click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/39.png

Select **Restore to Virtual Machine** and click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/40.png

.. note:: Files can also be restored directly to an SMB share.

Fill out and the fields and click **Restore**:

  - **Path** - Original Location
  - **Mode** - Rename restored
  - Select **Restore ACL**

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/41.png

In **Prism > VM > Table**, select the **WS12-HYCUBackupTest** VM and click **Launch Console**.

Log in to the VM as **Administrator** and validate the files have been restored with ``.hycu.restored`` file extensions. Remove the extention and open your previously deleted file.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/42.png

Performing SQL Server Backup And Restore
++++++++++++++++++++++++++++++++++++++++

.. note::

  This portion of lab should be completed **AFTER** the :ref:`xtractdb_lab` lab.

  Ensure NGT is Enabled on your migrated **UptickAppDB** VM by mounting Nutanix Guest Tools and restarting the **Nutanix Guest Tools Agent** service within the VM.

From **HYCU > Virtual Machines**, select **UptickAppDB**. Select **Credentials > + New**.

Fill out the following fields and click **Save**:

  - **Name** - NTNXLAB Administrator Credentials
  - **Username** - NTNXLAB\\Administrator
  - **Password** - nutanix/4u

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/43.png

Click **Assign**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/44.png

Click **Synchronize** and validate the **Discovery** column appears green for **UptickAppDB**.

Select **Applications** from the sidebar, select **UptickAppDB\\MSSQLSERVER**, and click **Policies**. Select **Gold** and click **Assign**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/45.png

Select **Jobs** from the sidebar and monitor the backup progress for **UptickAppDB**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/46.png

.. note::

  The job may finish with a warning status due to some databases on the **UptickAppDB** VM being configured with the **Simple Recovery Model** and not supporting transaction log backup. This warning can be ignored as the desired database, Uptick, is configured as **Full Recovery Model**. You can view the detailed output of any warnings or errors by selecting a Job and clicking **Report**.

Upon completion of the backup, connect to the **UptickAppDB** VM via RDP.

From the **UptickAppDB** console, open **PowerShell** and execute the following:

.. code-block:: posh
  :emphasize-lines: 3,5,7,9,13

  [System.Reflection.Assembly]::LoadWithPartialName('Microsoft.SqlServer.SMO') | out-null
  $dbs = New-Object ('Microsoft.SqlServer.Management.Smo.Server') "LOCALHOST"
  # Selects the Uptick database on the local SQL Server instance
  $uptickDb = $dbs.Databases["Uptick"]
  # Displays all tables within the Uptick database
  $uptickDb.Tables | SELECT Name
  # Displays the contents of the Make table
  $uptickDb.ExecuteWithResults("SELECT * FROM dbo.Make").Tables
  # Deletes the Make table from the Uptick database
  $uptickDb.ExecuteNonQuery("DROP TABLE dbo.Make")
  $dbs = New-Object ('Microsoft.SqlServer.Management.Smo.Server') "LOCALHOST"
  $uptickDb = $dbs.Databases["Uptick"]
  # Displays all tables within the Uptick database, note the Make table is no longer present
  $uptickDb.Tables | SELECT Name

From **HYCU > Applications**, select **UptickAppDB\\MSSQLSERVER**. Select the most recent Restore Point (prior to deleting the database table) and click **Restore**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/47.png

Select **Restore application items** and click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/48.png

Select **Uptick** and click **Restore**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/hycu/49.png

Select **Jobs** from the sidebar and monitor the restore progress for **UptickAppDB\\MSSQLSERVER**.

Upon completion of the restore, from the **UptickAppDB** console, open **PowerShell** and execute the following:

.. code-block:: posh
  :emphasize-lines: 3,5,7

  [System.Reflection.Assembly]::LoadWithPartialName('Microsoft.SqlServer.SMO') | out-null
  $dbs = New-Object ('Microsoft.SqlServer.Management.Smo.Server') "LOCALHOST"
  # Selects the Uptick database on the local SQL Server instance
  $uptickDb = $dbs.Databases["Uptick"]
  # Displays all tables within the RESTORED Uptick database, including the previously deleted Make table
  $uptickDb.Tables | SELECT Name
  # Displays the contents of the Make table
  $uptickDb.ExecuteWithResults("SELECT * FROM dbo.Make").Tables

The **Uptick** database has been restored to the in-place **UptickAppDB\\MSSQLSERVER** instance.

Takeaways
+++++++++

  - HYCU provides a full suite of VM and application backup capabilities for AHV & ESX.
  - HYCU is the first product to leverage Nutanix snapshots for both backup and recovery, eliminating VM stun and making it possible to recover rapidly from local Nutanix snapshots.
  - HYCU can also use Nutanix nodes as a backup storage target, providing Nutanix sellers an opportunity to increase deal size.
  - Similar to Prism, HYCU offers an easy, streamlined user experience.
  - HYCU is the only solution for ROBO customers that reduces network bandwidth by 50% by backing up from VM replicas.
  - HYCU will have the first scale-out backup and recovery for AFS reducing resource requirements and time to backup by 90%.

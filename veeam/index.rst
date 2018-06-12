--------------------------
Veeam Backup Proxy for AHV
--------------------------

Overview
++++++++

.. note::

  Estimated time to complete: **90 MINUTES**

  **Due to limited resources, this lab should be completed as a group.**

  This lab is built with an early Beta release of the Veeam Backup Proxy for AHV. You may encounter bugs or unimplemented features.

Veeam Backup & Replication 9.5 introduces support for Nutanix AHV through the Backup Proxy for AHV. The Backup Proxy is a Linux based virtual appliance that performs the role of a coordinator between the Nutanix platform and Veeam Backup & Replication. The Backup Proxy communicates with the AHV platform via Nutanix REST API, assigns necessary resources for backup and restore operations, reads/writes data from/to Nutanix storage containers and transports VM data to/from target Veeam backup repositories. The Backup Proxy is also responsible for job management and scheduling, data compression and deduplication, and applying retention policy settings to backup chains. Each Nutanix cluster leveraging Veeam for backup will require its own Backup Proxy VM.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/0.png

In this exercise you will deploy and configure a Veeam Backup Server, deploy and configure a Veeam Backup Proxy, configure the connection to your Nutanix cluster and existing Veeam Backup & Replication infrastructure, and execute backup and restore operations.

Getting Engaged with the Product Team
.....................................

- **Slack** - #veeam
- **Nutanix Product Manager** - Mark Nijmeijer, mark.nijmeijer@nutanix.com

Deploying Veeam Backup Server
+++++++++++++++++++++++++++++

The Veeam Backup Server is the main management component in the Veeam backup infrastructure. The Veeam Backup Server is responsible for managing Veeam backup repositories that are used as a target for backup. The Veeam Backup & Replication Console is also used for granular VM restore operations such as restoring individual files, AD objects, Exchange mailboxes, and SQL/Oracle databases.

In **Prism > VM > Table**, click **+ Create VM**.

Fill out the following fields and click **Save**:

- **Name** - VeeamServer
- **Description** - Veeam Backup & Replication 9.5 Update 3
- **vCPU(s)** - 2
- **Number of Cores per vCPU** - 1
- **Memory** - 4 GiB
- Select :fa:`pencil` beside **CD-ROM**

  - **Operation** - Clone from Image Service
  - **Image** - VeeamBR-9.5U3-ISO
  - Select **Update**
- Select **+ Add New Disk**

  - **Operation** - Clone from Image Service
  - **Image** - Windows2012
  - Select **Add**
- Select **+ Add New Disk**

  - **Operation** - Allocate on Storage Container
  - **Storage Container** - Default
  - **Size (GiB)** - 250
  - Select **Add**
- Select **Add New NIC**

  - **VLAN Name** - Primary
  - Select **Add**
- Select **Custom Script**
- Select **Type or Paste Script**

.. literalinclude:: VeeamServer-unattend.xml
   :caption: VeeamServer Unattend.xml Custom Script
   :language: xml

.. note::

 The Unattend script will change the hostname to **VeeamServer**, join the **NTNXLAB.local** domain, and disable the Windows Firewall.

Select the **VeeamServer** VM and click **Power on**.

Once the VM has started, click **Launch Console**.

.. note::

  The VM can also be accessed via Microsoft RDP, enabling you to copy and paste text from the lab guide into the virtual machine.

Open **PowerShell** and execute the following command:

.. code-block:: posh
  :emphasize-lines: 1

  # Initializes and formats the 250GB disk added to the VeeamServer VM
  Get-Disk | Where partitionstyle -eq 'raw' | Initialize-Disk -PartitionStyle MBR -PassThru | New-Partition -AssignDriveLetter -UseMaximumSize | Format-Volume -FileSystem NTFS -NewFileSystemLabel "Backups" -Confirm:$false

.. Download the :download:`Veeam NFR license file<./veeam_availability_suite_nfr_6_6.lic>`.

Open the **Veeam Backup and Replication 9.5** Setup from the mounted .iso image. Click **Install**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/1.png

Select **I accept the terms in the license agreement** and click **Next**.

Click **Browse** and select the downloaded Veeam NFS license file. Click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/2.png

Review selected Program Features. Click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/3.png

If prompted for missing pre-requisite components, click **Install**. After completion, click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/4.png

Review configuration and click **Install**. While waiting for installation to complete, proceed to `Deploying Veeam Proxy Appliance`_.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/5.png

.. note::

  By default the Veeam Backup Server will deploy a Windows SQL Server Express database instance. A production Veeam deployment would use an external, highly available database. The installer will also create a Veeam Backup Repository to act as a backup target, by default it will select the volume with the most free space exposed to the backup server (the local 250GB disk added to the **VeeamServer** VM). For storing backups of Nutanix AHV VMs, Veeam currently supports the use of simple backup repositories, scale-out backup repositories, and ExaGrid appliances. HPE StoreOne and DellEMC DataDomain appliances are not currently supported.

Deploying Veeam Backup Proxy
++++++++++++++++++++++++++++

In **Prism > VM > Table**, click **+ Create VM**.

Fill out the following fields and click **Save**:

- **Name** - VeeamProxy
- **Description** - Veeam Backup Proxy
- **vCPU(s)** - 2
- **Number of Cores per vCPU** - 1
- **Memory** - 4 GiB
- Select **+ Add New Disk**

  - **Operation** - Clone from Image Service
  - **Image** - VeeamBackupProxy
  - Select **Add**
- Select **Add New NIC**

  - **VLAN Name** - Primary
  - Select **Add**

Select the **VeeamProxy** VM and click **Power on**.

Once the VM has started, open \https://<*VeeamProxy-VM-IP*>:8100/ in a browser. Log in using the default credentials:

- **Username** - admin
- **Password** - admin

Click **Install**.

Select **I accept the terms of the license agreement** and click **Next**.

Fill out the following fields and click **Next**:

- **Old Password** - admin
- **New Password** nutanix/4u
- **Confirm New Password** - nutanix/4u

Fill out the following fields and click **Next**:

- **Appliance host name** - VeeamProxy
- Select **Obtain an IP address automatically**

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/6.png

Click **Finish**. After approximately 1 minute you will be redirected to log in to the **Veeam Backup Proxy Web Console**.

Adding A Nutanix Cluster
++++++++++++++++++++++++

Log in to the **Veeam Backup Proxy Web Console** using the updated **admin** credentials.

.. note:: The **Dashboard** and **Events** pages are unavailable in this early access build.

From the toolbar, click :fa:`cog` **> Manage Nutanix Clusters**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/7.png

Click **+ Add**, fill out the following fields and click **Add**:

  - **Cluster name of IP** - *<Cluster Virtual IP>*
  - **Port** - 9440
  - **Description** - *<Cluster Name>*
  - **User** - admin
  - **Password** - techX2018!

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/8.png

Adding A Veeam Backup Server
++++++++++++++++++++++++++++

From the toolbar, click :fa:`cog` **> Manage Veeam Servers**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/7.png

Click **+ Add**, fill out the following fields and click **Add**:

  - **DNS Name or IP** - *<VeeamServer IP>*
  - **Port** - 10006
  - **Description** - *<Cluster Name>* VeeamServer
  - **User Name** - Administrator
  - **Password** - nutanix/4u

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/9.png

Backing Up A VM
+++++++++++++++

Veeam Backup & Replication backs up Nutanix AHV VMs at the image level, just like VMware vSphere and Microsoft Hyper-V VMs. The Backup Proxy communicates with Nutanix AHV to trigger a VM snapshot, retrieves VM data block by block from Storage Containers hosting VMs, compresses and deduplicates the data, and writes to the Backup Repository in Veeam’s proprietary format.

For AHV VMs, Veeam Backup & Replication utilizes the forever forward incremental backup method. During the initial backup, Veeam  copies the whole content of the VM and creates a full backup file (VBK) in the target location. The full backup file acts as a starting point of the backup chain. During subsequent backup sessions, Veeam copies only those data blocks that have changed since the previous backup, and stores these data blocks to an incremental backup file in the target location. Incremental backup files depend on the full backup file and preceding incremental backup files in the backup chain. The Backup Proxy integrates with Nutanix's Change Block Tracking (CBT) API to determine the changed portion of a VM's data to enable efficient, incremental backups.

In **Prism > VM > Table**, click **+ Create VM**.

Fill out the following fields and click **Save**:

- **Name** - WS12-VeeamBackupTest
- **Description** - WS12-VeeamBackupTest
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

Select the **WS12-VeeamBackupTest** VM and click **Power on**.

Once the VM has started, click **Launch Console**.

Complete the Sysprep process and provide a password for the local Administrator account.

Log in as the local Administrator and create multiple files on the desktop (e.g. documents, images, etc.).

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/10.png

From the **Veeam Back Proxy Web Console**, select **Backup Jobs** from the toolbar.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/11.png

Click **+ Add**, fill out the following fields, and click **Next**:

- **Job Name** - Test Job

Click **+ Add** and select **<Cluster Virtual IP> > Unprotected VMs > WS12-VeeamBackupTest**. Click **Add**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/12.png

.. note:: Dynamic Mode allows you to backup all VMs within a Nutanix Protection Domain.

Click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/13.png

Select **Default Backup Repository** and click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/14.png

Fill out the following fields and click **Next**:

- Select **Run this job automatically**
- Select **Periodically every:**
- Select **1**
- Select **Hour**
- **Restore Points to keep on disk** - 5

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/15.png

Select **Run backup job when I click Finish** and click **Finish**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/16.png

Monitor **Test Job** until the job completes successfully. Click **Close**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/17.png

To manually trigger a backup job, select **Test Job** from **Veeam Backup Proxy Web Console > Backup Jobs**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/18.png

Click **Start**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/19.png

.. note:: The second backup job should complete in less than 1 minute as there should be no significant delta between the initial full backup and the manually triggered incremental backup.

Restoring A VM
++++++++++++++

Using the Backup Proxy Web Console, you can restore a VM from backup to the Nutanix AHV cluster. At present, Veeam Backup & Replication does not support restoring from one Nutanix cluster to another. During the restore process, the Backup Proxy retrieves VM disk data from the backup on the Veeam Backup Repository, copies it to the Storage Container where disks of the original VM were located, and registers a restored VM on the Nutanix AHV cluster.

From the **Veeam Back Proxy Web Console**, select **Protected VMs** from the toolbar.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/20.png

Select an available Restore Point and click **Restore**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/21.png

Verify selection and click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/22.png

Select **Restore to a new location** and click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/23.png

Select **WS12-VeeamBackupTest** and click **Rename VM**. Select **Add suffix** and click **OK > Next**:

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/24.png

Click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/25.png

.. note::

  The ability to modify destination Storage Container or Disk Type is unavailable in this early access build.

Specify a reason for the restore operation and click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/26.png

Click **Finish** and monitor the restore operation until successfully completed.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/27.png

Verify the restored VM is available in Prism.

.. note::

  In addition to full VM restores, the **Veam Backup Proxy Web Console** can also restore individual virtual disks which can be mapped to any VM within the cluster. This functionality can be helpful if virtual disks containing data become corrupted (e.g. cryptolocker, virus, etc.).

Restoring Granular VM Data
++++++++++++++++++++++++++

From the **VeeamServer** console, open **Veeam Backup & Replication Console**.

From the **Home** tab, select **Restore > Guest Files (Microsoft Windows)**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/28.png

Select the **WS12-VeeamBackupTest** VM and click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/29.png

Select an available Restore Point and click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/30.png

Specify a reason for the restore operation and click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/31.png

Click **Finish**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/32.png

Navigate to the desired files in the **Backup Browser**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/veeam/33.png

Takeaways
+++++++++

  - Veeam is a widely adopted backup technology that will soon feature native support for Nutanix AHV.
  - Veeam provides agentless VM backup.
  - Veeam has advanced restore capabilities including support for file level restore, Microsoft Active Directory, Microsoft Exchange, Microsoft SQL Server, and Oracle.

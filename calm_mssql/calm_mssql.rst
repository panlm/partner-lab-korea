.. _calm_mssql_lab:

***************************
Calm Blueprint (MSSQL 2014)
***************************

Overview:
*********

.. note::

  This lab should be completed **AFTER** the :ref:`karan_lab` lab.

  Estimated time to complete: **20 MINUTES**

  **IMPORTANT!** This Blueprint is for test purposes only. Do not put this into production without modifying it to meet your organizations requirements, Nutanix Best Practices, and Microsoft SQL Best Practices.

In this exercise you will use Calm to import and deploy a Blueprint that installs a Microsoft SQL Server 2014 Instance on a Windows Server 2012 R2 VM. This exercise serves as an example of the extensibility of Calm for automating and orchestrating Windows based applications.

Getting Engaged with the Product Team
=====================================
- **Slack** - #calm
- **Product Manager** - Jasnoor Gill, jasnoor.gill@nutanix.com
- **Product Marketing Manager** - Chris Brown, christopher.brown@nutanix.com
- **Technical Marketing Engineer** - Brian Suhr, brian.suhr@nutanix.com
- **Field Specialists** - Mark Lavi, mark.lavi@nutanix.com; Andy Schmid, andy.schmid@nutanix.com

Uploading the Blueprint
***********************
Download the provided :download:`MSSQL Blueprint<./blueprints/windowsMSSQL2014.json>`.

From **Prism Central > Apps**, select |image1| **Blueprints** from the sidebar.

Click **Upload Blueprint**.

.. figure:: https://s3.amazonaws.com//s3.nutanixworkshops.com/calm/lab3/image2.png

Select the downloaded MSSQL Blueprint (e.g. **windowsMSSQL2014.json**) and click **Open**.

Fill out the following fields and click **Upload**:

- **Blueprint Name** - mssql2014*<INITIALS>*
- **Project** - Calm

Updating the Credential
***********************
Blueprints are exported as clear text and subsequently do not retain credential details, as this information could be used maliciously.

Click **Credentials** and select **WINDOWS (Default)**.

Fill out the following fields and click **Back**:

- **Username** - ntnxlab.local\\administrator
- **Secret** - Password
- **Password** - nutanix/4u

Click **Save** to save the updated Blueprint.

Configuring Service Variables
*****************************
The Blueprint uses the following Service Variables to configure pre-runtime and post-runtime behavior:

+-----------------------+----------------------------------------------------------------------+
|**Variable Name**      |**Description**                                                       |
+-----------------------+----------------------------------------------------------------------+
|install_location       |A location directive (internal/external) definng if the image is      |
|                       |accessible internally (fiel share), or externally                     |
|                       |(Microsoft download site).                                            |
+-----------------------+----------------------------------------------------------------------+
|file_share_user        |The username used to authenticate to the internal/external file share.|
+-----------------------+----------------------------------------------------------------------+
|file_share_password    |The username used to authenticate to the internal/external file share.|
+-----------------------+----------------------------------------------------------------------+
|file_server_ip         |IP Address of the server hosting the file share.                      |
+-----------------------+----------------------------------------------------------------------+
|file_share_name        |The name of the share/folder containing the image to be downloaded.   |
+-----------------------+----------------------------------------------------------------------+
|sql_iso_path           |The image name including the full path (if applicable),               |
+-----------------------+----------------------------------------------------------------------+
|mapped_drive           |The logical drive designator if using a mapped location/drive.        |
+-----------------------+----------------------------------------------------------------------+

Select the **MSSQL** Service from your **Workspace**.

In the **Configuration Pane**, select the **Service** tab.

Verify the Variables match the following values and click **Save**:

- **install_location** - internal
- **file_share_user** - administrator
- **file_share_password** - nutanix/4u
- **file_server_ip** - 10.21.66.59
- **file_share_name** - sql
- **sql_iso_path** - SQLServer2014SP2-FullSlipstream-x64-ENU.iso
- **mapped_drive** - z

Configuring the VM
******************

Select the **MSSQL** Service from your **Workspace**.

In the **Configuration Pane**, select the **VM** tab.

Verify the following fields:

- **Service Name** - MSSQL
- **Name** - MSSQL2014
- **Cloud** - Nutanix
- **OS** - Windows
- **VM Name** - @@{calm_application_name}@@

Fill out the following fields:

- Select :fa:`plus-circle` under **vDisks**

  - **Device Type** - Disk
  - **Device** Bus - SCSI
  - **Size (GiB)** - 100

- **vCPUs** - 2
- **Cores per vCPU** - 2
- **Memory (GiB)** - 4
- Select :fa:`plus-circle` under **Images**

  - **Image** - Windows2012
  - **Device Type** - Disk
  - **Device** Bus - SCSI
  - Select **Bootable**

- Select **Guest Customization**

  - **Type** - Sysprep
  - **Script** - Copy the contents of **unattend.xml** and paste into the **Script** field.

  .. literalinclude:: unattend.xml
     :caption: MSSQL unattend.xml Custom Script
     :language: xml

  .. note:: The sysprep unattend.xml script automates the sysprep process and automatically joins the VM to the ntnxlab.local domain.

- Select :fa:`plus-circle` under **Network Adapters (NICs)**

  - **NIC** - Secondary
- **Connection**

  - **Crendential** - WINDOWS
  - **Address** - @@{platform.status.resources.nic_list[0].ip_endpoint_list[0].ip}@@
  - **Connection Type** - Windows (PowerShell)
  - **Connection Port** - 5985
  - **Timeout (secs)** - 600

Click **Save**.

Scroll to the top of the **Configuration Panel**, click **Package**.

Configuring the Package
***********************

Scroll to the top of the **Configuration Panel**, and select the **Package** tab.

Verify the following fields:

- **Name** - MSSQLPackage
- **Install Script Type** - Shell
- **Install Credential** - WINDOWS
- **Install Script** - ``Enable-WSManCredSSP -Role Server -Force``

If any changes were required, click **Save**.

Exploring the Create Action
***************************

In addition to the Package scripts used to install or uninstall a Service, individual Services within a Calm Blueprint can contain Actions consisting of multiple Tasks and Service Actions.

Tasks allow you to set variable values, execute shell scripts on a remote VM, or execute an EScript. EScripts are specialized Python scripts that run locally within Calm and are helpful for use cases such as making REST calls directly to a web service.

Service Actions allow you to Start, Stop, or Delete the VM associated with a Service. These Actions are typically used in lifecycle operations such as scaling up or scaling down an application.

In **Application Overview**, select **Services > MSSQL > Create**.

Select the **InitializeDisk** task and review the PowerShell script in the **Configuration Pane**.

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/calm_mssql/image3.png

Select the **InstallMSSQL** task and review the PowerShell script in the **Configuration Pane**.

Configuring Priveleges
**********************

.. note::

  To successfully perform a remote SQL Server installation the user account used to run the Karan Service (e.g. NTNXLAB\\Administrator) must be granted SE_INCREASE_QUOTA_NAME and SE_ASSIGNPRIMARYTOKEN_NAME permissions on the Karan VM.

  In a production environment with multiple Karan VMs these permissions could be automatically configured via Domain Group Policy.

From the **Karan** VM console, open **Control Panel > Administrative Tools > Local Security Policy**.

Navigate to **Local Policies > User Rights Assignment**.

Double-click the **Adjust memory quotas for a process** Policy.

Click **Add User or Group**.

Specify **Administrator** in the **Enter the object names to select** field, and click **OK > OK**.

Double-click the **Replace a process level token** Policy.

Click **Add User or Group**.

Specify **Administrator** in the **Enter the object names to select** field, and click **OK > OK**.

Open **Control Panel > Administrative Tools > Services**. Select the **karan_1** service and click **Restart**.


Launching the Blueprint
***********************

From the toolbar at the top of the Blueprint Editor, click **Launch**.

In the **Name of the Application** field, specify a unique name including your initials (e.g. CalmMSSQL*<INITIALS>*-1).

Click **Create**.

You will be taken directly to the **Applications** page to monitor the provisioning of your Blueprint.

Select **Audit > Create** to view the progress of your application. Once available, select **InstallMSSQL** to view the real time output of the installation script.

Note the status changes to **Running** after the Blueprint has been successfully provisioned.

Takeaways
***********

- Blueprints can be exported as JSON files.
- Blueprints can be easily shared without sharing sensitive data such as credentials and secrets.
- Calm is capable of automating and orchestrating Windows workloads with PowerShell and uses Karan to satisfy Kerberos authentication requirements.
- Tasks can be used to structure even complex workflows for workload automation.
- Variables can be defined globally within a Blueprint or specific to a given Service.

.. |image1| image:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab3/image1.png
.. |image5| image:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab3/image5.png

.. _unattend.xml: ./unattend.html

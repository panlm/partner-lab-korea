.. _xtractvm_lab:

--------------
Xtract for VMs
--------------

Overview
++++++++

.. note::

  Estimated time to complete: **1 HOUR**

  **Due to limited resources, this lab should be completed as a group.**

  Refer to :ref:`cluster_details` to find the source VM and vCenter assigned to your team.

In this exercise you will deploy Xtract for VMs and migrate a VM from ESXi to AHV.

Getting Engaged with the Product Team
.....................................

- **Slack** - #xtract
- **Product Manager** - Jeremy Launier, jeremy.launier@nutanix.com
- **Product Marketing Manager** - Marc Trouard-Riolle, marc.trouardriolle@nutanix.com
- **Technical Marketing Engineer** - Mike McGhee, michael.mcghee@nutanix.com

Deploying Xtract for VMs
++++++++++++++++++++++++

In **Prism Central > Explore > VMs**, click **Create VM**.

Fill out the following fields and click **Save**:

- **Name** - Xtract-VM
- **Description** - Xtract for VMs
- **vCPU(s)** - 2
- **Number of Cores per vCPU** - 2
- **Memory** - 4 GiB
- Select **+ Add New Disk**

  - **Operation** - Clone from Image Service
  - **Image** - Xtract-VM
  - Select **Add**
- Remove **CD-ROM** Disk
- Select **Add New NIC**

  - **VLAN Name** - Primary
  - **IP Address** - *10.21.XX.42*
  - Select **Add**
- Select **Custom Script**
- Select **Type or Paste Script**

.. literalinclude:: xtract-vm-cloudinit-script
   :caption: Xtract-VM Custom Script

Select the **Xtract-VM** VM and click **Actions > Power on**.

Open \https://<*XTRACT-VM-IP*>/ in a browser to access the **Xtract for VMs** dashboard.

Select **I have read and agree to terms and conditions**, and click **Continue**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm05.png

Click **OK** when prompted about the **Nutanix Customer Experience Program**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm06.png

Fill out the following fields and click **Set Password**:

- **Enter New Password** - nutanix/4u
- **Re-enter New Password** - nutanix/4u

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm07.png

Log in with the following credentials:

- **Username** - nutanix
- **Password** - nutanix/4u

Migrating a VM
++++++++++++++

In this portion of the lab we will configure source and target environments, create a migration plan, and finally perform a cutover operation.

Configuring Source and Target
.............................

In **Xtract**, click **+ Add Source Environment**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm08.png

Fill out the following fields and click **Add**:

- **Source Name** - Tech Summit 2018 vCenter
- **vCenter Server** - *<Source vCenter Server IP>*
- **User Name** - administrator@vsphere.local
- **Password** - *<vCenter Password>*

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm09.png

In **Xtract**, click **+ Add Target Environment**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm08.png

Fill out the following fields and click **Add**:

- **Target Name** - *<Target Nutanix Cluster Name>*
- **Nutanix Environment** - *<Nutanix Cluster Virtual IP>*
- **User Name** - admin
- **Password** - *<Nutanix admin Password>*

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm10.png

Note both **Source** and **Target** environments have been configured.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm11.png

Creating a Migration Plan
.........................

In **Xtract**, click **Create a Migration Plan**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm12.png

Enter a **Migration Plan Name** and click **OK**:

- **Migration Plan Name** - ViewImage-Team-*<XX>* Migration.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm13.png

Fill out the following fields and click **Next**:

- **Select Target** - *<Target Nutanix Cluster Name>*
- **Target Container** - Default

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm14.png

Select **ViewImage-Team-XX** VM and click **Next**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm15.png

Fill out the following fields and click **Next**:

- **Common Windows Credentials User Name** - administrator
- **Common Windows Credentials Password** - nutanix/4u
- **Target Network** - Primary

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm16.png

Click **Save and Start**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm17.png

Monitor the status of the Migration Plan from the dashboard.

.. note::

  Clicking the **Status** link will display data migration progress and estimated time remaining for individual VMs.

  Migrations can also be paused or aborted via the Migration Plan **Action** menu.

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm18.png

Performing VM Cutover
.....................

Once the migration completes (**Migrated Data Size** should match **Data Size**), Xtract can perform a cutover operation to automatically shutdown the source VM and power on the migrated VM.

In **Xtract**, click **Migration In Progress** Status for your Migration Plan.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm19.png

Select **ViewImage-Team-XX** and click **Cutover**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm20.png

Click **Continue**.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm21.png

After the Cutover is completed you can launch Prism directly from Xtract to manage your migrated VM.

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm22.png

  .. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/xtract-vm/xtractvm23.png

Takeaways
+++++++++

- Xtract for VMs simplifies bulk migration of existing VMs to Nutanix, eliminating the friction associated with onboarding new IT infrastructure.

- Businesses can quickly leverage the full potential of Nutanix Enterprise Cloud with near-zero VM downtime during the migration from vSphere ESXi to Nutanix AHV.

- Xtract features the ability to migrate all AHV certified OSes, scheduling data-seeding and migrations, multi-cluster migration management, and grouping/sorting VMs.

- Xtract is capable of maintaining MAC addresses and static IPs for migrated VMs.

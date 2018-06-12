.. _karan_lab:

******************
Calm Karan Service
******************

Overview
*********

.. note::

  This lab is **NOT** required for the Taiwan Partner Lab and is available here for later reference.

  Estimated time to complete: **30 MINUTES**

In this exercise you will deploy the Karan Service to a Windows Server VM. Karan is a scale out service used by Calm to orchestrate Windows VMs. Karan is responsible for proxying remote PowerShell commands from Calm Blueprints to Windows VMs.

Deploying Karan VM
******************

In **Prism Central > Explore > VMs**, click **Create VM**.

Fill out the following fields and click **Save**:

- **Name** - Karan
- **Description** - Karan Server
- **vCPU(s)** - 2
- **Number of Cores per vCPU** - 2
- **Memory** - 4 GiB
- Select **+ Add New Disk**

  - **Operation** - Clone from Image Service
  - **Image** - Windows2012
  - Select **Add**
- Select **Add New NIC**

  - **VLAN Name** - vlan.0
  - Select **Add**
- Select **Custom Script**
- Select **Type or Paste Script**

.. literalinclude:: unattend.xml
   :caption: Karan Unattend.xml Custom Script
   :language: xml

.. note::

  The Unattend script will change the hostname to **Karan**, join the **NTNXLAB.local** domain, and disable the Windows Firewall.

Verify the VM has the expected hostname and has been joined to the NTNXLAB.local domain.

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/karan/image15.png

Open **PowerShell** and execute the following commands:

.. code-block:: posh
  :emphasize-lines: 1,3,5

  # Enables the VM to receive Windows PowerShell remote commands sent using WS-Management. Enabled by default in Windows Server 2012 R2 and later.
  Enable-PSRemoting -Force
  # Default execution policy in Windows Server 2012 R2 and later. Requires a digital signature from a trusted publisher on scripts and configuration files that are downloaded from the Internet. Does not require digital signatures on scripts that you have written on the local computer.
  Set-ExecutionPolicy RemoteSigned -Force
  # Adds all potential target machines (Windows VMs being orchestrated by Calm) as trusted hosts
  Set-Item WSMan:\localhost\Client\TrustedHosts -Value * -Force

Installing Karan
****************

.. note:: The Karan Service is supported on both Windows Server 2012 R2 and Windows Server 2016 and requires Poweshell 3.0+ and either .NET Framework 4.0 or .NET Framework 4.5.

From your **Karan** VM console, download the `Karan installer <http://download.nutanix.com/calm/Karan/1.6.0/Karan-1.6.0.0.exe>`_.

.. note:: The provided link to the Karan 1.6.0.0 installer is current as of March 2018.

Launch the Karan installer as an Administrator.

Click **Install > Next**.

Fill out the following fields and click **Next**:

- Select **HTTP** (Do **NOT** select **HTTPS**)
- **Port** - 8090

.. note:: If a firewall is enabled on either the Karan or target VMs, TCP port 8090 must be allowed.

- **Service Count** - 1
- **Hostname** - *<Karan VM IP Address>*
- **Gateway UUID** - 2067b70d-bd3f-4b3d-9d82-3add93f30a0a
- **Epsilon Address** - http://*<Prism Central IP Address>*:8090

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/karan/karan2.png

.. note::

  Do **NOT** click **Check Epsilon IP**.

Fill out the following fields and click **Next**:

- **Logon Account** - NTNXLAB\\Administrator
- **Password** - nutanix/4u

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/karan/karan3.png

Click **Install**.

After installation has completed, click **Finish**.

Open **Control Panel > Administrative Tools > Services**. Select the **karan_1** service and click **Start**.

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/karan/karan4.png

.. note:: If the service fails to start, uninstall Karan and closely follow the installation steps. Common mistakes include selecting HTTPS or providing an incorrect Gateway UUID.

Enabling CredSSP
****************

CredSSP authentication allows user credentials to be passed to a remote computer to be authenticated. This type of authentication is designed for commands that create a remote session from another remote session. This allows Calm to validate credentials of a target VM through the Karan Service.

Open **PowerShell** and execute the following commands:

.. code-block:: posh
  :emphasize-lines: 1,3

  # Enables CredSSP as a client role and allows Karan VM to delegate credentials to all computers
  Enable-WSManCredSSP -Role Client -DelegateComputer * -Force
  # Opens Local Group Policy Editor
  gpedit

In the **Local Group Policy Editor**, open **Computer Configuration > Administrative Templates > System > Credentials Delegation**.

Double-click **Allow delegating fresh credentials with NTML-only server authentication**.

Select **Enabled**.

Click **Show** and specify **WSMAN/*** in the **Value** field.

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/karan/karan5.png

Click **OK > OK**.

.. figure:: https://s3.us-east-2.amazonaws.com/s3.nutanixtechsummit.com/karan/karan6.png

Takeaways
*********
- Calm can orchestrate Windows workloads via PowerShell scripts distributed with the Karan Service.
- Multiple Karan servers can be deployed in the same environment to meet the needs of an increasing number of Windows VMs.

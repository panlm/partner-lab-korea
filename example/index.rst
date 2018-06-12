.. Adding labels to the beginning of your lab is helpful for linking to the lab from other pages
.. _example_lab:

-----------
Example Lab
-----------

Overview
++++++++

Here is where we provide a high level description of what the user will be doing during this module. We want to frame why this content is relevant to an SE/Services Consultant and what we expect them to understand after completing the lab.

Using Text and Figures
++++++++++++++++++++++

Label sections appropriately, see existing labs if further guidance is required. Section titles should begin with present tense verbs to queue what is being done in each section. Use consistent markup for titles, subtitles, sub-subtitles, etc. The markup in the example can serve as a guide but other characters can be used within a given workshop, as long as they are consistent. Other than lab titles (that need to follow a certain linear progression) avoid numbering steps.

Below are examples of standards we should strive to maintain in writing lab guides. *Italics* is used to indicate when information of values external to the lab guide are referenced. **Bold** is used to reference words and phrases in the UI. **Bold** should also be used to highlight the key name in lists containing key/value pairs as shown below. The **>** character is used to show a reasonable progression of clicks, such as traversing a drop down menu. When appropriate, try to consolidate short, simple tasks. ``Literals`` should be used for file paths.

Actions should end with a period, or optionally with a colon as in the case of displaying a list of fields that need to be populated. Keep the language consistent: open, click/select, fill out, log in, and execute.

Use the **figure** directive to include images in your lab guide or appendix. Image files should not be included within the Git repository. Image files should be uploaded to the nutanixworkshops bucket in S3. Create a subdirectory in S3 that corresponds to the subdirectory for your workshop within the Git repository.

--------------------------------------

Open \https://<*NUTANIX-CLUSTER-IP*>:9440 in your browser to access Prism. Log in as a user with administrative priveleges.

  .. figure:: http://s3.nutanixworkshops.com/templates/ahv_windows/1.png

Click **Network Config > User VM Interfaces > +Create Network**.

  .. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab1/1.png

Select **Enable IP Address Management** and fill out the following fields:

  - **Name** - VM VLAN
  - **VLAN ID** - *Refer to your Environment Details Worksheet*
  - **Network IP Address/Prefix Length** - *Refer to your Environment Details Worksheet*
  - **Gateway IP Address** - *Refer to your Environment Details Worksheet*
  - **Domain Name Servers** - *Refer to your Environment Details Worksheet*

  .. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab1/2.png

Click **Submit > Save**.

Using Notes
+++++++++++

Notes provide easily noticable interjections to lab instructions. Reasons to use a note include calling attention to a step that requires additional context or referencing external resources. Make sure to include a return before and after the note directive for it to render properly. **Please don't abuse notes.**

.. note::

  Check out `this <http://openalea.gforge.inria.fr/doc/openalea/doc/_build/html/source/sphinx/rest_syntax.html>`_ cheat sheet for helpful documentation on formatting text, links, tables, etc. in Restructured Text.

Using Icons
+++++++++++

The Prism UI includes several unlabeled icons. When directing a user to one of these actions or menus, use the example inline markup below:

In **Prism**, click :fa:`cog` **> Manage VM High Availability**.

  .. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab9/6.png

Under **Disks**, select :fa:`pencil` to change the **CD-ROM** device configuration.

  .. figure:: http://s3.nutanixworkshops.com/vdi_ahv/lab11/3.png

Click :fa:`eject` to unmount the disk image from the **CD-ROM** device.

It is **not** necessary to include inline icons when the are accompanied by a label/text in the UI, such as :fa:`times` **Delete**

Using Codeblocks
++++++++++++++++

You can insert code into your lab guides both inline and via external files. Inline is a great option for providing command line instructions or display shorter code snippets.

.. note::

  `Here <http://www.sphinx-doc.org/en/stable/markup/code.html>`_ is a reference for even more code options available in Sphinx, including emphasizing individual lines.

Inline
......

Using an SSH client, execute the following:

  .. code-block:: bash
    :name: inline-code-example
    :caption: INLINE CODE EXAMPLE

    > ssh nutanix@<NUTANIX-CLUSTER-IP>
    > acli
    <acropolis> vm.create XD num_vcpus=4 num_cores_per_vcpu=1 memory=8G
    <acropolis> vm.disk_create XD cdrom=true empty=true
    <acropolis> vm.disk_create XD clone_from_image=<Windows 2012 Disk Image Name>
    <acropolis> vm.nic_create XD network=<Network Name> ip=<XD IP Address>
    <acropolis> vm.on XD

.. note:: When using **acli**, you can use the Tab key to autocomplete fields. Pressing Tab twice lists available namespaces and values.

The name/caption arguments are optional, and should only be used is you need to reference the code from another part of the document, like this: :ref:`inline-code-example`

External File
.............

Use the literalinclude directive to create a code block from an external file. The best option when dealing with longer scripts or the content of the script is maintained separate from the lab guide.

  .. literalinclude:: example.py
     :language: python
     :emphasize-lines: 2,7-8
     :linenos:
     :caption: EXAMPLE.PY
     :name: literal-include-example

.. note:: For proper color markup, specify the language of your code using one of the supported lexers found at `pygments.org <http://pygments.org/docs/lexers/>`_.

Creating a VM
+++++++++++++

Example markup for creating a VM in Prism Element:

  In **Prism > VM > Table**, click **+ Create VM**.

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

  Select the **Xtract-VM** VM and click **Power on**.

  Once the VM has started, click **Launch Console**.

Example markup for creating a VM in Prism Central:

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

  Select the **Xtract-VM** VM and click **Actions > Power on**.

  Once the VM has started, click **Actions > Launch console**.

Example markup for creating a Service in Calm:

  In **Application Overview > Services**, click :fa:`plus-circle`.

  Note **Service1** appears in the **Workspace** and the **Configuration Pane** reflects the configuration of the selected Service. You can rearrange the Service icons on the Workspace by clicking and dragging them.

  Fill out the following fields:

  - **Service Name** - APACHE_PHP
  - **Name** - APACHE_PHP_AHV
  - **Cloud** - Nutanix
  - **OS** - Linux
  - **VM Name** - APACHE_PHP
  - **Image** - CentOS
  - **Device Type** - Disk
  - **Device Bus** - SCSI
  - Select **Bootable**
  - **vCPUs** - 2
  - **Cores per vCPU** - 1
  - **Memory (GiB)** - 4
  - Select :fa:`plus-circle` under **Network Adapters (NICs)**
  - **NIC** - Secondary
  - **Crendential** - CENTOS

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


Takeaways
+++++++++

- Here is where we summarize any key takeaways from the module
- Such as how a Nutanix feature used in the lab delivers value
- Or highlighting a differentiator

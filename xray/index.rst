-----
X-Ray
-----

Overview
++++++++

.. note::

  **This lab should be completed at the end of the day on Tuesday as the Extended Node Failure test will run for several hours. If using a cluster with 128GB RAM nodes, shut down VMs provisioned via Calm Blueprints prior to running the Extended Node Failure Test.**

  Estimated time to complete: **1 HOUR**

  **Due to limited resources, this lab should be completed as a group.**

X-Ray is an automated testing application for virtualized infrastructure solutions. It is capable of running test scenarios end-to-end to evaluate system attributes in real-world use cases. The test scenarios in X-Ray provide information about the following system attributes:

- **Data Availability and Performance During Failure or Maintenance**
  X-Ray performs modeled failure scenarios to test data availability during a failure. Systems should be able to handle failures without losing data and with minimal
  impact to performance.
- **Performance Consistency with Mixed Workloads**
  X-Ray tests the system's ability to handle mixed workloads, demonstrating the degree to which workloads may interfere with one another. Systems should be able to
  perform well with mixed workloads.
- **Feature Set Implications**
  X-Ray tests use standard APIs throughout tests to clone and manage VMs, take snapshots, and perform other system manipulations. Systems should perform efficiently while using features intended for virtualized infrastructure.

Getting Engaged with the Product Team
.....................................

- **Slack** - #x-ray
- **Product Manager** - Priyadarshi Prasad, priyadarshi@nutanix.com
- **Product Marketing Manager** - Marc Trouard-Riolle, marc.trouardriolle@nutanix.com
- **Technical Marketing Engineer** - Gary Little, gary@nutanix.com
- **Engineering** - Chris Wilson, chris.wilson@nutanix.com

Configuring X-Ray VM
++++++++++++++++++++

In **Prism > VM > Table**, select the **X-Ray** VM and click **Update**.

Click **Set Affinity**. Select all hosts **EXCEPT** *<Cluster Name>-1*. Click **Save**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/0.png

Repeat for **DC** VM.

.. note::

  As X-Ray powers down hosts for tests that evaluate availability and data integrity, it is best practice to run the X-Ray VM outside of the target cluster. Additionally, the X-Ray VM itself creates a small amount of storage and CPU overhead that could potentially skew results. Due to resource constraints, your X-Ray VM is running on the cluster you will be targetting for tests. Tests run as part of this exercise only power off the first host in the cluster, so the X-Ray VM has been excluded from running on the first host to prevent accidentally being powered off.

  For environments where DHCP is unavailable, X-Ray supports "Zero Configuration" networking, where the VMs communicate via self-assigned link local IPv4 addresses. In order to work, all of the VMs (including the X-Ray VM) need to reside on the same Layer 2 network. To use Zero Configuration networking, your X-Ray VM's first NIC (eth0) should be on a network capable of communicating with your cluster. A second NIC (eth1) is added on a network without DHCP. No action is required as the X-Ray VM has already been created with both NICs, as seen below.

  **DO NOT ENABLE IPAM ON THE "Link-Local-DO-NOT-TOUCH" NETWORK!**

Open \https://<*XRAY-VM-IP*>/ in a browser. Enter a password for the local secret score, such as your HPOC cluster password, and click **Enter**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/2.png

Select **I have read and agree to the terms and conditions** and click **Accept**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/3.png

Select **I have read and agree to the terms and conditions** and click **Accept**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/4.png

.. Click **Use Token** and enter **XRY-BNJMN-AYIM-GDQ7**. Click **Activate > Done**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/1c.png

Click **Log in** and specify your my.nutanix.com credentials. Fill out the following fields and click **Activate**:

  - **Customer Name** - Nutanix Sales Enablement
  - **Opportunity ID** - Sales Enablement Opportunity
  - **Choose a reason for using X-Ray** - Self training on Nutanix

Select **Targets** from the navigation bar and click **+ New Target**. Fill out the following fields and click **Next**:

  - **Name** - *<Nutanix Cluster Name>*
  - **Manager Type** - Prism
  - **Power Management Type** - IPMI
  - **Username** - ADMIN
  - **Password** - ADMIN
  - **Prism Address** - *<Nutanix Cluster Virtual IP>*
  - **Username** - xray
  - **Password** - nutanix/4u

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/5b.png

.. note::

  The local Prism account **xray** has already been created for you. To function, the Prism service account used for the connection requires the **Cluster Admin** role.

Select **Link-Local-DO-NOT-TOUCH** under **Network** and click **Next**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/6.png

Select **Supermicro** from the **IPMI Type** menu. Review **Node Info** and click **Save**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/7.png

Adding Custom X-Ray Test
++++++++++++++++++++++++

Download ``http://10.21.64.50/images/XRay-Extended-Node-Failure-25-VDI-VMs.zip``.

In the navigation bar, click :fa:`cog` **> Add Custom Scenario**. Click **Choose File** and select **XRay-Extended-Node-Failure-25-VDI-VMs.zip**. Click **Save**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/13.png

.. note::

  You **DO NOT** need to make any modifications to the files below. You have already imported the custom test as part of the previous step. The following steps only serve to explain what changes were made to the X-Ray test.

Click **Tests** in the navigation bar and click **Run Test** in the lower left-hand panel. Select **Extended Node Failure** and click **Actions > Export**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/14.png

Unzip the package and open **test.yml**. Each test is comprised of a YAML file that defines the test and profiles used by **FIO** to generate storage load. In the excerpt of the YAML file below, note the highlighted lines. The test will provision 75x 2 vCPU/2GB RAM VMs, each with 1x 16GB disk.

  .. literalinclude:: original-test.yml
     :language: yaml
     :lines: 1-5,53-82
     :emphasize-lines: 17-22
     :linenos:
     :caption: Extended Node Failure - test.yml
     :name: originial-test.yml

Open **vdi.fio**. Note the highlighted lines below. As part of the test, 10GB of the 16GB disk will be prefilled. In the Extended Node Failure test, the VDI VMs only exist to fill up storage capacity. **This capacity is what ensures the cluster has work to do in reprotecting data after a node failure.**

  .. literalinclude:: original-vdi.fio
     :language: ini
     :emphasize-lines: 12,16,23,25
     :linenos:
     :caption: Extended Node Failure - vdi.fio
     :name: originial-vdi.fio

Due to memory restrictions, your cluster may not be able to support running the full VDI workload. To address this you will install a modified version of the test that will provision 25x 2vCPU/1GB RAM VMs, each with 1x 16GB disks prefilled with 10GB of data. Note the highlighted lines below for the key changes to the test. At test runtime, X-Ray will programmatically generate an FIO configuration to fill disks based based on the corresponding workload .fio file.

  .. literalinclude:: test.yml
     :language: yaml
     :lines: 1-5,55-84
     :emphasize-lines: 1,3,18,22
     :linenos:
     :caption: Extended Node Failure (25 VDI VMs) - test.yml
     :name: test.yml

..  .. literalinclude:: vdi.fio
    :language: ini
    :emphasize-lines: 31-37
    :linenos:
    :caption: Extended Node Failure (25 VDI VMs) - vdi.fio
    :name: vdi.fio

Running X-Ray Tests
++++++++++++++++++++

Click **Tests** in the navigation bar and click **Run Test** in the lower left-hand panel. Select **Four Corners Microbenchmark** and review the setup, measurement, and test requirements. Select your cluster from the **Targets** drop down menu and click **Add to Queue**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/9.png

Click **Tests** in the navigation bar and click **Run Test** in the lower left-hand panel. Select **Extended Node Failure (25 VDI VMs)** and review the setup, measurement, and test requirements. Select your cluster from the **Targets** drop down menu and click **Add to Queue**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/8.png

Select **Four Corners Microbenchmark** under **In Progress** to view test status. Clicking **In Progress** in the right-hand pane will provide additional detail on the current stage of the test.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/10.png

In **Prism > VM > Table**, observe X-Ray has created the Worker VMs and that each has received a 169.254.XXX.XXX IP address. Be patient, as receiving a self-assigned IP will not occur until attempts to obtain an IP via DHCP time out.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/11.png

Continue to monitor the test progress in the X-Ray console. The Four Corners test will run for approximately 15 minutes after the Worker VMs have been provisioned.

Upon completion, all Worker VMs and images will be removed from the cluster and the next queued test will begin. You can queue multiple tests against a single target, X-ray will execute one test per target at a time.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/12.png

Continue to monitor the test progress in the X-Ray console until it has reached the **OLTP: Prefilling** stage. The test will continue to run for the next ~10 hours.

Working with X-Ray Results
++++++++++++++++++++++++++

Click **Tests** in the navigation bar and select your completed **Four Corners Microbenchmark** test. Note that the graphs are interactive, you can click and drag on an individual graph to zoom in on a section of data.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/15.png

Select **Actions > Create Report** to generate a PDF report of the test. Note the report includes a table with the maximum IOPS and throughput figures attained during the test.

Select **Actions > Export test as .zip** to export your test data to save externally. Note that report generation and exporting test results can be performed in bulk by selecting the checkbox next to multiple tests.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/16.png

Click **Tests** in the navigation bar and select your completed **Extended Node Failure (25 VDI VMs)** test.

Select **Actions > Add Note** to provide additional context for your test results. Notes could include commentary on the results themselves or additional helpful information such as the hardware configuration used for the test. Notes aren't included in reports, but are included as part of exporting test results.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/17.png

Observe the graphs for **OLTP IOPS VM1**, **VM2**, and **VM3**. Immediately after Node 0 is powered off the cluster began reprotecting data with minimal impact to running workloads.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/21.png

Download ``http://10.21.64.50/images/XRay-Results.zip``.

In the navigation bar, click :fa:`cog` **> Import Test Results**. Click **Choose File** and select **XRay-Results.zip**. Click **Import**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/18.png

.. note::

  The data you are importing is VSAN 6.6 sample data.

  **VMware's EULA prevents individuals from distributing or publishing performance data without their consent. This data should only be used for this exercise - it is not to be shared with customers, partners, media, etc.**

Click **Analyses** in the navigation bar and click **Create Analysis** in the lower left-hand panel. Select **Sample: NX-3060** and **HCLVSAN-6.6** and click **Create**.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/19.png

.. note:: X-Ray will only compare results for the exact same test type, which is the reason Sample Extended Node Failure data is being used in this exercise.

Observe the resultant graphs with data overlayed from both sets of test results. Because X-Ray delivers consistent test automation that can be used to evaluate multiple platforms, the **Analyses** page is able to provide objective comparisons that extend beyond specification sheets and into real world scenarios. Test comparisons can also be exported as PDF reports for follow up with customers and prospects.

In this scenario we see a slight impact to Nutanix (green) OLTP IOPS immediately after Node 0 is powered off and soon returning to a steady 4,000 IOPS. The VSAN (blue) workload is unimpacted until 60+ minutes after Node 0 is powered off. This is due to VSAN waiting 60 minutes before beginning to rebuild the missing data. Once rebuilding begins, significant disruption to running workloads is seen as the OLTP VMs are unable to maintain 4,000 IOPS.

  .. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/ts18/xray/20.png

Takeaways
+++++++++++

  - Best practice is to deploy X-Ray on an external system.
  - X-Ray doesn't require complex network configuration and can be used with or without DHCP.
  - X-Ray is a key tool for differentiating the superior architecture of Nutanix compared to platforms such as VSAN, HyperFlex, and Simplivity.
  - X-Ray uses industry standard load generation tools such as **fio** and transparent/modifiable test cases to build customer confidence in test results.
  - In the same way Nutanix elevates conversation above basic HCI to focus on the real impacts of the Enterprise Cloud, X-Ray elevates conversations around platform evaluation and can act as key, measurable success criteria for POCs.
  - X-Ray can also be used in non-competitive situations to build customer confidence in the Nutanix platform and potential grow deal sizes and take on additional workloads.

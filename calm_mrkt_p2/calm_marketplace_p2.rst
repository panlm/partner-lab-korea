**************************
Calm Marketplace Part 2
**************************


Overview
************

.. note::

  This exercise assumes you have a Blueprint available from a previous exercise.

  Estimated time to completion: **40 MINUTES**

In this exercise you will learn how to manage Calm Blueprints within the Nutanix Marketplace. As part of the exercise you will publish a Blueprint from the Blueprint Editor, use Marketplace Manager to approve, assign roles and projects, and publish to the Marketplace. Finally you will edit a project environment so your Blueprint can be launched directly from the Marketplace.

Publishing Blueprints
*********************

From **Prism Central > Apps**, select |image1| **Blueprints** from the sidebar.

Open any **Active** Blueprint by clicking on its **Name**.

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image17.png

Click **Publish**.

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image15.png

Provide a **Name** (e.g. Blueprint Name *<INITIALS>*) and **Description**, and click **Publish**.

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image18.png

Approving Blueprints
********************

From **Prism Central > Apps**, select |image4| **Marketplace Manager** from the sidebar.

.. note:: You must be logged in as a Cluster Admin user to access the Marketplace Manager.

Note your Blueprint does not appear in the list of **Marketplace Items**.

Select the **Approval Pending** tab.

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image19.png

Select your **Pending** Blueprint.

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image20.png

Review the available actions:

- **Reject** - Prevents  Blueprint from being launched or published in the Marketplace. The Blueprint will need to be submitted again after being rejected before it can be published.
- **Approve** - Approves the Blueprint for publication to the Marketplace.
- **Launch** - Launches the Blueprint as an application, similar to launching from the Blueprint Editor.

Click **Approve**.

Once the application has been successfully approved, assign the appropriate **Category** and **Project Shared With**. Click **Apply**.

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image21.png

Select the **Marketplace Blueprints** tab and select your Blueprint. Click **Publish**.

Verify the Blueprint's **Status** is now shown as **Published**.

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image23.png

From **Prism Central > Apps**, select |image6| **Marketplace** from the sidebar. Verify your Blueprint is available for launching as an application.

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image24.png

Configuring Project Environment
*******************************

To launch a Blueprint directly from the Marketplace, we need to ensure our Project has all of the requisite environment details to satisfy the Blueprint.

From **Prism Central > Apps**, select |image13| **Projects** from the sidebar.

Select the Project **Name** associated with your Blueprint at the time of publishing (e.g. the **Calm** Project that was assigned as **Project Shared With**).

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image26.png

Select the **Environment** tab.

Under **Network Adapters (NICs)**, click :fa:`plus-circle` and select **Secondary**.

Under **Connection > Credential**, select **Add New Credential**. Fill out the following fields and click **Done**:

- **Credential Name** - CENTOS
- **Username** - root
- **Secret** - Password
- **Password** - nutanix/4u
- Select **Use as default**

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image27b.png

Click **Save**.

Launching Blueprint from the Marketplace
****************************************

From **Prism Central > Apps**, select |image6| **Marketplace** from the sidebar.

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image24.png

Select the Blueprint published as part of this exercise and click **Launch**.

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image28.png

Select the **Calm** Project and click **Launch**.

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image29.png

Specify a unique **Application Name** (e.g. Marketplace*<INITIALS>*) and click **Create**.

.. note::

  To see the configured **Environment** details, expand the **VM Configurations** entities.

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image30.png

Monitor the provisioning of the Blueprint until complete.

.. figure:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image31.png

Takeaways
*********
- Developers can publish Blueprints to the Marketplace for fast and easy consumption by users.
- Blueprints can be launched directly from the Marketplace with no additional configuration from users, delivering a public cloud-like SaaS experience for end users.
- Administrators have control over what Blueprints are published to the Marketplace and which projects have access to published Blueprints.

.. |image1| image:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image14.png
.. |image4| image:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab4/image4.png
.. |image6| image:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image10.png
.. |image13| image:: https://s3.amazonaws.com/s3.nutanixworkshops.com/calm/lab8/image25.png

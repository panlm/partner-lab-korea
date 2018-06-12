**************************
Calm Marketplace Part 1
**************************


Calm Glossary
*************

**Service:** One tier of a multiple tier application. This can be made up of 1 more VMs (or existing machines) that all have the same config and do the same thing

**Application (App):** A whole application with multiple parts that are all working towards the same thing (for example, a Web Application might be made up of an Apache Server, a MySQL database and a HAProxy Load balancer. Alone each service doesn’t do much, but as a whole they do what they’re supposed to)

**Macro:** A Calm construct that is evaluated before being ran on the target machine. Macros and Variables are denoted in the *@@{[name]}@@* format in the scripts.

Lab Overview
************

In this lab participants will learn how to manage NuCalm Blueprints within the NuCalm Marketplace.  After this lab
participants should know how to navigate and manage the Marketplace, publish blueprints to the market, deploy and/or clone
blueprints from the marketplace.

Part 1: Accessing and Navigating Calm
*************************************

Getting Familiar with the Tools

1. Connect to https://[HPOC-IP-ADDRESS]:9440
2. Login to Prism using the credentials specified above (use these credentials unless specified otherwise throughout this lab
3. Click on the Apps tab across the top of Prism

Welcome to Calm! Upon accessing this page you will now notice a new ribbon along the left ­ this is used to navigate through Calm.

You are, by default, dropped into the Applications tab and can see all the instances of applications that have been launched from a blueprint.

**Tab review:**

.. figure:: http://s3.nutanixworkshops.com/calm/lab4/image2.png

Part 2: Marketplace Control - Publish Blueprints
************************************************

Navigate the to Marketplace control by clicking (|image1|) icon located on the left tool ribbon.  This will open the Marketplace Control Center where pre-configured and self-authored blueprints are staged for publishing to the local Marketplace used for teaming and collaboration.

In this exercise we'll walk through the steps to:

1. Publish the pre-configured MongoDB Blueprint to the local Marketplace
2. Clone the Blueprint from the Marketplace for editing.
3. Edit the blueprint and launch as an application.

Make sure *Marketplace Blueprints* is selected along the top of the Blueprint grid. Locate the **Mongo** blueprint within the grid and click the checkbox.

.. figure:: http://s3.nutanixworkshops.com/calm/lab4/image5.png

Once the **Mongo** Blueprint has been selected, a catalog is displayed to the right of the grid containing a blueprint description, category, and project assignment. Insure both **Database** and **Calm** are selected for the categroy and project repsectively, and click *apply*.

.. figure:: http://s3.nutanixworkshops.com/calm/lab4/image8.png

Click **Publish**, and wait until the Blueprint status shows *published* in the grid as shown below.

.. figure:: http://s3.nutanixworkshops.com/calm/lab4/image9.png

Part 3: Marketplace - Clone Blueprint
*************************************

Navigate to the Marketplace by clicking (|image5|) icon located on the left tool ribbon.  This will open the Marketplace. Once Marketplace is displayed, the **Mongo** Blueprint icon should be visible...

.. figure:: http://s3.nutanixworkshops.com/calm/lab4/image11.png


Click the **Mongo** Blueprint Icon and then click **Clone** to copy the bluerpint to the Blueprint workspace for editing.

.. figure:: http://s3.nutanixworkshops.com/calm/lab4/image13.png

Part 4: Edit + Debug + Launch Cloned Blueprint
**********************************************

Navigate the Blueprint workspace by clicking the (|image8|) icon located on the left tool ribbon.  This will open the Blueprint Workspace.

.. figure:: http://s3.nutanixworkshops.com/calm/lab4/image15.png

Click the red exclamation point to see a list fo error desriptions.

.. figure:: http://s3.nutanixworkshops.com/calm/lab4/image16.png

Fix each of the errors listed within the Blueprint.  Once all the errors have been corrected, make additional changes to each of the **Mongo** services (i.e. VM, Package, etc...) and launch the blueprint.  Continue to make chnages until the the Blueprint successfully deploys.

.. figure:: http://s3.nutanixworkshops.com/calm/lab4/image17.png


.. |image0| image:: lab4/media/image2.png
.. |image1| image:: http://s3.nutanixworkshops.com/calm/lab4/image4.png
.. |image2| image:: lab4/media/image5.png
.. |image3| image:: lab4/media/image8.png
.. |image4| image:: lab4/media/image9.png
.. |image5| image:: http://s3.nutanixworkshops.com/calm/lab4/image10.png
.. |image6| image:: lab4/media/image11.png
.. |image7| image:: lab4/media/image13.png
.. |image8| image:: lab4/media/image14.png
.. |image9| image:: lab4/media/image15.png
.. |image10| image:: lab4/media/image16.png
.. |image11| image:: lab4/media/image17.png

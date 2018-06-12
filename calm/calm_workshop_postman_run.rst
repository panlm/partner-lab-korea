*********************************
Postman Execute/Save REST Request
*********************************

Copy the *Request URL* from the NTNX REST API Explorer you want to execute and persist in Postman.

.. figure:: http://s3.nutanixworkshops.com/calm/lab5/image10.png

Paste it to Postman, and select the appropriate REST Command (i.e. POST, GET, PUT, DELETE )from the command drop down

.. figure:: http://s3.nutanixworkshops.com/calm/lab5/image12.png

Configure *Authorization* by setting the Type to *Basic Auth* and using the *Prism* credentials:

**Note:** The Prism credentials may change from cluster to cluster. 

.. figure:: http://s3.nutanixworkshops.com/calm/lab5/image13.png

Configure the **Headers** Key(s) *Content-Type* and *Accept* with Value *application/json* as shown below:

.. figure:: http://s3.nutanixworkshops.com/calm/lab5/image14.png

If it's a **POST**, or **PUT** request, configure the **Body** using *raw* and select JSON (application/json), and paste the payload information into the text area needed to make the call as shown below:

.. figure:: http://s3.nutanixworkshops.com/calm/lab5/image15.png

Click **Send** within Postman.  If configured successfully, you should see **Status: 200 OK**, and valid response data in the **Body** as shown below: 

.. figure:: http://s3.nutanixworkshops.com/calm/lab5/image16.png


Click Save and assign a **Request name:** *Nutanix Calm (NuCalm) [SOME ACTION]*, where SOME ACTION might be:

- List {REST Target} (i.e. Applications, Blueprints, Roles, Projects, etc...).
- Delete {REST Target} (i.e. Applications, Blueprints, Roles, Projects, etc...).
- Create {REST Target} (i.e. Applications, Blueprints, Roles, Projects, etc...).
- Edit {REST Target} (i.e. Applications, Blueprints, Roles, Projects, etc...).
- Launch {REST Target} (i.e. Blueprint, etc...).
- Get {REST Target} (i.e. Application, Blueprint, Role, Project, etc...).

Save to **Collection:** (i.e. *Nutanix*, etc...), and to the appropriate **Subfolder:** (i.e. *V3:Apps*, *V3:Blueprints*, *V3:Projects*, or *V3:Roles*, etc...) .  

.. figure:: http://s3.nutanixworkshops.com/calm/lab5/image17.png

.. |image0| image:: ./media/image10.png
.. |image1| image:: ./media/image12.png
.. |image2| image:: ./media/image13.png
.. |image3| image:: ./media/image14.png
.. |image4| image:: ./media/image15.png
.. |image5| image:: ./media/image16.png
.. |image6| image:: ./media/image17.png


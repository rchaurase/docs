Getting Started Building an Application
==========================================================

Now that we have seen how a device can join an already-configured application and synchronize its view of the data with the ODK-X Sync Endpoint server hosting the application, it is time to set up our own ODK-X application.

.. contents:: :local:

.. _architect-odk-x-prereqs:

Prerequisites
------------------
This guide continues the tour where :doc:`survey-sample-app` left off. If you haven't yet completed that tour, do it first. When you have concluded the tour of the :doc:`survey-using` example application's screens, return to this guide, and we will turn to setting up our own application.

.. _architect-odk-x-config-setup-app-designer:

Setting up ODK-X Application Designer
-----------------------------------------
Read the :ref:`Intro <app-designer-intro>` and :ref:`Overview <app-designer-overview>` sections to get a sense of the features and functionality of the ODK-X Application Designer environment (we will install it below). Follow this guide to :doc:`app-designer-setup`.

Finally, follow this guide to :doc:`app-designer-launching`.

If successful, the :program:`cmd` window (on Windows) should display some status messages. Below is a screen-shot of my :program:`cmd` window beginning with a :program:`dir` of the contents of the directory, and running :program:`grunt` in that directory:

.. image:: /img/getting-started-2/getting-started-building-dir.*
  :alt: App Designer Command Window

And a :program:`Chrome` browser window should open to display:

.. image:: /img/getting-started-2/getting-started-building-chrome.*
  :alt: App Designer Chrome Window

If a :program:`Chrome` browser does not open, try manually launching it and opening http://localhost:8000/index.html.

You can further verify that the Application Designer works by clicking on the :guilabel:`exampleForm` button, then clicking on :guilabel:`Follow link`. This opens the *Example Form* on your computer and simulates all the features available to you on your device.

You can also try other things, like choosing different device dimensions to see how the form renders on different screen geometries.

.. _architect-odk-x-config-modify-app:

Modifying an ODK-X application
-------------------------------------
The next task is to modify the *Example Form* application by adding a new data field to it.

Return to your :program:`cmd` window and once again launch the ODK-X Application Designer environment (and a :program:`Chrome` browser) by typing:

.. code-block:: console

  $ grunt

Now, open a file browser and navigate to the directory where you downloaded the Application Designer. Then navigate within that directory to :file:`app/config/tables/exampleForm`. Rename the :file:`properties.csv` and :file:`definition.csv` files in this directory to :file:`orig.properties.csv` and :file:`orig.definition.csv`. These were the initialization files needed by ODK-X Tables and they will need to be regenerated because we are altering the data table to incorporate an additional question. When finished, the folder should look like this:

.. image:: /img/getting-started-2/example-form-folder.*
  :alt: Example Form Folder

Navigate within that directory to :file:`app/config/tables/exampleForm/forms/exampleForm`. Open the :file:`exampleForm.xlsx` file in :program:`Excel` (or :program:`OpenOffice`). This is the form definition used by `ODK-X Survey <https://docs.odk-x.org/survey-using/>`_.

We will be adding a question to ask the user to enter their favorite color. For this example, we will be collecting a text response. A more useful modification might restrict the user to a set of choices (red, orange, yellow, green, and so on).

On the survey worksheet, insert a row below the first row. Edit the values of the created row in each of the columns shown below, and leave the cells under all other columns in this row empty.

.. list-table:: New Survey Row
  :header-rows: 1

  * - type
    - name
    - display.prompt.text
  * - string
    - Color
    - What is your favorite color?

Save your changes and go back to the Application Designer window. Click on the tab that says :guilabel:`XLSX Converter`. Choose this XLSX file or use your file browser to drag and drop the :file:`exampleForm.xlsx` file onto this screen (dragging and dropping is not supported on all operating systems).

You should now see some JSON in the output window. Hit the :guilabel:`Save to File System` button. This will display three pop-up notifications announcing that the Application Designer is

1. Updating the :file:`definition.csv` file.
2. Updating the :file:`properties.csv` file.
3. Updating the :file:`tableSpecificDefinitions.js` file.
4. Writing the updated `ODK-X Survey <https://docs.odk-x.org/survey-using/>`_ form definition into the :file:`formDef.json` file in the same location as the :file:`exampleForm.xlsx` file.

.. note::
  **Manual Saving (When Automatic Saving Fails)**

  .. this instruction is needed when automatic saving fails in this case these .
  
  In case you encounter an error with the :guilabel:`Save to File System` button (e.g., "Something went wrong! Please save the file manually"), follow these steps to manually save the generated JSON:

  1. Copy the JSON output displayed in the output window.
  2. Open a text editor (like Notepad, VSCode, or Sublime Text).
  3. Paste the JSON into a new file.
  4. Save the file with the name `formDef.json` in the same location as the `exampleForm.xlsx` file (typically :file:`app/config/tables/exampleForm/forms/exampleForm`).
  5. Repeat this process for any other files that failed to save (e.g., :file:`definition.csv`, :file:`properties.csv`), if needed.

  The :file:`definition.csv` and :file:`properties.csv` files are updated because the *form_id* is the same as the *table_id*.


On the :program:`Chrome` Browser, click on the :guilabel:`Preview` tab. Click on :guilabel:`Purge Database`. This will delete the earlier *Example Form* data table -- a necessary step because we are adding a :th:`Color` column to that data table. Select :guilabel:`exampleForm` if you do not already have that form open.

Create a new instance of the *Example Form* and advance through it (this will create the data table with the new :th:`Color` column). Confirm that the new question is displayed as shown below.

.. image:: /img/getting-started-2/new-question-example-form.*
  :alt: Example Form Added Question

You have successfully modified the form. We will now walk through how to deploy your updated application to your device.

.. _architect-odk-x-config-deploy:

Deploying to the Device
-----------------------------------------------
Now that we have the design environment installed and have successfully modified the Example Form application, we can work through the steps of deploying that application to your device.

.. _architect-odk-x-deploy-prepare:

Preparing the Device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
If you followed along with the :doc:`survey-sample-app`, you should already have all the necessary tools installed on your device. If not, follow the :doc:`basics-install` instructions to install ODK-X Services, `ODK-X Survey <https://docs.odk-x.org/survey-using/>`_, and ODK-X Tables.

First, open the :guilabel:`Files by Google` app on the device. Delete the whole :guilabel:`opendatakit` folder by clicking the folder and holding it until it becomes highlighted in blue. Then press the delete icon and click :guilabel:`OK` in the resulting window.

.. image:: /img/getting-started-2/file-manager-delete-folder.*
  :alt: Delete opendatakit folder in OI File Manager

Next, you must force stop all ODK-X apps on the device. To do this, navigate to your device’s :guilabel:`Settings`, then go to :guilabel:`Apps`.

.. image:: /img/getting-started-2/settings-apps.*
  :alt: Finding Apps in Device Settings

Navigate to the three ODK-X Apps and :guilabel:`Force Stop` each of them (ending with ODK-X Services as the other two apps rely on it).

.. image:: /img/getting-started-2/settings-odkx-apps.*
  :alt: Finding ODK-X Apps in Settings

.. image:: /img/getting-started-2/apps-force-stop.*
  :alt: Force Stop an ODK-X App

Finally, confirm that your device has :guilabel:`USB debugging` enabled inside your device's :guilabel:`Settings`. This checkbox is in different places on different devices and may be hidden by default on some. See this guide to `USB debugging on Android <https://www.phonearena.com/news/How-to-enable-USB-debugging-on-Android_id53909>`_ for instructions.

.. _architect-odk-x-deploy-push:

Pushing the Application to the Device
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Return to the :program:`cmd` window on your computer. :kbd:`Control

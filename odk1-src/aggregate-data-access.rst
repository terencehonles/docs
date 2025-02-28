.. spelling::

  pushpins

Getting Data Out of Aggregate
================================

.. warning::
  Aggregate is no longer actively developed. Please use :doc:`ODK Central <central-intro>` instead.

ODK Aggregate supports three primary mechanisms for data transfer:

- :ref:`Exporting <export-data>`:  one time snapshot
- :ref:`Publishing <publish-data>`: continuous streaming of submissions
- :ref:`APIs <odk-api>`: programmatic access to the data

Since ODK supports complex data structures such as question grouping, repeating questions, and multimedia, compromises have been made for each mechanism in regards to these data structures. This document explains each mechanism and what each supports.

.. _export-data:

Exporting
-----------

The easiest way to get data from Aggregate is by using its ‘Export’ feature. Export allows the user to manually export all of the data (or filtered data) at any time into one of the following formats:

.. _export-to-csv:

CSV (comma separated values)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

CSV files are a text only, tabular representation of the data. Multimedia files are represented by including standard web links to the files. Repeats are also represented with links to the underlying data. Grouping information is not preserved. Metadata is only preserved if a filter is created with the metadata.

To download CSV files of forms with repeats, consider using ODK Briefcase instead of ODK Aggregate's export functionality. Instead of repeats that are represented with links to the underlying data, ODK Briefcase will export a set of CSV files, one for each repeating group. ODK Briefcase will also export any multimedia as files (e.g., pictures will get exported as JPEGs). The only metadata that is preserved is the submission date in the CSV is preserved, but the XML files that ODK Briefcase downloads has all form metadata.

.. note::

 To import CSVs into Excel, you cannot download and open in one step; nor can you double-click on the CSV. You must open Excel and choose Import. If you are asked, the file origin or encoding is UTF-8.

.. _export-to-json:

JSON
~~~~~~

JSON is a text only representation of the data in a key:value format. Multimedia files are represented by including standard web links to the files. Repeating data is preserved, but grouping information not related to repeats is not. Metadata is only preserved if a filter is created with the metadata.

.. _export-to-kml:

KML (Keyhole Markup Language)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

KML is a text only representation of the data similar to XML, but used for mapping applications like Google Earth. Multimedia files are represented by including standard web links to the files hosted on the server. Pictures will appear embedded in popup windows (when pushpins are clicked) in programs that render .kml files. Repeats and grouping information is not preserved. Metadata is only preserved if a filter is created with the metadata.

Export Data Summary
~~~~~~~~~~~~~~~~~~~
.. csv-table::
   :header: "Format", "Groups", "Repeats", "Multimedia", "Metadata"
   :widths: auto

   "CSV(Aggregate)", "not preserved", "links to", "links to", "preservable"
   "CSV(Briefcase)", "not preserved", "split into separate CSVs", "exported as files", "only submission date"
   "JSON", "not preserved", "preserved", "links to", "preservable"
   "KML", "not preserved", "not preserved", "links to", "preservable"

.. _publish-data:

Publishing
------------

Aggregate provides mechanism for either bulk publishing data to another service, or for streaming incoming data to a service as it is received, or both.

.. warning::

  Under certain failure conditions, the downstream service can receive multiple copies of a given submission. This is known, expected, behavior.

.. tip::

  - Duplicates typically occur if the downstream service is slow to respond or acknowledge a request.
  - It is your responsibility to detect and eliminate these duplicates should they occur (they will always have exactly the same information in all fields).

.. note::

  - Publishing attempts happen at most every 15 minutes. If there are pending submissions to publish, there will be attempts at most every 30 seconds until there are no pending submissions.
  - If ODK Aggregate aborts its publishing attempt before it gets an acknowledgment, it will re-send the submission a short time later. If the downstream server successfully processed the first request, the re-send of that same submission can cause a duplicate record of it to appear in the downstream system. The instance ID of the submission will appear as the metainstanceid column in Google Sheets and as the *meta-instance-id* column in Google Fusion tables.
  - When the downstream server fails to respond or responds with an error code, ODK Aggregate first delays a re-send for 60 seconds, and, if that also fails, it then backs off its publishing attempts, and will re-send at either 7.5-minute or 15-minute intervals until the downstream service successfully responds, after which ODK Aggregate will resume its normal publishing behavior.

Aggregate currently supports publishing data to the following services or in the following formats:

.. _google-spreadsheet:

Google Spreadsheets
~~~~~~~~~~~~~~~~~~~~~~~

Spreadsheets is Google’s cloud-hosted spreadsheet solution. Multimedia files are represented by including standard web links to the files. Repeats are also represented with links to the underlying data. All metadata is preserved. Spreadsheets has `cell limits <https://support.google.com/drive/answer/37603>`_ above which errors will be reported. Spreadsheets also has an API that can be used to export/publish data.

.. warning::

  Non-repeating grouping information is not preserved with Google Sheets. Use globally unique names throughout your form or data might not transfer correctly to Google Sheets.

.. admonition:: OAuth2 Service Account Required

  Publishing data to Google Spreadsheets requires an :doc:`oauth2-service`.

.. _json-server:

JSON Server
~~~~~~~~~~~~~

JSON preserves grouping and repeat structures. The user can choose to let multimedia files be represented as web links or embedded as base64 encoded strings. All metadata is preserved.


Publish Data Summary
~~~~~~~~~~~~~~~~~~~~

.. csv-table::
   :header: "Service", "Groups", "Repeats", "Multimedia", "Metadata"
   :widths: auto

   "Google Spreadsheets", "not preserved", "links to", "links to", "preserved"
   "JSON Server", "preserved", "preserved", "links to or embedded as base64", "preserved"

.. _odk-api:

APIs
------

ODK Aggregate has public APIs defined for sending data to and from its various components. This section gives references to those API implementations that can be used for connecting new applications to the ODK ecosystem.

.. _briefcase-cli:

Briefcase CLI
~~~~~~~~~~~~~~~~

ODK Briefcase includes a CLI (Command Line Interface) to automate downloading forms from ODK Aggregate (or ODK Collect) and exporting the forms to an Excel-compatible format like CSV. For more details, see :ref:`Working with command line on Briefcase <cli-use>`.

.. _briefcase-aggregate-api:

Briefcase-Aggregate API
~~~~~~~~~~~~~~~~~~~~~~~~~~

ODK Briefcase uses an API that external applications can use to pull all data from and push data to ODK Aggregate.

Interfaces used during pull actions are:

- OpenRosa Form Discovery API
- download forms and media files using URLs form discovery
- ``view/submissionList`` to obtain a chunk of submission keys
- ``view/downloadSubmission`` to download an individual submission

Interfaces used during push actions are:

- ``formUpload`` to upload a form and its media files to ODK Aggregate
- ``view/submissionList`` to obtain a chunk of submission keys
- OpenRosa Form Submission/Overwrite API (with extensions)

For more details, see `Briefcase Aggregate API <https://github.com/getodk/getodk/wiki/Briefcase-Aggregate-API>`_.

.. _openrosa-api:

OpenRosa API
~~~~~~~~~~~~~~~

Collect and Aggregate communicate using a standard set of API calls defined in the :doc:`openrosa` specification, which can also be used by alternative clients.

.. _direct-database:

Direct database connection
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. warning::

  This is an extremely dangerous way to access data from Aggregate because table structure can and does change between versions. Moreover, changing any of this data could corrupt your Aggregate install.

If you are running on a Tomcat server, you have access to the underlying MySQL or PostgreSQL tables. The structure of those tables are documented at `Aggregate Database Structure <https://github.com/getodk/getodk/wiki/Aggregate-Database-Structure>`_.

.. _media-access:

Accessing Media
------------------

Many of the export and publishing options provide a URL to the media (image, audio or video) without providing the content itself. To enable the viewing (following) of this link without requiring a log-in:

- Go to the :guilabel:`Permissions` sub-tab under the :guilabel:`Site Admin` tab.
- Check the checkbox for: `Allow anonymous retrieval of images, audio and video data (needed for GoogleEarth balloon displays)`.
- Click the :guilabel:`Save Changes` button.

This allows anyone to view the media files on your server. Even though you are granting anyone access to this information, it is still quite secure because the users would need to have a valid URL.

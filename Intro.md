
# Oracle Integration (OIC) Generation 2 + File Server information

..this page is a continuation of my [overarching OIC knowledge repo here](https://garyhostt.github.io/Oracle_Integration/). 

This page details:
- Why you should upgrade to OIC generation 2 and how to use it
- Using the OIC File Server  (gen2 only)
   - Configuration
   - Use cases
      - OIC log collection, BICC, Oracle SaaS import/export, other APIs
   - Best practices
   - Videos
   - FTP tools

## Oracle Integration, Generation 2

[Main Documentation for OIC Gen 2](https://docs.oracle.com/en/cloud/paas/integration-cloud/oracle-integration-oci/overview-oracle-integration-generation-2.html)

>Oracle Integration Generation 2 is the next generation of our Oracle Integration platform. This upgrade delivers improved performance and reliability as well as significant improvements in provisioning and other lifecycle management activities by more deeply leveraging the power of Oracle Cloud Infrastructure.

[Lists all benefits of Gen 2 over Gen 1](https://blogs.oracle.com/integration/oracle-integration-oic-generation-2-is-now-available-in-all-cloud-tenancies) - more than just the File Server!

- OIC generation 2 enables OIC to be managed by the OCI API, you can see an example of interacting with OIC via the OCI API [here](https://garyhostt.github.io/OIC_start-stop/).

[How to upgrade from Gen 1 to Gen 2](https://docs.oracle.com/en/cloud/paas/integration-cloud/oracle-integration-oci/upgrade-oracle-integration-generation-2.html#GUID-22F20017-87C7-47A5-8AEF-1CDBF564C7A6) - it's easy as 1, 2, 3 **and doesn't cost extra $**

- You can also manually export your integrations, processes, and VBCS apps. Spin up a gen 2 instance, and then import those to your new environment. However, your instance URL will change and you will need to reconfigure your connections & users & potentially more. IF this manual process is verified 100% to work for you - then you can delete your gen 1 instance. **This is not the default recommended approach**, but it may be satisfactory for some.

[IAM roles for OIC Gen 2](https://docs.cloud.oracle.com/en-us/iaas/integration/doc/setting-users-and-groups-oracle-integration-generation-2.html)
- With generation 2, you still use IDCS to manage permissions of what users can on the OIC Service Console, but for managing OIC on the OCI console, that is managed by IAM. Click here to learn more about [identity federation](https://docs.cloud.oracle.com/en-us/iaas/Content/Identity/Tasks/federatingIDCS.htm) on OCI. This and other tasks are explained on the 
[main documentation for gen 2](https://docs.oracle.com/en/cloud/paas/integration-cloud/oracle-integration-oci/overview-oracle-integration-generation-2.html).

**"How do I know if I have gen 2 or gen 1?"** - Here's how to access each in the [OCI console](https://console.us-ashburn-1.oraclecloud.com/):

- Oracle Integration Generation 2: Application Integration -> Integration
- Oracle Integration Generation 1: Platform Services -> Integration

## File Server

[Main file server documentation](https://docs.oracle.com/en/cloud/paas/integration-cloud/file-server.html)

[PDF form of documentation](https://docs.oracle.com/en/cloud/paas/integration-cloud/file-server/using-file-server-oracle-integration-generation-2.pdf)

The file server **does not cost any extra money**. If you use it in integrations, it will count towards your billable messages.

### Configuration

[Follow these steps to configure your file server](https://blogs.oracle.com/integration/embedded-file-server-sftp-in-oracle-integration)

[To configure the file server with the FTP adapter in OIC](https://docs.oracle.com/en/cloud/paas/integration-cloud/ftp-adapter/create-connection.html#GUID-662EF1FD-2841-4A9A-87B3-FD8B8796510D)

- You can use a ssh key you upload for your user or their password. On the adapter page, you need to set the SFTP connection selectBox to yes. You can get your IP address and port from the Settings -> File Server page in your OIC instance. Make sure your user is enabled and has write access. Inside your integration, you need to use the folder for which your user has write access.

[User permissions and enabling users](https://blogs.oracle.com/integration/leveraging-oracle-integration-file-server-for-file-based-integrations-v2)

### Use Cases

#### 1. OIC log collection & analysis

* [Download the OIC logs from its API](https://docs.oracle.com/en/cloud/paas/integration-cloud/rest-api/op-ic-api-integration-v1-monitoring-logs-id-get.html)

* [My detailed write up on this use case](https://github.com/GaryHostt/Oracle_Integration/blob/master/OIC_logs.md)

- You can call this OIC endpoint, and have your OIC logs stored on your embedded FTP server and/or sent to object storage for analysis by your security platform of choice, such as [Oracle Management Cloud](https://docs.oracle.com/en/cloud/paas/management-cloud/logcs/ingest-logs-oci-object-storage-buckets.html#GUID-4B2BED39-CF5F-450A-B0E5-6C36FBFB80F4).

#### 2. BICC Extract from object storage to File Server

You can extract information from Fusion SaaS using [BICC](https://docs.oracle.com/en/cloud/saas/applications-common/r13-update17d/biacc/toc.htm).

>To extract data from a Fusion Applications Cloud data source, you use the BI Cloud Connector Console to schedule a once-only or regular data load, known as a Cloud Extract. For example, you might extract data from Oracle Fusion HCM Cloud. You can load the extracted data into an Oracle Cloud Object Storage bucket or into an Oracle Universal Content Management (UCM) server.

[Configuring BICC with Object Storage](https://www.ateam-oracle.com/set-up-oracle-fusion-saas-business-intelligence-cloud-connector-bicc-to-use-oracle-cloud-infrastructure-oci-object-storage)

#### 3. Pulling to/from external system

   * [This previous lab shows how OIC can read data in a csv and pass it elsewhere](https://garyhostt.github.io/BigQueryIntegration/)
   * [You can use your file server for FBDI import files](https://antonyjr.github.io/Hands-On-Labs/ERP-Integration-Patterns/html/erp-cloud-fbdi-import-simple.html)
      - [alternative FBDI lab](https://github.com/maldu23/Fusion-FBDI-Integration/blob/master/FBDI_Wkshp.md)
   * [Or HDL imports in HCM](https://antonyjr.github.io/Hands-On-Labs/HCM-Integration-Patterns/html/hcm-cloud-pending-worker-import-simple.html)
   * [Oracle Cloud Applications/SaaS REST APIs documentation](https://docs.oracle.com/en/cloud/saas/index.html)
      - then you can use the [write file](https://docs.oracle.com/en/cloud/paas/integration-cloud/ftp-adapter/invoke-operations-page.html#GUID-314D1E6C-80E0-49BD-803B-A31BA99C53EA) action in the FTP adapter to create a csv based upon the JSON response from the API

### Best practices for handling files in OIC
[File Handling Primer](https://www.ateam-oracle.com/integration-cloud-file-handling-primer)

[File integration best practices](https://blogs.oracle.com/fmw/oracle-integration-cloud-oic-file-based-integration-best-practices)

* How to handle the file depends upon its size, where it’s from, and what you want to do with it. For using the FTP adapter in integration, you will typically use scheduled integrations - maybe app-driven if you’re having an API append rows to an existing csv file.

[Scheduling scheduled integrations](https://docs.oracle.com/en/cloud/paas/integration-cloud/integrations-user/creating-scheduled-integrations.html#GUID-9632A5C8-98A7-4371-B542-6A8583427C8D)

* This shows how to use iCal expressions to set up more complex schedules with OIC.

### Videos
The latest videos describe some key components used creating an integration to import and process bulk files using the FTP Adapter with Oracle Integration:
* [Bulk File Transfer with the FTP Adapter](https://www.youtube.com/watch?v=fWvbnIh6WvQ&t=9s)
* [Use the Stage File Action in Oracle Integrations](https://www.youtube.com/watch?v=LLEHt4kno9M&t=158s)
* [Use the For-Each Action in Oracle Integrations](https://www.youtube.com/watch?v=-Cfq2fYwCTk)

### FTP tools

[cyberduck](https://cyberduck.io/download/)

[filezilla](https://filezilla-project.org/download.php)

- the above are both clients you can use with your laptop to access FTP servers

[Python with sFTP](https://pysftp.readthedocs.io/en/release_0.2.9/)

- the OIC file server uses sFTP protocol

## Closing comic

Thanks for reading, hope it was helpful. Find [me on LinkedIn](bit.ly/3lRd5e0).

<p align="center">
  <img src="https://static.boredpanda.com/blog/wp-content/uploads/2018/11/363-system-32-comics-22-5bfc736f87910-png__880.jpg?raw=true" width="600" height="720" alt="comic"/>
</p>


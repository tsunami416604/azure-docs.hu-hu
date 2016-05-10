<properties urlDisplayName="HDInsight Introduction" pageTitle="Introduction to Hadoop in HDInsight: Big data analysis in the cloud | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Apache Hadoop clusters in the cloud to provide a software framework to manage, analyze, and report on big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Introduction to Hadoop in HDInsight: Big data processing and analysis in the cloud" authors="cgronlun" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="cgronlun"></tags>

# Introduction to Hadoop in HDInsight: Big data processing and analysis in the cloud

Get an overview of HDInsight components, common terminology, and scenarios, and see resources for using Hadoop in HDInsight.

Azure HDInsight deploys and provisions Apache Hadoop clusters in the cloud, providing a software framework designed to manage, analyze, and report on big data. The Hadoop core provides reliable data storage with the Hadoop Distributed File System (HDFS), and a simple MapReduce programming model to process and analyze, in parallel, the data stored in this distributed system.

### What is big data?

Big data refers to data being collected in ever-escalating volumes, at increasingly high velocities, and for a widening variety of unstructured formats and variable semantic contexts.

Big data describes any large body of digital information from the text in a Twitter feed, to the sensor information from industrial equipment, to information about customer browsing and purchases on an online catalog. Big data can be historical (meaning stored data) or real-time (meaning streamed directly from the source).

For big data to provide actionable intelligence or insight, not only must the right questions be asked and data relevant to the issues be collected, the data must be accessible, cleaned, analyzed, and then presented in a useful way. That's where Hadoop in HDInsight can help.

## In this article

This article provides an overview of Hadoop on HDInsight, including:

-   **[Overview of the Hadoop ecosystem on HDInsight:][Overview of the Hadoop ecosystem on HDInsight:]** HDInsight is the Hadoop solution on Azure and provides implementations of Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari, and so on. HDInsight also integrates with business intelligence (BI) tools such as Excel, SQL Server Analysis Services, and SQL Server Reporting Services.
-   **[Advantages of Hadoop in the cloud:][Advantages of Hadoop in the cloud:]** Reasons you should consider HDInsight's cloud implementation of Hadoop.
-   **[HDInsight solutions for big data analysis:][HDInsight solutions for big data analysis:]** Some practical ways you can you HDInsight to answer questions for your organization, from analyzing Twitter sentiment to analyzing HVAC system effectiveness.
-   **[Resources for learning more about big data analysis, Hadoop, and HDInsight:][Resources for learning more about big data analysis, Hadoop, and HDInsight:]** Links to additional information.

## <a name="overview"></a>Overview of the Hadoop ecosystem on HDInsight

Hadoop is the rapidly expanding technology stack that is the go-to solution for big data analysis. HDInsight is the framework for the Microsoft Azure cloud implementation of Hadoop.

Hadoop clusters in HDInsight use versions of the Hortonworks Data Platform (HDP) distribution and the set of Hadoop components within that distribution. For information on the components and their versions in HDInsight clusters, see [What's new in the Hadoop cluster versions provided by HDInsight?][What's new in the Hadoop cluster versions provided by HDInsight?]

Apache Hadoop is a software framework for big data management and analysis. Apache Hadoop core provides reliable data storage with the Hadoop Distributed File System (HDFS), and a simple MapReduce programming model to process and analyze, in parallel, the data stored in this distributed system. HDFS uses data replication to address hardware failure issues that arise when deploying such highly distributed systems.

Here are the Hadoop technologies in HDInsight:

-   **[Ambari][Ambari]:** Cluster provisioning, management, and monitoring
-   **[Avro][Avro]** (Microsoft .NET Library for Avro): Data serialization for the Microsoft .NET environment
-   **[HBase][HBase]:** Non-relational database for very large tables
-   **[HDFS][HDFS]:** Hadoop Distributed File System
-   **[Hive][Hive]:** SQL-like querying
-   **[Mahout][Mahout]:** Machine learning
-   **[MapReduce and YARN][MapReduce and YARN]:** Distributed processing and resource management
-   **[Oozie][Oozie]:** Workflow management
-   **[Pig][Pig]:** Simpler scripting for MapReduce transformations
-   **[Sqoop][Sqoop]:** Data import and export
-   **[Storm][Storm]:** Real-time processing of fast, large data streams
-   **[Zookeeper][Zookeeper]:** Coordinates processes in distributed systems

Also, find out about **[business intelligence tools][business intelligence tools]** you can use with HDInsight.

### <a name="ambari"></a>Ambari

Apache Ambari is for provisioning, managing and monitoring Apache Hadoop clusters. It includes an intuitive collection of operator tools and a robust set of APIs that hide the complexity of Hadoop, simplifying the operation of clusters. See [Monitor Hadoop clusters in HDInsight using the Ambari API][Monitor Hadoop clusters in HDInsight using the Ambari API] and [Apache Ambari API reference][Apache Ambari API reference].

### <a name="avro"></a>Avro (Microsoft .NET Library for Avro)

The Microsoft .NET Library for Avro implements the Apache Avro compact binary data interchange format for serialization for the Microsoft .NET environment. It uses [JSON][JSON] to define a language-agnostic schema that underwrites language interoperability, meaning data serialized in one language can be read in another. Detailed information on the format can be found in the [Apache Avro Specification][Apache Avro Specification].
The format of Avro files supports the distributed MapReduce programming model. Files are “splittable”, meaning you can seek any point in a file and start reading from a particular block. To find out how, see [Serialize data with the Microsoft .NET Library for Avro][Serialize data with the Microsoft .NET Library for Avro].

### <a name="hbase"></a>HBase

[Apache HBase][Apache HBase] is a non-relational database built on Hadoop and designed for large amounts of unstructured and semi-structured data - potentially billions of rows times millions of columns. HBase clusters on HDInsight are configured to store data directly in Azure Blob storage, with low latency and increased elasticity. See [Overview of HBase on HDInsight][Overview of HBase on HDInsight].

### <a name="hdfs"></a>HDFS

Hadoop Distributed File System (HDFS) is a distributed file system that, with MapReduce and YARN, is the core of the Hadoop ecosystem. HDFS is the standard file system for Hadoop clusters on HDInsight.

### <a name="hive"></a>Hive

[Apache Hive][Apache Hive] is data warehouse software built on Hadoop that allows you to query and manage large datasets in distributed storage using a SQL-like language call HiveQL. Hive, like Pig, is an abstraction on top of MapReduce and when run, Hive translates queries into a series of MapReduce jobs. Hive is conceptually closer to a relational database management system than Pig, and is therefore appropriate for use with more structured data. For unstructured data, Pig is better choice. See [Use Hive with Hadoop in HDInsight][Use Hive with Hadoop in HDInsight]

### <a name="mahout"></a>Mahout

[Apache Mahout][Apache Mahout] is a scalable library of machine learning algorithms that run on Hadoop. Using principles of statistics, machine learning applications teach systems to learn from data and to use past outcomes to determine future behavior. See [Generate movie recommendations using Mahout on Hadoop][Generate movie recommendations using Mahout on Hadoop].

### <a name="mapreduce"></a>MapReduce and YARN

Hadoop MapReduce is a software framework for writing applications to process big data sets in parallel. A MapReduce job splits large data sets and organizes the data into key-value pairs for processing.

Apache YARN is the next generation of MapReduce (MapReduce 2.0, or MRv2) that splits the two major tasks of JobTracker - resource management and job scheduling/monitoring - into separate entities.

For more information on MapReduce, see [MapReduce][MapReduce] in the Hadoop Wiki. To learn about YARN, see [Apache Hadoop NextGen MapReduce (YARN)][Apache Hadoop NextGen MapReduce (YARN)].

### <a name="oozie"></a>Oozie

[Apache Oozie][Apache Oozie] is a workflow coordination system that manages Hadoop jobs. It is integrated with the Hadoop stack and supports Hadoop jobs for MapReduce, Pig, Hive, and Sqoop. It can also be used to schedule jobs specific to a system, like Java programs or shell scripts. See [Use a time-based Oozie Coordinator with Hadoop in HDInsight][Use a time-based Oozie Coordinator with Hadoop in HDInsight].

### <a name="pig"></a>Pig

[Apache Pig][Apache Pig] is a high-level platform that allows you to perform complex MapReduce transformations on very large data sets using a simple scripting language called Pig Latin. Pig translates the Pig Latin scripts so they’ll run within Hadoop. You can create User Defined Functions (UDFs) to extend Pig Latin. See [Use Pig with Hadoop to analyze an Apache log file][Use Pig with Hadoop to analyze an Apache log file].

### <a name="sqoop"></a>Sqoop

[Apache Sqoop][Apache Sqoop] is tool that transfers bulk data between Hadoop and relational databases such a SQL, or other structured data stores, as efficiently as possible. See [Use Sqoop with Hadoop][Use Sqoop with Hadoop].

### <a name="storm"></a>Storm

[Apache Storm][Apache Storm] is a distributed, real-time computation system for processing large streams of data fast. Storm is offered as a managed cluster in HDInsight. See [Analyze real-time sensor data using Storm and Hadoop][Analyze real-time sensor data using Storm and Hadoop].

### <a name="zookeeper"></a>Zookeeper

[Apache Zookeeper][Apache Zookeeper] coordinates processes in large distributed systems by means of a shared hierarchical namespace of data registers (znodes). Znodes contain small amounts of meta information needed to coordinate processes: status, location, configuration, and so on.

### <a name="bi"></a>Business intelligence tools

Familiar business intelligence (BI) tools - such as Excel, PowerPivot, SQL Server Analysis Services and Reporting Services - retrieve, analyze, and report data integrated with HDInsight using either the Power Query add-in or the Microsoft Hive ODBC Driver.

These BI tools can help in your big data analysis:

-   [Download Microsoft Power Query for Excel][Download Microsoft Power Query for Excel]
-   [Download Microsoft Hive ODBC Driver][Download Microsoft Hive ODBC Driver]
-   [Learn more about SQL Server Analysis Services][Learn more about SQL Server Analysis Services]
-   [Learn about SQL Server Reporting Services][Learn about SQL Server Reporting Services]

## <a name="advantage"></a>Advantages of Hadoop in the cloud

As part of the Azure cloud ecosystem, Hadoop in HDInsight offers a number of benefits, among them:

-   State-of-the-art Hadoop components. For details, see [What's new in the Hadoop cluster versions provided by HDInsight?][What's new in the Hadoop cluster versions provided by HDInsight?]
-   High availability and reliability of clusters. See [Availability and reliability of Hadoop clusters in HDInsight][Availability and reliability of Hadoop clusters in HDInsight] for details.
-   Efficient and economical data storage with Azure Blob storage, a Hadoop-compatible option. See [Use Azure Blob storage with Hadoop in HDInsight][Use Azure Blob storage with Hadoop in HDInsight] for details.
-   Integration with other Azure services, including [Websites][Websites] and [SQL Database][SQL Database].
-   Low entry cost. Start a [free trial][free trial], or consult [HDInsight pricing details][HDInsight pricing details].

To read more about the advantages on Hadoop in HDInsight, see the [Azure features page for HDInsight][Azure features page for HDInsight].

## <a name="solutions"></a>Try HDInsight solutions for big data analysis

Analyze data from your organization to gain insights into your business. Here are some examples:

-   [Analyze HVAC sensor data][Analyze HVAC sensor data]: Learn how to analyze sensor data using Hive with HDInsight (Hadoop), and then visualize the data in Microsoft Excel. In this sample, you'll use Hive to process historical data produced by HVAC systems to see which systems can't reliably maintain a set temperature.
-   [Use Hive with HDInsight to analyze website logs][Use Hive with HDInsight to analyze website logs]: Learn how to use HiveQL in HDInsight to analyze website logs to get insight into the frequency of visits in a day from external websites, and a summary of website errors that the users experience.
-   [Analyze sensor data in real-time with Storm and HBase in HDInsight (Hadoop)][Analyze real-time sensor data using Storm and Hadoop]: Learn how to build a solution that uses a Storm cluster in HDInsight to process sensor data from Azure Event Hubs, and then displays the processed sensor data as near-real-time information on a web-based dashboard.

To try Hadoop on HDInsight, see "Get started" articles in the Explore section on the [HDInsight documentation page][HDInsight documentation page]. To try more advanced examples, scroll down to the Analyze section.

## <a name="resources"></a>Resources for learning more about big data analysis, Hadoop, and HDInsight

### Hadoop in HDInsight

-   [HDInsight documentation][HDInsight documentation page]: The documentation page for Azure HDInsight with links to articles, videos, and more resources.

-   [Learning map for Hadoop in HDInsight][Learning map for Hadoop in HDInsight]: A guided tour of Hadoop documentation for HDInsight.

-   [Get started with Azure HDInsight][Get started with Azure HDInsight]: A quick-start tutorial for using Hadoop in HDInsight.

-   [Run the HDInsight samples][Run the HDInsight samples]: A tutorial on how the run the samples that ship with HDInsight.

-   [Azure HDInsight SDK][Azure HDInsight SDK]: Reference documentation for the HDinsight SDK.

### SQL Database on Azure

-   [Azure SQL Database][Azure SQL Database]: MSDN documentation for SQL Database.

-   [Management Portal for SQL Database][Management Portal for SQL Database]: A lightweight and easy-to-use database management tool for managing SQL Database in the cloud.

-   [Adventure Works for SQL Database][Adventure Works for SQL Database]: Download page for SQL Database sample database.

### Microsoft business intelligence

-   [Connect Excel to Hadoop with Power Query][Connect Excel to Hadoop with Power Query]: Learn how to connect Excel to the Azure storage account that stores the data associated with your HDInsight cluster by using Microsoft Power Query for Excel.

-   [Connect Excel to Hadoop with the Microsoft Hive ODBC Driver][Connect Excel to Hadoop with the Microsoft Hive ODBC Driver]: Learn how to import data from HDInsight with the Microsoft Hive ODBC Driver.

-   [Microsoft Business Intelligence (BI)][Microsoft Business Intelligence (BI)]: Learn about Power BI for Office 365, download the SQL Server trial, and set up SharePoint Server 2013 and SQL Server BI.

### Apache Hadoop

-   [Apache Hadoop][Apache Hadoop]: Learn more about the Apache Hadoop software library, a framework that allows for the distributed processing of large data sets across clusters of computers.

-   [HDFS][1]: Learn more about the architecture and design of the Hadoop Distributed File System (HDFS), the primary storage system used by Hadoop applications.

-   [MapReduce][2]: Learn more about the programming framework for writing Hadoop applications that rapidly process vast amounts of data in parallel on large clusters of compute nodes.

  [Overview of the Hadoop ecosystem on HDInsight:]: #overview
  [Advantages of Hadoop in the cloud:]: #advantage
  [HDInsight solutions for big data analysis:]: #solutions
  [Resources for learning more about big data analysis, Hadoop, and HDInsight:]: #resources
  [What's new in the Hadoop cluster versions provided by HDInsight?]: ../hdinsight-component-versioning/
  [Ambari]: #ambari
  [Avro]: #avro
  [HBase]: #hbase
  [HDFS]: #hdfs
  [Hive]: #hive
  [Mahout]: #mahout
  [MapReduce and YARN]: #mapreduce
  [Oozie]: #oozie
  [Pig]: #pig
  [Sqoop]: #sqoop
  [Storm]: #storm
  [Zookeeper]: #zookeeper
  [business intelligence tools]: #bi
  [Monitor Hadoop clusters in HDInsight using the Ambari API]: ../hdinsight-monitor-use-ambari-api/
  [Apache Ambari API reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [JSON]: http://www.json.org/
  [Apache Avro Specification]: http://avro.apache.org/docs/current/spec.html
  [Serialize data with the Microsoft .NET Library for Avro]: ../hdinsight-dotnet-avro-serialization/
  [Apache HBase]: http://hbase.apache.org/
  [Overview of HBase on HDInsight]: ../hdinsight-hbase-overview/
  [Apache Hive]: http://hive.apache.org/
  [Use Hive with Hadoop in HDInsight]: ../hdinsight-use-hive/
  [Apache Mahout]: https://mahout.apache.org/
  [Generate movie recommendations using Mahout on Hadoop]: ../hdinsight-mahout/
  [MapReduce]: http://wiki.apache.org/hadoop/MapReduce
  [Apache Hadoop NextGen MapReduce (YARN)]: http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html
  [Apache Oozie]: http://oozie.apache.org/
  [Use a time-based Oozie Coordinator with Hadoop in HDInsight]: ../hdinsight-use-oozie-coordinator-time/
  [Apache Pig]: http://pig.apache.org/
  [Use Pig with Hadoop to analyze an Apache log file]: ../hdinsight-use-pig/
  [Apache Sqoop]: http://sqoop.apache.org/
  [Use Sqoop with Hadoop]: ../hdinsight-use-sqoop/
  [Apache Storm]: https://storm.incubator.apache.org/
  [Analyze real-time sensor data using Storm and Hadoop]: ../hdinsight-storm-sensor-data-analysis/
  [Apache Zookeeper]: http://zookeeper.apache.org/
  [Download Microsoft Power Query for Excel]: http://www.microsoft.com/en-us/download/details.aspx?id=39379
  [Download Microsoft Hive ODBC Driver]: http://www.microsoft.com/en-us/download/details.aspx?id=40886
  [Learn more about SQL Server Analysis Services]: http://www.microsoft.com/en-us/server-cloud/solutions/business-intelligence/analysis.aspx
  [Learn about SQL Server Reporting Services]: http://msdn.microsoft.com/en-us/library/ms159106.aspx
  [Availability and reliability of Hadoop clusters in HDInsight]: ../hdinsight-high-availability/
  [Use Azure Blob storage with Hadoop in HDInsight]: ../hdinsight-use-blob-storage/
  [Websites]: ../documentation/services/websites/
  [SQL Database]: ../documentation/services/sql-database/
  [free trial]: /pricing/free-trial/
  [HDInsight pricing details]: ../pricing/details/hdinsight/
  [Azure features page for HDInsight]: ../services/hdinsight/
  [Analyze HVAC sensor data]: ../hdinsight-hive-analyze-sensor-data/
  [Use Hive with HDInsight to analyze website logs]: ../hdinsight-hive-analyze-website-log/
  [HDInsight documentation page]: ../documentation/services/hdinsight/
  [Learning map for Hadoop in HDInsight]: ../hdinsight-learn-map
  [Get started with Azure HDInsight]: ../hdinsight-get-started/
  [Run the HDInsight samples]: ../hdinsight-run-samples/
  [Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx
  [Azure SQL Database]: http://msdn.microsoft.com/en-us/library/windowsazure/ee336279.aspx
  [Management Portal for SQL Database]: http://msdn.microsoft.com/en-us/library/windowsazure/gg442309.aspx
  [Adventure Works for SQL Database]: http://msftdbprodsamples.codeplex.com/releases/view/37304
  [Connect Excel to Hadoop with Power Query]: ../hdinsight-connect-excel-power-query/
  [Connect Excel to Hadoop with the Microsoft Hive ODBC Driver]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Microsoft Business Intelligence (BI)]: http://www.microsoft.com/en-us/server-cloud/solutions/business-intelligence/default.aspx
  [Apache Hadoop]: http://hadoop.apache.org/
  [1]: http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html
  [2]: http://mapreduce.org/

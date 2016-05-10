<properties urlDisplayName="Monitor Hadoop clusters  in HDInsight using the Ambari API" pageTitle="Monitor Hadoop clusters in HDInsight using the Ambari API | Azure" metaKeywords="" description="Use the Apache Ambari APIs for provisioning, managing, and monitoring Hadoop clusters. Ambari's intuitive operator tools and APIs hide the complexity of Hadoop." services="hdinsight" documentationCenter="" title="Monitor Hadoop clusters in HDInsight using the Ambari API" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Monitor Hadoop clusters in HDInsight using the Ambari API

Learn how to monitor HDInsight clusters versions 3.1 and 2.1 using Ambari APIs.

**Estimated time to complete:** 15 minutes

## In this article

-   [What is Ambari?][What is Ambari?]
-   [Prerequisites][Prerequisites]
-   [Jump start][Jump start]
-   [Ambari monitoring APIs][Ambari monitoring APIs]
-   [Next steps][Next steps]

## <span id="whatisambari"></span></a> What is Ambari?

[Apache Ambari][Apache Ambari] is for provisioning, managing and monitoring Apache Hadoop clusters. It includes an intuitive collection of operator tools and a robust set of APIs that hide the complexity of Hadoop, simplifying the operation of clusters. For more information about the APIs, see [Ambari API reference][Ambari API reference].

HDInsight currently only supports the Ambari monitoring feature. Ambari API v1.0 is supported by HDInsight cluster version 2.1 and 3.0. This article covers accessing Ambari APIs on HDInsight cluster versions 3.1 and 2.1. The key difference between the two is that some of the components have changed with the introduction of new capabilities (such as the Job History Server).

## <span id="prerequisites"></span></a>Prerequisites

Before you begin this tutorial, you must have the following:

-   **A workstation** with Azure PowerShell installed and configured. For instructions, see [Install and configure Azure PowerShell][Install and configure Azure PowerShell]. To execute PowerShell scripts, you must run Azure PowerShell as administrator and set the execution policy to *RemoteSigned*. See [Run Windows PowerShell scripts][Run Windows PowerShell scripts].

    [Curl][Curl] is optional. It can be installed from [here][here].

    > [WACOM.NOTE] When use the curl command on Windows, use double-quotes instead of single-quotes for the option values.

-   **An Azure HDInsight cluster**. For instructions on cluster provision, see [Get started using HDInsight][Get started using HDInsight] or [Provision HDInsight clusters][Provision HDInsight clusters]. You will need the following data to go through the tutorial:

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Cluster property</th>
    <th align="left">PowerShell variable name</th>
    <th align="left">Value</th>
    <th align="left">Description</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">HDInsight cluster name</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">The name of your HDInsight cluster.</td>
    </tr>
    <tr class="even">
    <td align="left">Cluster username</td>
    <td align="left">$clusterUsername</td>
    <td align="left"></td>
    <td align="left">Cluster username specified at provision.</td>
    </tr>
    <tr class="odd">
    <td align="left">Cluster password</td>
    <td align="left">$clusterPassword</td>
    <td align="left"></td>
    <td align="left">Cluster user password.</td>
    </tr>
    </tbody>
    </table>

    > [WACOM.NOTE] Fill the values into the tables. It will be helpful for going through this tutorial.

## <span id="jumpstart"></span></a>Jump start

There are several ways to use Ambari to monitor HDInsight clusters.

**Use Azure PowerShell**

The following is a PowerShell script for getting the MapReduce jobtracker information *on a 3.1 cluster.* The key difference here is that we will now pull these details from the YARN service (rather than Map Reduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 

    $response.metrics.'yarn.queueMetrics'

The following is a PowerShell script for getting the MapReduce jobtracker information *on a 2.1 cluster*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 

    $response.metrics.'mapred.JobTracker'

The output is:

![Jobtracker Output][Jobtracker Output]

**Use curl**

The following is an example of getting cluster information using Curl:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

The output is:

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

Note for the 10/8/2014 release:
When using Ambari endpoint, "[https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}][https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}]", the *host\_name* field now returns the fully qualified domain name (FQDN) of the node instead of the host name. Before the 10/8/2014 release, this example returned" simply **headnode0**". After the 10/8/2014, you get the FQDN “**headnode0.{ClusterDNS}.azurehdinsight.net**” as shown in the example above. This change was required to facilitate scenarios where multiple cluster types such as HBase and Hadoop can be deployed in one Virtual Network (VNET). This happens, for example, when using HBase as a back-end platform for Hadoop.

## <span id="monitor"></span></a>Ambari monitoring APIs

The following table lists some of the most common Ambari monitoring API calls. For more information about the API, see [Ambari API reference][Ambari API reference].

<table border="1">
<tr>
<th>
Monitor API call

</th>
<th>
URI

</th>
<th>
Description

</th>
</tr>
<tr>
<td>
Get clusters

</td>
<td>
`/api/v1/clusters`

</td>
<td>
</td>
</tr>
<tr>
<td>
Get cluster info.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net`

</td>
<td>
clusters, services, hosts

</td>
</tr>
<tr>
<td>
Get services

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services`

</td>
<td>
Services include: hdfs, mapreduce

</td>
</tr>
<tr>
<td>
Get services info.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>`

</td>
<td>
</td>
</tr>
<tr>
<td>
Get service components

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components`

</td>
<td>
HDFS: namenode, datanode
MapReduce: jobtracker; tasktracker

</td>
</tr>
<tr>
<td>
Get component info.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>`

</td>
<td>
ServiceComponentInfo, host-components, metrics

</td>
</tr>
<tr>
<td>
Get hosts

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts`

</td>
<td>
headnode0, workernode0

</td>
</tr>
<tr>
<td>
Get host info.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName> `

<td>
</td>
</tr>
<tr>
<td>
Get host components

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components`

</td>
<td>
namenode, resourcemanager

</td>
</tr>
<tr>
<td>
Get host component info.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>`

</td>
<td>
HostRoles, component, host, metrics

</td>
</tr>
<tr>
<td>
Get configurations

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations `

</td>
<td>
Config types: core-site, hdfs-site, mapred-site, hive-site

</td>
</tr>
<tr>
<td>
Get configuration info.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName> `

</td>
<td>
Config types: core-site, hdfs-site, mapred-site, hive-site

</td>
</tr>
</table>
## <span id="nextsteps"></span></a>Next Steps

Now you have learned how to use Ambari monitoring API calls. To learn more, see:

-   [Administer HDInsight clusters using the Management portal][Administer HDInsight clusters using the Management portal]
-   [Administer HDInsight clusters using Azure PowerShell][Administer HDInsight clusters using Azure PowerShell]
-   [Administer HDInsight clusters using command-line interface][Administer HDInsight clusters using command-line interface]
-   [HDInsight documentation][HDInsight documentation]
-   [Get started with HDInsight][Get started using HDInsight]

  [What is Ambari?]: #whatisambari
  [Prerequisites]: #prerequisites
  [Jump start]: #jumpstart
  [Ambari monitoring APIs]: #monitor
  [Next steps]: #nextsteps
  [Apache Ambari]: http://ambari.apache.org/
  [Ambari API reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [Install and configure Azure PowerShell]: ../install-configure-powershell/
  [Run Windows PowerShell scripts]: http://technet.microsoft.com/en-us/library/ee176949.aspx
  [Curl]: http://curl.haxx.se
  [here]: http://curl.haxx.se/download.html
  [Get started using HDInsight]: ../hdinsight-get-started/
  [Provision HDInsight clusters]: ../hdinsight-provision-clusters/
  [Jobtracker Output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
  [https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}]: https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}
  [Administer HDInsight clusters using the Management portal]: ../hdinsight-administer-use-management-portal/
  [Administer HDInsight clusters using Azure PowerShell]: ../hdinsight-administer-use-powershell/
  [Administer HDInsight clusters using command-line interface]: ../hdinsight-administer-use-command-line/
  [HDInsight documentation]: /en-us/documentation/services/hdinsight/

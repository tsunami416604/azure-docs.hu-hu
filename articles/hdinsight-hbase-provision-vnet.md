<properties urlDisplayName="Provision HBase clusters on Azure Virtual Network" pageTitle="Provision HBase clusters on Azure Virtual Network | Azure" metaKeywords="" description="Learn how to create HDInsight clusters on Azure Virtual Network." metaCanonical="" services="hdinsight" documentationCenter="" title="Provision HBase clusters on Azure Virtual Network" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao"></tags>

# Provision HBase clusters on Azure Virtual Network

Learn how to create HDInsight clusters on Azure Virtual Network.

With the virtual network integration, HBase clusters can be deployed to the same virtual network as your applications so that applications can communicate with HBase directly. The benefits include:

-   Direct connectivity of the web application to the nodes of the HBase cluster which enables communication using HBase Java RPC APIs.
-   Improve performance by not having your traffic go over multiple gateway and load-balancer.
-   process sensitive information in a more secure manner without exposing a public endpoint

## In this article

-   [Prerequisites][Prerequisites]
-   [Provision HBase clusters into a virtual network][Provision HBase clusters into a virtual network]
-   [Connect to the HBase cluster provisioned in virtual network using HBase Java RPC APIs][Connect to the HBase cluster provisioned in virtual network using HBase Java RPC APIs]
-   [Provision an HBase cluster using PowerShell][Provision an HBase cluster using PowerShell]
-   [Next steps][Next steps]

## <span id="prerequisites"></span></a>Prerequisites

Before you begin this tutorial, you must have the following:

-   **An Azure subscription**. Azure is a subscription-based platform. For more information about obtaining a subscription, see [Purchase Options][Purchase Options], [Member Offers][Member Offers], or [Free Trial][Free Trial].

-   **A workstation with Azure PowerShell installed and configured**. For instructions, see [Install and configure Azure PowerShell][Install and configure Azure PowerShell]. To execute PowerShell scripts, you must run Azure PowerShell as administrator and set the execution policy to *RemoteSigned*. See [Run Windows PowerShell scripts][Run Windows PowerShell scripts].

    Before running PowerShell scripts, make sure you are connected to your Azure subscription using the following cmdlet:

        Add-AzureAccount

    If you have multiple Azure subscriptions, use the following cmdlet to set the current subscription:

        Select-AzureSubscription <AzureSubscriptionName>

## <span id="hbaseprovision"></span></a>Provision an HBase cluster into a virtual network.

**To create a Virtual Network using the management portal:**

1.  Sign in to the [Azure Management portal][Azure Management portal].
2.  On the bottom of the page, click **NEW**, click **NETWORK SERVICES**, click **VIRTUAL NETWORK**, and then click **QUICK CREATE**.
3.  Type or select the following values:

    -   **Name**: The name of your virtual network.
    -   **Address space**: Choose an address space for the virtual network that is large enough to provide addresses for all nodes in the cluster. Otherwise the provision will fail.
    -   **Maximum VM count**: Choose one of the Maximum VM counts.
    -   **Location**: The location must be the same as the HBase cluster that you will create.
    -   **DNS server**: This article uses internal DNS server provided by Azure, therefore you can choose **None**. More advanced networking configuration with custom DNS servers are also supported. For the detailed guidance, see [][]<http://msdn.microsoft.com/library/azure/jj156088.aspx></a>.

4.  Click **CREATE A VIRTUAL NETWORK**. The new virtual network name will appear in the list. Wait until the Status column shows **Created**.
5.  In the main pane, click the virtual network you just created.
6.  On the top of the page, click **DASHBOARD**.
7.  Under **quick glance**, make a note of **VIRTUAL NETWORK ID**. You will need it when provisioning HBase cluster.
8.  On the top of the page, click **CONFIGURE**.
9.  On the bottom of the page, the default subnet name is **Subnet-1**. You can optionally rename the subnet or add a new subnet for the HBase cluster. Make a note of the subnet name, you will need it when provisioning the cluster
10. Verify the **CIDR(ADDRESS COUNT)** for the subnet that will be used for the cluster. The address count must be greater than the number of worker nodes plus seven (Gateway: 2, Headnode: 2, Zookeeper: 3). For example, if you need a 10 node HBase cluster, the address count for the subnet must be greater than 17 (10+7). Otherwise the deployment will fail.

    > [WACOM.NOTE] It is highly recommended to designate a single subnet for one cluster.

11. Click **Save** on the bottom of the page, if you have updated the subnet values.

> [WACOM.NOTE] HDInsight clusters use Azure Blob storage for storing data. For more information, see [Use Azure Blob storage with Hadoop in HDInsight][Use Azure Blob storage with Hadoop in HDInsight]. You will need a storage account and a Blob storage container. The storage account location must match the virtual network location and the cluster location.

**To create an Azure Storage account and a Blob storage container:**

1.  Sign in to the [Azure Management Portal][Azure Management portal].
2.  Click **NEW** on the lower left corner, point to **DATA SERVICES**, point to **STORAGE**, and then click **QUICK CREATE**.

3.  Type or select the following values:

    -   **URL**: The name of the storage account
    -   **LOCATION**: The location of the storage account. Make sure it matches the virtual network location. Affinity groups are not supported.
    -   **REPLICATION**: For testing purposes, use **Locally Redundant** to reduce the cost.

4.  Click **CREATE STORAGE ACCOUNT**. You will see the new storage account in the storage list.
5.  Wait until the **STATUS** of the new storage account changes to **Online**.
6.  Click the new storage account from the list to select it.
7.  Click **MANAGE ACCESS KEYS** from the bottom of the page.
8.  Make a note of the **STORAGE ACCOUNT NAME** and the **PRIMARY ACCESS KEY** (or the **SECONDARY ACCESS KEY**. Either of the keys works). You will need them later in the tutorial.
9.  From the top of the page, click **CONTAINER**.
10. From the bottom of the page, click **ADD**.
11. Enter the container name. This container will be used as the default container for the HBase cluster. By default, the default container name matches the cluster name. Keep the **ACCESS** field as **Private**.
12. Click the check icon to create the container.

**To provision an HBase cluster using the Azure Portal:**

> [WACOM.NOTE] For information on provisioning a new HBase cluster using PowerShell, see [Provision an HBase cluster using PowerShell][Provision an HBase cluster using PowerShell].

1.  Sign in to the [Azure Management Portal][Azure Management portal].
2.  Click **NEW** on the lower left corner, point to **DATA SERVICES**, point to **HDINSIGHT**, and then click **CUSTOM CREATE**.
3.  Enter a **CLUSTER NAME** and select the **HDINSIGHT VERSION** to use for the cluster.

    ![cluster name and version fields][cluster name and version fields]

4.  Select the number of **DATA NODES** to create for the cluster, and the region or Azure Virtual network (**REGION/VIRTUAL NETWORK**) to use for this cluster.

    ![number of nodes and region fields][number of nodes and region fields]

5.  Enter the administrator **USER NAME** and **PASSWORD** to use for this cluster.

    ![administrator name and password fields][administrator name and password fields]

6.  Select whether to use a new storage account or an existing one. If a new one, enter the **ACCOUNT NAME** and **DEFAULT CONTAINER** to create. Finally, select the checkmark to create the cluster.

    ![storage account selection][storage account selection]

To begin working with your new HBase cluster, you can use the procedures found in [Get started using HBase with Hadoop in HDInsight][Get started using HBase with Hadoop in HDInsight].

## <span id="connect"></span></a>Connect to the HBase cluster provisioned in virtual network using HBase Java RPC APIs

1.  Provision an IaaS virtual machine into the same Azure virtual network and the same subnet. So both the virtual machine and the HBase cluster use the same internal DNS server to resolve host names. To do so, you must choose the From Gallery option, and select the virtual network instead of a data center. For the instructions, see [Create a Virtual Machine Running Windows Server][Create a Virtual Machine Running Windows Server]. A standard Windows Server 2012 image with a small VM size is sufficient.

2.  When using a Java application to connect to HBase remotely, you must use the fully qualified domain name (FQDN). To determine this, we must get the connection-specific DNS suffix of the HBase cluster. To do that use Curl to query Ambari, or remote desktop to connect to the cluster.

    -   **Curl** - use the following command:

            curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

        In the JSON data returned, find the "host\_name" entry. This will contain the fully qualified domain name (FQDN) for the nodes in the cluster. For example:

            ...
            "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
            ...

        The portion of the domain name beginning with the cluster name is the DNS suffix. For example, mycluster.b1.cloudapp.net.

    -   **PowerShell** - use the following PowerShell script to register the **Get-ClusterDetail** function, which can be used to return the DNS suffix.

            function Get-ClusterDetail(
                [String]
                [Parameter( Position=0, Mandatory=$true )]
                $ClusterDnsName,
                [String]
                [Parameter( Position=1, Mandatory=$true )]
                $Username,
                [String]
                [Parameter( Position=2, Mandatory=$true )]
                $Password,
                [String]
                [Parameter( Position=3, Mandatory=$true )]
                $PropertyName
                )
            {
            <#
                .SYNOPSIS 
                 Displays information to facilitate HDInsight cluster to cluster scinario within same virtual network.
                .Description
                 This command shows following 4 properties of an HDInsight cluster.
                 1. ZookeeperQuorum (only support HBase type cluster)
                    Shows the value of hbase property "hbase.zookeeper.quorum".
                 2. ZookeeperClientPort (only support HBase type cluster)
                    Shows the value of hbase property "hbase.zookeeper.property.clientPort".
                 3. HBaseRestServers (only support HBase type cluster)
                    Shows a list of host FQDNs which run HBase rest server.
                 4. FQDNSuffix (support all type cluster)
                    Shows FQDN suffix of hosts in the cluster.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
                 This command shows the value of hbase property "hbase.zookeeper.quorum".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
                 This command shows the value of hbase property "hbase.zookeeper.property.clientPort".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
                 This command shows a list of host FQDNs which run HBase rest server.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
                 This command shows FQDN suffix of hosts in the cluster.
            #>

                $DnsSuffix = ".azurehdinsight.net"

                $ClusterFQDN = $ClusterDnsName + $DnsSuffix
                $webclient = new-object System.Net.WebClient
                $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

                if($PropertyName -eq "ZookeeperQuorum")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
                }
                if($PropertyName -eq "ZookeeperClientPort")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
                }
                if($PropertyName -eq "HBaseRestServers")
                {
                    $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                    $Response1 = $webclient.DownloadString($Url1)
                    $JsonObject1 = $Response1 | ConvertFrom-Json
                    $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                    $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                    $Response2 = $webclient.DownloadString($Url2)
                    $JsonObject2 = $Response2 | ConvertFrom-Json
                    foreach ($host_component in $JsonObject2.host_components)
                    {
                        $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                        Write-host $ConnectionString
                    }
                }
                if($PropertyName -eq "FQDNSuffix")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                    $pos = $FQDN.IndexOf(".")
                    $Suffix = $FQDN.Substring($pos + 1)
                    Write-host $Suffix
                }
            }

        After running the PowerShell script, use the following command to return the DNS suffix using the Get-ClusterDetail function. Specify your HDInsight HBase cluster name, admin name, and admin password when using this command.

            Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

        This will return the DNS suffix. For example, **yourclustername.b4.internal.cloudapp.net**.

    > [WACOM.NOTE] You can also use Remote Desktop to connect the HBase cluster (you will be connected to the headnode) and run **ipconfig** from a command prompt to obtain the DNS suffix. For instructions on enabling RDP and connect to the cluster using RDP, see [Manage Hadoop clusters in HDInsight using the Azure Management Portal][Manage Hadoop clusters in HDInsight using the Azure Management Portal].
    >
    > ![hdinsight.hbase.dns.surffix][hdinsight.hbase.dns.surffix]

<!-- 
3.  Change the Primary DNS Suffix configuration of the virtual machine. This enables virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to the workernode0 of the HBase cluster. 

    To make the configuration change:

    1. RDP into the virtual machine. 
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**. 
    - Set **Primary DNS Suffix** to the value obtained in the step 2: 

        ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
    4. Click **OK**. 
    5. Reboot the virtual machine.
-->

To verify that the virtual machine can communicate with the HBase cluster, use the following command `ping headnode0.<dns suffix>` from the virtual machine. For example, ping headnode0.mycluster.b1.cloudapp.net

To use this information in a Java application, you can follow the steps in [Use Maven to build Java applications that use HBase with HDInsight (Hadoop)][Use Maven to build Java applications that use HBase with HDInsight (Hadoop)] to create an application. To have the application connect to a remote HBase server, modify the **hbase-site.xml** file in this example to use the FQDN for ZooKeeper. For example:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [WACOM.NOTE] For more information on name resolution in Azure Virtual Networks, including how to use your own DNS server, see [Name Resolution (DNS)][Name Resolution (DNS)].

## <span id="powershell"></span></a>Provision an HBase cluster using Azure PowerShell:\*\*

1.  Open PowerShell ISE.
2.  Copy and paste the following copy into the script pane.

        $hbaseClusterName = "<HBaseClusterName>"
        $hadoopUserName = "<HBaseClusterUsername>"
        $hadoopUserPassword = "<HBaseClusterUserPassword>"
        $location = "<HBaseClusterLocation>"  #i.e. "West US"
        $clusterSize = <HBaseClusterSize>  
        $vnetID = "<AzureVirtualNetworkID>"
        $subNetName = "<AzureVirtualNetworkSubNetName>"
        $storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
        $storageAccountKey = "<AzureStorageAccountKey>"
        $storageContainerName = "<AzureBlobStorageContainer>"

        $password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password) 

        New-AzureHDInsightCluster -Name $hbaseClusterName `
                                  -ClusterType HBase `
                                  -Version 3.1 `
                                  -Location $location `
                                  -ClusterSizeInNodes $clusterSize `
                                  -Credential $creds `
                                  -VirtualNetworkId $vnetID `
                                  -SubnetName $subNetName `
                                  -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
                                  -DefaultStorageAccountKey $storageAccountKey `
                                  -DefaultStorageContainerName $storageContainerName

3.  Click **Run Script**, or press **F5**.
4.  To validate the cluster, you can either check the cluster from the management portal, or run the following PowerShell cmdlet from the bottom pane:

    Get-AzureHDInsightCluster

## <span id="nextsteps"></span></a>Next Steps

In this tutorial we have learned how to provision an HBase cluster. To learn more, see:

-   [Get started with HDInsight][Get started with HDInsight]
-   [Provision Hadoop clusters in HDInsight][Provision Hadoop clusters in HDInsight]
-   [Get started using HBase with Hadoop in HDInsight][Get started using HBase with Hadoop in HDInsight]
-   [Analyze Twitter sentiment with HBase in HDInsight][Analyze Twitter sentiment with HBase in HDInsight]
-   [Virtual Network Overview][Virtual Network Overview].

  [Prerequisites]: #prerequisites
  [Provision HBase clusters into a virtual network]: #hbaseprovision
  [Connect to the HBase cluster provisioned in virtual network using HBase Java RPC APIs]: #connect
  [Provision an HBase cluster using PowerShell]: #powershell
  [Next steps]: #nextsteps
  [Purchase Options]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [Member Offers]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [Free Trial]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Install and configure Azure PowerShell]: ../install-configure-powershell
  [Run Windows PowerShell scripts]: http://technet.microsoft.com/en-us/library/ee176949.aspx
  [Azure Management portal]: https://manage.windowsazure.com
  []: http://msdn.microsoft.com/library/azure/jj156088.aspx
  [Use Azure Blob storage with Hadoop in HDInsight]: ../hdinsight-use-blob-storage/
  [cluster name and version fields]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png
  [number of nodes and region fields]: ./media/hdinsight-hbase-provision-vnet/hbasewizard2.png
  [administrator name and password fields]: ./media/hdinsight-hbase-provision-vnet/hbasewizard3.png
  [storage account selection]: ./media/hdinsight-hbase-provision-vnet/hbasewizard4.png
  [Get started using HBase with Hadoop in HDInsight]: ../hdinsight-hbase-get-started/
  [Create a Virtual Machine Running Windows Server]: ../virtual-machines-windows-tutorial/
  [Manage Hadoop clusters in HDInsight using the Azure Management Portal]: ../hdinsight-administer-use-management-portal/#rdp
  [hdinsight.hbase.dns.surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
  [Use Maven to build Java applications that use HBase with HDInsight (Hadoop)]: azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-build-java-maven/
  [Name Resolution (DNS)]: http://msdn.microsoft.com/en-us/library/azure/jj156088.aspx
  [Get started with HDInsight]: ../hdinsight-get-started/
  [Provision Hadoop clusters in HDInsight]: ../hdinsight-provision-clusters/
  [Analyze Twitter sentiment with HBase in HDInsight]: ../hdinsight-hbase-twitter-sentiment/
  [Virtual Network Overview]: http://msdn.microsoft.com/library/azure/jj156007.aspx

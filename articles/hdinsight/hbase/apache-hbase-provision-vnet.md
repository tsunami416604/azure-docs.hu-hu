---
title: HBase-fürtök létrehozása a virtuális hálózat – Azure
description: Az Azure HDInsight HBase használatának első lépései. Útmutató a HDInsight HBase-fürtök létrehozása az Azure Virtual Network.
services: hdinsight,virtual-network
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: 73ac2072a087f0931b6c9c776d3ad0bfedb4320b
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199528"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Az Azure virtuális hálózaton található HDInsight az Apache HBase-fürtök létrehozása
Ismerje meg, hogyan hozhat létre az Azure HDInsight az Apache HBase-fürtök egy [Azure Virtual Network][1].

Virtuális hálózati integráció, az Apache HBase-fürtök telepíthetők az alkalmazások azonos virtuális hálózaton, hogy az alkalmazások közvetlenül kommunikálhatnak a HBase. Az előnyök:

* A HBase-fürtöt, amely lehetővé teszi a távoli eljáráshívás HBase Java-kommunikációra, a csomópontok a webalkalmazás közvetlen kapcsolódás (RPC) API-jainak hívására.
* Továbbfejlesztett teljesítmény szerint nem rendelkezik a forgalmat több átjárók és terheléselosztók lépjen.
* Bizalmas adatok feldolgozása még biztonságosabb módon anélkül, hogy egy nyilvános végpontot lehetővé teszi.

### <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Munkaállomás Azure PowerShell-lel**. Lásd: [telepítése és használata az Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Virtuális hálózatban az Apache HBase-fürt létrehozása
Ebben a szakaszban egy Linux-alapú Apache HBase-fürt létrehozása az Azure virtual network használatával a függő Azure Storage-fiók egy [Azure Resource Manager-sablon](../../azure-resource-manager/resource-group-template-deploy.md). Egyéb Fürtlétrehozási módszerekhez és az beállításainak ismertetése, lásd: [létre HDInsight-fürtök](../hdinsight-hadoop-provision-linux-clusters.md). A HDInsight Apache Hadoop-fürtök létrehozása sablon használatával kapcsolatos további információkért lásd: [Apache Hadoop-fürtök létrehozása a HDInsight az Azure Resource Manager-sablonok használatával](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Egyes tulajdonságok módosíthatóként a sablonba. Példa:
>
> * **Hely**: USA 2. keleti régiója
> * **Fürt verziója**: 3.6
> * **A fürt munkavégző csomópontok száma**: 2
> * **Storage-fiók alapértelmezett**: egy egyedi karakterlánccá
> * **Virtuális hálózat neve**: &lt;Fürt neve >-vnet
> * **Virtuális hálózat címtere**: 10.0.0.0/16
> * **Alhálózat neve**: subnet1
> * **Alhálózati címtartomány**: 10.0.0.0/24
>
> &lt;Fürt neve > helyére a fürt nevét, adja meg a sablon használata esetén.
>
>

1. Az alábbi képre kattintva megnyithatja a sablont az Azure Portalon. A sablonban található [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Az a **egyéni üzembe helyezés** panelen adja meg a következő tulajdonságokat:

   * **Előfizetés**: Válassza ki a HDInsight-fürtöt, a függő tárfiókot és az Azure virtuális hálózat létrehozásához használt Azure-előfizetés.
   * **Erőforráscsoport**: Válassza ki **új létrehozása**, és a egy új erőforráscsoport-nevet adja meg.
   * **Hely**: Válasszon egy helyet az erőforráscsoportnak.
   * **ClusterName**: Adja meg a létrehozandó Hadoop-fürt nevét.
   * **Fürt bejelentkezési neve és jelszava**: Az alapértelmezett bejelentkezési név az **admin**.
   * **SSH-felhasználónevet és jelszót**: Az alapértelmezett felhasználónév az **sshuser**.  Ezt át lehet nevezni.
   * **Elfogadom a feltételeket és a fenti feltételeket**: (Válasszon)
3. Kattintson a **Purchase** (Vásárlás) gombra. Egy fürt létrehozása nagyjából 20 percet vesz igénybe. Miután a fürt létrejött, kattintson a portálon nyissa meg a fürt paneljén.

Az oktatóanyag befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. A fürtök törlésével az utasításokért lásd: [kezelése az Apache Hadoop-fürtök a HDInsight az Azure portal használatával](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Az új HBase-fürt használatának megkezdése előtt, használhatja a található eljárások [Apache hadooppal a HDInsight Apache HBase használatának első lépései](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Csatlakozás az Apache HBase-fürtöt, az Apache HBase Java RPC API-k használatával
1. Hozzon létre egy infrastruktúra szolgáltatás (IaaS) virtuális gép ugyanabban az Azure virtuális hálózatban, és ugyanahhoz az alhálózathoz. Új IaaS virtuális gép létrehozásával kapcsolatos útmutatóért lásd: [hozzon létre egy virtuális gépen futó Windows Server](../../virtual-machines/windows/quick-create-portal.md). A jelen dokumentum lépéseit követve a következőket, amikor a hálózati konfigurációt a következő értékeket kell használnia:

   * **Virtuális hálózat**: &lt;Fürt neve >-vnet
   * **Alhálózat**: subnet1

   > [!IMPORTANT]  
   > Cserélje le &lt;fürt neve > néven az előző lépésekben a HDInsight-fürt létrehozásakor használt.

   Ezeket az értékeket használja, a virtuális gép bekerül az azonos virtuális hálózatot és alhálózatot a HDInsight-fürttel. Ez a konfiguráció lehetővé teszi, hogy azokat közvetlenül kommunikálnak egymással. Az üres élcsomópontot egy HDInsight-fürt létrehozásának egyik módja van. Az élcsomópont a fürt kezelésére használható.  További információkért lásd: [üres élcsomópontok használata a HDInsight](../hdinsight-apps-use-edge-node.md).

2. Ha a Java-alkalmazás használatával távolról csatlakozhat a HBase, a teljesen minősített tartománynevét (FQDN) kell használnia. Ennek meghatározásához, be kell szereznie a kapcsolatspecifikus DNS-utótagja a HBase-fürtnek. Ehhez a következő módszerek egyikét használhatja:

   * Győződjön meg arról, hogy egy webes böngésző használata egy [Apache Ambari](https://ambari.apache.org/) hívása:

     Keresse meg a https://&lt;ClusterName >.azurehdinsight.net/api/v1/clusters/&lt;ClusterName > / tároló? minimal_response = true. Kapcsolódó egy JSON-fájlt, a DNS-utótagot.
   * Az Ambari webhelyet használják:

     1. Keresse meg a https://&lt;ClusterName >. azurehdinsight.NET formátumban van.
     2. Kattintson a **gazdagépek** a felső menüben.
   * REST-hívásokat Curl használata:

     ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
     ```

     A JavaScript Object Notation (JSON) visszaadott adatok keresse meg a "gazdaszámítógép_neve" bejegyzés. Tartalmazza a fürtben lévő csomópontok teljes Tartománynevét. Példa:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     A fürt nevét tartomány nevét kezdetű része a DNS-utótagot. Ha például mycluster.b1.cloudapp.net.
   * Azure PowerShell használatával

     A következő Azure PowerShell-parancsfájl segítségével regisztrálja a **Get-ClusterDetail** függvény, amely segítségével adja vissza a DNS-utótag:

     ```powershell
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
            Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
            .Description
            This command shows the following 4 properties of an HDInsight cluster:
            1. ZookeeperQuorum (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.quorum".
            2. ZookeeperClientPort (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.property.clientPort".
            3. HBaseRestServers (supports only HBase type cluster)
                Shows a list of host FQDNs that run the HBase REST server.
            4. FQDNSuffix (supports all cluster types)
                Shows the FQDN suffix of hosts in the cluster.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
            This command shows the value of HBase property "hbase.zookeeper.quorum".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
            This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
            This command shows a list of host FQDNs that run the HBase REST server.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
            This command shows the FQDN suffix of hosts in the cluster.
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
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                $pos = $FQDN.IndexOf(".")
                $Suffix = $FQDN.Substring($pos + 1)
                Write-host $Suffix
            }
        }
     ```

     Az Azure PowerShell-szkript futtatása után az alábbi parancs segítségével a DNS-utótag használatával adja vissza a **Get-ClusterDetail** függvény. Ez a parancs használata esetén adja meg a HDInsight HBase-fürt neve, a rendszergazda neve és a rendszergazdai jelszót.

     ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
     ```

     Ez a parancs a DNS-utótagot adja vissza. Ha például **yourclustername.b4.internal.cloudapp.net**.


<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/PrimaryDNSSuffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Győződjön meg arról, hogy a virtuális gép kommunikálhatnak a HBase-fürtnek, használja a parancsot `ping headnode0.<dns suffix>` a virtuális gépről. Ha például a ping headnode0.mycluster.b1.cloudapp.net.

Használja ezt az információt egy Java-alkalmazást, kövesse a lépéseket a [Apache-Maven használata Java-alkalmazások, amelyek használják az Apache HBase a HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) alkalmazás létrehozása. Ahhoz, hogy az alkalmazás HBase távoli kiszolgálóhoz csatlakozik, módosítsa a **hbase-site.xml** ebben a példában a teljes tartománynév használata Zookeeper fájlt. Példa:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> Az Azure-ban a névfeloldással kapcsolatos további információk virtuális hálózatok, beleértve a saját DNS-kiszolgáló használata: [neve (DNS) névfeloldási](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtudhatta, hogyan hozhat létre Apache HBase-fürtöt. További tudnivalókért lásd:

* [HDInsight – első lépések](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Üres élcsomópontok használata a HDInsight](../hdinsight-apps-use-edge-node.md)
* [A HDInsight Apache HBase-replikálás konfigurálása](apache-hbase-replication.md)
* [A HDInsight Apache Hadoop-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md)
* [Az Apache hadooppal a HDInsight Apache HBase használatának első lépései](./apache-hbase-tutorial-get-started-linux.md)
* [A Virtual Network áttekintése](../../virtual-network/virtual-networks-overview.md)

[1]: https://azure.microsoft.com/services/virtual-network/
[2]: https://technet.microsoft.com/library/ee176961.aspx
[3]: https://technet.microsoft.com/library/hh847889.aspx


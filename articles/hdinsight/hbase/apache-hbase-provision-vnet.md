---
title: "Hozzon létre HBase-fürtökkel egy virtuális hálózat - Azure |} Microsoft Docs"
description: "Ismerkedés az Azure HDInsight HBase használatával. Útmutató a HDInsight HBase fürtök létrehozása az Azure-beli virtuális hálózathoz."
keywords: 
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 8de8e446-f818-4e61-8fad-e9d38421e80d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: 08f71340fea7424262fc9c549351bd50bcfcafe2
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2018
---
# <a name="create-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Hozzon létre HBase-fürtökkel a HDInsight az Azure-beli virtuális hálózathoz
Útmutató az Azure HDInsight HBase-fürtök létrehozása egy [Azure Virtual Network][1].

A virtuális hálózati integráció HBase-fürtökkel telepíthetők az alkalmazások azonos virtuális hálózaton, hogy az alkalmazások közvetlenül kommunikálhatnak a HBase. A következő előnyöket nyújtja:

* Közvetlen kapcsolatot a webes alkalmazás csomópontjának a HBase-fürtöt, amely lehetővé teszi a kommunikációt keresztül HBase Java távoli eljáráshívásnak (RPC) API-k hívható.
* Javítja a teljesítményt a forgalom nem rendelkezik több átjárók és terheléselosztók lépjen.
* A képes-e feldolgozni bizalmas adatokat több biztonságos elérését anélkül, hogy egy nyilvános végpontot.

### <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Munkaállomás Azure PowerShell-lel**. Lásd: [telepítése és használata az Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-hbase-cluster-into-virtual-network"></a>HBase-fürt a virtuális hálózat létrehozása
Ebben a szakaszban egy Linux-alapú HBase fürt létrehozása a függő Azure Storage-fiók egy Azure-beli virtuális hálózat használatával egy [Azure Resource Manager sablon](../../azure-resource-manager/resource-group-template-deploy.md). Egyéb Fürtlétrehozási módszerekhez és a beállítások ismertetése, lásd: [HDInsight-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md). Hdinsight Hadoop-fürtök létrehozása sablon használatával kapcsolatos további információkért lásd: [létrehozása Hadoop-fürtök Azure Resource Manager-sablonok használata a hdinsightban](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]
> Néhány tulajdonság kódolva a sablonba. Példa:
>
> * **Hely**: USA keleti régiója 2. régiója
> * **Fürt verziója**: 3.6
> * **A fürt feldolgozó csomópontok száma**: 2. régiója
> * **Alapértelmezett tárfiók**: egy egyedi karakterlánc
> * **Virtuális hálózati név**: &lt;fürt neve >-hálózatok
> * **Virtuális hálózat címtere**: 10.0.0.0/16
> * **Alhálózati név**: Alhalozat_1
> * **Alhálózati címtartományt**: 10.0.0.0/24
>
> &lt;A fürt neve > helyére a fürt neve a sablon használata esetén adja meg.
>
>

1. Az alábbi képre kattintva megnyithatja a sablont az Azure Portalon. A sablon található [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Az a **egyéni telepítési** panelen adja meg a következő tulajdonságokkal:

   * **Előfizetés**: válassza ki a HDInsight-fürthöz, a függő tárfiókot és az Azure virtuális hálózat létrehozásához használt Azure-előfizetéssel.
   * **Erőforráscsoport**: válasszon **hozzon létre új**, és adjon meg egy új erőforráscsoport neve.
   * **Hely**: Válasszon egy helyet az erőforráscsoportnak.
   * **ClusterName**: Adja meg a létrehozandó Hadoop-fürt nevét.
   * **A fürt bejelentkezési neve és jelszava**: Az alapértelmezett bejelentkezési név az **admin**.
   * **SSH-felhasználónév és jelszó**: Az alapértelmezett felhasználónév az **sshuser**.  Ezt át lehet nevezni.
   * **Elfogadom a feltételeket és a fenti feltételek**: (válassza ki)
3. Kattintson a **Purchase** (Vásárlás) gombra. Egy fürt létrehozása nagyjából 20 percet vesz igénybe. A fürt létrehozása után kattintson a portálon, hogy megnyissa a fürt paneljén.

Az oktatóanyag befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. A fürtök törlésével utasításokért lásd: [kezelése Hadoop-fürtök a HDInsight az Azure portál használatával](../hdinsight-administer-use-management-portal.md#delete-clusters).

Az új HBase fürtöt használatának megkezdése előtt, akkor az eljárásokkal található [HBase a Hadoop HDInsight használatának megkezdésében](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-hbase-cluster-using-hbase-java-rpc-apis"></a>A HBase Java RPC API-val HBase-fürtöt kapcsolódni
1. Hozzon létre egy infrastruktúra (IaaS) szolgáltatás virtuális gépként az azonos Azure virtuális hálózat és az ugyanazon az alhálózaton. Egy új infrastruktúra-szolgáltatási virtuális gép létrehozása, lásd: [hozzon létre egy virtuális gép futó Windows Server](../../virtual-machines/windows/quick-create-portal.md). Ha ez a dokumentum lépéseit követve, a hálózati konfiguráció a következő értékeket kell használnia:

   * **Virtuális hálózati**: &lt;fürt neve >-hálózatok
   * **Alhálózati**: Alhalozat_1

   > [!IMPORTANT]
   > Cserélje le &lt;fürtnév > az előző lépésben a HDInsight-fürt létrehozásakor használt név.
   >
   >

   Ezeket az értékeket használja, a virtuális gép el van helyezve a azonos virtuális hálózati és alhálózati a HDInsight-fürttel. Ez a konfiguráció lehetővé teszi, hogy azokat közvetlenül kommunikálhatnak egymással. Nincs olyan módon, egy üres élcsomópontot HDInsight-fürtöt létrehozni. Élcsomópont használható a fürt kezeléséhez.  További információkért lásd: [üres peremhálózati csomópontok használata a Hdinsightban](../hdinsight-apps-use-edge-node.md).

2. Java-alkalmazások távolról csatlakozni a HBase használatakor a teljesen minősített tartománynevét (FQDN) kell használnia. Ennek meghatározásához, ha előbb telepítik azokra a kapcsolatspecifikus DNS-utótagja a HBase-fürtöt. Ehhez a következő módszerek egyikét használhatja:

   * Egy webes böngésző segítségével az Ambari hívást:

     Browse to https://&lt;ClusterName>.azurehdinsight.net/api/v1/clusters/&lt;ClusterName>/hosts?minimal_response=true. A JSON-fájl, a DNS-utótagok változik.
   * Az Ambari webhelyet használja:

     1. Keresse meg a https://&lt;ClusterName >. azurehdinsight.net.
     2. Kattintson a **állomások** a felső menüben.
   * A REST-hívások Curl használja:

    ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
    ```

     A JavaScript Object Notation (JSON) visszaadott adatok keresse meg a "gazdaszámítógép_neve" bejegyzés. A fürt a csomópontok teljes Tartománynevét tartalmaz. Példa:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     A tartomány nevét-tól kezdődően a fürt neve része a DNS-utótagot. Például mycluster.b1.cloudapp.net.
   * Azure PowerShell használatával

     A következő Azure PowerShell-parancsfájl segítségével regisztrálja a **Get-ClusterDetail** függvénynek, amely segítségével adja vissza a DNS-utótag:

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

     Az Azure PowerShell parancsprogram futtatása után az alábbi parancs segítségével a DNS-utótag használatával adja vissza a **Get-ClusterDetail** függvény. Ez a parancs használata esetén adja meg a HDInsight HBase fürt nevét, a felügyeleti neve és a rendszergazdai jelszó.

    ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
    ```

     A parancs a DNS-utótagot adja vissza. Például **yourclustername.b4.internal.cloudapp.net**.


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

Győződjön meg arról, hogy a virtuális gép kommunikál a HBase-fürtöt, a parancs segítségével `ping headnode0.<dns suffix>` a virtuális gépről. Például a ping headnode0.mycluster.b1.cloudapp.net.

Ezeket az információkat a Java-alkalmazások használatához hajtsa végre a lépéseket [Maven használata a hdinsight (Hadoop) eszközzel HBase használó Java-alkalmazások létrehozásához](./apache-hbase-build-java-maven-linux.md) alkalmazás létrehozása. Ahhoz, hogy az alkalmazás HBase távoli kiszolgálóhoz csatlakozik, módosítsa a **hbase-site.xml** fájl ebben a példában a Zookeeper a teljes Tartománynevet használja. Példa:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]
> További információ a névfeloldás az Azure-ban virtuális hálózatok, beleértve a saját DNS-kiszolgáló használata: [Name (DNS) névfeloldási](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
>
>

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtudta, hogyan hozhat létre HBase-fürtöt. További tudnivalókért lásd:

* [Első lépései a hdinsight eszközzel](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Üres peremhálózati csomópontok használata a Hdinsightban](../hdinsight-apps-use-edge-node.md)
* [HBase-replikálás konfigurálása a HDInsightban](apache-hbase-replication.md)
* [Hdinsight Hadoop-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md)
* [A HBase és a Hadoop használatának első lépései a HDInsightban](./apache-hbase-tutorial-get-started-linux.md)
* [A Virtual Network áttekintése](../../virtual-network/virtual-networks-overview.md)

[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx


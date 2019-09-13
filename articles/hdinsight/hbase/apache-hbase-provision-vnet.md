---
title: HBase-fürtök létrehozása egy Virtual Networkban – Azure
description: A HBase használatának első lépései az Azure HDInsight. Ismerje meg, hogyan hozhat létre HDInsight HBase-fürtöket az Azure Virtual Network.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: c8fc7c931f31e1ff58f41faa9a29f7e77e9655fd
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917320"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Apache HBase-fürtök létrehozása a HDInsight-ben az Azure-ban Virtual Network
Ismerje meg, hogyan hozhat létre Azure HDInsight Apache HBase-fürtöket egy [Azure-Virtual Network][1].

A Virtual Network Integration használatával az Apache HBase-fürtök ugyanarra a virtuális hálózatra telepíthetők, mint az alkalmazások, így az alkalmazások közvetlenül kommunikálhatnak a HBase. Az előnyök a következők:

* A webalkalmazás közvetlen kapcsolódása a HBase-fürt csomópontjaihoz, amely lehetővé teszi a kommunikációt a HBase Java távoli eljáráshívás (RPC) API-kon keresztül.
* Jobb teljesítmény azáltal, hogy a forgalom nem a több átjáróra és terheléselosztó-ra mutat.
* A bizalmas adatok biztonságos módon történő feldolgozásának lehetősége a nyilvános végpontok kimutatása nélkül.

### <a name="prerequisites"></a>Előfeltételek
A cikk elkezdése előtt a következő elemeket kell megadnia:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Munkaállomás Azure PowerShell-lel**. Lásd: [Azure PowerShell telepítése és használata](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Apache HBase-fürt létrehozása virtuális hálózatban
Ebben a szakaszban egy Linux-alapú Apache HBase-fürtöt hoz létre egy Azure-beli virtuális hálózatban található függő Azure Storage-fiókkal egy [Azure Resource Manager sablon](../../azure-resource-manager/resource-group-template-deploy.md)használatával. Más fürtök létrehozási módszereivel és a beállítások megismerésével kapcsolatban lásd: [HDInsight-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md). A Apache Hadoop-fürtök HDInsight való létrehozásával kapcsolatos további információkért lásd: [Apache Hadoop-fürtök létrehozása a HDInsight-ben Azure Resource Manager sablonok használatával](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Néhány tulajdonságot a sablonban rögzítettek. Példa:
>
> * **Hely**: USA 2. keleti régiója
> * **Fürt verziója**: 3.6
> * **Fürt munkavégző csomópontjainak száma**: 2
> * **Alapértelmezett Storage-fiók**: egyedi karakterlánc
> * **Virtuális hálózat neve**: &lt;Fürt neve >-vnet
> * **Virtuális hálózati címtartomány**: 10.0.0.0/16
> * **Alhálózat neve**: subnet1
> * **Alhálózati címtartomány**: 10.0.0.0/24
>
> &lt;A fürt neve > a sablon használatakor megadott fürt nevével lesz lecserélve.


1. Az alábbi képre kattintva megnyithatja a sablont az Azure Portalon. A sablon az [Azure Gyorsindítás sablonjaiban](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/)található.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure"></a>
2. Az **Egyéni telepítés** panelen adja meg a következő tulajdonságokat:

   * **Előfizetés**: Válasszon ki egy Azure-előfizetést, amely a HDInsight-fürt, a függő Storage-fiók és az Azure virtuális hálózat létrehozásához használatos.
   * **Erőforráscsoport**: Válassza az **új létrehozása**lehetőséget, és adjon meg egy új erőforráscsoport-nevet.
   * **Hely**: Válasszon egy helyet az erőforráscsoportnak.
   * **ClusterName**: Adja meg a létrehozandó Hadoop-fürt nevét.
   * **Fürt bejelentkezési neve és jelszava**: Az alapértelmezett bejelentkezési név az **admin**.
   * **SSH-Felhasználónév és-jelszó**: Az alapértelmezett felhasználónév az **sshuser**.  Ezt át lehet nevezni.
   * **Elfogadom a fenti feltételeket és a fent leírt feltételeket**: Válassza
3. Kattintson a **Purchase** (Vásárlás) gombra. Egy fürt létrehozása nagyjából 20 percet vesz igénybe. A fürt létrehozása után a portálon a fürt paneljére kattintva nyithatja meg.

A cikk befejezése után érdemes lehet törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. A fürtök törlésére vonatkozó utasításokért lásd: [Apache Hadoop-fürtök kezelése a HDInsight-ben a Azure Portal használatával](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Az új HBase-fürt használatának megkezdéséhez használja az első [lépések az Apache Apache Hadoop HBase használata a HDInsight-ben](./apache-hbase-tutorial-get-started-linux.md)című témakörben található eljárásokat.

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Kapcsolódás az Apache HBase-fürthöz Apache HBase Java RPC API-k használatával
1. Hozzon létre egy infrastruktúra-szolgáltatási (IaaS) virtuális gépet ugyanabba az Azure-beli virtuális hálózatba és ugyanahhoz az alhálózatba. Az új IaaS virtuális gépek létrehozásával kapcsolatos utasításokért lásd: [Windows Server rendszerű virtuális gép létrehozása](../../virtual-machines/windows/quick-create-portal.md). A jelen dokumentum lépéseinek követése során a következő értékeket kell használnia a hálózati konfigurációhoz:

   * **Virtuális hálózat**: &lt;Fürt neve >-vnet
   * **Alhálózat**: subnet1

   > [!IMPORTANT]  
   > Cserélje &lt;le a fürt nevét > a HDInsight-fürt előző lépésekben való létrehozásakor használt névre.

   Ezeknek az értékeknek a használatával a virtuális gép ugyanahhoz a virtuális hálózathoz és alhálózathoz kerül, mint a HDInsight-fürt. Ez a konfiguráció lehetővé teszi, hogy közvetlenül kommunikáljanak egymással. Egy üres peremhálózati csomóponttal rendelkező HDInsight-fürtöt is létrehozhat. A peremhálózati csomópontot a fürt kezelésére használhatja.  További információ: [üres peremhálózati csomópontok használata a HDInsight-ben](../hdinsight-apps-use-edge-node.md).

2. Ha Java-alkalmazás használatával távolról csatlakozik a HBase, a teljes tartománynevet (FQDN) kell használnia. Ennek megállapításához be kell szereznie a HBase-fürt kapcsolatspecifikus DNS-utótagját. Ehhez a következő módszerek egyikét használhatja:

   * [Apache Ambari](https://ambari.apache.org/) -hívás létrehozása webböngésző használatával:

     Tallózással keresse&lt;meg a https://ClusterName >. azurehdinsight. NET/API/v1&lt;/Clusters/ClusterName >/hosts? minimal_response = True értéket. A rendszer a DNS-utótagokat tartalmazó JSON-fájlt kapcsolja be.
   * A Ambari webhely használata:

     1. Keresse meg a&lt;https://ClusterName >. azurehdinsight. net parancsot.
     2. Kattintson a felső menüben található **gazdagépek** elemre.
   * REST-hívások a curl használatával:

     ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
     ```

     A visszaadott JavaScript Object Notation-(JSON-) adatban keresse meg a "host_name" bejegyzést. Tartalmazza a fürt csomópontjainak teljes tartománynevét. Példa:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     A tartománynév a fürt nevétől kezdődő része a DNS-utótag. Például: mycluster.b1.cloudapp.net.
   * Azure PowerShell használatával

     A következő Azure PowerShell parancsfájl használatával regisztrálja a **Get-ClusterDetail** függvényt, amely a DNS-utótag visszaküldésére használható:

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

     A Azure PowerShell parancsfájl futtatása után a következő parancs használatával adja vissza a DNS-utótagot a **Get-ClusterDetail** függvénnyel. A parancs használata esetén adja meg a HDInsight HBase, a rendszergazda nevét és a rendszergazdai jelszót.

     ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
     ```

     Ez a parancs visszaadja a DNS-utótagot. Például: **yourclustername.B4.internal.cloudapp.net**.


<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/hdi-primary-dns-suffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Annak ellenőrzéséhez, hogy a virtuális gép tud-e kommunikálni a HBase- `ping headnode0.<dns suffix>` fürttel, használja a parancsot a virtuális gépről. Például Pingelje a headnode0.mycluster.b1.cloudapp.net.

Ha ezt az információt Java-alkalmazásokban szeretné használni, kövesse az [Apache Maven használata olyan Java-alkalmazások létrehozásához, amelyek Apache HBase-t használnak a HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) alkalmazással. Ha azt szeretné, hogy az alkalmazás kapcsolódjon egy távoli HBase-kiszolgálóhoz, módosítsa az ebben a példában szereplő **hbase-site. XML** fájlt úgy, hogy a teljes tartománynevet használja a Zookeeper. Példa:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> További információ az Azure-beli virtuális hálózatok névfeloldásáról, beleértve a saját DNS-kiszolgáló használatának módját: [névfeloldás (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan hozhat létre Apache HBase-fürtöt. További tudnivalókért lásd:

* [Ismerkedés a HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Üres peremhálózati csomópontok használata a HDInsight](../hdinsight-apps-use-edge-node.md)
* [Apache HBase-replikálás konfigurálása a HDInsight-ben](apache-hbase-replication.md)
* [Apache Hadoop-fürtök létrehozása a HDInsight-ben](../hdinsight-hadoop-provision-linux-clusters.md)
* [Az Apache HBase használatának első lépései a HDInsight Apache Hadoop](./apache-hbase-tutorial-get-started-linux.md)
* [A Virtual Network áttekintése](../../virtual-network/virtual-networks-overview.md)

[1]: https://azure.microsoft.com/services/virtual-network/
[2]: https://technet.microsoft.com/library/ee176961.aspx
[3]: https://technet.microsoft.com/library/hh847889.aspx


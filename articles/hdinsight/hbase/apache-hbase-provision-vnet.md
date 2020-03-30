---
title: HBase-fürtök létrehozása virtuális hálózatban – Azure
description: Ismerkedhet a HBase használatával az Azure HDInsightban. Ismerje meg, hogyan hozhat létre HDInsight HBase-fürtöket az Azure virtuális hálózaton.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: e4e15d1c6554fc567f668b2033bff5b5664db918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972798"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Apache HBase-fürtök létrehozása a HDInsight szolgáltatásban az Azure Virtuális Hálózatban

Ismerje meg, hogyan hozhat létre Azure HDInsight Apache HBase fürtöket egy [Azure virtuális hálózatban.](https://azure.microsoft.com/services/virtual-network/)

A virtuális hálózati integrációval az Apache HBase fürtök ugyanarra a virtuális hálózatra telepíthetők, mint az alkalmazások, így az alkalmazások közvetlenül kommunikálhatnak a HBase-lel. Ez a következő előnyöket nyújtja:

* A webalkalmazás közvetlen kapcsolata a HBase fürt csomópontjaival, amely lehetővé teszi a HBase Java távoli eljáráshívási (RPC) API-kon keresztüli kommunikációt.
* Jobb teljesítmény azáltal, hogy nem a forgalom megy át több átjárók és terheléselosztók.
* A bizalmas információk biztonságosabb módon történő feldolgozásának lehetősége nyilvános végpont felfedése nélkül.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Apache HBase fürt létrehozása virtuális hálózatba

Ebben a szakaszban egy Linux-alapú Apache HBase-fürtöt hoz létre a függő Azure Storage-fiókkal egy Azure virtuális hálózatban egy [Azure Resource Manager-sablon](../../azure-resource-manager/templates/deploy-powershell.md)használatával. A fürtlétrehozási módszerekről és a beállítások megértéséről a [HDInsight-fürtök létrehozása című](../hdinsight-hadoop-provision-linux-clusters.md)témakörben olvashat. Az Apache Hadoop-fürtök HDInsightban való létrehozásához sablon használatával kapcsolatos további tudnivalókért olvassa el az [Apache Hadoop-fürtök létrehozása a HDInsightban az Azure Resource Manager-sablonok használatával című témakört.](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Egyes tulajdonságok kódolva vannak a sablonban. Példa:
>
> * **Helyszín**: USA keleti része 2
> * **Fürt verziója**: 3.6
> * **Fürtmunkavégző csomópontszáma**: 2
> * **Alapértelmezett tárfiók:** egyedi karakterlánc
> * **Virtuális hálózat neve**: CLUSTERNAME-vnet
> * **Virtuális hálózati címtér**: 10.0.0.0/16
> * **Alhálózat neve**: alhálózat1
> * **Alhálózati címtartomány:** 10.0.0.0/24
>
> `CLUSTERNAME`a sablon használatakor megadott fürtnév relevancia-ra kerül.

1. Válassza ki az alábbi képet a sablon megnyitásához az Azure Portalon. A sablon az [Azure rövid útmutató sablonjaiban](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/)található.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Az **Egyéni központi telepítés** párbeszédpanelen válassza a **Sablon szerkesztése**lehetőséget.

1. A 165-ös `Standard_A3` sorban `Standard_A4_V2`módosítsa az értéket a értékre. Ezután válassza a **Save** (Mentés) lehetőséget.

1. Töltse ki a fennmaradó sablont a következő információkkal:

    |Tulajdonság |Érték |
    |---|---|
    |Előfizetés|Válasszon ki egy Azure-előfizetést a HDInsight-fürt, a függő storage-fiók és az Azure virtuális hálózat létrehozásához.|
    Erőforráscsoport|Válassza **az Új létrehozása**lehetőséget, és adjon meg egy új erőforráscsoport nevet.|
    |Hely|Válasszon egy helyet az erőforráscsoportnak.|
    |Fürt neve|Adja meg a létrehozandó Hadoop-fürt nevét.|
    |Fürt bejelentkezési felhasználóneve és jelszava|Az alapértelmezett felhasználónév az **admin**. Adja meg a jelszót.|
    |Ssh felhasználónév és jelszó|Az alapértelmezett felhasználónév az **sshuser**.  Adja meg a jelszót.|

    Válassza az **Elfogadom a fenti feltételeket és feltételeket.**

1. Válassza a **Beszerzés** lehetőséget. Egy fürt létrehozása nagyjából 20 percet vesz igénybe. A fürt létrehozása után kiválaszthatja a fürtöt a portálon a megnyitásához.

A cikk befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. A fürt törlésével kapcsolatos útmutatásért olvassa [el az Apache Hadoop-fürtök kezelése a HDInsightban az Azure Portal használatával című témakört.](../hdinsight-administer-use-portal-linux.md#delete-clusters)

Az új HBase-fürttel való együttműködés megkezdéséhez használhatja az [Apache HBase használatának első lépései az Apache Hadoop használatával](./apache-hbase-tutorial-get-started-linux.md)a HDInsight ban című részben található eljárásokat.

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Csatlakozás az Apache HBase fürthöz Apache HBase Java RPC API-k használatával

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy infrastruktúrát szolgáltatásként (IaaS) virtuális gép ugyanabban az Azure virtuális hálózatban és ugyanabban az alhálózatban. Az új IaaS virtuális gépek létrehozásáról a Windows Server rendszert futtató virtuális gép létrehozása című témakörben [talál.](../../virtual-machines/windows/quick-create-portal.md) Ha a jelen dokumentum lépéseit követi, a hálózati konfigurációhoz a következő értékeket kell használnia:

* **Virtuális hálózat**: CLUSTERNAME-vnet
* **Alhálózat**: alhálózat1

> [!IMPORTANT]  
> Cserélje `CLUSTERNAME` le a HDInsight-fürt korábbi lépésekben történő létrehozásakor használt névre.

Ezekkel az értékekkel a virtuális gép ugyanabba a virtuális hálózatba és alhálózatba kerül, mint a HDInsight-fürt. Ez a konfiguráció lehetővé teszi számukra, hogy közvetlenül kommunikáljanak egymással. Van egy módja annak, hogy hozzon létre egy HDInsight-fürt egy üres peremhálózati csomópont. A peremhálózati csomópont a fürt kezeléséhez használható.  További információ: [Üres peremhálózati csomópontok használata a HDInsightban.](../hdinsight-apps-use-edge-node.md)

### <a name="obtain-fully-qualified-domain-name"></a>Teljesen minősített tartománynév beszerzése

Ha egy Java-alkalmazást használ a HBase-hez való távoli csatlakozáshoz, a teljesen minősített tartománynevet (FQDN) kell használnia. Ennek megállapításához be kell szereznie a HBase fürt kapcsolatspecifikus DNS-utótagot. Ehhez az alábbi módszerek egyikét használhatja:

* [Apache Ambari-hívás](https://ambari.apache.org/) browser használata:

    Nyissa meg a következő címet: `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true`. A DNS-utótagokkal rendelkező JSON-fájlt ad vissza.

* Használja az Ambari weboldalát:

    1. Nyissa meg a következő címet: `https://CLUSTERNAME.azurehdinsight.net`.
    2. Válassza a felső menü **Állomások** parancsát.

* Rest-hívások kezdeményezéséhez használja a Curl t:

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

A JavaScript Object Notation (JSON) visszaadott adatokközött keresse meg a "host_name" bejegyzést. A fürt csomópontjainak teljes tartománynát tartalmazza. Példa:

```
"host_name" : "hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net"
```

A tartománynévnek a fürtnévvel kezdődő része a DNS-utótag. Például: `hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

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

### <a name="verify-communication-inside-virtual-network"></a>A virtuális hálózaton belüli kommunikáció ellenőrzése

Annak ellenőrzéséhez, hogy a virtuális gép képes-e `ping headnode0.<dns suffix>` kommunikálni a HBase fürttel, használja a virtuális gép parancsát. Például: `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

Ha ezt az információt java alkalmazásban szeretné használni, kövesse az [Apache Maven használata című, az Apache HBase és A HDInsight (Hadoop) alkalmazást használó Java-alkalmazások létrehozásához](./apache-hbase-build-java-maven-linux.md) című lépéseit. Ha azt szeretné, hogy az alkalmazás egy távoli HBase-kiszolgálóhoz csatlakozzon, módosítsa a példában szereplő **hbase-site.xml** fájlt a Zookeeper teljes tartománynévének használatához. Példa:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> Az Azure virtuális hálózatokban a névfeloldásról, többek között a saját DNS-kiszolgáló használatáról a [Névfeloldás (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan hozhat létre apache HBase-fürtöt. További tudnivalókért lásd:

* [Ismerkedés a HDInsight szolgáltatással](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Üres peremhálózati csomópontok használata a HDInsightban](../hdinsight-apps-use-edge-node.md)
* [Az Apache HBase replikáció konfigurálása a HDInsightban](apache-hbase-replication.md)
* [Apache Hadoop-fürtök létrehozása a HDInsightban](../hdinsight-hadoop-provision-linux-clusters.md)
* [Az Apache HBase és az Apache Hadoop használatának első lépései a HDInsightban](./apache-hbase-tutorial-get-started-linux.md)
* [A Virtual Network áttekintése](../../virtual-network/virtual-networks-overview.md)

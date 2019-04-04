---
title: Használja az Apache Phoenix és az SQLLine a az Azure HDInsight hbase-ben
description: Ismerje meg, az Apache Phoenix használata a HDInsight. Emellett ismerje meg, telepítése és beállítása az SQLLine a számítógépen szeretne csatlakozni a HDInsight HBase-fürtöt.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: hrasheed
ms.openlocfilehash: 38f86bd19c85440fbad0e7fd56a3dd9ba836c7b8
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58903302"
---
# <a name="use-apache-phoenix-with-linux-based-apache-hbase-clusters-in-hdinsight"></a>Az Apache HBase Linux-alapú Apache Phoenix használata a HDInsight-fürtök
Ismerje meg, hogyan használható [Apache Phoenix](https://phoenix.apache.org/) az Azure HDInsight és az SQLLine használata. A Phoenix kapcsolatos további információkért lásd: [kevesebb mint 15 perc alatt az Apache Phoenix](https://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). A Phoenix szintaxis megtekintéséhez [Apache Phoenix nyelvtani](https://phoenix.apache.org/language/index.html).

> [!NOTE]  
> HDInsight Phoenix verzió információ: [a HDInsight által biztosított az Apache Hadoop-fürtverziók újdonságai](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Az SQLLine használata
[Az SQLLine](http://sqlline.sourceforge.net/) parancssori segédprogram az SQL végrehajtásához.

### <a name="prerequisites"></a>Előfeltételek
Az SQLLine használata előtt a következőkkel kell rendelkeznie:

* **A HDInsight Apache HBase-fürtöt**. Hozzon létre egyet, tekintse meg [HDInsight az Apache HBase használatának első lépései](./apache-hbase-tutorial-get-started-linux.md).

HBase-fürt való csatlakozáskor kapcsolódnia kell a [Apache ZooKeeper](https://zookeeper.apache.org/) virtuális gépeket. HDInsight-fürtökön három ZooKeeper virtuális gép rendelkezik.

**A ZooKeeper-gazdagép nevének lekérése érdekében**

1. Nyissa meg [Apache Ambari](https://ambari.apache.org/) megkeresve **https://\<fürtnév\>. azurehdinsight.net**.
2. Jelentkezzen be, adja meg a HTTP (fürt) felhasználónevét és jelszavát.
3. A bal oldali menüben válassza ki a **ZooKeeper**. Három **ZooKeeper Server** példányok szerepelnek.
4. Válassza ki az egyik a **ZooKeeper Server** példányok. Az a **összefoglalás** ablaktáblán keresse meg a **állomásnév**. It looks similar to *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Az SQLLine használata**

1. Csatlakozzon a fürthöz SSH használatával. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Az ssh-t a következő parancsok használatával futtassa az SQLLine:

        cd /usr/hdp/current/phoenix-client/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. Egy HBase tábla létrehozásához és adatok beszúrása, futtassa a következő parancsokat:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

További információkért lásd: a [az SQLLine manuális](http://sqlline.sourceforge.net/#manual) és [Apache Phoenix nyelvtani](https://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedett az Apache Phoenix használata a HDInsight. További információkért tanulmányozza a következő cikkeket:

* [HDInsight HBase overview][hdinsight-hbase-overview].
  Az Apache HBase egy Apache, nyílt forráskódú nosql-alapú adatbázis az Apache hadoop, amely véletlenszerű hozzáférést és erős konzisztenciát biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatot.
* [Az Azure Virtual Network üzembe helyezése az Apache HBase-fürtök][hdinsight-hbase-provision-vnet].
  Virtuális hálózati integráció, az Apache HBase-fürtök telepíthetők ugyanazon a virtuális hálózaton az alkalmazások, így az alkalmazások közvetlenül kommunikálhatnak a HBase.
* [Az Apache HBase-replikálás konfigurálása a HDInsight](apache-hbase-replication.md). Ismerje meg, az Apache HBase-replikálás beállítása két Azure-adatközpontok között.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md



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
ms.openlocfilehash: c87596099995cbd34a0ee32ec287de81ec741bda
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51008062"
---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Az Apache Phoenix használata a HDInsight Linux-alapú HBase-fürtök
Ismerje meg, hogyan használható [Apache Phoenix](http://phoenix.apache.org/) az Azure HDInsight és az SQLLine használata. A Phoenix kapcsolatos további információkért lásd: [Phoenix kevesebb mint 15 perc alatt](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). A Phoenix szintaxis megtekintéséhez [Phoenix nyelvtani](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> HDInsight Phoenix verzió információ: [a HDInsight által biztosított Hadoop-fürtverziók újdonságai](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Az SQLLine használata
[Az SQLLine](http://sqlline.sourceforge.net/) parancssori segédprogram az SQL végrehajtásához.

### <a name="prerequisites"></a>Előfeltételek
Az SQLLine használata előtt a következőkkel kell rendelkeznie:

* **A HDInsight HBase-fürtöt**. Hozzon létre egyet, tekintse meg [HDInsight az Apache HBase használatának első lépései](./apache-hbase-tutorial-get-started-linux.md).

HBase-fürt való csatlakozáskor a ZooKeeper-beli virtuális gépek csatlakozni kell. HDInsight-fürtökön három ZooKeeper virtuális gép rendelkezik.

**A ZooKeeper-gazdagép nevének lekérése érdekében**

1. Navigáljon az Ambari megnyitásához **https://\<fürtnév\>. azurehdinsight.net**.
2. Jelentkezzen be, adja meg a HTTP (fürt) felhasználónevét és jelszavát.
3. A bal oldali menüben válassza ki a **ZooKeeper**. Három **ZooKeeper Server** példányok szerepelnek.
4. Válassza ki az egyik a **ZooKeeper Server** példányok. Az a **összefoglalás** ablaktáblán keresse meg a **állomásnév**. Ez hasonlít *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Az SQLLine használata**

1. Csatlakozzon a fürthöz SSH használatával. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Az ssh-t a következő parancsok használatával futtassa az SQLLine:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. Egy HBase tábla létrehozásához és adatok beszúrása, futtassa a következő parancsokat:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

További információkért lásd: a [az SQLLine manuális](http://sqlline.sourceforge.net/#manual) és [Phoenix nyelvtani](http://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedett az Apache Phoenix használata a HDInsight. További információkért tanulmányozza a következő cikkeket:

* [HDInsight HBase overview][hdinsight-hbase-overview].
  A HBase egy Apache, nyílt forráskódú nosql-alapú adatbázis, amely véletlenszerű hozzáférést és erős konzisztenciát biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatot hadoop.
* [Az Azure Virtual Network HBase-fürtök kiépítése][hdinsight-hbase-provision-vnet].
  Virtuális hálózati integráció, a HBase-fürtök telepíthetők ugyanazon a virtuális hálózaton az alkalmazások, így az alkalmazások közvetlenül kommunikálhatnak a HBase.
* [A HDInsight HBase-replikálás konfigurálása](apache-hbase-replication.md). Ismerje meg, hogyan hozza létre a HBase-replikáció Azure két adatközpontjában.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md



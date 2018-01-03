---
title: "Használja az Apache Phoenix és az Azure hdinsight HBase SQLLine |} Microsoft Docs"
description: "Útmutató Apache Phoenix használata a Hdinsightban. Is megtudhatja, hogyan kell telepíteni, és állítsa be SQLLine a számítógépre való kapcsolódáshoz a hdinsight HBase-fürtöt."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: cda0f33b-a2e8-494c-972f-ae0bb482b818
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/22/2017
ms.author: jgao
ms.openlocfilehash: 70f8786bae555456dd019ad76bda974667cec5ba
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>Hdinsight Linux-alapú HBase-fürtökkel Apache Phoenix használata
Ismerje meg, hogyan használható [Apache Phoenix](http://phoenix.apache.org/) Azure HDInsight és SQLLine használata. Phoenix kapcsolatos további információkért lásd: [Phoenix kevesebb mint 15 perc alatt](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). A Phoenix nyelvtan, lásd: [Phoenix nyelvtan](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> HDInsight Phoenix verzió információ: [What's new in HDInsight által biztosított Hadoop-fürt verziók](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>SQLLine használata
[SQLLine](http://sqlline.sourceforge.net/) egy parancssori segédprogram SQL végrehajtása.

### <a name="prerequisites"></a>Előfeltételek
Mielőtt SQLLine használatához rendelkeznie kell a következő elemek:

* **A HDInsight HBase-fürtöt**. Szeretne létrehozni egyet, lásd: [Ismerkedés az Apache HBase hdinsightban](./apache-hbase-tutorial-get-started-linux.md).

Való csatlakozáskor HBase-fürtöt, a ZooKeeper virtuális gépek csatlakozni szeretne. Minden egyes HDInsight-fürt három ZooKeeper virtuális gépek rendelkezik.

**A ZooKeeper állomás nevét**

1. Keresse meg azt az Ambari megnyitásához **https://\<fürtnév\>. azurehdinsight.net**.
2. A bejelentkezéshez adja meg a HTTP (fürt) felhasználónevet és jelszót.
3. A bal oldali menüben válassza ki a **ZooKeeper**. Három **ZooKeeper Server** példányok szerepelnek.
4. Válasszon egyet a a **ZooKeeper Server** példányok. Az a **összegzés** ablaktáblában található a **állomásnév**. Hasonló *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**SQLLine használata**

1. Csatlakozzon a fürthöz SSH használatával. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Az SSH az alábbi parancsokkal SQLLine futtatásához:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. Egy HBase tábla létrehozásához, és néhány adat beviteléhez, futtassa a következő parancsokat:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

További információkért lásd: a [SQLLine manuális](http://sqlline.sourceforge.net/#manual) és [Phoenix nyelvtan](http://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, Apache Phoenix használata a Hdinsightban. További tudnivalókért lásd: ezek a cikkek:

* [HDInsight HBase áttekintése][hdinsight-hbase-overview].
  A HBase egy Apache, nyílt forráskódú NoSQL-adatbázis, amely véletlenszerű hozzáférést és erős konzisztenciát biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatok Hadoopra épülő.
* [Azure virtuális hálózat HBase-fürtök kiépítése][hdinsight-hbase-provision-vnet].
  A virtuális hálózati integráció HBase-fürtökkel telepíthetők az alkalmazások, mint az azonos virtuális hálózatban, az alkalmazások közvetlenül kommunikálhatnak a HBase.
* [A HDInsight HBase-replikálás konfigurálása](apache-hbase-replication.md). Megtudhatja, hogyan hozza létre a HBase-replikálás két Azure adatközpontjaiban.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md



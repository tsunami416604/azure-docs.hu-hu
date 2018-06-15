---
title: Külső metaadat-tároló - Azure HDInsight használata |} Microsoft Docs
description: Külső metaadatok tárolók használata a HDInsight-fürtök.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
tags: azure-portal
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 2eadee1a8695450e2219031ea1a65ee3624f26b5
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202560"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Külső metaadatait tárolja az Azure HDInsight használata

A Hive metaadattárhoz hdinsight Hadoop-architektúra alapvető része. A metaadattárhoz a központi séma tárház, amely a big Data típusú adatok hozzáférési eszközöket használhatja például a Spark, interaktív lekérdezés (LLAP), Presto vagy Pig használatával. A HDInsight az Azure SQL Database használja, mint a Hive metaadattárhoz.

![HDInsight Hive metaadat-tároló-architektúra](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

A HDInsight-fürtök egy metaadattárhoz állíthat be két módja van:

* [Alapértelmezett metaadattárhoz](#default-metastore)
* [Egyéni metaadattárhoz](#custom-metastore)

## <a name="default-metastore"></a>Alapértelmezett metaadattárhoz

Alapértelmezés szerint a HDInsight kiépítése egy metaadattárhoz minden fürt típusú. Helyette megadhatja egy egyéni metaadattárhoz. Az alapértelmezett metaadattárhoz az alábbiakat tartalmazza:
- További költség nélkül. A HDInsight kiépítése a metaadattárhoz minden fürt típusú meg semmilyen további költségek nélkül.
- Minden alapértelmezett metaadattárhoz a fürt életciklus részét képezi. Ha töröl egy fürtöt, hogy metaadattárhoz és metaadatok is törlődnek.
- Az alapértelmezett metaadattárhoz nem osztható meg más fürtökkel.
- Az alapértelmezett metaadattárhoz az alapszintű Azure SQL Database, amely 5 DTU (adatbázis-tranzakciós egység) maximális használja.
Az alapértelmezett metaadattárhoz tipikus felhasználási területe a viszonylag egyszerű munkaterheléseknek, amelyek nem igényelnek több fürt, és nem szükséges metaadatok megmaradjanak a fürt életciklus túl.


## <a name="custom-metastore"></a>Egyéni metaadattárhoz

HDInsight is támogatja az egyéni metastores, amely a termelési fürtök használata ajánlott:
- A saját Azure SQL Database a metaadattárhoz adja meg.
- A metaadattárhoz életciklusát nem kötődik fürtök életciklusa, létrehozhat és fürtök törlése metaadatok elvesztése nélkül. Például a Hive sémák metaadatok törölje és hozza létre újból a HDInsight-fürt után is megmaradnak.
- Egy egyéni metaadattárhoz lehetővé teszi, hogy metaadattárhoz több fürt és a fürt típusok csatolni. Például egyetlen metaadattárhoz is oszthatók meg interaktív lekérdezése, a Hive és a Spark-fürt hdinsightban.
- A metaadattárhoz (Azure SQL Database) alapján választja teljesítményszintjét költségét kell fizetnie.
- Igény szerint is növelheti a metaadattárhoz.


![HDInsight Hive metaadat-tároló használati eset](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

<!-- Image – Typical shared custom Metastore scenario in HDInsight (?) -->



### <a name="select-a-custom-metastore-during-cluster-creation"></a>Egy egyéni metaadattárhoz válassza ki a fürt létrehozása során

Egy korábban Azure SQL-adatbázis létrehozása a fürt a fürt létrehozása során mutathat, és konfigurálhatja az SQL-adatbázis, a fürt létrehozása után. Ez a beállítás van megadva a tárolási > Azure-portálon fürt létrehozásakor egy új Hadoop, Spark vagy interaktív Hive Metaadattárhoz beállításait.

![HDInsight Hive metaadatokat tároló Azure portál](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Azt is megteheti további fürtök egy egyéni metaadattárhoz az Azure-portálon vagy az Ambari konfigurációk (Hive > Speciális)

![HDInsight Hive metaadat-tároló Ambari](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Hive metaadattárhoz gyakorlati tanácsok

Az alábbiakban néhány általános HDInsight Hive metaadattárhoz gyakorlati tanácsokat:

- Egy egyéni metaadattárhoz, amikor csak lehetséges – Ez segít a különálló számítási erőforrások (a működő fürthöz) és a metaadatok (tárolja a metaadattárhoz) használja.
- Készítsen elő egy S2 réteg, mely 50 DTU és 250 GB tárhelyet biztosít. Ha megjelenik a szűk keresztmetszetek, legfeljebb az adatbázisban.
- Győződjön meg arról, hogy létrehozott egy HDInsight-fürt verziószáma nem megosztott HDInsight-fürt különböző verzióin átívelő metaadattárhoz. Hive különböző verziói különböző sémák használja. Például egy metaadattárhoz nem oszthatók Hive 1.2-es és a Hive 2.1 fürtökkel.
- Rendszeresen biztonsági másolatot az egyéni metaadattárhoz.
- Tartsa a metaadattárhoz és a HDInsight-fürt ugyanabban a régióban.
- A teljesítmény és rendelkezésre állási Azure SQL Database figyelése eszközökkel, például az Azure-portálon vagy az Azure Naplóelemzés metaadattárhoz figyelése.

## <a name="oozie-metastore"></a>Oozie Metaadattárhoz

Apache Oozie egy munkafolyamat-koordinációs rendszer, amely a Hadoop-feladatokat kezeli.  Oozie Apache MapReduce, Pig, a Hive és mások támogatja a Hadoop-feladatokat.  Oozie használ egy metaadattárhoz aktuális és a befejezett munkafolyamatok adatainak tárolásához. Oozie használata esetén a teljesítmény növelése érdekében az Azure SQL Database akár egy egyéni metaadattárhoz is használhatja. A metaadattárhoz a fürt törlése után is megadhatja Oozie feladat adatainak eléréséhez.

Az Oozie metaadattárhoz létrehozása az Azure SQL Database, lásd: [munkafolyamatokat használjon Oozie](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>További lépések

- [Hdinsight Hadoop, Spark, Kafka és több fürt beállítása](./hdinsight-hadoop-provision-linux-clusters.md)

---
title: Apache Hadoop architektúra – Azure HDInsight
description: Az Apache Hadoop tárolása és feldolgozása az Azure HDInsight-fürtökön.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/07/2020
ms.openlocfilehash: 3feacd94558ba275c81469827993aef106ae633c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162208"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Apache Hadoop-architektúra a HDInsightban

[Az Apache Hadoop](https://hadoop.apache.org/) két alapvető összetevőt tartalmaz: az [Apache Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) tárolót, és az [Apache Hadoop Another Resource Negotiator (YARN),](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) amely feldolgozást biztosít. A tárolási és feldolgozási lehetőségekkel a fürt képeslesz [a MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) programok futtatására a kívánt adatfeldolgozás elvégzéséhez.

> [!NOTE]  
> A HDFS általában nem központi lag a HDInsight-fürtön belüli tárolás biztosítása érdekében. Ehelyett a Hadoop-összetevők hdfs-kompatibilis felületi réteget használnak. A tényleges tárolási lehetőséget az Azure Storage vagy az Azure Data Lake Storage biztosítja. A Hadoop esetében a HDInsight-fürtön futó MapReduce feladatok úgy futnak, mintha hdfs lenne jelen, ezért nem igényelnek módosításokat a tárolási igényeik támogatásához. A HDInsight Hadoop ban a tárolás ki van szervezve, de a YARN feldolgozás továbbra is alapvető összetevő marad. További információ: [Bevezetés az Azure HDInsightba.](hadoop/apache-hadoop-introduction.md)

Ez a cikk bemutatja a YARN-t, és azt, hogy hogyan koordinálja a HDInsight-alkalmazások végrehajtását.

## <a name="apache-hadoop-yarn-basics"></a>Az Apache Hadoop YARN alapjai

A YARN szabályozza és vezényli az adatfeldolgozást a Hadoopban. A YARN két alapvető szolgáltatással rendelkezik, amelyek a fürt csomópontjain folyamatokként futnak:

* ResourceManager
* NodeManager

A ResourceManager fürtszámítási erőforrásokat biztosít az olyan alkalmazásoknak, mint a MapReduce feladatok. A ResourceManager ezeket az erőforrásokat tárolóként adja, ahol minden tároló a PROCESSZORmagok és a RAM-memória lefoglalásából áll. Ha egy fürtben rendelkezésre álló összes erőforrást kombinált, majd a magokat és a memóriát blokkokban osztotta el, az erőforrások minden blokkja egy tároló. A fürt minden csomópontja rendelkezik egy bizonyos számú tároló kapacitásával, ezért a fürt rendelkezik egy rögzített korláttal a rendelkezésre álló tárolók számára. A tárolóban lévő erőforrások lekötése konfigurálható.

Amikor egy MapReduce alkalmazás egy fürtön fut, a ResourceManager biztosítja az alkalmazás számára azokat a tárolókat, amelyekben végre kell hajtani. A ResourceManager nyomon követi a futó alkalmazások állapotát, a rendelkezésre álló fürtkapacitást, és nyomon követi az alkalmazásokat az erőforrások befejezése és felszabadítása közben.

A ResourceManager egy webkiszolgálói folyamatot is futtat, amely webes felhasználói felületet biztosít az alkalmazások állapotának figyelésére.

Amikor egy felhasználó elküldi a MapReduce alkalmazást a fürtön való futtatásra, az alkalmazás elküldésre kerül a ResourceManager-nek. A ResourceManager viszont lefoglal egy tárolót a rendelkezésre álló NodeManager-csomópontokon. A NodeManager-csomópontok azok, ahol az alkalmazás ténylegesen végrehajtja. Az első lefoglalt tároló egy speciális alkalmazást futtat, amelyet Az ApplicationMaster néven hoz. Ez az ApplicationMaster felelős a beküldött kérelem futtatásához szükséges további tárolók formájában történő beszerzéséért. Az ApplicationMaster megvizsgálja az alkalmazás szakaszait, például a térképfázist és a fázis csökkentésének, valamint a feldolgozáshoz szükséges adatok mennyiségének tényezőit. Az ApplicationMaster ezután az alkalmazás nevében kéri (*egyezteti)* a ResourceManager erőforrásait. A ResourceManager viszont a fürt nodemanager-ei től erőforrásokat ad az ApplicationMasternek az alkalmazás végrehajtásához.

A NodeManagers futtatja az alkalmazást kikészítő feladatokat, majd jelentést tesz azok előrehaladásáról és állapotáról az ApplicationMasternek. Az ApplicationMaster viszont jelenti az alkalmazás állapotát vissza a ResourceManager. A ResourceManager minden eredményt visszaad az ügyfélnek.

## <a name="yarn-on-hdinsight"></a>YARN a HDInsight-on

Minden HDInsight-fürttípus telepíti a YARN-t. A ResourceManager magas rendelkezésre állású egy elsődleges és másodlagos példány, amely a fürt első és második fő csomópontjain fut. Egyszerre csak a ResourceManager egyetlen példánya aktív. A NodeManager-példányok a fürt ben elérhető feldolgozócsomópontokon futnak.

![Apache YARN az Azure HDInsighton](./media/hdinsight-hadoop-architecture/apache-yarn-on-hdinsight.png)

## <a name="soft-delete"></a>Helyreállítható törlés

Ha vissza szeretne vonni egy fájl törlését a tárfiókból, olvassa el a következő témakört:

### <a name="azure-storage"></a>Azure Storage

* [Az Azure Storage-blobok helyreállítható törlése](../storage/blobs/storage-blob-soft-delete.md)
* [Blob törlésének törlése](https://docs.microsoft.com/rest/api/storageservices/undelete-blob)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

[Visszaállítás-AzDataLakeStoreDeleteditem](https://docs.microsoft.com/powershell/module/az.datalakestore/restore-azdatalakestoredeleteditem)

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

[Ismert problémák az Azure Data Lake Storage Gen2 szolgáltatással kapcsolatban](../storage/blobs/data-lake-storage-known-issues.md)

## <a name="trash-purging"></a>Szemét tisztítása

A `fs.trash.interval` **HDFS** > **Advanced core-site** tulajdonságának az `0` alapértelmezett értéken kell maradnia, mert nem szabad adatokat tárolni a helyi fájlrendszeren. Ez az érték nincs hatással a távtároló-fiókokra(WASB, ADLS GEN1, ABFS)

## <a name="next-steps"></a>További lépések

* [A MapReduce használata a HDInsight-alapú Apache Hadoopban](hadoop/hdinsight-use-mapreduce.md)
* [Bevezetés az Azure HDInsight ba](hadoop/apache-hadoop-introduction.md)

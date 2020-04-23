---
title: Az Azure Storage áttekintése a HDInsightban
description: Az Azure Storage áttekintése a HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 43d948a2a98407bacc212ddc6e065c67a105f332
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873380"
---
# <a name="azure-storage-overview-in-hdinsight"></a>Az Azure Storage áttekintése a HDInsightban

Az Azure Storage egy robusztus általános célú tárolási megoldás, amely zökkenőmentesen integrálható a HDInsightdal. A HDInsight egy blobtárolót használhat az Azure Storage-ben a fürt alapértelmezett fájlrendszereként. Egy HDFS-felületen keresztül a HDInsight összetevőinek teljes készlete közvetlenül működhet a blobként tárolt strukturált vagy strukturálatlan adatokon.

Azt javasoljuk, hogy külön tárolótárolókat használjon az alapértelmezett fürttároláshoz és az üzleti adatokhoz. A szétválasztás célja a HDInsight-naplók és az ideiglenes fájlok elkülönítése a saját üzleti adataitól. Azt is javasoljuk, hogy minden használat után a tárolási költségek csökkentése után az alapértelmezett blob tároló, amely tartalmazza az alkalmazás és a rendszer naplók. A tároló törlése előtt gondoskodjon a naplók begyűjtéséről.

Ha úgy dönt, hogy a tárfiókot a **tűzfalak és** a virtuális hálózatok korlátozásával biztosítja a **kiválasztott hálózatokon,** győződjön meg róla, hogy engedélyezi a **kivételt Megbízható Microsoft-szolgáltatások engedélyezése...**. A kivétel az, hogy a HDInsight hozzáférhessen a tárfiókhoz.

## <a name="hdinsight-storage-architecture"></a>HDInsight tároló-architektúra

Az alábbi ábra az Azure Storage HDInsight-architektúrájának absztrakt nézetét tartalmazza:

!["HDInsight tárolási architektúra"](./media/overview-azure-storage/storage-architecture.png "HDInsight tárolási architektúra")

A HDInsight hozzáférést nyújt a helyileg a számítási csomópontokhoz csatlakozó elosztott fájlrendszerhez. Ez a fájlrendszer a teljes URI használatával érhető el, például:

    hdfs://<namenodehost>/<path>

A HDInsight segítségével az Azure Storage-ban is hozzáférhet az adatokhoz. A szintaxis a következő:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Az Azure Storage-fiók HDInsight-fürtökkel való használatakor vegye figyelembe az alábbi alapelveket:

* **Fürthöz csatlakozó tárolók a tárfiókokban:** Mivel a fiók neve és kulcsa társítva van a fürttel a létrehozás során, teljes hozzáféréssel rendelkezik a tárolókban lévő összes blobhoz.

* **Nyilvános tárolók vagy nyilvános blobok a fürthöz nem kapcsolódó tárfiókokban:** Csak olvasási engedéllyel rendelkezik a tárolókban lévő blobokhoz.
  
  > [!NOTE]  
  > Nyilvános tárolók lehetővé teszi, hogy egy listát az összes blobok, amelyek a tárolóban elérhető, és a tároló metaadatok beszerzése. A nyilvános blobok esetén csak akkor érheti el a blobokat, ha ismeri a pontos URL-t. További információt a [Névtelen olvasási hozzáférés kezelése a tárolókhoz és blobokhoz című témakörben talál.](../storage/blobs/storage-manage-access-to-resources.md)

* **Fürthöz nem kapcsolódó tárolók ban:** A tárolókban lévő blobok csak akkor férhetnek hozzá a tárolókban, ha a WebHCat-feladatok elküldésekor definiálja a tárfiókot.

A létrehozási folyamat során meghatározott tárfiókok és a kulcsaik a %HADOOP_HOME%/conf/core-site.xml fájlban találhatók a fürtcsomópontokon. Alapértelmezés szerint a HDInsight a core-site.xml fájlban definiált tárfiókokat használja. Ezt a beállítást az [Apache Ambari segítségével módosíthatja.](./hdinsight-hadoop-manage-ambari.md)

Több WebHCat-feladat, beleértve az Apache Hive-t is. A MapReduce, az Apache Hadoop streaming és az Apache Pig pedig a tárfiókok és a metaadatok leírását hordozza. (Ez a szempont jelenleg a storage-fiókkal rendelkező Pig esetében igaz, metaadatokesetében azonban nem.) További információ: [HDInsight-fürt használata alternatív tárfiókokkal és metaáruházakkal.](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)

A blobok a strukturált és strukturálatlan adatokhoz használhatók. A Blob-tárolók kulcs-/értékpárokként tárolják az adatokat, és nem rendelkeznek címtárhierarchiával. A kulcsnév azonban tartalmazhat perjel karaktert ( / ), hogy úgy tűnjön, mintha egy fájl egy könyvtárstruktúrában lenne tárolva. Például egy blob kulcsa `input/log1.txt`lehet. Nincs `input` tényleges könyvtár, de a kulcsnevében lévő perjel miatt a kulcs fájlelérési útnak tűnik.

## <a name="benefits-of-azure-storage"></a>Az Azure Storage előnyei

A nem közös helyen található számítási fürtök és tárolási erőforrások implicit teljesítményköltségekkel járnak. Ezeket a költségeket mérséklődik a számítási fürtök az Azure-régión belüli tárfiók-erőforrások közelében történő létrehozásának módja. Ebben a régióban a számítási csomópontok hatékonyan hozzáférhetnek az adatokhoz az Azure Storage-on belüli nagy sebességű hálózaton keresztül.

Ha az adatokat az Azure Storage-ban tárolja a HDFS helyett, számos előnnyel jár:

* **Adatok újbóli használata és megosztása:** A HDFS-ben az adatok a számítási fürtön belül találhatóak. Csak a számítási fürtöt elérő alkalmazások használhatják az adatokat HDFS API-k használatával. Ezzel szemben az Azure Storage-ban tárolt adatok a HDFS API-kon vagy a Blob storage REST API-kon keresztül érhetők el. Ezen elrendezés miatt az alkalmazások (beleértve más HDInsight-fürtöket is) és eszközök nagyobb készlete használható az adatok előállításához és felhasználásához.

* **Adatarchiválás:** Ha az adatokat az Azure Storage tárolja, a hdinsight-fürtök számítási biztonságosan törölhetők felhasználói adatok elvesztése nélkül.

* **Adattárolási költség:** Az adatok dfs-ben való hosszú távú tárolása költségesebb, mint az adatok tárolása az Azure Storage-ban. Mivel a számítási fürt költsége magasabb, mint az Azure Storage költsége. Emellett mivel az adatokat nem kell újratölteni minden számítási fürt létrehozásakor, az adatbetöltési költségeket is megtakaríthatja.

* **Rugalmas kibővítés:** Bár a HDFS kibővített fájlrendszert biztosít, a léptéket a fürthöz létrehozott csomópontok száma határozza meg. A méretezés módosítása bonyolultabb lehet, mint az Azure Storage-ban automatikusan megkapandó rugalmas skálázási képességek.

* **Georeplikáció:** Az Azure Storage georeplikált lehet. Bár a georeplikáció földrajzi helyreállítást és adatredundanciát biztosít, a georeplikált helyre történő feladatátvétel súlyosan befolyásolja a teljesítményt, és további költségekkel járhat. Ezért körültekintően válassza a georeplikációt, és csak akkor, ha az adatok értéke indokolja a többletköltséget.

Bizonyos MapReduce feladatok és csomagok köztes eredményeket hozhatnak létre, amelyeket nem szeretne tárolni az Azure Storage-ban. Ebben az esetben választhatja az adatokat a helyi HDFS-ben tárolhatja. A HDInsight a DFS-t használja a Hive-feladatokban és más folyamatokban elért több köztes eredményhez.

> [!NOTE]  
> A legtöbb HDFS-parancs `ls` `copyFromLocal`(például , és `mkdir`) a várt módon működik az Azure Storage-ban. Csak a natív HDFS-implementációra jellemző parancsok (amelyet DFS-nek neveznek), például `fschk` és `dfsadmin`a , különböző viselkedést mutatnak az Azure Storage-ban.

## <a name="next-steps"></a>További lépések

* [Bevezetés az Azure Data Lake Storage Gen2 szolgáltatásba](../storage/blobs/data-lake-storage-introduction.md)
* [A Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md)
* [Az Azure Data Lake Storage Gen1 áttekintése](./overview-data-lake-storage-gen1.md)
---
title: 'Adatáttelepítés: Helyszíni Apache Hadoop az Azure HDInsightba'
description: Ismerje meg az adatok áttelepítésének gyakorlati tanácsait a helyszíni Hadoop-fürtök Azure HDInsightba való áttelepítéséhez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 41112359408497d84243ed9bb06f396acf008dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74666001"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Helyszíni Apache Hadoop-fürtök áttelepítése az Azure HDInsightba – ajánlott az adatok áttelepítése

Ez a cikk javaslatokat tartalmaz az Azure HDInsightba való adatáttelepítéshez. Ez egy olyan sorozat része, amely gyakorlati tanácsokkal segíti a helyszíni Apache Hadoop-rendszerek Azure HDInsightba való áttelepítését.

## <a name="migrate-on-premises-data-to-azure"></a>Helyszíni adatok áttelepítése az Azure-ba

Két fő lehetőség van az adatok helyszíni és azure-környezetbe való áttelepítésére:

* Adatátvitel hálózaton keresztül a TLS-sel
    * Interneten keresztül – Az adatok átvitele az Azure storage-ba egy rendszeres internet-kapcsolat on-én számos eszköz, mint például: Azure Storage Explorer, AzCopy, Azure Powershell és az Azure CLI. További információ: [Adatok áthelyezése az Azure Storage-ba és onnan.](../../storage/common/storage-moving-data.md)

    * Express Route – ExpressRoute egy Azure-szolgáltatás, amely lehetővé teszi, hogy privát kapcsolatokat hozzon létre a Microsoft adatközpontok és infrastruktúra, amely a helyszínen vagy egy helymegosztási létesítményben. Az ExpressRoute-kapcsolatok nem mennek át a nyilvános interneten, és nagyobb biztonságot, megbízhatóságot és sebességet kínálnak, mint az interneten keresztüli tipikus kapcsolatok. További információt az [ExpressRoute-kapcsolat létrehozása és módosítása](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)című témakörben talál.

    * Data Box online adatátvitel – A Data Box Edge és a Data Box Gateway olyan online adatátviteli termékek, amelyek hálózati tárolóátjáróként működnek a webhely és az Azure közötti adatok kezeléséhez. A Data Box Edge, egy helyszíni hálózati eszköz, adatokat továbbít az Azure-ba és az Azure-ból, és mesterséges intelligenciával (AI) képes peremhálózati számítási rendszerrel dolgozza fel az adatokat. A Data Box Gateway egy virtuális berendezés, amely rendelkezik tárolási átjárói képességekkel. További információ: [Azure Data Box Documentation - Online Transfer](https://docs.microsoft.com/azure/databox-online/).

* Szállítási adatok kapcsolat nélkül

    Data Box offline adatátvitel – Data Box, Data Box Disk, és a Data Box Heavy eszközök segítségével nagy mennyiségű adatot az Azure-ba, ha a hálózat nem egy lehetőség. Ezeket az offline adatátviteli eszközöket a szervezet és az Azure adatközpont között szállítjuk. Aes titkosítást használnak az átvitel során használt adatok védelmére, és a feltöltés utáni tisztítási folyamaton mennek keresztül, hogy töröljék az adatokat az eszközről. Az offline átviteli eszközökről az [Azure Data Box Dokumentáció – Offline átvitel](https://docs.microsoft.com/azure/databox/)című témakörben olvashat bővebben. A Hadoop-fürtök áttelepítéséről az Azure Data Box használata a [helyszíni HDFS-tárolóból az Azure Storage-ba című](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md)témakörben talál további információt.

Az alábbi táblázat az adatmennyiség és a hálózati sávszélesség alapján megközelítő időtartamú. Használjon Adat mezőt, ha az adatáttelepítés várhatóan több mint három hetet vesz igénybe.

|Adatmennyiség|Hálózati sávszélesség||||
|---|---|---|---|---|
|| **45 Mbps (T3)**|**100 Mbps**|**1 Gbps**|**10 Gbps**|
|1 TB|2 nap|1 nap| 2 óra|14 perc|
|10 TB|22 nap|10 nap|1 nap|2 óra|
|35 TB|76 nap|34 nap|3 nap|8 óra|
|80 TB|173 nap|78 nap|8 nap|11 óra|
|100 TB|216 nap|97 nap|10 nap|1 nap|
|200 TB|1 év|194 nap|19 nap|2 nap|
|500 TB|3 év|1 év|49 nap|5 nap|
|1 PB|6 év|3 év|97 nap|10 nap|
|2 PB|12 éves|5 év|194 nap|19 nap|

Az Azure-ban őshonos eszközök, például az Apache Hadoop DistCp, az Azure Data Factory és az AzureCp, adatok átvitelére használhatók a hálózaton keresztül. A harmadik féltől származó eszköz WANDisco is használható ugyanarra a célra. Az Apache Kafka Mirrormaker és az Apache Sqoop a helyszíni és az Azure-tárolórendszerekbe történő folyamatos adatátvitelhez használható.

## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Teljesítménybeli szempontok az Apache Hadoop DistCp használatakor

A DistCp egy Apache projekt, amely MapReduce Map feladatot használ az adatok átviteléhez, a hibák kezeléséhez és a hibák helyreállításához. Minden egyes leképezési feladathoz hozzárendeli a forrásfájlok listáját. A Leképezés i doea ezután az összes hozzárendelt fájlt átmásolja a célhelyre. Számos technika javíthatja a DistCp teljesítményét.

### <a name="increase-the-number-of-mappers"></a>A leképezők számának növelése

A DistCp úgy próbál meg leképezési feladatokat létrehozni, hogy mindegyik nagyjából ugyanannyi bájtot másoljon. Alapértelmezés szerint a DistCp-feladatok 20 leképezőt használnak. Ha több leképezőt használ a Distcp-hez (a parancssorban az "m" paraméterrel) növeli a párhuzamosságot az adatátviteli folyamat során, és csökkenti az adatátvitel hosszát. Azonban két dolgot kell figyelembe venni, miközben növeli a Mappers:

* A DistCp legalacsonyabb részletessége egyetlen fájl. A forrásfájlok számánál több leképező megadása nem segít, és a rendelkezésre álló fürterőforrásokat pazarolja.

* A leképezők számának meghatározásához vegye figyelembe a fürtön rendelkezésre álló fonalmemóriát. Minden térképfeladat fonaltárolóként indul el. Feltételezve, hogy nincs más nagy számítási feladatok futnak a fürtön, a mappers száma a következő \* képlettel határozható meg: m = (az egyes munkavégző csomópontok YARN-memóriájának száma az egyes munkavégző csomópontokhoz) / YARN tároló mérete. Ha azonban más alkalmazások is használnak memóriát, akkor úgy dönt, hogy csak a YARN memória egy részét használja a DistCp-feladatokhoz.

### <a name="use-more-than-one-distcp-job"></a>Egynél több DistCp-feladat használata

Ha az áthelyezendő adatkészlet mérete 1 TB-nál nagyobb, használjon egynél több DistCp-feladatot. Egynél több feladat használata korlátozza a hibák hatását. Ha bármelyik feladat meghibásodik, csak az adott feladatot kell újraindítani, nem pedig az összes feladatot.

### <a name="consider-splitting-files"></a>Fájlok felosztásának megfontolása

Ha van egy kis számú nagy fájlokat, majd érdemes felosztása őket 256 MB-os fájl darabokat, hogy minél több potenciális egyidejűség több Mappers.

### <a name="use-the-strategy-command-line-parameter"></a>A "stratégia" parancssori paraméter használata

Fontolja `strategy = dynamic` meg a paraméter használatát a parancssorban. A paraméter alapértelmezett `strategy` értéke, `uniform size`ebben az esetben minden térkép nagyjából azonos számú bájtot másol. Ha ez a paraméter `dynamic`változik, a listafájl több "darabfájlra" oszlik. Az adattömbfájlok száma a térképek többszöröse. Minden leképezési feladat hoz létre egy adattömb-fájlokat. Miután egy adattömbösszes elérési útja feldolgozásra kerül, az aktuális adattömb törlődik, és egy új adattömb beszerzése történik. A folyamat addig folytatódik, amíg nem áll rendelkezésre több adattömb. Ez a "dinamikus" megközelítés lehetővé teszi, hogy a gyorsabb térképfeladatok több útvonalat használjanak fel, mint a lassabbak, így felgyorsítva a DistCp-feladatot.

### <a name="increase-the-number-of-threads"></a>A szálak számának növelése

Nézze meg, `-numListstatusThreads` hogy a paraméter növelése javítja-e a teljesítményt. Ez a paraméter határozza meg a fájlok felsorolásához használandó szálak számát, és a 40 a maximális érték.

### <a name="use-the-output-committer-algorithm"></a>A kimeneti véglegesítő algoritmusának használata

Nézd meg, `-Dmapreduce.fileoutputcommitter.algorithm.version=2` hogy a paraméter átadása javítja-e a DistCp teljesítményét. Ez a kimeneti véglegesítő algoritmus optimalizálások körül írásban kimeneti fájlokat a cél. A következő parancs egy példa, amely bemutatja a különböző paraméterek használatát:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Metaadatok áttelepítése

### <a name="apache-hive"></a>Apache Hive

A struktúra metastore áttelepíthető akár a parancsfájlok használatával, vagy a DB replikáció használatával.

#### <a name="hive-metastore-migration-using-scripts"></a>Hive metatár-áttelepítés parancsfájlok használatával

1. A Hive DDL-ek létrehozása a helyszíni Hive metastore.Generate the Hive DDLs from on premises Hive metastore. Ez a lépés egy [burkoló bash parancsfájl](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md)használatával végezhető el.
1. A generált DDL szerkesztésével cserélje le a HDFS url-t WASB/ADLS/ABFS URL-címekre.
1. Futtassa a frissített DDL-t a metastore-on a HDInsight-fürtből.
1. Győződjön meg arról, hogy a Hive metastore verziója kompatibilis a helyszíni és a felhőbeli.

#### <a name="hive-metastore-migration-using-db-replication"></a>Hive metatár-áttelepítés a db replikáció használatával

- Állítsa be az adatbázis-replikációt a helyszíni Hive metastore DB és a HDInsight metastore DB között.
- A "Hive MetaTool" segítségével cserélje le a HDFS url-címét WASB/ADLS/ABFS URL-címekre, például:

    ```bash
    ./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
    ```

### <a name="apache-ranger"></a>Apache Ranger

- Helyszíni Ranger-házirendek exportálása XML-fájlokba.
- Alakítson át a helyszínen megadott HDFS-alapú elérési utakon WASB/ADLS-vé egy olyan eszközzel, mint az XSLT.
- Importálja a házirendeket a HDInsight-on futó Rangerbe.

## <a name="next-steps"></a>További lépések

Olvassa el a sorozat következő cikkét:

- [Biztonsági és DevOps gyakorlati tanácsok a helyszíni Azure HDInsight Hadoop-áttelepítéshez](apache-hadoop-on-premises-migration-best-practices-security-devops.md)

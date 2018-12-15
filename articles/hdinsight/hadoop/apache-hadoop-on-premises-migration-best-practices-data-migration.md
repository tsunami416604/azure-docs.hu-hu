---
title: A helyszíni Apache Hadoop-fürtök áttelepítése az Azure HDInsight - adatok áttelepítése – ajánlott eljárások
description: Ismerje meg az adatok áttelepítési ajánlott eljárások az áttelepítése a helyszíni Hadoop-fürtöket az Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 5d0259726a45346f1e9b891cb235531d6c24d4a2
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53433423"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>A helyszíni Apache Hadoop-fürtök áttelepítése az Azure HDInsight - adatok áttelepítése – ajánlott eljárások

Ez a cikk az Azure HDInsight az adatáttelepítés javaslatokat biztosít. Ez azt egy olyan sorozat részét, amely ajánlott eljárásokat, amelyek segítik az Azure HDInsight áttelepítése a helyszíni Apache Hadoop-rendszerekhez biztosít.

## <a name="migrate-on-premises-data-to-azure"></a>A helyszíni adatok migrálása az Azure-bA

Két fő lehetőség át a helyszíni adatok Azure-környezet van:

1.  Adatok átvitele a TLS-hálózaton keresztül
    1. Interneten – keresztül adatokat lehet vinni az Azure storage használatával több eszközt például rendszeres internetkapcsolaton keresztül: Az Azure Storage Explorer, az AzCopy, az Azure Powershell és az Azure parancssori felület.  Lásd: [adatok áthelyezése az Azure Storage szolgáltatásba vagy onnan](../../storage/common/storage-moving-data.md) további információt.
    2. Express Route - ExpressRoute egy Azure-szolgáltatás, amely lehetővé teszi, hogy között Microsoft-adatközpontok és infrastruktúra, amely a helyszínen vagy egy közös elhelyezési létesítményből csatlakozó privát kapcsolatok hozhatók létre. Az ExpressRoute-kapcsolatok nem a nyilvános interneten keresztül, és magasabb szintű biztonságra, megbízhatóságra és a kisebb a késésük, mint a szokásos internetkapcsolatoknál megbízhatóbbak kínálnak az interneten keresztül. További információkért lásd: [létrehozása és módosítása egy ExpressRoute-kapcsolatcsoport](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    1. Data Box online adatátvitel – Data Box Edge és a Data Box-átjáró proxyként tárolási átjárók kezelése a hely és az Azure közötti hálózati online adatok átvitel termékek. A Data Box Edge egy helyszíni hálózati eszköz, amely az adatokat az Azure és a helyszín között helyezi át, és az adatok feldolgozásához mesterséges intelligenciát használó peremhálózati számítási megoldást használ. A Data Box Gateway egy tárolóátjáró képességgel rendelkező virtuális berendezés. További információkért lásd: [Azure Data Box dokumentációja – Online átviteli](https://docs.microsoft.com/azure/databox-online/).
1.  Szállítási adatok offline állapotban
    1. Importálási / exportálási szolgáltatás – fizikai lemezek küldhet az Azure-ba, és Önnek töltődnek fel. További információkért lásd: [Mi az Azure Import/Export szolgáltatást?](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).
    1. Adatok offline adatátvitel – Data Box, Data Box-lemezek mezőbe, és a Data Box nehéz eszközök segítséget nagy mennyiségű adat átvitele az Azure-ba, amikor a hálózat nincs lehetőség. Ezeket az offline adatátviteli eszközöket az Ön cége és az Azure-adatközpontok között szállítjuk. Az eszközök AES-titkosítást használnak, amelyek a szállítás közben védik az adatait, a feltöltés után pedig alapos megtisztítási folyamaton esnek át, amelynek során minden adata törölve lesz az eszközről. További információkért lásd: [Azure Data Box dokumentációja – Offline Transfer](https://docs.microsoft.com/azure/databox/).

Az alábbi táblázat a hozzávetőleges adatok átvitel időtartama az adatok mennyisége és a hálózati sávszélesség alapján van. A Data box használja, ha az adatok migrálása várhatóan több mint három hetet igénybe vehet.

|**Adatok mennyisége**|**Hálózati sávszélesség**|||
|---|---|---|---|
|| **45 MB/s (T3)**|**100 MB/s**|**1 GB/s**|**10 GB/s**
|1 TB|2 nap|1 nap| 2 óra|14 perc|
|10 TB|22 nap|10 nap|1 nap|2 óra|
|35 TB|76 nap|34 nap|3 nap|8 óra|
|80 TB|173 nap|78 nap|8 nap|19 órát|
|100 TB|216 nap|97 nap|10 nap|1 nap|
|200 TB|1 év|194 nap|19 nap|2 nap|
|500 TB|3 év|1 év|49 nap|5 nap|
|1 PB|6 év|3 év|97 nap|10 nap|
|2 PB|12 éve|5 év|194 nap|19 nap|

Az Azure-ba, például az Apache Hadoop DistCp, az Azure Data Factory és a AzureCp, natív eszközök segítségével adatokat átvitel a hálózaton keresztül. A külső eszköz, a WANDisco ugyanerre a célra is használható. Az Apache Kafka Mirrormakerrel és az Apache sqoop használatával folyamatos adatátvitel a helyszínről az Azure storage-rendszerekhez is használható.


## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Teljesítménnyel kapcsolatos szempontok, ha az Apache Hadoop a DistCp használata


A DistCp egy Apache-projecttel, amely egy térkép MapReduce feladatot használ adatátvitelt, hibák kezelésére, és ezeket a hibákat helyreállítása. Forrásfájljainak listáját rendel térkép feladatokon. A térkép feladat majd átmásolja a hozzárendelt fájlok mindegyikét a célhelyre. Nincsenek a különböző módszereket vetnek javíthatja a DistCp teljesítményét.

### <a name="increase-the-number-of-mappers"></a>Ha több leképező

A DistCp megpróbálja létrehozni a térkép feladatokat, hogy mindegyik másolja nagyjából azonos számú bájt. Alapértelmezés szerint a DistCp feladatok 20 leképező használják. A Distcp használatával több leképező (az a vagyok "paraméter a parancssorban) párhuzamosság növeli az adatok átvitel során, és csökkenti az adatok átvitele a hosszát. Azonban két dolgot leképező számának növelése során érdemes figyelembe venni:

1. A DistCp a legalacsonyabb Granularitás egyetlen fájlt. Forrásfájljainak számánál több leképező számos megadásával nem segít, és lesz a rendelkezésre álló erőforrások pazarlom.
1. Fontolja meg a rendelkezésre álló Yarn memória leképező számának meghatározásához a fürtön. Térkép feladatokon Yarn tárolójaként indították el. Feltételezve, hogy nincs más terhelés a fürtön futnak, leképező számát határozza meg a következő képletet: m = (munkavégző csomópontok száma \* egyes feldolgozó csomópontok YARN memória) / YARN-tároló mérete. Azonban ha más alkalmazásokat használ a memória, majd válassza a DistCp-feladatok YARN memória egy részét csak használandó.

### <a name="use-more-than-one-distcp-job"></a>A DistCp egynél több feladat használja

Ha az adatkészlet áthelyezésének mérete nagyobb, mint 1 TB-os, a DistCp egynél több feladat használja. Több feladat használatával korlátozza a hibák. Ha minden olyan feladat sikertelen, csak indítsa újra az adott feladathoz helyett összes feladat kell.

### <a name="consider-splitting-files"></a>Fontolja meg a fájlok felosztása

Ha kevés a nagy méretű fájlok, megfontolása letárolni adattömbökbe beolvasni a több leképező több lehetséges egyidejűséget 256 MB-os fájlt.

### <a name="use-the-strategy-command-line-parameter"></a>"Stratégia" parancssori paraméter

Fontolja meg `strategy = dynamic` paraméter a parancssorban. Az alapértelmezett érték a `strategy` paraméter `uniform size`, ebben az esetben minden leképezés másolja nagyjából azonos számú bájt. Ha ez a paraméter módosul `dynamic`, a listaelem fájl van felosztva, amelyek több "adattömbök-fájlok". Adatrészlet-fájlok száma a maps számú többszöröse. Térkép feladatokon hozzá van rendelve egy adattömb-fájlt. Követően dolgozzák fel a rendszer az összes elérési utat, a aktuální Blok dat törlődik, és egy új adattömb igényelve. A folyamat folytatódik, amíg nincs több adattömbök nem érhető el. Ez a "dinamikus" megközelítés lehetővé teszi, hogy a gyorsabb térkép-feladatok, mint a lassabb is, így felgyorsítva a teljes feladat a DistCp további elérési utak felhasználásához.

### <a name="increase-the-number-of-threads"></a>Növelje a szálak száma

Ha növelése a `-numListstatusThreads` paraméter javítja a teljesítményt. Ezzel a paraméterrel állítható fájl listaelem létrehozásához használni kívánt szálak számát, és 40 a maximális értéket.

### <a name="use-the-output-committer-algorithm"></a>A kimeneti committer algoritmus használata

Ha a paraméter átadásával `-Dmapreduce.fileoutputcommitter.algorithm.version=2` növeli a DistCp teljesítményt. A kimeneti committer algoritmus rendelkezik optimalizálások körül kimeneti fájlok írása a célhelyre. A következő példa bemutatja a különböző paraméterek használatát a következő parancsot:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Metaadatok migrálása

### <a name="apache-hive"></a>Apache Hive

A hive-metaadattár a parancsprogramok használatával vagy az adatbázis-replikálás használatával telepíthetők át.

#### <a name="hive-metastore-migration-using-scripts"></a>Hive-metaadattár áttelepítési parancsfájlok használata

1. Hozza létre a Hive-DDLs helyszíni Hive-metaadattár. Ebben a lépésben végezhető használatával egy [burkoló bash-szkript](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md).
1. A létrehozott DDL HDFS URL-címet lecseréli a WASB vagy ADLS/ABFS URL-címek szerkesztése.
1. Futtassa a frissített DDL a metaadattár a HDInsight-fürtből.
1. Győződjön meg arról, hogy a Hive-metaadattár verziója kompatibilis a helyszíni és a felhő között.

#### <a name="hive-metastore-migration-using-db-replication"></a>Hive-metaadattár migrálás, adatbázis-replikációval

- Adatbázis-replikáció beállítása a helyszíni Hive-metaadattár DB és a HDInsight-metaadattár DB között.
- Használja a "Hive MetaTool" HDFS URL-címet lecseréli WASB vagy ADLS/ABFS URL-címek, például:

```bash
./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
```

### <a name="apache-ranger"></a>Apache Ranger

- Exportálja a helyi Ranger-házirendek xml-fájlok.
- Alakítsa át a helyszíni egyedi HDFS-alapú elérési utak a WASB vagy ADLS XSLT hasonló eszköz használatával.
- A HDInsight futó Ranger be házirendek importálása.

## <a name="next-steps"></a>További lépések

Olvassa el az oktatóanyag-sorozatban a következő cikkben:

- [Gyakorlati tanácsok az Azure HDInsight Hadoop-áttelepítési helyszíni biztonsági és DevOps](apache-hadoop-on-premises-migration-best-practices-security-devops.md)
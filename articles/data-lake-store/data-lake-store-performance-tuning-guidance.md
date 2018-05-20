---
title: Az Azure Data Lake Store teljesítményhangolás irányelvek |} Microsoft Docs
description: Az Azure Data Lake Store teljesítményhangolás irányelvek
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: cgronlun
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: 29b662aa2f30083b444483554a78d53f0d05cb7f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="tuning-azure-data-lake-store-for-performance"></a>Azure Data Lake Store teljesítményének hangolása

Data Lake Store nagy átviteli intenzív i/o-analitikához és adatátvitelt jelölik a támogatja.  Az Azure Data Lake Store használja az összes elérhető átviteli sebesség – olvassa el, vagy másodpercenként írt adatok mennyisége – fontos, hogy a legjobb teljesítmény eléréséhez.  Tetszőleges számú olvasási műveletek elvégzésével érhető el, és a lehető párhuzamosan írja.

![Data Lake Store-teljesítmény](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Azure Data Lake Store méretezhető, adja meg a szükséges átviteli összes analytics forgatókönyvhöz. Alapértelmezés szerint az Azure Data Lake Store-fiók automatikusan használati esetek széles körét igényeinek elegendő teljesítményt nyújt. Az olyan esetekben, ahol az ügyfelek tapasztal-e az alapértelmezett határérték a ADLS-fiók beállítható úgy, hogy adja meg a további átviteli megszüntetéséhez forduljon a Microsoft támogatási szolgálatához.

## <a name="data-ingestion"></a>Adatfeldolgozás

Az ADLS forrásrendszer adatok bevitele, esetén fontos figyelembe venni, a forrás hardver, a forrás hálózati hardver és a hálózati kapcsolatot ADLS szűk is lehet.  

![Data Lake Store-teljesítmény](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

Fontos győződjön meg arról, hogy ezek a tényezők nincs hatással az adatátvitelt jelölik.

### <a name="source-hardware"></a>Forrás hardver

Függetlenül attól, hogy a helyszíni gépeket vagy virtuális gépek Azure-ban, ki kell választania a megfelelő hardver gondosan. A forrás lemez hardverekhez inkább HDD SSD-k, és válassza ki a gyorsabb forgórészek lemez hardver. A forrás hálózati hardverek használja a leggyorsabb hálózati adapterek lehetséges.  Azure-ban Azure D14 virtuális gépekhez, amely rendelkezik a megfelelő hatékony lemez és a hálózati hardver ajánlott.

### <a name="network-connectivity-to-azure-data-lake-store"></a>Azure Data Lake Store-hálózati kapcsolat

A hálózati kapcsolat a forrásadatok és az Azure Data Lake store között szűk néha lehetnek. A forrásadatok esetén a helyszíni, érdemes lehet egy dedikált hivatkozás a [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Ha a forrásadatok az Azure-ban, a teljesítmény lesz ajánlott, amikor az adatok és a Data Lake Store azonos Azure régióban.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Maximális párhuzamos folyamatkezelést biztosítja az adatfeldolgozást eszközök konfigurálása

Amennyiben a forrás hardver foglalkoztak, és a hálózati kapcsolat szűk keresztmetszetek a fenti, készen áll az adatfeldolgozást eszközök konfigurálásához. A következő táblázat összefoglalja a számos népszerű adatfeldolgozást eszköz beállításait, és biztosít részletes teljesítményhangolás cikkek őket.  A forgatókönyvben használandó eszköztől kapcsolatos további információkért látogasson el a [cikk](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios).

| Eszköz               | Beállítások     | További részletekért                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount |  [Hivatkozás](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell#performance-guidance-while-using-powershell) |
| AdlCopy    | Azure Data Lake Analytics-egységek  |   [Hivatkozás](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| Ból a DistCp            | -m (eseményleképező)   | [Hivatkozás](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Hivatkozás](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS.Azure.Block.size, -m (eseményleképező)    |   [Hivatkozás](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Az adatkészlet struktúra

Data Lake Store, a fájl mérete a tárolt adatok fájlok számát, és mappastruktúrát hatással a teljesítményt.  Az alábbi szakasz ismerteti ezek a területek a bevált gyakorlat.  

### <a name="file-size"></a>Fájlméret

Például a HDInsight és az Azure Data Lake Analytics analytics motorok általában egy fájl terhelés kell.  Ha az adatok annyi kis fájlok tárolásához, ez negatívan befolyásoló kódokat is teljesítményét.  

Az adatok általában rendezze nagyobb méretű fájlok, a jobb teljesítmény érdekében.  A szokásos megoldás, mint 256MB vagy annál nagyobb fájlok data készletek rendszerezését. Bizonyos esetekben – például képeket és a bináris adatokat nincs lehetőség a párhuzamosan dolgozza fel őket.  Ezekben az esetekben javasoljuk, hogy az egyes fájlok a 2GB.

Egyes esetekben adatok folyamatok ellenőrzése alatt tartja a nyers adatait, amely a sok kisméretű fájlok korlátozott.  Javasoljuk, hogy az alsóbb rétegbeli alkalmazásoknak használandó nagyobb fájlokat generál egy "főzése" folyamat.

### <a name="organizing-time-series-data-in-folders"></a>A mappákban Idősoros adatok rendezése

A Hive és ADLA munkaterhelésekhez partíció törlése a idősorozat adatok segítségével néhány lekérdezést, olvassa el az adatokat, ami javítja a teljesítményt, csak egy részét.    

Ezek a folyamatok, amelyek betöltik az idősorozat adatokat, gyakran a fájlok és mappák nagyon strukturált elnevezési fájljaik helyezze el. Alább látható adatok, amelyek dátum szerint szerkezete nagyon gyakori példa van:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Figyelje meg, hogy a datetime adatokat mappák és a fájlnév megjelenik-e.

Dátum és idő a következő nem egy közös mintát

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Ebben az esetben a választott ellenőrizze a mappa, és fájl szervezet kell optimalizálhatja a nagyobb méretűek és ésszerű száma minden mappában található fájlokat.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>A HDInsight Hadoop és a Spark munkaterhelések egy feladat intenzív i/o optimalizálása

Feladatok tartoznak, a következő három kategóriába oszthatók:

* **CPU-intenzív.**  Ezek a feladatok rendelkezik hosszú kiszámításához szükséges idő minimális i/o-idő feltüntetésével.  Ilyen például a gépi tanulási és természetes nyelvű feladatokat dolgoz fel.  
* **Memóriaigényes.**  Ezek a feladatok nagy mennyiségű memóriát használja.  Például PageRank és a valós idejű elemzési feladatot.  
* **I/o-igényes.**  Ezeket a feladatokat az idő során i/o legmagasabbak.  Ilyenek például egy másolási feladat, amely csak olvasási és írási műveletet.  Ezenkívül például az előkészítő feladatok nagy mennyiségű adat, hajt végre bizonyos adatok átalakítása, és ezután ír vissza adatokat a tárolóban.  

A következő útmutatást tulajdonság csak i/o-igényes feladatok vonatkozik.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>A HDInsight-fürtök általános szempontok

* **HDInsight-verziókról.** A legjobb teljesítmény érdekében a HDInsight a legújabb kiadás további használatára.
* **Régiók.** Helyezze el a Data Lake Store és a HDInsight-fürt ugyanabban a régióban.  

HDInsight-fürtök két átjárócsomópontokkal és az egyes feldolgozó csomópontok állnak. Egyes feldolgozó csomópontok biztosít egy adott magok száma és memória, a VM-típus alapján határozza meg.  A feladat futtatásakor a YARN az erőforrás-egyeztető által lefoglalt a rendelkezésre álló memória és -magok tárolók létrehozása.  Minden egyes tároló fut a feladat végrehajtásához szükséges feladatok.  Tárolók feladatok gyorsan feldolgozása párhuzamosan futnak. Ezért a teljesítmény növelése a lehető legtöbb párhuzamos tárolók futtatásával.

Az alábbi három réteg belül is kell beállítani, hogy növelje a tárolók száma, és használja az összes elérhető átviteli sebesség HDInsight-fürtöt.  

* **Fizikai réteg**
* **YARN réteg**
* **Munkaterhelés réteg**

### <a name="physical-layer"></a>Fizikai réteg

**Futtassa a fürt további csomópontokat és/vagy nagyobb méretű virtuális gépeken.**  Automatikusan nagyobb fürt lehetővé teszi több YARN tároló az alábbi képen látható módon futtatásához.

![Data Lake Store-teljesítmény](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Használja a virtuális gépek nagyobb hálózati sávszélességet.**  Hálózati sávszélességet a szűk keresztmetszetek lehet, ha kevesebb mint a Data Lake Store a sávszélesség.  Különböző virtuális gépek lesz a hálózati sávszélesség mérete változó.  Válasszon, amely rendelkezik a legnagyobb sávszélesség VM-típust.

### <a name="yarn-layer"></a>YARN réteg

**Kisebb YARN tárolók használata.**  Az egyes YARN tárolót, hogy hozzon létre több tároló erőforrások akkora méretének csökkentésére.

![Data Lake Store-teljesítmény](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

Attól függően, hogy a munkaterhelés mindig lesz egy minimális YARN tároló mérete szükséges. Ha túl kicsi a tároló válassza ki, a feladatok a kevés memória problémák fog futni. YARN tárolók általában nem 1GB-nál kisebbnek kell lennie. Esetében gyakori, hogy 3 GB-os YARN tárolók. Bizonyos munkaterhelések esetén szükség lehet a nagyobb YARN tárolók.  

**Növelje a YARN tárolóban található magok.**  Növelje a párhuzamos minden egyes tárolóban futó feladatok számának növelése érdekében minden egyes tároló lefoglalt magok száma.  Ez a módszer alkalmazások, például a Spark, amelyek több tevékenységek maximális száma tároló.  Alkalmazások, például Hive egyetlen szálon futtatja az egyes tárolókban érdemes további tárolóban található magok helyett a több tároló van.

### <a name="workload-layer"></a>Munkaterhelés réteg

**Az összes rendelkezésre álló tárolók használata.**  Adjon meg feladatokat egyenlő vagy nagyobb, mint a rendelkezésre álló tárolók, hogy az összes erőforrás ki van használva.

![Data Lake Store-teljesítmény](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**A sikertelen feladatokat is költséges.** Ha minden tevékenység rendelkezik egy nagy mennyiségű adat feldolgozására, feladat sikertelenségét egy drága újrapróbálkozási okoz.  Ezért érdemes további feladatokat, amelyek dolgozza fel a kisebb mennyiségű adatot létrehozásához.

A fenti általános útmutatást, felül minden alkalmazás rendelkezik az adott alkalmazás hangolására használható különböző paramétereket. Az alábbi táblázat mutatja a paraméterek és a hivatkozások teljesítményének hangolása minden alkalmazáshoz használatába.

| Számítási feladat               | Paraméter segítségével állítsa be a feladatok                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [A Spark on HDInisight](data-lake-store-performance-tuning-spark.md)       | <ul><li>NUM-végrehajtója</li><li>Végrehajtó memória</li><li>Végrehajtó-magok</li></ul> |
| [A HDInsight Hive](data-lake-store-performance-tuning-hive.md)    | <ul><li>Hive.tez.Container.size</li></ul>         |
| [A HDInsight MapReduce](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.Map.Memory</li><li>Mapreduce.job.Maps</li><li>Mapreduce.reduce.Memory</li><li>Mapreduce.job.reduces</li></ul> |
| [A HDInsight alatt futó Storm](data-lake-store-performance-tuning-storm.md)| <ul><li>Munkavégző folyamatok száma</li><li>Spout végrehajtó példányok száma</li><li>Bolt végrehajtó példányok száma </li><li>Spout feladatok száma</li><li>Bolt feladatok száma</li></ul>|

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake Store áttekintése](data-lake-store-overview.md)
* [Ismerkedés az Azure Data Lake Analytics szolgáltatással](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

---
title: Az Azure Data Lake Storage Gen2 teljesítmény-finomhangolási útmutató |} A Microsoft Docs
description: Az Azure Data Lake Storage Gen2 teljesítmény-finomhangolási útmutató
services: storage
author: swums
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: stewu
ms.openlocfilehash: f175360586428b57d1ff10e3529ae9e3283399e8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117054"
---
# <a name="tuning-azure-data-lake-storage-gen2-for-performance"></a>Az Azure Data Lake Storage Gen2 teljesítmény hangolása

Az Azure Data Lake Storage Gen2 támogatja a nagy átviteli sebességű i/o nagy számításigényű analytics és az adatok áthelyezését.  A Data Lake Storage Gen2 használatával az összes elérhető átviteli sebesség – olvassa el, vagy másodpercenként írt adatok mennyisége – fontos, hogy a legjobb teljesítményt.  Ez tetszőleges számú olvasási elvégzésével érhető el, és írja a lehető párhuzamosan.

![Data Lake Storage Gen2-teljesítmény](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen2 adja meg a szükséges átviteli összes analytics-forgatókönyv méretezheti. Alapértelmezés szerint egy Data Lake Storage Gen2-fiók automatikusan elegendő átviteli sebességet biztosít, ha alkalmazási helyzetek széles körét. Olyan esetekben, ahol tapasztalja a vevők az alapértelmezett korlát, a Data Lake Storage Gen2 fiók beállítható úgy, hogy adja meg a kapacitás növelése érdekében kapcsolatba lép a [Azure-támogatási](https://azure.microsoft.com/support/faq/).

## <a name="data-ingestion"></a>Adatfeldolgozás

Data Lake Storage Gen2-re a forrásrendszerben érkező adatok feldolgozása, esetén fontos figyelembe venni, a forrás hardver, a forrás hálózati hardverek, valamint a hálózati kapcsolat Data Lake Storage Gen2-re, hogy a szűk keresztmetszetet.  

![Data Lake Storage Gen2-teljesítmény](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

Fontos győződjön meg arról, hogy az adatok áthelyezése nincs hatással a tényezők.

### <a name="source-hardware"></a>Forrás hardver

Akár a helyszíni gépek és virtuális gépek az Azure-ban az, körültekintően jelölje ki a megfelelő hardvert. A forrás lemez hardverekhez SSD-k inkább HDD-k, és válassza ki a gyorsabb forgórészek lemez hardver. Forrás hálózati hardverek használja a leggyorsabb hálózati adapterek lehetséges.  Az Azure-ban javasoljuk, hogy Azure D14 virtuális gépek, amelyek megfelelő teljesítményű lemez és hálózatkezelési hardverek.

### <a name="network-connectivity-to-data-lake-storage-gen2"></a>Hálózati kapcsolat a Data Lake Storage Gen2-re

A hálózati kapcsolat a forrásadatok és a Data Lake Storage Gen2 néha lehet a szűk keresztmetszetet. Ha a forrásadatokat a helyszínen, fontolja meg dedikált hivatkozást [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Ha a forrásadatok az Azure-ban, a teljesítmény lesz ajánlott ugyanabban a régióban az Azure Data Lake Storage Gen2 fiókként az adatok esetén.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Maximális párhuzamos feldolgozás az adatfeldolgozási eszközök konfigurálása

A forrás hardver foglalkoztak, és a hálózati kapcsolat szűk fenti, készen áll az adatfeldolgozási eszközök konfigurálásához. Az alábbi táblázat foglalja össze a legfontosabb számos népszerű adatfeldolgozási eszközök beállításainak és biztosít részletes teljesítmény-finomhangolási a cikkek a számukra.  Melyik eszközt használja a forgatókönyvben kapcsolatos további tudnivalókért keresse fel a [cikk](data-lake-storage-data-scenarios.md).

| Eszköz               | Beállítások     | További részletek                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| DistCp            | -m (mapper)   | [Hivatkozás](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Hivatkozás](../../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (mapper)    |   [Hivatkozás](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Az adatkészlet struktúra

Data Lake Storage Gen2, a fájl mérete a tárolt adatok a fájlok és a gyökérmappa-szerkezetében hatással vannak a teljesítményre.  Az alábbi szakasz ismerteti az ajánlott eljárások a következő területeken.  

### <a name="file-size"></a>Fájlméret

Például a HDInsight és az Azure Data Lake Analytics elemzési motor rendszerint egy fájl terhelést kell. Ha az adatokat tetszőleges számú kis fájlokat tárolja, ez azonban negatív hatással a teljesítményre. Az adatok általában szervezheti nagyobb méretű fájlok, a jobb teljesítmény érdekében (100GB méretű 256MB). Bizonyos motorok és alkalmazások előfordulhat, hogy problémája hatékonyan a több mint 100GB méretű fájlok feldolgozása.

Egyes esetekben adatfolyamatok korlátozott a nyers adatokat, amelyek vonása a sok kis méretű fájl felett. Azt javasoljuk, hogy egy "hogy" folyamatot, amely létrehozza az alárendelt alkalmazások használandó nagyobb fájlok.

### <a name="organizing-time-series-data-in-folders"></a>Idősorozat-adatok mappákba rendezése

A Hive számítási feladatokhoz partíció törlési idősorozat-adatok segítségével olvassa el az adatokat, ami javítja a teljesítményt, csak egy részhalmazát néhány lekérdezést.    

Idősorozat-adatokat, ezen folyamatok gyakran helyezze el a fájlok és mappák nagyon strukturált elnevezési fájljaikat. Alább egy nagyon gyakori példa látható adatok esetén, amelyek dátum szerint van strukturálva a következő:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Figyelje meg, hogy a DateTime típusú adatokat egyaránt mappákat, és a fájlnév jelenik-e.

Dátum és idő, a következő egy gyakori minta

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Újra az Ön által meghozott döntés mappában, és a nagyobb méretűek, és minden egyes mappában lévő fájlok észszerű mennyiségű optimalizálja a szervezet fájlt.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>A HDInsight Hadoop és Spark számítási feladatok nagy számításigényű feladatok i/o optimalizálása

Feladatok a következő három kategóriába sorolhatók:

* **CPU-igényes.**  Ezek a feladatok hosszú kiszámításához szükséges idő minimális i/o-rövidebb rendelkezik.  Ilyenek például a machine learning és természetes nyelvi feldolgozási feladatokat.  
* **Memóriaigényes.**  Ezek a feladatok nagy mennyiségű memóriát használja.  Ilyenek például PageRank és valós idejű elemzési feladatokat.  
* **I/o-igényes.**  Ezeket a feladatokat legtöbb idejüket i/o ezt töltenek.  Ilyenek például a csak olvasási és írási műveletek egy másolási feladat.  További példák lehetnek, olvassa el a nagy mennyiségű adatot, hajt végre bizonyos adatátalakítás és majd visszaírja az adatokat a tároló adatelőkészítési feladatok.  

Az alábbi útmutatót csak akkor érvényes, az i/o-igényes feladatok.

## <a name="general-considerations"></a>Általános megfontolások

Használhat egy feladatot, amely olvas vagy ír, amennyire csak 100 MB-os egyetlen műveletben, de egy adott méretű puffert veszélyeztetheti a teljesítményt.
A teljesítmény optimalizálása érdekében próbálja méretének csökkentése érdekében egy i/o-művelet 4MB, 16MB között.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Egy HDInsight-fürtön az vonatkozó általános szempontok

* **HDInsight-verziók.** A legjobb teljesítmény érdekében a HDInsight legújabb kiadását használja.
* **Régiók.** Helyezze el a Data Lake Storage Gen2-fiók ugyanabban a régióban, mint a HDInsight-fürtöt.  

Egy HDInsight-fürt két fő csomópont és az egyes munkavégző csomópontokhoz tevődik össze. Egyes feldolgozó csomópontok biztosít bizonyos számú maggal és a memória, amely a virtuális gép típusa határozza meg.  Feladat futtatása YARN esetén a resource negotiator, foglalja le a rendelkezésre álló memória és magok tárolók létrehozásához.  A tárolók futtatja a feladatokat, a feladat végrehajtásához szükséges.  Tárolók gyors feldolgozására feladatok párhuzamosan futtathatók. Ezért a teljesítmény növelése a lehető tetszőleges számú párhuzamos tárolók futtatásával.

Az alábbi három réteg egy HDInsight-fürtön, amelyek megfelelő hangolásával növeli a tárolók számát, és az összes elérhető átviteli sebesség belül.  

* **Fizikai réteg**
* **YARN-réteg**
* **Munkaterhelés-réteg**

### <a name="physical-layer"></a>Fizikai réteg

**Futtassa a fürt további csomópontokat és/vagy nagyobb méretű virtuális gépeket.**  Nagyobb fürt lehetővé teszi az alábbi képen látható módon további YARN-tárolók futtatásához.

![Data Lake Storage Gen2-teljesítmény](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Virtuális gépek nagyobb hálózati sávszélességet használhat.**  Hálózati sávszélesség szűk keresztmetszetté lehet, ha a kevesebb, mint a Data Lake Storage Gen2 átviteli sebesség a sávszélesség.  Különböző virtuális gépek lesz a hálózati sávszélesség mérete változó.  Válasszon egy Virtuálisgép-típust, amely rendelkezik a legnagyobb lehetséges hálózati sávszélesség.

### <a name="yarn-layer"></a>YARN-réteg

**Kisebb a YARN-tárolók használata.**  Az egyes YARN-tárolók további tárolók létrehozásához az erőforrások ugyanannyi méretének csökkentésére.

![Data Lake Storage Gen2-teljesítmény](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

Attól függően, a számítási feladatok mindig lesz egy minimális YARN tárolóméret szükséges. Ha túl kicsi tároló kiválasztása, a feladatok memóriahiány problémákat fog futni. YARN-tárolók általában nem a 1GB-nál kisebbnek kell lennie. Szokás 3 GB-os YARN-tárolók megtekintéséhez. Bizonyos munkaterhelések esetén szükség lehet a nagyobb YARN-tárolók.  

**Növelje a magok száma a YARN-tároló.**  Az egyes tárolókban futó párhuzamos feladatok számának növelése érdekében egyes tárolók számára lefoglalt magok számának növelése.  Ez a módszer az alkalmazások, mint a Spark, tárolónként több feladatok futtatásához, amelyek.  Az alkalmazásokra, például a Hive egyetlen szálon futó minden egyes tárolóban célszerűbb tárolónként több maggal helyett több tároló van.

### <a name="workload-layer"></a>Munkaterhelés-réteg

**Az összes elérhető tárolót használja.**  Egyenlő vagy nagyobb, mint a rendelkezésre álló tárolók száma is, hogy az összes erőforrás felhasználtuk feladatok számának megadása.

![Data Lake Storage Gen2-teljesítmény](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**A sikertelen feladatok olyan költséges.** Ha egyes feladatok nagy mennyiségű adat feldolgozása, feladat sikertelenségét az olyan költséges újrapróbálkozási eredményez.  Ezért érdemes további tevékenységeket, amelyek mindegyike egy kisebb mennyiségű adatot folyamatok létrehozásához.

Minden alkalmazás a fenti általános irányelveket, most finomhangolása az adott alkalmazás számára elérhető különböző paramétereket. Az alábbi táblázat felsorolja az egyes paraméterek és teljesítmény-finomhangolási az egyes alkalmazások – első lépések mutató hivatkozásokat.

| Számítási feladat | Paraméter segítségével állítsa be a feladatok |
|----------|------------------------|
| [A Spark on HDInsight](data-lake-storage-performance-tuning-spark.md) | <ul><li>Num-executors</li><li>Végrehajtó – memória</li><li>Végrehajtó virtuális mag</li></ul> |
| [A HDInsight Hive](data-lake-storage-performance-tuning-hive.md) | <ul><li>hive.tez.container.size</li></ul> |
| [MapReduce on HDInsight](data-lake-storage-performance-tuning-mapreduce.md) | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.Maps</li><li>Mapreduce.reduce.Memory</li><li>Mapreduce.job.reduces</li></ul> |
| [HDInsight alatt futó stormmal](data-lake-storage-performance-tuning-storm.md)| <ul><li>Munkavégző folyamatok száma</li><li>Spout végrehajtó példányok száma</li><li>Bolt végrehajtó példányok száma </li><li>Spout feladatok száma</li><li>Bolt feladatok száma</li></ul>|

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake Storage Gen2 áttekintése](data-lake-storage-introduction.md)

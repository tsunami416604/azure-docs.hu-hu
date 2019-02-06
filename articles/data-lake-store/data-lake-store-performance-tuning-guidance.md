---
title: Az Azure Data Lake Storage Gen1 teljesítmény-finomhangolási útmutató |} A Microsoft Docs
description: Az Azure Data Lake Storage Gen1 teljesítmény-finomhangolási útmutató
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
ms.openlocfilehash: a8a50db5ece242bc00a28e66e21c863388950d6f
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756835"
---
# <a name="tuning-azure-data-lake-storage-gen1-for-performance"></a>Az Azure Data Lake Storage Gen1 teljesítmény hangolása

Az Azure Data Lake Storage Gen1 támogatja a nagy átviteli sebességű i/o nagy számításigényű analytics és az adatok áthelyezését.  A Data Lake Storage Gen1 használatával az összes elérhető átviteli sebesség – olvassa el, vagy másodpercenként írt adatok mennyisége – fontos, hogy a legjobb teljesítményt.  Ez tetszőleges számú olvasási elvégzésével érhető el, és írja a lehető párhuzamosan.

![Data Lake Storage Gen1 teljesítmény](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen1 adja meg a szükséges átviteli összes analytics-forgatókönyv méretezheti. Alapértelmezés szerint egy Data Lake Storage Gen1 fiókot automatikusan elegendő átviteli sebességet biztosít, ha alkalmazási helyzetek széles körét. Olyan esetekben, ahol tapasztalja a vevők az alapértelmezett korlát a Data Lake Storage Gen1 fiók konfigurálható további átviteli sebességet biztosít a Microsoft támogatási szolgálatával.

## <a name="data-ingestion"></a>Adatfeldolgozás

Ha a Data Lake Storage Gen1 a forrásrendszerben érkező adatok feldolgozása, fontos vegye figyelembe, hogy a forrás hardver, a forrás hálózati hardverek, valamint a hálózati kapcsolat a Data Lake Storage Gen1 lehet a szűk keresztmetszetet.  

![Data Lake Storage Gen1 teljesítmény](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

Fontos győződjön meg arról, hogy az adatok áthelyezése nincs hatással a tényezők.

### <a name="source-hardware"></a>Forrás hardver

Akár a helyszíni gépek és virtuális gépek az Azure-ban az, körültekintően jelölje ki a megfelelő hardvert. A forrás lemez hardverekhez SSD-k inkább HDD-k, és válassza ki a gyorsabb forgórészek lemez hardver. Forrás hálózati hardverek használja a leggyorsabb hálózati adapterek lehetséges.  Az Azure-ban javasoljuk, hogy Azure D14 virtuális gépek, amelyek megfelelő teljesítményű lemez és hálózatkezelési hardverek.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Hálózati kapcsolat és a Data Lake Storage Gen1

A hálózati kapcsolat a forrásadatok és a Data Lake Storage Gen1 néha lehet a szűk keresztmetszetet. Ha a forrásadatokat a helyszínen, fontolja meg dedikált hivatkozást [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Ha a forrásadatok az Azure-ban, a teljesítmény lesz ajánlott ugyanabban a régióban az Azure Data Lake Storage Gen1 fiókként az adatok esetén.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Maximális párhuzamos feldolgozás adatbetöltés eszközök konfigurálása

A forrás hardver foglalkoztak, és a hálózati kapcsolat szűk fenti, készen áll az adatfeldolgozási eszközök konfigurálásához. Az alábbi táblázat foglalja össze a legfontosabb számos népszerű adatfeldolgozási eszközök beállításainak és biztosít részletes teljesítmény-finomhangolási a cikkek a számukra.  Melyik eszközt használja a forgatókönyvben kapcsolatos további tudnivalókért keresse fel a [cikk](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios).

| Eszköz               | Beállítások     | További részletek                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount |  [Hivatkozás](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell) |
| Az AdlCopy    | Az Azure Data Lake Analytics-egységek  |   [Hivatkozás](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (mapper)   | [Hivatkozás](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Hivatkozás](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (mapper)    |   [Hivatkozás](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Az adatkészlet struktúra

Data Lake Storage Gen1, a fájl mérete a tárolt adatok a fájlok és a gyökérmappa-szerkezetében hatással vannak a teljesítményre.  Az alábbi szakasz ismerteti az ajánlott eljárások a következő területeken.  

### <a name="file-size"></a>Fájlméret

Például a HDInsight és az Azure Data Lake Analytics elemzési motor rendszerint egy fájl terhelést kell.  Ha az adatokat tetszőleges számú kis fájlokat tárolja, ez azonban negatív hatással a teljesítményre.  

Az adatok általában szervezheti nagyobb méretű fájlok, a jobb teljesítmény érdekében.  Célravezető, mint 256 MB-os vagy nagyobb méretű fájlok adatkészletek rendszerezheti. Bizonyos esetekben, például képeket vagy bináris adatot nincs lehetőség azok párhuzamos feldolgozásához.  Ezekben az esetekben ajánlott, hogy az egyes fájlok a 2GB.

Egyes esetekben adatfolyamatok korlátozott a nyers adatokat, amelyek vonása a sok kis méretű fájl felett.  Azt javasoljuk, hogy egy "hogy" folyamatot, amely létrehozza az alárendelt alkalmazások használandó nagyobb fájlok.

### <a name="organizing-time-series-data-in-folders"></a>Idősorozat-adatok mappákba rendezése

A Hive és a ADLA számítási feladatokhoz partíció törlési idősorozat-adatok segítségével néhány lekérdezést, olvassa el az adatokat, ami javítja a teljesítményt, csak egy részét.    

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

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Egy HDInsight-fürtön az vonatkozó általános szempontok

* **HDInsight-verziók.** A legjobb teljesítmény érdekében a HDInsight legújabb kiadását használja.
* **Régiók.** Helyezze el a Data Lake Storage Gen1 fiók ugyanabban a régióban, mint a HDInsight-fürtöt.  

Egy HDInsight-fürt két fő csomópont és az egyes munkavégző csomópontokhoz tevődik össze. Egyes feldolgozó csomópontok biztosít bizonyos számú maggal és a memória, amely a virtuális gép típusa határozza meg.  Feladat futtatása YARN esetén a resource negotiator, foglalja le a rendelkezésre álló memória és magok tárolók létrehozásához.  A tárolók futtatja a feladatokat, a feladat végrehajtásához szükséges.  Tárolók gyors feldolgozására feladatok párhuzamosan futtathatók. Ezért a teljesítmény növelése a lehető tetszőleges számú párhuzamos tárolók futtatásával.

Az alábbi három réteg egy HDInsight-fürtön, amelyek megfelelő hangolásával növeli a tárolók számát, és az összes elérhető átviteli sebesség belül.  

* **Fizikai réteg**
* **YARN-réteg**
* **Munkaterhelés-réteg**

### <a name="physical-layer"></a>Fizikai réteg

**Futtassa a fürt további csomópontokat és/vagy nagyobb méretű virtuális gépeket.**  Nagyobb fürt lehetővé teszi az alábbi képen látható módon további YARN-tárolók futtatásához.

![Data Lake Storage Gen1 teljesítmény](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Virtuális gépek nagyobb hálózati sávszélességet használhat.**  Hálózati sávszélesség szűk keresztmetszetté lehet, ha a kevesebb, mint a Data Lake Storage Gen1 átviteli sebesség a sávszélesség.  Különböző virtuális gépek lesz a hálózati sávszélesség mérete változó.  Válasszon egy Virtuálisgép-típust, amely rendelkezik a legnagyobb lehetséges hálózati sávszélesség.

### <a name="yarn-layer"></a>YARN-réteg

**Kisebb a YARN-tárolók használata.**  Az egyes YARN-tárolók további tárolók létrehozásához az erőforrások ugyanannyi méretének csökkentésére.

![Data Lake Storage Gen1 teljesítmény](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

Attól függően, a számítási feladatok mindig lesz egy minimális YARN tárolóméret szükséges. Ha túl kicsi tároló kiválasztása, a feladatok memóriahiány problémákat fog futni. YARN-tárolók általában nem a 1GB-nál kisebbnek kell lennie. Szokás 3 GB-os YARN-tárolók megtekintéséhez. Bizonyos munkaterhelések esetén szükség lehet a nagyobb YARN-tárolók.  

**Növelje a magok száma a YARN-tároló.**  Az egyes tárolókban futó párhuzamos feladatok számának növelése érdekében egyes tárolók számára lefoglalt magok számának növelése.  Ez a módszer az alkalmazások, mint a Spark, tárolónként több feladatok futtatásához, amelyek.  Az alkalmazásokra, például a Hive egyetlen szálon futó minden egyes tárolóban célszerűbb tárolónként több maggal helyett több tároló van.

### <a name="workload-layer"></a>Munkaterhelés-réteg

**Az összes elérhető tárolót használja.**  Egyenlő vagy nagyobb, mint a rendelkezésre álló tárolók száma is, hogy az összes erőforrás felhasználtuk feladatok számának megadása.

![Data Lake Storage Gen1 teljesítmény](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**A sikertelen feladatok olyan költséges.** Ha egyes feladatok nagy mennyiségű adat feldolgozása, feladat sikertelenségét az olyan költséges újrapróbálkozási eredményez.  Ezért érdemes további tevékenységeket, amelyek mindegyike egy kisebb mennyiségű adatot folyamatok létrehozásához.

Minden alkalmazás a fenti általános irányelveket, most finomhangolása az adott alkalmazás számára elérhető különböző paramétereket. Az alábbi táblázat felsorolja az egyes paraméterek és teljesítmény-finomhangolási az egyes alkalmazások – első lépések mutató hivatkozásokat.

| Számítási feladat               | Paraméter segítségével állítsa be a feladatok                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [A Spark on HDInsight](data-lake-store-performance-tuning-spark.md)       | <ul><li>Num-executors</li><li>Végrehajtó – memória</li><li>Végrehajtó virtuális mag</li></ul> |
| [A HDInsight Hive](data-lake-store-performance-tuning-hive.md)    | <ul><li>hive.tez.container.size</li></ul>         |
| [MapReduce on HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.Maps</li><li>Mapreduce.reduce.Memory</li><li>Mapreduce.job.reduces</li></ul> |
| [HDInsight alatt futó stormmal](data-lake-store-performance-tuning-storm.md)| <ul><li>Munkavégző folyamatok száma</li><li>Spout végrehajtó példányok száma</li><li>Bolt végrehajtó példányok száma </li><li>Spout feladatok száma</li><li>Bolt feladatok száma</li></ul>|

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
* [Ismerkedés az Azure Data Lake Analytics szolgáltatással](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

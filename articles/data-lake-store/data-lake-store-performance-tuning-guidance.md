---
title: Azure Data Lake Storage Gen1 – teljesítményhangolás
description: Bemutatja, hogyan lehet az Azure Data Lake Storage Gen1 teljesítményre finomhangolni.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: 2521700e0f07691541ee6cbbf085a8be72f08129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904623"
---
# <a name="tune-azure-data-lake-storage-gen1-for-performance"></a>Az Azure Data Lake Storage Gen1 finomhangolása teljesítményre

A Data Lake Storage Gen1 támogatja a nagy átviteli sebességet az I/O-intenzív elemzéshez és az adatmozgatáshoz. A Data Lake Storage Gen1,az összes rendelkezésre álló átviteli teljesítmény – az adatok másodpercenként idotartama – használata fontos a legjobb teljesítmény érdekében. Ez úgy érhető el, hogy a lehető legtöbb olvasást és írást párhuzamosan hajtja végre.

![A Data Lake Storage Gen1 teljesítménye](./media/data-lake-store-performance-tuning-guidance/throughput.png)

A Data Lake Storage Gen1 skálázható az összes elemzési forgatókönyvhöz szükséges átviteli kapacitás biztosítása érdekében. Alapértelmezés szerint a Data Lake Storage Gen1 fiók automatikusan elegendő átviteli kapacitást biztosít a használati esetek széles kategóriájának igényeinek kielégítésére. Azokban az esetekben, amikor az ügyfelek az alapértelmezett korláton futnak be, a Data Lake Storage Gen1 fiók beállítható úgy, hogy nagyobb átviteli kapacitást biztosítson a Microsoft támogatási szolgálatával.

## <a name="data-ingestion"></a>Adatfeldolgozás

Amikor adatokat ad be a forrásrendszerből a Data Lake Storage Gen1-be, fontos figyelembe venni, hogy a forráshardver, a forráshálózati hardver és a Data Lake Storage Gen1 hálózati kapcsolata lehet a szűk keresztmetszet.

![A Data Lake Storage Gen1 teljesítménye](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

Fontos annak biztosítása, hogy az adatmozgást ne befolyásolják ezek a tényezők.

### <a name="source-hardware"></a>Forráshardver

Akár helyszíni gépeket, akár az Azure-ban lévő virtuális gépeket használ, gondosan válassza ki a megfelelő hardvert. A Forráslemez hardvere esetén az SSD-ket részesíti előnyben a HDD-knél, és vegye ki a gyorsabb orsóval rendelkező lemezhardvereket. A Forrás hálózati hardver esetében a lehető leggyorsabb hálózati adaptereket használja. Az Azure-ban azt javasoljuk, hogy az Azure D14 virtuális gépek, amelyek rendelkeznek a megfelelő en-hatékony lemez-és hálózati hardver.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Hálózati kapcsolat a Data Lake Storage Gen1 szolgáltatással

A forrásadatok és a Data Lake Storage Gen1 közötti hálózati kapcsolat néha szűk keresztmetszetet okozhat. Ha a forrásadatok helyszíni, fontolja meg egy dedikált kapcsolat használatát az [Azure ExpressRoute-tal.](https://azure.microsoft.com/services/expressroute/) Ha a forrásadatok az Azure-ban vannak, a teljesítmény akkor lesz a legjobb, ha az adatok ugyanabban az Azure-régióban vannak, mint a Data Lake Storage Gen1 fiók.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Adatbetöltési eszközök konfigurálása a maximális párhuzamosításhoz

Miután kikezelte a forráshardver és a hálózati kapcsolat szűk keresztmetszeteit, készen áll a betöltési eszközök konfigurálására. Az alábbi táblázat számos népszerű betöltési eszköz legfontosabb beállításait foglalja össze, és részletes teljesítményhangoló cikkeket tartalmaz számukra. Ha többet szeretne megtudni arról, hogy melyik eszközt használja a forgatókönyvhöz, látogasson el ebbe a [cikkbe.](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios)

| Eszköz          | Beállítások | További részletek                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount | [Hivatkozás](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell) |
| AdlCopy    | Az Azure Data Lake Analytics egységei | [Hivatkozás](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (mapper) | [Hivatkozás](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| párhuzamos másolatok | [Hivatkozás](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (mapper) | [Hivatkozás](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Az adatkészlet strukturálása

Ha az adatokat a Data Lake Storage Gen1 tárolja, a fájlméret, a fájlok száma és a mappastruktúra befolyásolja a teljesítményt. A következő szakasz az ezeken a területeken bevált módszereket ismerteti.

### <a name="file-size"></a>Fájlméret

Az olyan elemzési motorok, mint a HDInsight és az Azure Data Lake Analytics általában fájlonkénti többletterheléssel rendelkeznek. Ha az adatokat annyi kisfájlban tárolja, ez negatívan befolyásolhatja a teljesítményt.

Az adatokat általában nagyobb méretű fájlokba rendezheti a jobb teljesítmény érdekében. Ökölszabályként az adatkészleteket 256 MB-os vagy nagyobb fájlokba rendezheti. Bizonyos esetekben, például a képek és a bináris adatok nem lehet azokat párhuzamosan feldolgozni. Ezekben az esetekben ajánlott az egyes fájlokat 2 GB alatt tartani.

Néha az adatfolyamatok korlátozott mértékben szabályozták a nyers adatokat, amelyek sok kis fájlokat. Javasoljuk, hogy a "főzés" folyamat, amely generál nagyobb fájlokat használni az alsóbb rétegbeli alkalmazások.

### <a name="organize-time-series-data-in-folders"></a>Idősorozat-adatok rendezése mappákban

Hive és ADLA számítási feladatok esetén az idősorozat-adatok partícióinak metszése segíthet egyes lekérdezések nek az adatok egy részét olvasni, ami javítja a teljesítményt.

Azok a folyamatok, amelyek idősorozat-adatokat betöltése, gyakran helyezik el fájljaikat a fájlok és mappák strukturált elnevezésével. Az alábbiakban egy gyakori példát látunk a dátum szerint strukturált adatokesetében:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Figyelje meg, hogy a datetime információ mappákként és fájlnévként is megjelenik.

A dátum és az idő, a következő egy közös minta

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Ismét, a választás, hogy a mappa és a fájl szervezet kell optimalizálni a nagyobb fájlméretet, és ésszerű számú fájlt minden mappában.

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>I/O-igényes feladatok optimalizálása a Hadoop és spark számítási feladatokon a HDInsight-on

A munkahelyek a következő három kategória egyikébe tartoznak:

* **CPU-igényes.** Ezek a feladatok hosszú számítási idő minimális I/O-idő. Ilyenek például a gépi tanulás és a természetes nyelvi feldolgozási feladatok.
* **Memóriaigényes.** Ezek a feladatok sok memóriát használnak. Ilyenek például a PageRank és a valós idejű elemzési feladatok.
* **I/O intenzív.** Ezek a munkák töltik a legtöbb időt csinál I / O. Gyakori példa egy másolási feladat, amely csak olvasási és írási műveleteket végez. További példák közé tartozik az adatok előkészítési feladatok, amelyek számos adatot olvasnak, bizonyos adatátalakítást hajtanak végre, majd visszaírják az adatokat a tárolóba.

A következő útmutató csak az I/O-intenzív feladatokra vonatkozik.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>HDInsight-fürt általános szempontjai

* **HDInsight-verziók.** A legjobb teljesítmény érdekében használja a HDInsight legújabb kiadását.
* **Régiók.** Helyezze a Data Lake Storage Gen1 fiókot ugyanabban a régióban, mint a HDInsight-fürt.

A HDInsight-fürt két főcsomópontból és néhány munkavégző csomópontból áll. Minden munkavégző csomópont egy adott számú mag és memória, amely a virtuális gép típusa határozza meg. Feladat futtatásakor yarn az erőforrás-tárgyaló, amely lefoglalja a rendelkezésre álló memória és a magok tárolók létrehozásához. Minden tároló futtatja a feladat végrehajtásához szükséges feladatokat. A tárolók párhuzamosan futnak a feladatok gyors feldolgozásához. Ezért a teljesítmény a lehető legtöbb párhuzamos tároló futtatásával javul.

Egy HDInsight-fürtön belül három réteg van, amelyek a tárolók számának növelése és az összes rendelkezésre álló átviteli hang használata érdekében hangolhatók.

* **Fizikai réteg**
* **YARN réteg**
* **Számítási feladatok rétege**

### <a name="physical-layer"></a>Fizikai réteg

**Több csomós és/vagy nagyobb méretű virtuális gépekkel rendelkező fürt futtatása.** Egy nagyobb fürt lehetővé teszi, hogy több YARN-tárolót futtasson az alábbi képen látható módon.

![A Data Lake Storage Gen1 teljesítménye](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Nagyobb hálózati sávszélességgel rendelkező virtuális gépek használata.** A hálózati sávszélesség mennyisége szűk keresztmetszetet okozhat, ha a Data Lake Storage Gen1 átviteli igénynél kisebb a hálózati sávszélesség. A különböző virtuális gépek különböző hálózati sávszélesség-méretekkel rendelkeznek. Válasszon olyan virtuális géptípust, amely a lehető legnagyobb hálózati sávszélességgel rendelkezik.

### <a name="yarn-layer"></a>YARN réteg

**Használjon kisebb YARN tartályokat.** Csökkentse az egyes YARN-tárolók méretét, hogy több tárolót hozzon létre azonos mennyiségű erőforrással.

![A Data Lake Storage Gen1 teljesítménye](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

A számítási feladatoktól függően mindig lesz egy minimális YARN tárolóméret, amely szükséges. Ha túl kis méretű tárolót választ, a feladatok memóriaen kívüli problémákba ütköznek. A YARN-tárolók általában nem lehetnek kisebbek 1 GB-nál. Gyakori, hogy 3 GB YARN konténerek. Egyes számítási feladatok esetén nagyobb YARN-tárolókra lehet szükség.

**Növelje a magok növelését YARN-tartályonként.** Növelje az egyes tárolókhoz rendelt magok számát az egyes tárolókban futó párhuzamos feladatok számának növelése érdekében. Ez működik az alkalmazások, például a Spark, amely tárolónként több feladatot futtat. Az alkalmazások, mint a Hive, amely egyetlen szálat futtat minden tárolóban, jobb, ha több tárolót, nem pedig több mag tárolónként.

### <a name="workload-layer"></a>Számítási feladatok rétege

**Használja az összes rendelkezésre álló tárolót.** Állítsa be, hogy a tevékenységek száma egyenlő vagy nagyobb legyen a rendelkezésre álló tárolók számánál, hogy az összes erőforrást fel lehessen használni.

![A Data Lake Storage Gen1 teljesítménye](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**A sikertelen feladatok költségesek.** Ha minden feladat nagy mennyiségű adatot dolgoz fel, akkor egy feladat sikertelensége költséges újrapróbálkozást eredményez. Ezért jobb, ha több feladatot hoz létre, amelyek mindegyike kis mennyiségű adatot dolgoz fel.

A fenti általános irányelvek mellett minden alkalmazás különböző paramétereket rendelkezik az adott alkalmazáshoz való hangoláshoz. Az alábbi táblázat felsorolja az egyes alkalmazások teljesítményhangolásának megkezdéséhez szolgáló paramétereket és hivatkozásokat.

| Számítási feladat               | Feladatok beállítására megadott paraméter                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Spark on HDInsight](data-lake-store-performance-tuning-spark.md)  | <ul><li>Num-végrehajtók</li><li>Végrehajtó-memória</li><li>Végrehajtó-magok</li></ul> |
| [Hive a HDInsight-on](data-lake-store-performance-tuning-hive.md)    | <ul><li>hive.tez.container.size</li></ul>         |
| [MapReduce a HDInsight-on](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
| [Storm on HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Munkavégző folyamatok száma</li><li>Spout végrehajtó példányainak száma</li><li>A bolt végrehajtó példányainak száma </li><li>Kifolyó feladatok száma</li><li>Bolt-feladatok száma</li></ul>|

## <a name="see-also"></a>Lásd még

* [Az Azure Data Lake storage gen1 áttekintése](data-lake-store-overview.md)
* [Ismerkedés az Azure Data Lake Analytics szolgáltatással](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

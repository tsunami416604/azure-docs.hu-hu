---
title: Az Azure Data Lake Storage Gen2 optimalizálása teljesítményre | Microsoft dokumentumok
description: Az Azure Data Lake Storage Gen2 teljesítményhangolási irányelvei
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: f1a16228b72d7e0f45048669ade94a0c78d9ac52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327943"
---
# <a name="optimize-azure-data-lake-storage-gen2-for-performance"></a>Az Azure Data Lake Storage Gen2 optimalizálása teljesítményre

Az Azure Data Lake Storage Gen2 támogatja a nagy átviteli sebességet az I/O-intenzív elemzéshez és az adatmozgatáshoz.  A Data Lake Storage Gen2,-ban az összes rendelkezésre álló átviteli - az adatok másodpercenként idotartama – használata fontos a legjobb teljesítmény érdekében.  Ez úgy érhető el, hogy a lehető legtöbb olvasást és írást párhuzamosan hajtja végre.

![A Data Lake Storage Gen2 teljesítménye](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

A Data Lake Storage Gen2 skálázható az összes elemzési forgatókönyvhöz szükséges átviteli kapacitás biztosítása érdekében. Alapértelmezés szerint a Data Lake Storage Gen2 fiók automatikusan elegendő átviteli kapacitást biztosít a használati esetek széles kategóriájának igényeinek kielégítésére. Azokban az esetekben, amikor az ügyfelek az alapértelmezett korláton futnak be, a Data Lake Storage Gen2 fiók beállítható úgy, hogy nagyobb átviteli kapacitást biztosítson az [Azure-támogatással](https://azure.microsoft.com/support/faq/)való kapcsolatfelvételrévén.

## <a name="data-ingestion"></a>Adatfeldolgozás

Amikor adatokat ad be a forrásrendszerből a Data Lake Storage Gen2-be, fontos figyelembe venni, hogy a forráshardver, a forráshálózati hardver és a Data Lake Storage Gen2 hálózati kapcsolata lehet a szűk keresztmetszet.  

![A Data Lake Storage Gen2 teljesítménye](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

Fontos annak biztosítása, hogy az adatmozgást ne befolyásolják ezek a tényezők.

### <a name="source-hardware"></a>Forráshardver

Akár helyszíni gépeket, akár virtuális gépeket használ az Azure-ban, gondosan válassza ki a megfelelő hardvert. A Forráslemez hardvere esetén az SSD-ket részesíti előnyben a HDD-knél, és vegye ki a gyorsabb orsóval rendelkező lemezhardvereket. A Forrás hálózati hardver esetében a lehető leggyorsabb hálózati adaptereket használja.  Az Azure-ban azt javasoljuk, hogy az Azure D14 virtuális gépek, amelyek rendelkeznek a megfelelő en-hatékony lemez-és hálózati hardver.

### <a name="network-connectivity-to-data-lake-storage-gen2"></a>Hálózati kapcsolat a Data Lake Storage Gen2 szolgáltatással

A forrásadatok és a Data Lake Storage Gen2 közötti hálózati kapcsolat néha szűk keresztmetszetet okozhat. Ha a forrásadatok helyszíni, fontolja meg egy dedikált kapcsolat használatát az [Azure ExpressRoute-tal.](https://azure.microsoft.com/services/expressroute/) Ha a forrásadatok az Azure-ban vannak, a teljesítmény akkor lesz a legjobb, ha az adatok ugyanabban az Azure-régióban vannak, mint a Data Lake Storage Gen2 fiók.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Adatbetöltési eszközök konfigurálása a maximális párhuzamosításhoz

Miután a fenti forráshardver- és hálózati kapcsolati szűk keresztmetszeteket kezelte, készen áll a betöltési eszközök konfigurálására. Az alábbi táblázat számos népszerű betöltési eszköz legfontosabb beállításait foglalja össze, és részletes teljesítményhangoló cikkeket tartalmaz számukra.  Ha többet szeretne megtudni arról, hogy melyik eszközt használja a forgatókönyvhöz, látogasson el ebbe a [cikkbe.](data-lake-storage-data-scenarios.md)

| Eszköz               | Beállítások     | További részletek                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| DistCp            | -m (mapper)   | [Hivatkozás](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| párhuzamos másolatok    | [Hivatkozás](../../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (mapper)    |   [Hivatkozás](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Az adatkészlet strukturálása

Ha az adatokat a Data Lake Storage Gen2 tárolja, a fájlméret, a fájlok száma és a mappastruktúra hatással van a teljesítményre.  A következő szakasz az ezeken a területeken bevált módszereket ismerteti.  

### <a name="file-size"></a>Fájlméret

Az olyan elemzési motorok, mint a HDInsight és az Azure Data Lake Analytics általában fájlonkénti többletterheléssel rendelkeznek. Ha az adatokat annyi kisfájlban tárolja, ez negatívan befolyásolhatja a teljesítményt. Általánosságban elmondható, hogy az adatokat nagyobb méretű fájlokba rendezheti a jobb teljesítmény érdekében (256 MB és 100 GB közötti méret). Egyes motorok és alkalmazások problémásak lehetnek a 100 GB-nál nagyobb méretű fájlok hatékony feldolgozása során.

Néha az adatfolyamatok korlátozott mértékben szabályozták a nyers adatokat, amelyek sok kis fájlt tartalmaznak. Javasoljuk, hogy a "főzés" folyamat, amely generál nagyobb fájlokat használni az alsóbb rétegbeli alkalmazások.

### <a name="organizing-time-series-data-in-folders"></a>Idősorozat-adatok rendszerezése mappákban

Hive-számítási feladatok esetén az idősorozat-adatok partícióinak metszése segíthet abban, hogy egyes lekérdezések csak az adatok egy részét olvassák, ami javítja a teljesítményt.    

Azok a folyamatok, amelyek idősorozat-adatokat betöltése, gyakran a fájlok és mappák nagyon strukturált elnevezésével helyezik el a fájlokat. Az alábbiakban egy nagyon gyakori példát látunk az adatok, amelyek szerint a dátum:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Figyelje meg, hogy a datetime információ mappákként és fájlnévként is megjelenik.

A dátum és az idő, a következő egy közös minta

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Ismét, a választás, hogy a mappa és a fájl szervezet kell optimalizálni a nagyobb fájlméretet, és ésszerű számú fájlt minden mappában.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>I/O-igényes feladatok optimalizálása a Hadoop és a Spark számítási feladatokon a HDInsight-on

A munkahelyek a következő három kategória egyikébe tartoznak:

* **CPU-igényes.**  Ezek a feladatok hosszú számítási idő minimális I/O-idő.  Ilyenek például a gépi tanulás és a természetes nyelvi feldolgozási feladatok.  
* **Memóriaigényes.**  Ezek a feladatok sok memóriát használnak.  Ilyenek például a PageRank és a valós idejű elemzési feladatok.  
* **I/O intenzív.**  Ezek a munkák töltik a legtöbb időt csinál I / O.  Gyakori példa egy másolási feladat, amely csak olvasási és írási műveleteket végez.  További példák közé tartozik az adatok előkészítése feladatok, amelyek sok adatot olvasnak, bizonyos adatátalakítást hajt végre, majd visszaírja az adatokat a tárolóba.  

A következő útmutató csak az I/O-intenzív feladatokra vonatkozik.

## <a name="general-considerations"></a>Általános megfontolások

Lehet egy feladat, amely beolvassa vagy írja, mint 100MB egyetlen műveletben, de egy ilyen méretű puffer veszélyeztetheti a teljesítményt.
A teljesítmény optimalizálása érdekében próbálja meg az I/O-műveletek méretét 4 MB és 16 MB között tartani.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>HDInsight-fürt általános szempontjai

* **HDInsight-verziók.** A legjobb teljesítmény érdekében használja a HDInsight legújabb kiadását.
* **Régiók.** Helyezze a Data Lake Storage Gen2 fiókot ugyanabban a régióban, mint a HDInsight-fürt.  

A HDInsight-fürt két főcsomópontból és néhány munkavégző csomópontból áll. Minden munkavégző csomópont egy adott számú mag és memória, amely a virtuális gép típusa határozza meg.  Feladat futtatásakor yarn az erőforrás-tárgyaló, amely lefoglalja a rendelkezésre álló memória és a magok tárolók létrehozásához.  Minden tároló futtatja a feladat végrehajtásához szükséges feladatokat.  A tárolók párhuzamosan futnak a feladatok gyors feldolgozásához. Ezért a teljesítmény a lehető legtöbb párhuzamos tároló futtatásával javul.

Egy HDInsight-fürtön belül három réteg van, amelyek a tárolók számának növelése és az összes rendelkezésre álló átviteli hang használata érdekében hangolhatók.  

* **Fizikai réteg**
* **YARN réteg**
* **Számítási feladatok rétege**

### <a name="physical-layer"></a>Fizikai réteg

**Több csomós és/vagy nagyobb méretű virtuális gépekkel rendelkező fürt futtatása.**  Egy nagyobb fürt lehetővé teszi, hogy több YARN-tárolót futtasson az alábbi képen látható módon.

![A Data Lake Storage Gen2 teljesítménye](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Nagyobb hálózati sávszélességgel rendelkező virtuális gépek használata.**  A hálózati sávszélesség mennyisége szűk keresztmetszetet okozhat, ha a Data Lake Storage Gen2 átviteli igénynél kisebb a hálózati sávszélesség.  A különböző virtuális gépek különböző hálózati sávszélesség-méretekkel rendelkeznek.  Válasszon olyan virtuális géptípust, amely a lehető legnagyobb hálózati sávszélességgel rendelkezik.

### <a name="yarn-layer"></a>YARN réteg

**Használjon kisebb YARN tartályokat.**  Csökkentse az egyes YARN-tárolók méretét, hogy több tárolót hozzon létre azonos mennyiségű erőforrással.

![A Data Lake Storage Gen2 teljesítménye](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

A számítási feladatoktól függően mindig lesz egy minimális YARN tárolóméret, amely szükséges. Ha túl kis méretű tárolót választ, a feladatok memóriaen kívüli problémákba ütköznek. Jellemzően YARN konténerek nem lehet kisebb, mint 1GB. Gyakori, hogy 3 GB YARN konténerek. Egyes számítási feladatok esetén nagyobb YARN-tárolókra lehet szükség.  

**Növelje a magok növelését YARN-tartályonként.**  Növelje az egyes tárolókhoz rendelt magok számát az egyes tárolókban futó párhuzamos feladatok számának növelése érdekében.  Ez olyan alkalmazásokesetében működik, mint a Spark, amely tárolónként több feladatot futtat.  Az alkalmazások, mint a Hive, amely egyetlen szálat futtat minden tárolóban, jobb, ha több tárolót, nem pedig több mag tárolónként.

### <a name="workload-layer"></a>Számítási feladatok rétege

**Használja az összes rendelkezésre álló tárolót.**  Állítsa be, hogy a tevékenységek száma egyenlő vagy nagyobb legyen, mint a rendelkezésre álló tárolók száma, hogy az összes erőforrást használja.

![A Data Lake Storage Gen2 teljesítménye](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**A sikertelen feladatok költségesek.** Ha minden feladat nagy mennyiségű adatot dolgoz fel, akkor egy feladat sikertelensége költséges újrapróbálkozást eredményez.  Ezért jobb, ha több feladatot hoz létre, amelyek mindegyike kis mennyiségű adatot dolgoz fel.

A fenti általános irányelvek mellett minden alkalmazás különböző paramétereket rendelkezik az adott alkalmazáshoz való hangoláshoz. Az alábbi táblázat felsorolja az egyes alkalmazások teljesítményhangolásának megkezdéséhez szolgáló paramétereket és hivatkozásokat.

| Számítási feladat | Feladatok beállítására megadott paraméter |
|----------|------------------------|
| [Spark on HDInsight](data-lake-storage-performance-tuning-spark.md) | <ul><li>Num-végrehajtók</li><li>Végrehajtó-memória</li><li>Végrehajtó-magok</li></ul> |
| [Hive a HDInsight-on](data-lake-storage-performance-tuning-hive.md) | <ul><li>hive.tez.container.size</li></ul> |
| [MapReduce a HDInsight-on](data-lake-storage-performance-tuning-mapreduce.md) | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
| [Storm on HDInsight](data-lake-storage-performance-tuning-storm.md)| <ul><li>Munkavégző folyamatok száma</li><li>Spout végrehajtó példányainak száma</li><li>A bolt végrehajtó példányainak száma </li><li>Kifolyó feladatok száma</li><li>Bolt-feladatok száma</li></ul>|

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake Storage Gen2 áttekintése](data-lake-storage-introduction.md)

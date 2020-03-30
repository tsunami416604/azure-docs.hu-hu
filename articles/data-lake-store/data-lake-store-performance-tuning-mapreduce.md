---
title: Azure Data Lake Storage Gen1 teljesítményhangolása – MapReduce
description: Az Azure Data Lake Storage Gen1 mapreduce teljesítményhangolási irányelvei
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: a645049665bc1d51efa94a879b9d2e4e5529282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904595"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Teljesítményhangolási útmutató a MapReduce szolgáltatáshoz a HDInsight és az Azure Data Lake Storage Gen1 szolgáltatásban

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Storage Gen1 fiók.** Az azure [Data Lake Storage gen1](data-lake-store-get-started-portal.md) című témakörben talál útmutatást a létrehozásról.
* **Azure HDInsight-fürt,** amely hozzáférést biztosít a Data Lake Storage Gen1 fiókhoz. Lásd: [HDInsight-fürt létrehozása a Data Lake Storage Gen1 szolgáltatással című témakört.](data-lake-store-hdinsight-hadoop-use-portal.md) Győződjön meg arról, hogy engedélyezi a Távoli asztal szolgáltatást a fürtszámára.
* **A MapReduce használata a HDInsight on**. További információ: [MapReduce használata a Hadoopban a HDInsight-on](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Tekintse át a Data Lake Storage Gen1 teljesítményhangolási irányelveit.** Az általános teljesítménykoncepciókról a [Data Lake Storage Gen1 teljesítményhangolási útmutatója című témakörben talál.](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Paraméterek

A MapReduce feladatok futtatásakor az alábbiakban a Data Lake Storage Gen1 teljesítményének növelésére konfigurálható legfontosabb paramétereket használhatja:

|Paraméter      | Leírás  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  Az egyes leképezőkszámára lefoglalandó memória mennyisége.  |
|`Mapreduce.job.maps`     |  A feladatonkénti leképezési feladatok száma.  |
|`Mapreduce.reduce.memory.mb`     |  Az egyes szűkítők számára lefoglalandó memória mennyisége.  |
|`Mapreduce.job.reduces`    |   A feladatok számának csökkentése feladatonként.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>Mapreduce.map.memory / Mapreduce.reduce.memory

Állítsa be ezt a számot annak alapján, hogy mennyi memória szükséges a térképhez és/vagy csökkentse a feladatot. Az Ambari `mapreduce.map.memory` és `mapreduce.reduce.memory` az Ambari alapértelmezett értékeit a Fonal konfiguráción keresztül tekintheti meg. Az Ambari ban keresse meg a YARN billentyűt, és tekintse meg a **Configs** lapot. Megjelenik a YARN memória.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>Mapreduce.job.maps / Mapreduce.job.reduces

Ez határozza meg a létrehozandó leképezők vagy szűkítők maximális számát. A felosztások száma határozza meg, hogy hány leképező jön létre a MapReduce feladathoz. Ezért előfordulhat, hogy kevesebb leképezőt kap, mint amennyit kért, ha kevesebb a felosztás, mint a kért leképezők száma.

## <a name="guidance"></a>Útmutatás

### <a name="step-1-determine-number-of-jobs-running"></a>1. lépés: A futó feladatok számának meghatározása

Alapértelmezés szerint a MapReduce a teljes fürtöt fogja használni a feladathoz. A fürtből kevesebbet használhat, ha kevesebb leképezőt használ, mint amennyi elérhető tároló. A jelen dokumentumban található útmutató feltételezi, hogy az alkalmazás az egyetlen, amely a fürtön fut.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>2. lépés: Set mapreduce.map.memory/mapreduce.reduce.memory

A térkép és a feladatok csökkentéséhez szükséges memória mérete az adott feladattól függ. Csökkentheti a memória méretét, ha növelni szeretné az egyidejűséget. Az egyidejűleg futó feladatok száma a tárolók számától függ. A leképezőnkénti vagy szűkítőnkénti memória mennyiségének csökkentésével több tároló hozható létre, amelyek lehetővé teszik, hogy több leképező vagy szűkítő egyidejűleg fusson. A memória mennyiségének túl nagy mértékű csökkentése egyes folyamatok memóriavesztését okozhatja. Ha halommemória-hibát kap a feladat futtatásakor, növelje a leképezőnkénti vagy szűkítőnkénti memóriát. Vegye figyelembe, hogy további tárolók hozzáadása további többletterhelést minden további tároló, amely potenciálisan ronthatja a teljesítményt. Egy másik alternatíva, hogy több memóriát kap egy olyan fürt használatával, amely nagyobb mennyiségű memóriát használ, vagy növeli a csomópontok számát a fürtben. Több memória lehetővé teszi, hogy több tárolót kell használni, ami azt jelenti, több egyidejűség.

### <a name="step-3-determine-total-yarn-memory"></a>3. lépés: A teljes YARN memória meghatározása

A mapreduce.job.maps/mapreduce.job.reduces hangolásához vegye figyelembe a használható yarn memória teljes mennyiségét. Ez az információ elérhető Ambari. Nyissa meg a YARN lapot, és tekintse meg a **Configs** lapot. Ebben az ablakban megjelenik a YARN memória. Szorozza meg a YARN memóriát a fürtben lévő csomópontok számával a teljes YARN memória leolvasásához.

`Total YARN memory = nodes * YARN memory per node`

Ha üres fürtöt használ, akkor a memória lehet a fürt teljes YARN memóriája. Ha más alkalmazások is használnak memóriát, akkor dönthet úgy, hogy csak a fürt memóriájának egy részét használja a leképezők vagy szűkítők számának a használni kívánt tárolók számára való csökkentésével.

### <a name="step-4-calculate-number-of-yarn-containers"></a>4. lépés: Számítsa ki a YARN tartályok számát

Yarn tárolók diktálják a feladathoz rendelkezésre álló egyidejűség mennyiségét. Vegye ki a teljes YARN memóriát, és ossza el mapreduce.map.memory.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>5. lépés: Set mapreduce.job.maps/mapreduce.job.reduces

Állítsa be a mapreduce.job.maps/mapreduce.job.reduces beállítását legalább a rendelkezésre álló tárolók számára. Kísérletezhet tovább a leképezők és szűkítők számának növelésével, hogy lássa, jobb teljesítményt érhet-e el. Ne feledje, hogy több leképező lesz további többletterhelést, így miután túl sok leképező ronthatja a teljesítményt.

A PROCESSZOR-ütemezés és a PROCESSZOR elkülönítése alapértelmezés szerint ki van kapcsolva, így a YARN-tárolók számát a memória korlátozza.

## <a name="example-calculation"></a>Példa számítása

Tegyük fel, hogy jelenleg 8 D14-csomópontból álló fürttel rendelkezik, és i/o-intenzív feladatot szeretne futtatni. Itt vannak a számításokat meg kell tennie:

### <a name="step-1-determine-number-of-jobs-running"></a>1. lépés: A futó feladatok számának meghatározása

Példánkban azt feltételezzük, hogy a mi munkánk az egyetlen, ami fut.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>2. lépés: Set mapreduce.map.memory/mapreduce.reduce.memory

Példánkban egy I/O-intenzív feladatot futtat, és úgy dönt, hogy 3 GB memória elegendő a térképfeladatokhoz.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>3. lépés: A teljes YARN memória meghatározása

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>4. lépés: Számítsa ki a YARN konténerek #

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>5. lépés: Set mapreduce.job.maps/mapreduce.job.reduces

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Korlátozások

**Data Lake Storage Gen1 szabályozás**

Több-bérlős szolgáltatásként a Data Lake Storage Gen1 fiókszintű sávszélesség-korlátokat állít be. Ha bejön ezek a korlátok, akkor kezdődik, hogy feladat hibák. Ez a feladatnaplókban szereplő szabályozási hibák megfigyelésével azonosítható. Ha nagyobb sávszélességre van szüksége a munkájához, kérjük, vegye fel velünk a kapcsolatot.

Annak ellenőrzéséhez, hogy szabályozás alá kerül-e, engedélyeznie kell a hibakeresési naplózást az ügyféloldalon. Ezt a következőképpen teheti meg:

1. Helyezze a következő tulajdonságot az Ambari > YARN > > Config > Advanced yarn-log4j log4j log4j log4j log4j log4j tulajdonságába: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Indítsa újra az összes csomópontot/szolgáltatást a konfiguráció érvénybe léptetéséhez.

3. Ha szabályozás alatt áll, a HTTP 429 hibakód jelenik meg a YARN naplófájlban. A YARN naplófájl a /tmp/&lt;user&gt;/yarn.log fájlban található.

## <a name="examples-to-run"></a>Példák a futtatáshoz

A MapReduce data lake storage gen1-en való futtatásának bemutatásához a következő mintakód fut egy fürtön a következő beállításokkal:

* 16 csomópont D14v2
* HDI 3.6-os futtatásakor működő Hadoop-fürt

A kiindulási pont, itt van néhány példa parancsok futtatásához MapReduce Teragen, Terasort és Teravalidate. Ezeket a parancsokat az erőforrások alapján módosíthatja.

### <a name="teragen"></a>Teragen között

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort között

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalid

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```

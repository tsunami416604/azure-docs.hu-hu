---
title: Az Azure Data Lake Storage Gen2 MapReduce teljesítmény-finomhangolási útmutató |} A Microsoft Docs
description: Az Azure Data Lake Storage Gen2 MapReduce teljesítmény-finomhangolási útmutató
services: storage
author: swums
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: stewu
ms.openlocfilehash: 0cd7ecdc6ee7b6ccd66c9d0d88ebef0a1220a40a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249112"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Teljesítmény-finomhangolási útmutató a MapReduce on HDInsight és az Azure Data Lake Storage Gen2

Ismerje meg, amikor beállítja a Mapreduce-feladatok teljesítményét, megfontolandó tényezők. Ez a cikk ismerteti a teljesítmény-finomhangolási útmutató számos.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Az Azure Data Lake Storage Gen2 fiók**. Létrehozásával kapcsolatos utasításokért lásd: [a rövid útmutató: Hozzon létre egy Azure Data Lake Storage Gen2 tárfiókot](data-lake-storage-quickstart-create-account.md).
* **Az Azure HDInsight-fürt** hozzáférést egy Data Lake Storage Gen2-fiókot. Ellenőrizze, hogy engedélyezi a távoli asztal a fürtöt.
* **A MapReduce használata a HDInsight**.  További információkért lásd: [a HDInsight a Hadoop MapReduce használata](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Teljesítmény-finomhangolási útmutató a Data Lake Storage Gen2**.  Az általános teljesítmény fogalmak, lásd: [Data Lake Storage Gen2 teljesítményének hangolása útmutatója](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Paraméterek

MapReduce-feladatok futtatásakor az alábbiakban a Data Lake Storage Gen2 a teljesítmény növelése érdekében konfigurálható paraméterek:

* **Mapreduce.Map.Memory.MB** – megadott memóriát lefoglalni az egyes leképezője
* **Mapreduce.job.Maps** – térkép műveletek feladatonként száma
* **Mapreduce.reduce.Memory.MB** – lefoglalni az egyes nyomáscsökkentő memória mennyisége
* **Mapreduce.job.reduces** – csökkentse a feladatok feladatonként száma

**Mapreduce.Map.Memory / Mapreduce.reduce.memory** ezt a számot kell igazítani mennyi memóriára van szükség, a térkép és/vagy csökkentse a tevékenység.  Az alapértelmezett értékeket mapreduce.map.memory és mapreduce.reduce.memory keresztül a Yarn-konfiguráció Ambari lehet megtekinteni.  Az Ambari keresse meg a YARN és a Configs lapon.  A YARN memóriát fog megjelenni.  

**Mapreduce.job.Maps / Mapreduce.job.reduces** ez leképező és csökkentő létrehozandó maximális száma határozza meg.  A megszakítások számát határozza meg, hány leképező a MapReduce-feladatot hoz létre.  Tehát hogy vannak-e a kért leképező számánál kisebb elágazást kértnél kevesebb leképező kaphat.       

## <a name="guidance"></a>Útmutatás

> [!NOTE]
> Ez a dokumentum az útmutató feltételezi, hogy az alkalmazás az egyetlen, a fürtben futó alkalmazás.

**1. lépés: Futó feladatok számának meghatározása**

Alapértelmezés szerint a MapReduce a feladat az egész fürt használja.  Kisebb, a fürt használhatja a rendelkezésre álló tárolók számánál kisebb leképező használatával.        

**2. lépés: Mapreduce.map.memory/mapreduce.reduce.memory beállítása**

A térkép memória mérete, és csökkentse feladatok lesz az adott feladat függ.  Ha azt szeretné, egyidejűség mértékének növelése érdekében csökkentheti a memória méretét.  Egyidejűleg futó feladatok száma attól függ, hogy a tárolók száma.  Eseményleképező vagy nyomáscsökkentő memória csökkentésével több tároló létrehozása, amelyek lehetővé teszik több leképező és csökkentő egyidejű futtatását.  Túl sok csökkenő memória mennyisége, előfordulhat, hogy bizonyos folyamatok futtatásához nincs elég memória.  Ha egy halom hiba történt a feladat futtatásakor, növelje a memória eseményleképező vagy nyomáscsökkentő.  Vegye figyelembe, hogy további tárolók hozzáadása felveszi nagyon általános további tárolókhoz, amelyek vélhetően ronthatja a teljesítményt.  Egy másik lehetőség, hogy több memóriát kap a nagyobb mennyiségű memóriával rendelkező fürt használatával, vagy a fürtben található csomópontok számának növelése.  Több memóriát lehetővé teszi több tároló használható, ami azt jelenti, hogy több egyidejűséget.  

**3. lépés: Memória összesen YARN meghatározása**

Finomhangolása mapreduce.job.maps/mapreduce.job.reduces, érdemes teljes YARN használható memória mennyisége.  Ez az információ Ambari érhető el.  Keresse meg a YARN és a konfigurációkat lapon.  A YARN memória ebben az ablakban jelenik meg.  A fürt YARN memória összesen beolvasásához kell szorozza meg a YARN memóriát, valamint a csomópontok számát.

    Total YARN memory = nodes * YARN memory per node

Ha egy üres fürtöt használ, memória-fürthöz tartozó teljes YARN memóriából lehet.  Ha más alkalmazásokat használ a memóriát, majd lehet váltani, csak a fürt memória egy részét használja leképező és csökkentő számának csökkentése a használni kívánt tárolók száma.  

**4. lépés: YARN-tárolók számának kiszámítása**

YARN-tárolók diktálni a feldolgozási feladat érhető el.  YARN teljes memória és az, hogy nullával való osztás mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**5. lépés: Mapreduce.job.maps/mapreduce.job.reduces beállítása**

Állítsa be a mapreduce.job.maps/mapreduce.job.reduces legalább az elérhető tárolók száma.  Kísérletezhet további korlátozásokat leképező és csökkentő megjelenítéséhez jobb teljesítményt érhet számának növelése.  Ne feledje, hogy több leképező lesz-e további erőforrásokra, így túl sok leképező kellene ronthatja a teljesítményt.  

CPU ütemezés és a CPU-elkülönítési vannak kapcsolva alapértelmezés szerint, a memória a YARN-tárolók számát korlátozza.

## <a name="example-calculation"></a>Példa kiszámítása

Tegyük fel, hogy van-e a fürt mikroszolgáltatásokból álló, D14 8 csomópont, és intenzív i/o-feladat futtatása szeretnénk.  Hajtsa végre a számítások a következők:

**1. lépés: Futó feladatok számának meghatározása**

Ebben a példában feltételezzük, hogy a feladat-e a egyetlen feladat, hogy fut-e.  

**2. lépés: Mapreduce.map.memory/mapreduce.reduce.memory beállítása**

Ebben a példában azt az i/o-intenzív feladatok futnak, és döntse el, hogy elegendő lesz-e a 3GB memória, a térkép feladatokhoz.

    mapreduce.map.memory = 3GB

**3. lépés: Memória összesen YARN meghatározása**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**4. lépés: YARN-tárolók száma kiszámítása**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**5. lépés: Mapreduce.job.maps/mapreduce.job.reduces beállítása**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Példák futtatása

Mutatja be, hogyan MapReduce fut-e a Data Lake Storage Gen2, alább néhány mintakódját az alábbi beállításokkal egy fürtön futtatott van:

* 16 node D14v2
* Hadoop-fürt HDI 3.6-ot futtató

A kiindulási pont Íme néhány példaparancs MapReduce Teragen Terasort és Teravalidate futtatásához.  Beállíthatja, hogy ezeket a parancsokat az erőforrások alapján.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate

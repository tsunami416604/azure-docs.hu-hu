---
title: Az Azure Data Lake Storage Gen1 MapReduce teljesítmény-finomhangolási útmutató |} A Microsoft Docs
description: Az Azure Data Lake Storage Gen1 MapReduce teljesítmény-finomhangolási útmutató
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: b661499786057a3083f79684dfd12c85266b7b5c
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128791"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Teljesítmény-finomhangolási útmutató a MapReduce on HDInsight és az Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Az Azure Data Lake Storage Gen1 fiók**. Létrehozásával kapcsolatos utasításokért lásd: [Ismerkedés az Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Az Azure HDInsight-fürt** hozzáférést egy Data Lake Storage Gen1 fiókot. Lásd: [egy HDInsight-fürt létrehozása a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Ellenőrizze, hogy engedélyezi a távoli asztal a fürtöt.
* **A MapReduce használata a HDInsight**.  További információkért lásd: [a HDInsight a Hadoop MapReduce használata](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Teljesítmény-finomhangolási útmutató a Data Lake Storage Gen1**.  Az általános teljesítmény fogalmak, lásd: [Data Lake Storage Gen1 teljesítményének hangolása útmutatója](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Paraméterek

MapReduce-feladatok futtatásakor a következők a legfontosabb paramétereket, amelyeket a Data Lake Storage Gen1 teljesítményének növelése érdekében konfigurálhat:

* **Mapreduce.Map.Memory.MB** – megadott memóriát lefoglalni az egyes leképezője
* **Mapreduce.job.Maps** – térkép műveletek feladatonként száma
* **Mapreduce.reduce.Memory.MB** – lefoglalni az egyes nyomáscsökkentő memória mennyisége
* **Mapreduce.job.reduces** – csökkentse a feladatok feladatonként száma

**Mapreduce.Map.Memory / Mapreduce.reduce.memory** ezt a számot kell igazítani mennyi memóriára van szükség, a térkép és/vagy csökkentse a tevékenység.  Az alapértelmezett értékeket mapreduce.map.memory és mapreduce.reduce.memory keresztül a Yarn-konfiguráció Ambari lehet megtekinteni.  Az Ambari keresse meg a YARN és a Configs lapon.  A YARN memóriát fog megjelenni.  

**Mapreduce.job.Maps / Mapreduce.job.reduces** ez leképező és csökkentő létrehozandó maximális száma határozza meg.  A megszakítások számát határozza meg, hány leképező a MapReduce-feladatot hoz létre.  Tehát hogy vannak-e a kért leképező számánál kisebb elágazást kértnél kevesebb leképező kaphat.       

## <a name="guidance"></a>Útmutatás

**1. lépés: A futó feladatok száma határozza meg** -MapReduce alapértelmezés szerint az egész fürt használja a feladatot.  Kisebb, a fürt használhatja a rendelkezésre álló tárolók számánál kisebb leképező használatával.  Ez a dokumentum az útmutató feltételezi, hogy az alkalmazás az egyetlen, a fürtben futó alkalmazás.      

**2. lépés: Állítsa be a mapreduce.map.memory/mapreduce.reduce.memory** – térkép a memória méretét, és csökkentse feladatok lesz az adott feladat függ.  Ha azt szeretné, egyidejűség mértékének növelése érdekében csökkentheti a memória méretét.  Egyidejűleg futó feladatok száma attól függ, hogy a tárolók száma.  Eseményleképező vagy nyomáscsökkentő memória csökkentésével több tároló létrehozása, amelyek lehetővé teszik több leképező és csökkentő egyidejű futtatását.  Túl sok csökkenő memória mennyisége, előfordulhat, hogy bizonyos folyamatok futtatásához nincs elég memória.  Ha egy halom hiba történt a feladat futtatásakor, növelje a memória eseményleképező vagy nyomáscsökkentő.  Vegye figyelembe, hogy további tárolók hozzáadása felveszi nagyon általános további tárolókhoz, amelyek vélhetően ronthatja a teljesítményt.  Egy másik lehetőség, hogy több memóriát kap a nagyobb mennyiségű memóriával rendelkező fürt használatával, vagy a fürtben található csomópontok számának növelése.  Több memóriát lehetővé teszi több tároló használható, ami azt jelenti, hogy több egyidejűséget.  

**3. lépés: Határozható meg a teljes YARN** – finomhangolása mapreduce.job.maps/mapreduce.job.reduces, érdemes lehet a teljes YARN használható memória mennyiségét.  Ez az információ Ambari érhető el.  Keresse meg a YARN és a konfigurációkat lapon.  A YARN memória ebben az ablakban jelenik meg.  A fürt YARN memória összesen beolvasásához kell szorozza meg a YARN memóriát, valamint a csomópontok számát.

    Total YARN memory = nodes * YARN memory per node
Ha egy üres fürtöt használ, memória-fürthöz tartozó teljes YARN memóriából lehet.  Ha más alkalmazásokat használ a memóriát, majd lehet váltani, csak a fürt memória egy részét használja leképező és csökkentő számának csökkentése a használni kívánt tárolók száma.  

**4. lépés: A YARN-tárolók száma kiszámítása** – a YARN-tárolók diktálni, egyidejűség érhető el a feladatot.  YARN teljes memória és az, hogy nullával való osztás mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**5. lépés: Állítsa be a mapreduce.job.maps/mapreduce.job.reduces** mapreduce.job.maps/mapreduce.job.reduces állítsa legalább a rendelkezésre álló tárolók száma.  Kísérletezhet további korlátozásokat leképező és csökkentő megjelenítéséhez jobb teljesítményt érhet számának növelése.  Ne feledje, hogy több leképező lesz-e további erőforrásokra, így túl sok leképező kellene ronthatja a teljesítményt.  

CPU ütemezés és a CPU-elkülönítési vannak kapcsolva alapértelmezés szerint, a memória a YARN-tárolók számát korlátozza.

## <a name="example-calculation"></a>Példa kiszámítása

Tegyük fel, jelenleg egy fürt mikroszolgáltatásokból álló, D14 8 csomópont van, és intenzív i/o-feladat futtatása szeretne.  Hajtsa végre a számítások a következők:

**1. lépés: A futó feladatok száma határozza meg** –. a példa kedvéért feltételezzük, hogy a feladat a csak egy futó-e.  

**2. lépés: Állítsa be a mapreduce.map.memory/mapreduce.reduce.memory** – példa egy i/o-igényes feladat fut, és döntse el, hogy elegendő lesz-e a 3 GB memória, a térkép feladatokhoz.

    mapreduce.map.memory = 3GB
**3. lépés: A teljes YARN memória meghatározása**

    total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**4. lépés: A YARN-tárolók száma kiszámítása**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**5. lépés: Mapreduce.job.maps/mapreduce.job.reduces beállítása**

    mapreduce.map.jobs = 256

## <a name="limitations"></a>Korlátozások

**Data Lake Storage Gen1 szabályozása**

Több-bérlős szolgáltatás a Data Lake Storage Gen1 szint sávszélesség korlátok állítja be.  Ha eléri ezeket a korlátokat, hozzákezdhet tevékenységhibák megtekintéséhez. Ez a feladat naplókban szabályozási hibákat észlelt problémát úgy azonosítható.  Ha a feladat több sávszélességet van szüksége, lépjen kapcsolatba velünk a következő címen.   

Annak ellenőrzéséhez, hogy ha Ön első szabályozott, akkor engedélyeznie kell a hibakeresési naplózás az ügyféloldalon. Itt látható, hogyan teheti, hogy:

1. A következő tulajdonság helyezett Ambari log4j tulajdonság > YARN > Konfiguráció > yarn-log4j speciális: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Indítsa újra az összes a csomópontok/szolgáltatást a konfiguráció érvénybe léptetéséhez.

3. Ha, első szabályozott, látni fogja a HTTP 429-es hibakód: a YARN log fájl. A YARN log fájl van /tmp/&lt;felhasználói&gt;/yarn.log

## <a name="examples-to-run"></a>Futtassa a példákat

Mutatja be, hogyan MapReduce fut-e a Data Lake Storage Gen1, alább néhány mintakódját az alábbi beállításokkal egy fürtön futtatott van:

* 16 csomópontra D14v2
* Hadoop-fürt HDI 3.6-ot futtató

A kiindulási pont Íme néhány példaparancs MapReduce Teragen Terasort és Teravalidate futtatásához.  Beállíthatja, hogy ezeket a parancsokat az erőforrások alapján.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate

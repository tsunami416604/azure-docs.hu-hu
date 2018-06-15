---
title: Az Azure Data Lake Store MapReduce teljesítményének hangolása irányelvek |} Microsoft Docs
description: Az Azure Data Lake Store MapReduce teljesítményének hangolása irányelvek
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
ms.openlocfilehash: f5586e7706d4dad7e3c943b2a661fa296b4d30bf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198633"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-store"></a>Útmutató a HDInsight és az Azure Data Lake Store MapReduce teljesítményhangolása

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Store-fiók**. Hogyan hozhat létre ilyet, lásd: [Ismerkedés az Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Az Azure HDInsight-fürt** a Data Lake Store-fiók eléréséhez. Lásd: [HDInsight-fürtök létrehozása a Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Győződjön meg arról, hogy a fürt számára engedélyezi a távoli asztal.
* **MapReduce használata a HDInsight**.  További információkért lásd: [használata MapReduce a Hadoop on HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Teljesítményhangolás ADLS iránymutatást**.  Általános teljesítmény fogalmakat, lásd: [Data Lake Store teljesítmény hangolása útmutató](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Paraméterek

MapReduce-feladatok futtatásakor az alábbiakban a legfontosabb paraméterek ADLS teljesítményének javítása konfigurálható:

* **Mapreduce.Map.Memory.MB** – memória foglal minden leképezője
* **Mapreduce.job.Maps** – térkép feladatok feladat másodpercenkénti száma
* **Mapreduce.reduce.Memory.MB** – foglal minden nyomáscsökkentő memória mennyisége
* **Mapreduce.job.reduces** – csökkentse feladatok feladat másodpercenkénti száma

**Mapreduce.Map.Memory / Mapreduce.reduce.memory** ezt a számot kell igazítani, hogy mennyi memóriát van szükség a térkép és/vagy csökkentse a tevékenység.  A Yarn konfigurációs tekinthetők az Ambari mapreduce.map.memory és mapreduce.reduce.memory alapértelmezett értékeit.  Az Ambari YARN keresse meg, és a Configs lapon.  A YARN memória jelenik meg.  

**Mapreduce.job.Maps / Mapreduce.job.reduces** Ez határozza meg a létrehozandó mappers vagy szűkítő maximális számát.  A megszakítások számát határozza meg, hány mappers a MapReduce feladatot hoz létre.  Ezért kevesebb, mint a kért vannak-e a kért mappers számánál kisebb elágazást mappers jelenhet meg.       

## <a name="guidance"></a>Útmutatás

**1. lépés: A futó feladatok száma meghatározása** -MapReduce alapértelmezés szerint a teljes fürt használja a feladatot.  Kevesebb, a fürt kisebb, mint amennyi a rendelkezésre álló tárolók mappers segítségével is használhatók.  Ez a dokumentum az útmutató azt feltételezi, hogy az alkalmazás az egyetlen, a fürtben futó alkalmazás.      

**2. lépés:, Állítsa be mapreduce.map.memory/mapreduce.reduce.memory** – térkép memória mérete, ami csökkenti feladatok lesz az adott feladat függ.  Ha azt szeretné, hogy növelheti a párhuzamosságot csökkentése érdekében a memória méretét.  Egyidejűleg futó feladatok száma attól függ, hogy a tárolók száma.  Eseményleképező vagy nyomáscsökkentő memória mennyisége csökkentésével több tároló létre lehet hozni, amelyek lehetővé teszik több mappers vagy szűkítő egyidejű futtatását.  Túl sok csökkentése a memória mennyiségét, előfordulhat, hogy bizonyos folyamatok futtatásához nincs elegendő szabad memória.  Ha egy halommemóriában hibaüzenet jelenik meg a feladat futtatásakor, növelni kell a memória mennyisége vagy az nyomáscsökkentő.  Vegye figyelembe, hogy több tároló felveszi extra általános, minden további tároló, amely potenciálisan ronthatja a teljesítményt.  Egy másik lehetőség több memória nagyobb mennyiségű memóriával rendelkező fürtnek használatával, vagy a fürtben található csomópontok számának növelésével eléréséhez.  További memória lehetővé teszi több tároló használni, ami azt jelenti, hogy több egyidejű.  

**3. lépés: Teljes YARN memória meghatározása** hangolására mapreduce.job.maps/mapreduce.job.reduces, - érdemes teljes YARN használatra rendelkezésre álló memória.  Ez az információ Ambari érhető el.  A YARN és a Configs lapon.  A YARN memória ebben az ablakban jelenik meg.  A fürt eléréséhez a teljes YARN memória kell szorozza meg a YARN memória, a csomópontok számát.

    Total YARN memory = nodes * YARN memory per node
Ha egy üres fürtöt használ, memória a fürt teljes YARN memóriából lehet.  Ha más alkalmazásokat memóriát használ, akkor választhatja csak használatához a fürt memória azon része használni kívánt tárolók száma mappers vagy szűkítő számának csökkentésével.  

**4. lépés: A YARN a tárolók száma kiszámításához** – YARN tárolók diktálni a feldolgozási feladat érhető el.  Teljes YARN memória eltarthat, és osztás, amely mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**5. lépés:, Állítsa be mapreduce.job.maps/mapreduce.job.reduces** mapreduce.job.maps/mapreduce.job.reduces értéke legalább az elérhető tárolók száma.  Kísérletezhet további, a több mappers és szűkítő megjelenítéséhez, ha a jobb teljesítmény eléréséhez.  Ne feledje, hogy a további mappers lesz-e további erőforrásokra, hogy túl sok mappers ronthatja a teljesítményt.  

CPU ütemezés és a CPU-elkülönítési alapértelmezés szerint vannak kapcsolva, a memória a YARN a tárolók száma korlátozza.

## <a name="example-calculation"></a>Példa kiszámítása

Tegyük fel, jelenleg egy fürt 8 D14 csomópont álló rendelkezik, és egy i/o-igényes feladat futtatni szeretné.  A számításokat, akkor tegye a következők:

**1. lépés: A futó feladatok száma meghatározása** -a fenti példában feltételezzük, hogy a folyamat csak egy futtatását.  

**2. lépés:, Állítsa be mapreduce.map.memory/mapreduce.reduce.memory** – a fenti példában egy i/o-igényes feladat fut, és döntse el, hogy elegendő lesz-e a 3 GB memóriát a térkép feladatokhoz.

    mapreduce.map.memory = 3GB
**3. lépés: Teljes YARN memória meghatározása**

    total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**4. lépés: # YARN tároló kiszámítása**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**5. lépés: Mapreduce.job.maps/mapreduce.job.reduces beállítása**

    mapreduce.map.jobs = 256

## <a name="limitations"></a>Korlátozások

**ADLS-szabályozás**

Több-bérlős szolgáltatásként ADLS állítja be a fiók szintű sávszélesség korlátja.  Ha ezek a korlátozások kattint, hozzákezdhet feladat hibáihoz megjelenítéséhez. Ez úgy azonosítható betartásával szabályozási hibák feladat naplókban által.  Ha a feladat több sávszélesség van szüksége, lépjen kapcsolatba velünk a következő címen.   

Ha Ön első szabályozott ellenőrzéséhez szeretne engedélyezni a hibakeresési naplózás az ügyféloldalon. Ez hogyan azt teheti meg:

1. A következő tulajdonság be log4j tulajdonságait az Ambari > YARN > konfigurációs > speciális yarn-log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Indítsa újra az összes a csomópontok/szolgáltatást a konfiguráció életbe léptetéséhez.

3. Ha Ön első szabályozott, látni fogja, a HTTP 429 hibakód a YARN naplófájlban. A YARN naplófájl nem a /tmp/&lt;felhasználói&gt;/yarn.log

## <a name="examples-to-run"></a>Példák futtatását

Annak bemutatásához, hogyan MapReduce fut-e az Azure Data Lake Store, alatt van néhány mintakód futtatott egy fürtön a következő beállításokkal:

* 16 csomópont D14v2
* Hadoop-fürt HDI 3.6 fut

A kiindulási pont Íme néhány példaparancs MapReduce Teragen Terasort és Teravalidate futtatásához.  Beállíthatja, hogy ezek a parancsok a erőforrások alapján.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate

---
title: "Az Azure Data Lake Store Spark teljesítményének hangolása irányelvek |} Microsoft Docs"
description: "Az Azure Data Lake Store Spark teljesítményének hangolása irányelvek"
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 2109744fb7ffdfafb7a86bbea355e119718af099
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-store"></a>A Spark on HDInsight és az Azure Data Lake Store útmutatást teljesítményhangolása

Amikor Spark teljesítményének hangolása, kell figyelembe venni a fürtön futó alkalmazások számát.  Alapértelmezés szerint 4 futtatható egyidejűleg a HDI-fürtön lévő alkalmazások (Megjegyzés: az alapértelmezett beállítás: változhat).  Előfordulhat, hogy kevesebb alkalmazásokat használ, így felülírják az alapértelmezett beállításokat, és több fürt használata az alkalmazások mellett dönt.  

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Store-fiók**. Hogyan hozhat létre ilyet, lásd: [Ismerkedés az Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Az Azure HDInsight-fürt** a Data Lake Store-fiók eléréséhez. Lásd: [HDInsight-fürtök létrehozása a Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Győződjön meg arról, hogy a fürt számára engedélyezi a távoli asztal.
* **Spark-fürtön futó Azure Data Lake Store**.  További információkért lásd: [adatelemzéshez a Data Lake Store használata a HDInsight Spark-fürt](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Teljesítményhangolás ADLS iránymutatást**.  Általános teljesítmény fogalmakat, lásd: [Data Lake Store teljesítmény hangolása útmutató](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance) 

## <a name="parameters"></a>Paraméterek

Amikor Spark futó feladatok, az alábbiakban a legfontosabb beállítások, amelyek szabályozható ADLS a teljesítmény növelése érdekében:

* **NUM-végrehajtója** -végrehajtható egyidejű feladatok száma.

* **Végrehajtó memóriában** -minden végrehajtó kiosztott memória mennyisége.

* **Végrehajtó-magok** -az egyes végrehajtó lefoglalt magok száma.                     

**NUM-végrehajtója** Num-végrehajtója állítja be a párhuzamosan futó feladatok maximális száma.  A párhuzamosan futó feladatok tényleges száma a memória és a fürt rendelkezésre álló Processzor-erőforrások korlátozódik.

**Végrehajtó memóriában** Ez az egyes végrehajtó lefoglalt memória mennyiségét.  A minden végrehajtó szükséges memória mérete a feladat függ.  Összetett műveletek a memória eltérőnek kell lennie a magasabb.  Egyszerű műveletek tartoznak, mint az olvasási és írási alacsonyabb lesz memóriára vonatkozó követelményeknek.  Az egyes végrehajtó memória Ambari tekintheti meg.  Az Ambari keresse meg a Spark, és a Configs lapon.  

**Végrehajtó-magok** Ez meghatározza azt a végrehajtó, amely megadja, hogy futtatható végrehajtó száma párhuzamos szálak száma használt magszámra vonatkozó követelménynek.  Például ha végrehajtó-magok = 2, majd minden egyes végrehajtó 2 párhuzamos feladatokat futtathat a végrehajtó.  Az executor-magok függ, a feladat szükség lesz.  Intenzív i/o-feladatok nem igényelnek nagy mennyiségű memória feladat, ezért minden végrehajtó kezelni tud a további párhuzamos tevékenységeket.

Alapértelmezés szerint két virtuális YARN magok megadva minden fizikai magok a HDInsight Spark futtatásakor.  Ez a szám concurrecy és a környezetben több szál való váltás egyensúlyt biztosít.  

## <a name="guidance"></a>Útmutatás

Futtatásakor a Spark adatok a Data Lake Store koncepción alapuló adatelemzési célokra, azt javasoljuk, hogy a HDInsight legfrissebb használatával a Data Lake Store a legjobb teljesítmény eléréséhez. Ha a feladat több i/o-igényes, majd bizonyos paraméterek beállítható úgy, hogy a teljesítmény javítása.  Azure Data Lake Store egy kiválóan méretezhető tárolás platform, amely képes kezelni a magas teljesítmény.  Ha a feladat főként olvasási vagy írási műveleteket, majd növelése egyidejű i/o, és az Azure Data Lake Store sikerült teljesítmény növelése érdekében.

Növelheti a párhuzamosságot i/o-igényes feladatok néhány általános módja van.

**1. lépés: Annak ellenőrzése, hogy hány alkalmazások futnak, a fürt** – Ha tisztában van a fürt, beleértve a jelenlegivel hány alkalmazásokat futtat.  Minden egyes Spark, a beállítás azt feltételezi, hogy az alapértelmezett értékeit, hogy problémamentes-4 egyidejűleg futó alkalmazások.  Ezért csak akkor 25 %-át a fürt minden alkalmazás elérhető.  Ahhoz, hogy a jobb teljesítmény, a beállítás felülbírálható az alapértelmezett beállításokat végrehajtója számának módosítása.  

**2. lépés: Állítsa be az executor-memória** – először hozzá kell beállítani az executor-memória.  A memória lesz a feladatot, amely futtatni fogja függ.  Párhuzamossági növelheti, ha a végrehajtó kevesebb memória lefoglalásakor.  Ha memória kivételek kívül a feladat futtatásakor, majd növelje a paraméter értékét.  Egy esetben több memóriát beolvasása nagyobb mennyiségű memóriával rendelkező fürtnek használatával, vagy a fürt méretének növelését.  További memória lehetővé teszi több végrehajtója használni, ami azt jelenti, hogy több egyidejű.

**3. lépés: Állítsa be az executor-magok** – az I/O teljesítményigényű munkaterhelések kiszolgálásához, amelyek nem rendelkeznek összetett műveletek már jó végrehajtó-magok száma párhuzamos tevékenységek maximális száma végrehajtó növeléséhez nagyszámú kezdődik.  4-es végrehajtó-magok beállítással remek kezdőpont.   

    executor-cores = 4
Az executor-magok száma növelése Erre azért van szükség további párhuzamossági, különböző végrehajtó-magok kísérletezhet.  Az összetettebb műveleteket, feladatok csökkentse végrehajtó magok számát.  Ha végrehajtó-magok nagyobb, mint 4, majd szemétgyűjtés előfordulhat, hogy nem elég hatékony lesz és csökkentheti a teljesítményt.

**4. lépés: Fürt YARN memóriamennyiség meghatározása** – ezt az információt az Ambari érhető el.  A YARN és a Configs lapon.  A YARN memória ebben az ablakban jelenik meg.  
Megjegyzés:, amíg az ablak, megtekintheti az alapértelmezett YARN tároló mérete.  A YARN tároló mérete megegyezik a memória mennyisége végrehajtó paramétere.

    Total YARN memory = nodes * YARN memory per node
**5. lépés: Num-végrehajtója kiszámítása**

**Memória megkötés kiszámításához** -num-végrehajtója paraméter memória vagy CPU korlátozza.  Az memória határértékét az alkalmazáshoz rendelkezésre álló YARN memória mennyisége határozza meg.  Teljes YARN memória érvénybe kell, és osztás, amely végrehajtó memóriában.  A korlátozás kell lennie való méretezése alkalmazások számát, ezért azt el kell osztani a alkalmazások száma.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**CPU-korlátozás kiszámításához** -végrehajtó magok száma osztva a teljes virtuális magok számítása a CPU-korlátozás.  Nincsenek minden fizikai magok virtuális 2 magos.  A memória megkötés hasonló, tudunk osztási alkalmazások száma szerint.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Num-végrehajtója beállítása** – a num-végrehajtója paraméter határozza meg a minimális a memória korlátozás és a CPU-korlátozás véve. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Ha egy nagyobb num-végrehajtója nem feltétlenül teljesítmény növelése.  Vegye figyelembe, hogy további végrehajtója felveszi extra általános az egyes további végrehajtó, amely potenciálisan ronthatja a teljesítményt.  NUM-végrehajtója a fürterőforrásokat korlátozódik.    

## <a name="example-calculation"></a>Példa kiszámítása

Tegyük fel, már van egy 8 D4v2 csomópont álló, 2 rendszert futtató fürt alkalmazások, beleértve futtatni fogja.  

**1. lépés: Annak ellenőrzése, hogy hány alkalmazások futnak, a fürt** –, hogy rendelkezik-e 2 ismeri a fürtben, beleértve futtatni kívánt alkalmazásokhoz.  

**2. lépés: Állítsa be az executor-memória** – ebben a példában azt határozza meg, hogy 6 GB memória végrehajtó intenzív i/o-feladat elegendő lesz-e.  

    executor-memory = 6GB
**3. lépés: Állítsa be az executor-magok** – mivel ez egy i/o-igényes feladat, azt adhatja meg az magok száma minden végrehajtó 4.  Magok száma végrehajtó beállítást nagyobb, mint 4 szemétgyűjtési gyűjtemény problémákat okozhat.  

    executor-cores = 4
**4. lépés: Fürt YARN memóriamennyiség meghatározása** – azt navigáljon Ambari tudja meg, hogy minden D4v2 rendelkezik-e a YARN memória 25 GB.  Mivel nincsenek 8 csomópont, a rendszer megszorozza a YARN memória 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**5. lépés: Kiszámításához num-végrehajtója** – a num-végrehajtója paraméter határozza meg a minimális memória-korlátozás véve, és a CPU-korlátozás Spark futó alkalmazások száma osztva.    

**Memória megkötés kiszámításához** – a memória megkötés kiszámítása a teljes YARN memória, a memória mennyisége végrehajtó hányadosa.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**CPU-korlátozás kiszámításához** -végrehajtó magok száma osztva teljes yarn mag számítása a CPU-korlátozás.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Num-végrehajtója beállítása**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    


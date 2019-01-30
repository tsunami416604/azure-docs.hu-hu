---
title: Az Azure Data Lake Storage Gen2 Spark teljesítmény-finomhangolási útmutató |} A Microsoft Docs
description: Az Azure Data Lake Storage Gen2 Spark teljesítmény-finomhangolási útmutató
services: storage
author: swums
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: stewu
ms.openlocfilehash: 78402299aa0abc9b51b2995ffe6d2fc9b55c02aa
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241424"
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Teljesítmény-finomhangolási útmutató a Spark on HDInsight és az Azure Data Lake Storage Gen2

A Spark teljesítményének hangolása, amikor kell figyelembe venni a fürtön futó alkalmazások száma.  Alapértelmezés szerint 4 futtatható egyidejűleg a HDI-fürtön lévő alkalmazások (Megjegyzés: az alapértelmezett beállítás: változhat).  Kevesebb alkalmazások használatához, így felülírják az alapértelmezett beállításokat, és használja a fürt több ezen alkalmazások esetén dönthet.  

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Az Azure Data Lake Storage Gen2 fiók**. Létrehozásával kapcsolatos utasításokért lásd: [a rövid útmutató: Hozzon létre egy Azure Data Lake Storage Gen2 tárfiókot](data-lake-storage-quickstart-create-account.md).
* **Az Azure HDInsight-fürt** hozzáférést egy Data Lake Storage Gen2-fiókot. Ellenőrizze, hogy engedélyezi a távoli asztal a fürtöt. 
* **Spark-fürtön futó Data Lake Storage Gen2**.  További információkért lásd: [használata a HDInsight Spark-fürt a Data Lake Storage Gen2 adatok elemzése](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Teljesítmény-finomhangolási útmutató a Data Lake Storage Gen2**.  Az általános teljesítmény fogalmak, lásd: [Data Lake Storage Gen2 teljesítményének hangolása útmutatója](data-lake-storage-performance-tuning-guidance.md) 

## <a name="parameters"></a>Paraméterek

Amikor Spark futó feladatok, az alábbiakban a legfontosabb beállítások, amelyek megfelelő hangolásával a Data Lake Storage Gen2 a teljesítmény növelése:

* **NUM-végrehajtóval** -végrehajtható egyidejű feladatok számát.

* **Végrehajtó memóriabeli** – minden végrehajtó kiosztott memória mennyiségét.

* **Végrehajtó virtuális mag** – az egyes végrehajtó lefoglalt magok száma.                     

**NUM-végrehajtóval** Num-végrehajtóval fogja beállítani a párhuzamosan futtatható feladatok maximális számát.  A párhuzamosan futtatható feladatok tényleges száma a memória és a fürtben rendelkezésre álló Processzor-erőforrások korlátozódik.

**Végrehajtó memóriabeli** Ez az egyes végrehajtó lefoglalt memória mennyisége.  A memória szükséges minden egyes végrehajtó szolgáltatás a feladat függ.  Összetett művelet a memória van szüksége, magasabb lehet.  Egyszerű műveletek, mint például az olvasási és írási alacsonyabb lesz a memóriára vonatkozó követelményeknek.  Az egyes végrehajtó memória Ambari lehet megtekinteni.  Az Ambari keresse meg a Spark, és a konfigurációkat lapon.  

**Végrehajtó virtuális mag** Ezzel beállítja, hogy a használt végrehajtó, amely megadja, hogy kiszolgálónként végrehajtó futtatható párhuzamos szálak száma / magok számát.  Például ha végrehajtó virtuális mag = 2, majd minden egyes végrehajtó 2 párhuzamos feladatokat futtathat a végrehajtó.  Az executor-magok szükség lesz a feladat függ.  Nagy i/o-feladatok nem igényelnek nagy mennyiségű memóriát biztosít a feladat, így minden végrehajtó további párhuzamos feladatok képes kezelni.

Alapértelmezés szerint két virtuális YARN magok minden egyes fizikai maghoz vannak meghatározva a HDInsight Spark futtatásakor.  Ez a szám egyidejűség és a környezet több szálon való váltás mennyisége jó biztosít.  

## <a name="guidance"></a>Útmutatás

Futtatásakor a Spark a Data Lake Storage Gen2 adatokkal való munka adatelemzési számítási feladatokhoz, azt javasoljuk, hogy a HDInsight legfrissebb használatával a Data Lake Storage Gen2 a legjobb teljesítmény. Ha a feladat további i/o-igényes, majd bizonyos paraméterek konfigurálható teljesítmény javítása érdekében.  Data Lake Storage Gen2 egy rugalmasan méretezhető tárolási platform, amely képes kezelni a nagy átviteli sebességet.  Ha a feladat főként olvasási vagy írási áll, egyidejűségi majd növeli az i/o és a Data Lake Storage Gen2 megnövelheti teljesítményét.

Többféleképpen néhány általános i/o-igényes feladatok az egyidejűség mértékének növelése érdekében.

**1. lépés: Határozza meg, hány alkalmazásokat a fürtön futnak** – Ha tisztában van a fürtön, beleértve a jelenlegivel hány alkalmazások futnak.  Minden Spark beállítás feltételezi, hogy az alapértelmezett értékeit, ha 4 egyidejűleg futó alkalmazások.  Ezért csak akkor 25 %-át a fürt minden alkalmazáshoz elérhető.  Jobb teljesítményt érhet el, hogy az alapértelmezett értéket felülírhatja végrehajtóval számának módosításával.  

**2. lépés: Állítsa be a memórián belüli végrehajtó** – az első lépésben állítsa be a végrehajtó memória.  A memória lesz, amelyek futtatni kívánja a feladat függ.  Egyidejűségi növelheti végrehajtó kevesebb memória lefoglalásával.  Ha memória kivételek kívül a feladat futtatásakor, majd, növelje a paraméter értéke.  Egy másik, hogy több memóriát kap a nagyobb mennyiségű memóriával rendelkező fürt használatával, vagy a fürt méretének növelését.  Több memóriát lehetővé teszi több végrehajtóval használható, ami azt jelenti, hogy több egyidejűséget.

**3. lépés: Állítsa be az executor-magok** – az I/O-igényű számítási feladatokhoz, amelyek nem rendelkeznek összetett operations, fontos executor-magok / végrehajtó párhuzamos feladatok számának növeléséhez nagy számú kezdeni.  Remek kezdőpont beállítása végrehajtó virtuális mag, 4.   

    executor-cores = 4
Executor-magok számának növelésével kap további párhuzamosság, kísérletezhet a különböző végrehajtó virtuális mag.  Összetettebb műveleteket rendelkező feladatok akkor csökkentse a végrehajtó / magok számát.  Ha végrehajtó virtuális mag nagyobb, mint 4, ezután szemétgyűjtés előfordulhat, hogy nem elég hatékony válnak, és ronthatja a teljesítményt.

**4. lépés: Határozza meg a fürt YARN memória mennyisége** – Ez az információ érhető el az Ambari.  Keresse meg a YARN és a konfigurációkat lapon.  A YARN memória ebben az ablakban jelenik meg.  
Megjegyzés: amíg a ablakban, láthatja az alapértelmezett YARN tárolóméret is.  A YARN-tároló mérete megegyezik a memória végrehajtó paraméterenként.

    Total YARN memory = nodes * YARN memory per node
**5. lépés: Num-végrehajtóval kiszámítása**

**Memória megkötés kiszámítása** -num-végrehajtóval paraméter által korlátozott, memória, vagy CPU.  A memória korlátozás az alkalmazás elérhető YARN memória mennyisége határozza meg.  YARN teljes memória igénybe kell, és, hogy nullával való osztás végrehajtó memóriában.  A korlátozás kell lennie megszüntetéséhez méretezett, az alkalmazások száma, ezért azt el kell osztani alkalmazások száma.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**CPU-korlátozás kiszámítása** – a CPU-korlátozás számítjuk ki, hogy a virtuális magok száma összesen a magok száma végrehajtó száma osztva.  Nincsenek 2 virtuális mag, minden egyes fizikai maghoz.  A memória megkötés hasonlóan kell, hogy nullával való osztás alkalmazások száma.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Állítsa be a num-végrehajtóval** – a num-végrehajtóval paraméter határozza meg a minimális, a memória korlátozás és a CPU-korlátozás véve. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Num-végrehajtóval, ha nagyobb értékre nem feltétlenül növelheti teljesítményt.  Vegye figyelembe, hogy további végrehajtóval hozzáadása felveszi nagyon általános, az egyes további végrehajtó, amely potenciálisan ronthatja a teljesítményt.  A fürt erőforrásainak NUM-végrehajtóval korlátozódik.    

## <a name="example-calculation"></a>Példa kiszámítása

Tegyük fel, jelenleg rendelkezik 8 D4v2 csomópontokból álló fürttel 2 rendszerű alkalmazásokat, beleértve futtatni kívánja.  

**1. lépés: Határozza meg, hány alkalmazásokat a fürtön futnak** – tudja, hogy 2 is beleértve futtatni kívánja, a fürtön lévő alkalmazások.  

**2. lépés: Állítsa be a memórián belüli végrehajtó** – ebben a példában azt határozza meg, hogy az i/o-igényes feladat elegendő lesz-e a 6 GB memória végrehajtó.  

    executor-memory = 6GB
**3. lépés: Állítsa be az executor-magok** – mivel ez egy i/o-igényes feladat, azt az egyes végrehajtó 4 állíthatja a magok számát.  Magok száma végrehajtó beállítás nagyobb, mint 4 szemétgyűjtési gyűjtemény problémákat okozhat.  

    executor-cores = 4
**4. lépés: Határozza meg a fürt YARN memória mennyisége** – azt keresse meg az Ambari tudja meg, hogy minden egyes D4v2 25 GB-nyi memóriát YARN rendelkezik-e.  Mivel ebben az esetben 8 csomópont, a rendszer megszorozza a rendelkezésre álló memória YARN 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**5. lépés: Num-végrehajtóval kiszámítása** – a num-végrehajtóval paraméter határozza meg a minimális, a memória megkötés véve, és a CPU-korlátozás a Sparkon futó alkalmazások száma osztva.    

**Memória megkötés kiszámítása** – a memória megkötés számítjuk ki, hogy elosztja az executor-memória memória összesen YARN.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**CPU-korlátozás kiszámítása** – a CPU-korlátozás számítjuk ki, hogy a teljes yarn Processzormagok száma osztva a végrehajtó / magok számát.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Set num-végrehajtóval**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    


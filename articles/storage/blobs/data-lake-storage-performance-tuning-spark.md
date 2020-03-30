---
title: 'Hangolási teljesítmény: Spark, HDInsight & Azure Data Lake Storage Gen2 | Microsoft dokumentumok'
description: Az Azure Data Lake Storage Gen2 Spark teljesítményhangolási irányelvei
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a70b8112af201a49e7eece8b689e75102ec55880
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327545"
---
# <a name="tune-performance-spark-hdinsight--azure-data-lake-storage-gen2"></a>Hangolási teljesítmény: Spark, HDInsight & Azure Data Lake Storage Gen2

A Spark teljesítményének finomhangolásakor figyelembe kell vennie a fürtön futó alkalmazások számát.  Alapértelmezés szerint 4 alkalmazást futtathat egyidejűleg a HDI-fürtön (Megjegyzés: az alapértelmezett beállítás változhat).  Dönthet úgy, hogy kevesebb alkalmazást használ, így felülbírálhatja az alapértelmezett beállításokat, és több fürtöt használhat ezekhez az alkalmazásokhoz.  

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Storage Gen2 fiók.** A létrehozásról a [rövid útmutató: Azure Data Lake Storage Gen2 tárfiók létrehozása](data-lake-storage-quickstart-create-account.md)című témakörben talál útmutatást.
* **Azure HDInsight-fürt,** amely hozzáférést biztosít a Data Lake Storage Gen2-fiókhoz. Lásd: [Azure Data Lake Storage Gen2 használata Az Azure HDInsight-fürtökkel.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) Győződjön meg arról, hogy engedélyezi a Távoli asztal szolgáltatást a fürtszámára.
* **Spark-fürt futtatása a Data Lake Storage Gen2 szolgáltatáson.**  További információ: [A HDInsight Spark-fürt használata a Data Lake Storage Gen2 adatainak elemzéséhez](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store)
* **Teljesítményhangolási irányelvek a Data Lake Storage Gen2 szolgáltatáshoz.**  Az általános teljesítménykoncepciókról a [Data Lake Storage Gen2 teljesítményhangolási útmutatócímű témakörben talál.](data-lake-storage-performance-tuning-guidance.md) 

## <a name="parameters"></a>Paraméterek

Spark-feladatok futtatásakor az alábbiak a legfontosabb beállításokat, amelyek et lehet hangolni, hogy növelje a teljesítményt a Data Lake Storage Gen2:

* **Num-executors** - A végrehajtható egyidejű feladatok száma.

* **Végrehajtó-memória** – Az egyes végrehajtók számára lefoglalt memória mennyisége.

* **Végrehajtó-magok** – Az egyes végrehajtók számára kiosztott magok száma.                     

**Num-végrehajtók** A num-executors a párhuzamosan futtatható feladatok maximális számát állítja be.  A párhuzamosan futtatható feladatok tényleges számát a fürtben rendelkezésre álló memória- és processzorerőforrások határolja.

**Végrehajtó-memória** Ez az egyes végrehajtók számára lefoglalt memória mennyisége.  Az egyes végrehajtókszámára szükséges memória a feladattól függ.  Összetett műveletek esetén a memóriának magasabbnak kell lennie.  Az olyan egyszerű műveletek esetében, mint az olvasás és írás, a memóriakövetelmények alacsonyabbak lesznek.  Az egyes végrehajtók memóriájának mennyisége megtekinthető az Ambari-ban.  Az Ambari ban keresse meg a Spark ot, és tekintse meg a Configs lapot.  

**Végrehajtó-magok** Ez beállítja a végrehajtónként használt magok számát, amely meghatározza a végrehajtónként futtatható párhuzamos szálak számát.  Ha például a végrehajtó-magok = 2, majd minden végrehajtó futtathat 2 párhuzamos feladatokat a végrehajtó.  A szükséges végrehajtómagok a feladattól függenek.  Az I/O-feladatok nem igényelnek nagy mennyiségű memóriát feladatonként, így minden végrehajtó több párhuzamos feladatot képes kezelni.

Alapértelmezés szerint két virtuális YARN magok vannak definiálva minden fizikai mag, amikor a Spark hdinsight fut.  Ez a szám biztosítja az egyidejűség és a több szálról való környezetváltás mennyiségének megfelelő egyensúlyát.  

## <a name="guidance"></a>Útmutatás

Spark-analitikus számítási feladatok futtatása közben a Data Lake Storage Gen2 adatokkal való együttműködésre, javasoljuk, hogy a legfrissebb HDInsight-verziót használja a Data Lake Storage Gen2 legjobb teljesítményének kihasználásához. Ha a feladat i/o-igényesebb, akkor bizonyos paraméterek beállíthatók a teljesítmény javítására.  A Data Lake Storage Gen2 egy jól méretezhető tárolási platform, amely képes kezelni a nagy átviteli sebességű.  Ha a feladat elsősorban olvasási vagy írási, majd az I/O-tároló és a Data Lake Storage Gen2 egyidejűsítésének növelése növelheti a teljesítményt.

Az I/O-intenzív munkahelyek egyidejűsítésének néhány általános módja van.

**1. lépés: Határozza meg, hogy hány alkalmazás fut a fürtön** – Tudnia kell, hogy hány alkalmazás fut a fürtön, beleértve az aktuálisat is.  Az egyes Spark-beállítások alapértelmezett értékei feltételezik, hogy egyszerre 4 alkalmazás fut.  Ezért az egyes alkalmazásokhoz csak a fürt 25%-a lesz elérhető.  A jobb teljesítmény érdekében felülbírálhatja az alapértelmezett értékeket a végrehajtók számának módosításával.  

**2. lépés: Állítsa be a végrehajtó-memória** - Az első dolog, hogy állítsa be a végrehajtó-memória.  A memória a futtatni kívánt feladattól függ.  Az egyidejűség növelhető, ha végrehajtónként kevesebb memóriát oszt ki.  Ha a feladat futtatásakor memóriakivételeket lát, akkor növelnie kell a paraméter értékét.  Az egyik alternatíva az, hogy több memóriát kap egy nagyobb mennyiségű memóriával vagy a fürt méretének növelésével.  Több memória lehetővé teszi, hogy több végrehajtók kell használni, ami azt jelenti, több egyidejűség.

**3. lépés: Állítsa be a végrehajtó-magok** – Az I/O-intenzív számítási feladatok, amelyek nem rendelkeznek összetett műveletek, érdemes kezdeni a végrehajtó-magok nagy számú végrehajtó-magok számának növelése a párhuzamos feladatok végrehajtónként.  A végrehajtómagok beállítása 4-re jó kezdet.   

    executor-cores = 4
A végrehajtómagok számának növelése több párhuzamosságot biztosít, így kísérletezhet a különböző végrehajtómagokkal.  Az összetettebb műveleteket tartalmazó feladatok esetében csökkentenie kell a végrehajtónkénti magok számát.  Ha a végrehajtómagok 4-nél nagyobbra vannak állítva, akkor a szemétgyűjtés nem hatékony, és ronthatja a teljesítményt.

**4. lépés: Határozza meg a YARN memória mennyiségét a fürtben** – Ez az információ az Ambari nyelven érhető el.  Nyissa meg a YARN lapot, és tekintse meg a Configs lapot.  Ebben az ablakban megjelenik a YARN memória.  
Megjegyzés: Az ablakban az alapértelmezett YARN-tárolóméretet is láthatja.  A YARN tároló mérete megegyezik a végrehajtó paraméterenkénti memóriával.

    Total YARN memory = nodes * YARN memory per node
**5. lépés: Számvégrehajtók számítása**

**Memóriamegkötés számítása** – A num-executors paramétert a memória vagy a PROCESSZOR korlátozza.  A memóriamegkötést az alkalmazáshoz rendelkezésre álló YARN memória mennyisége határozza meg.  A teljes YARN memóriát úgy kell venni, és el kell osztania azt a végrehajtó-memóriával.  A megkötést az alkalmazások számához képest le kell csökkenteni, hogy elosszuk az alkalmazások számával.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**CPU-megkötés számítása** – A CPU-megkötés számítása úgy történik, hogy az összes virtuális mag osztva a végrehajtónkénti magok számával.  Minden fizikai maghoz 2 virtuális mag tartoz.  A memóriamegkötéshez hasonlóan el kell osztanunk az alkalmazások számával.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Set num-executors** – A num-executors paraméter határozza meg azáltal, hogy a minimális a memória megszorítás és a CPU-megkötés. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
A num-executorok nagyobb számának beállítása nem feltétlenül növeli a teljesítményt.  Figyelembe kell vennie, hogy további végrehajtók hozzáadása további többletterhelést minden további végrehajtó, amely potenciálisan ronthatja a teljesítményt.  A num-executorokat a fürt erőforrásai határolja.    

## <a name="example-calculation"></a>Példa számítása

Tegyük fel, hogy jelenleg 8 D4v2 csomópontból álló fürttel rendelkezik, amely 2 alkalmazást futtat, beleértve azt is, amelyet futtatni fog.  

**1. lépés: Határozza meg, hogy hány alkalmazás fut a fürtön** – tudja, hogy 2 alkalmazás van a fürtön, beleértve azt is, amelyet futtatni fog.  

**2. lépés: Állítsa be a végrehajtó-memória** – ebben a példában azt állapítjuk meg, hogy 6 GB végrehajtó-memória elegendő lesz az I / O intenzív feladat.  

    executor-memory = 6GB
3. lépés: Állítsa be a végrehajtó-magok – Mivel ez egy I/O-intenzív feladat, beállíthatjuk, hogy az egyes végrehajtók magok száma **4.Step 3: Set executor-cores** – Since this is a I/O intensive job, we can set the number of cores for each executor to 4.  Ha a végrehajtónkénti magokat 4-nél nagyobbra állítja, az szemétgyűjtési problémákat okozhat.  

    executor-cores = 4
**4. lépés: Határozza meg a YARN memória mennyiségét a fürtben** – Az Ambari-ba navigálunk, hogy megtudjuk, hogy minden D4v2 25 GB YARN memóriával rendelkezik.  Mivel 8 csomópont van, a rendelkezésre álló YARN memória megszorozva 8-val.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**5. lépés: Számítás num-executors** – A num-executors paraméter határozza meg azáltal, hogy a minimális a memória megszorítás és a CPU-megkötés osztva a # futó alkalmazások Spark.    

**Memóriamegkötés számítása** – A memóriamegkötés számítása úgy történik, hogy a yarn memória és a végrehajtónkénti memória hányadosa.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**CPU-megkötés számítása** – A CPU-megkötés számítása úgy történik, hogy a teljes fonalmagok osztva vannak a végrehajtónkénti magok számával.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Num-végrehajtók beállítása**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    


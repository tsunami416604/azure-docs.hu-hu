---
title: 'Teljesítmény finomhangolása: MapReduce, HDInsight & Azure Data Lake Storage Gen2 | Microsoft dokumentumok'
description: Az Azure Data Lake Storage Gen2 mapreduce teljesítményhangolási irányelvei
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a3ea6858355d6cb921f629bf36134d96371f6244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327931"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>Teljesítmény finomhangolása: MapReduce, HDInsight & Azure Data Lake Storage Gen2

A Map Reduce feladatok teljesítményének finomhangolásakor figyelembe veheti azokat a tényezőket, amelyeket figyelembe kell vennie. Ez a cikk a teljesítményhangolási irányelvek számos sorát ismerteti.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Storage Gen2 fiók.** A létrehozásról a [rövid útmutató: Azure Data Lake Storage Gen2 tárfiók létrehozása](data-lake-storage-quickstart-create-account.md)című témakörben talál útmutatást.
* **Azure HDInsight-fürt,** amely hozzáférést biztosít a Data Lake Storage Gen2-fiókhoz. Lásd: [Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **A MapReduce használata a HDInsight on**.  További információ: [MapReduce használata a Hadoopban a HDInsight-on](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Teljesítményhangolási irányelvek a Data Lake Storage Gen2 szolgáltatáshoz.**  Az általános teljesítménykoncepciókról a [Data Lake Storage Gen2 teljesítményhangolási útmutatócímű témakörben talál.](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Paraméterek

A MapReduce feladatok futtatásakor az alábbiakban azokat a paramétereket állíthatja be a Data Lake Storage Gen2 teljesítményének növelésére:

* **Mapreduce.map.memory.mb** – Az egyes leképezők számára lefoglalandó memória mennyisége
* **Mapreduce.job.maps** – A feladatonkénti térképfeladatok száma
* **Mapreduce.reduce.memory.mb** – Az egyes szűkítők számára lefoglalandó memória mennyisége
* **Mapreduce.job.reduces** – A feladatok száma feladatonként

**Mapreduce.map.memory / Mapreduce.reduce.memory** Ezt a számot a térképhez és/vagy a feladat csökkentéséhez szükséges memória mennyisége alapján kell beállítani.  A mapreduce.map.memory és a mapreduce.reduce.memory alapértelmezett értékei a fonal konfigurációján keresztül tekinthetők meg Ambari nyelven.  Az Ambari ban keresse meg a YARN billentyűt, és tekintse meg a Configs lapot.  Megjelenik a YARN memória.  

**Mapreduce.job.maps / Mapreduce.job.reduces** Ez határozza meg a létrehozandó leképezők vagy szűkítők maximális számát.  A felosztások száma határozza meg, hogy hány leképező jön létre a MapReduce feladathoz.  Ezért előfordulhat, hogy kevesebb leképezőt kap, mint amennyit kért, ha a kért leképezők száma kevesebb.       

## <a name="guidance"></a>Útmutatás

> [!NOTE]
> A jelen dokumentumban található útmutató feltételezi, hogy az alkalmazás az egyetlen, amely a fürtön fut.

**1. lépés: A futó feladatok számának meghatározása**

Alapértelmezés szerint a MapReduce a teljes fürtöt fogja használni a feladathoz.  Kevesebb fürtöt használhat kevesebb leképező használatával, mint amennyi elérhető tárolók.        

**2. lépés: Set mapreduce.map.memory/mapreduce.reduce.memory**

A térkép és a feladatok csökkentéséhez szükséges memória mérete az adott feladattól függ.  Csökkentheti a memória méretét, ha növelni szeretné az egyidejűséget.  Az egyidejűleg futó feladatok száma a tárolók számától függ.  A leképezőnkénti vagy szűkítőnkénti memória mennyiségének csökkentésével több tároló hozható létre, amelyek lehetővé teszik, hogy több leképező vagy szűkítő egyidejűleg fusson.  A memória mennyiségének túl nagy mértékű csökkentése egyes folyamatok memóriavesztését okozhatja.  Ha halommemória-hibát észlel a feladat futtatásakor, növelnie kell a leképezőnkénti vagy szűkítőnkénti memóriát.  Érdemes figyelembe venni, hogy további tárolók hozzáadása további többletterhelést minden további tároló, amely potenciálisan ronthatja a teljesítményt.  Egy másik alternatíva, hogy több memóriát kap egy olyan fürt használatával, amely nagyobb mennyiségű memóriát használ, vagy növeli a csomópontok számát a fürtben.  Több memória lehetővé teszi, hogy több tárolót kell használni, ami azt jelenti, több egyidejűség.  

**3. lépés: A yarn teljes memóriájának meghatározása**

A mapreduce.job.maps/mapreduce.job.reduces hangolásához vegye figyelembe a használható yarn memória teljes mennyiségét.  Ez az információ elérhető Ambari.  Nyissa meg a YARN lapot, és tekintse meg a Configs lapot.  Ebben az ablakban megjelenik a YARN memória.  A YARN memóriát meg kell szorozni a fürtben lévő csomópontok számával a teljes YARN memória leolvasásához.

    Total YARN memory = nodes * YARN memory per node

Ha üres fürtöt használ, akkor a memória lehet a fürt teljes YARN memóriája.  Ha más alkalmazások is használnak memóriát, akkor dönthet úgy, hogy csak a fürt memóriájának egy részét használja a leképezők vagy szűkítők számának a használni kívánt tárolók számára való csökkentésével.  

**4. lépés: Számítsa ki a YARN tartályok számát**

Yarn tárolók diktálják a feladathoz rendelkezésre álló egyidejűség mennyiségét.  Vegye ki a teljes YARN memóriát, és ossza el mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**5. lépés: Set mapreduce.job.maps/mapreduce.job.reduces**

Állítsa be a mapreduce.job.maps/mapreduce.job.reduces beállítását legalább a rendelkezésre álló tárolók számára.  Kísérletezhet tovább a leképezők és szűkítők számának növelésével, hogy lássa, jobb teljesítményt érhet-e el.  Ne feledje, hogy több leképező lesz további többletterhelést, így miután túl sok leképező ronthatja a teljesítményt.  

A PROCESSZOR-ütemezés és a PROCESSZOR elkülönítése alapértelmezés szerint ki van kapcsolva, így a YARN-tárolók számát a memória korlátozza.

## <a name="example-calculation"></a>Példa számítása

Tegyük fel, hogy 8 D14 csomópontból álló fürttel rendelkezünk, és i/o-intenzív feladatot szeretnénk futtatni.  Itt vannak a számításokat meg kell tennie:

**1. lépés: A futó feladatok számának meghatározása**

Ebben a példában tegyük fel, hogy a mi feladatunk az egyetlen futó feladat.  

**2. lépés: Set mapreduce.map.memory/mapreduce.reduce.memory**

Ebben a példában egy I/O-intenzív feladatot futtatunk, és úgy döntünk, hogy elegendő 3 GB memóriát elegendő lesz a térképfeladatokhoz.

    mapreduce.map.memory = 3GB

**3. lépés: A yarn teljes memóriájának meghatározása**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**4. lépés: Számítsa ki a YARN konténerek #**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**5. lépés: Set mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Példák a futtatáshoz

A MapReduce data lake storage gen2-en való futtatásának bemutatásához az alábbiakban néhány olyan mintakód látható, amelyet a következő beállításokkal futtattak egy fürtön:

* 16 csomópont D14v2
* HDI 3.6-os futtatásakor működő Hadoop-fürt

A kiindulási pont, itt van néhány példa parancsok futtatásához MapReduce Teragen, Terasort és Teravalidate.  Ezeket a parancsokat az erőforrások alapján módosíthatja.

**Teragen között**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Terasort között**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teravalid**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate

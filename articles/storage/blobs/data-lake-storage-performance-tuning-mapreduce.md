---
title: Azure Data Lake Storage Gen2 MapReduce teljesítmény-hangolási útmutatója | Microsoft Docs
description: Azure Data Lake Storage Gen2 MapReduce teljesítményének finomhangolására vonatkozó irányelvek
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3bd73b62b8859ffc5a71f610ebbdb55705284a76
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855516"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Teljesítmény-finomhangolási útmutató a HDInsight és Azure Data Lake Storage Gen2 MapReduce

Ismerje meg azokat a tényezőket, amelyeket figyelembe kell vennie a Térkép teljesítményének csökkentése érdekében. Ez a cikk a teljesítmény-hangolási irányelvek széles körét ismerteti.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Storage Gen2-fiók**. A létrehozásával kapcsolatos utasításokért lásd [: gyors útmutató: Hozzon létre egy Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)Storage-fiókot.
* **Azure HDInsight-fürt** Data Lake Storage Gen2 fiókhoz való hozzáféréssel. Lásd: [Azure Data Lake Storage Gen2 használata az Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) -fürtökkel
* **A MapReduce használata a HDInsight-on**.  További információ: [MapReduce használata a Hadoop on HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Teljesítmény-finomhangolási irányelvek a Data Lake Storage Gen2**.  Az általános teljesítménnyel kapcsolatos fogalmakat lásd: [Data Lake Storage Gen2 teljesítmény-finomhangolási útmutató](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Paraméterek

A MapReduce-feladatok futtatásakor a Data Lake Storage Gen2 teljesítményének növeléséhez konfigurálható paraméterek:

* **MapReduce. map. Memory. MB** – az egyes leképezések számára lefoglalható memória mennyisége
* **MapReduce. job. Maps** – feladat-hozzárendelési feladatok száma
* **MapReduce. csökkentse. Memory. MB** – az egyes redukálók számára lefoglalható memória mennyisége
* **MapReduce. job.** csökkentsük – a feladatok csökkentésének száma feladat alapján

**MapReduce. map. Memory/MapReduce. csökkentse a memóriát** Ezt a számot úgy kell beállítani, hogy mennyi memóriát igényel a Térkép és/vagy a feladat csökkentése.  A MapReduce. map. Memory és a MapReduce. csökkentse. memória alapértelmezett értékei a Ambari a fonal konfigurációján keresztül tekinthetők meg.  A Ambari-ben navigáljon a fonal lapra, és tekintse meg a konfigurációk lapot.  Ekkor megjelenik a szál memóriája.  

**MapReduce. job. Maps/MapReduce. job. csökkentse** Ez határozza meg a létrehozandó leképezések vagy szűkítők maximális számát.  A felosztások száma határozza meg, hogy a MapReduce-feladatokhoz hány leképezést hoz létre a rendszer.  Ezért előfordulhat, hogy kevesebb leképezést kap, mint amennyit kért, ha a kért adatleképezések száma kevesebb.       

## <a name="guidance"></a>Útmutatás

> [!NOTE]
> A jelen dokumentumban szereplő útmutatás azt feltételezi, hogy az alkalmazás az egyetlen, a fürtön futó alkalmazás.

**1. lépés: A futó feladatok számának meghatározása**

Alapértelmezés szerint a MapReduce a teljes fürtöt fogja használni a feladatokhoz.  Az elérhető tárolók közül kevesebbet használhat a fürt kevesebb leképező használatával.        

**2. lépés: Állítsa be a MapReduce. map. Memory/MapReduce. csökkentse a memóriát**

A térképhez és a tevékenységek csökkentéséhez használt memória mérete az adott feladattól függ.  Ha a párhuzamosságot szeretné bővíteni, csökkentheti a memória méretét.  Az egyidejűleg futó feladatok száma a tárolók számától függ.  Ha csökkenti a memória mennyiségét a leképező vagy a csökkentő memóriában, több tárolót is létrehozhat, amelyek lehetővé teszik, hogy egyidejűleg több leképezést vagy szűkítőt futtasson.  A túl sok memória mennyiségének csökkentése miatt előfordulhat, hogy bizonyos folyamatok elfogynak a memóriából.  Ha a feladatok futtatásakor egy halom hibaüzenetet kap, növelje a memóriát a leképező vagy a csökkentő alapján.  Érdemes figyelembe vennie, hogy a további tárolók hozzáadásával további terhelések is megadhatók, ami potenciálisan csökkentheti a teljesítményt.  Egy másik alternatíva, hogy több memóriát kell használnia egy olyan fürt használatával, amely nagyobb mennyiségű memóriával rendelkezik, vagy növeli a fürt csomópontjainak számát.  Több memória használata több tároló használatát teszi lehetővé, ami nagyobb párhuzamosságot jelent.  

**3. lépés: A fonal teljes memóriájának meghatározása**

A MapReduce. job. Maps/MapReduce. job. reakcióhoz. csökkentse a rendelkezésre álló szál teljes memóriájának mennyiségét.  Ez az információ a Ambari-ben érhető el.  Navigáljon a FONALhoz, és tekintse meg a konfigurációk lapot.  Ebben az ablakban a szál memóriája jelenik meg.  A fonalak memóriáját a fürtben lévő csomópontok számával kell szorozni a teljes fonal memóriájának beolvasásához.

    Total YARN memory = nodes * YARN memory per node

Ha üres fürtöt használ, a memória a fürt teljes FONALának memóriája lehet.  Ha más alkalmazások használják a memóriát, akkor úgy is dönthet, hogy csak a fürt memóriájának egy részét használja. ehhez csökkentse a használni kívánt tárolók számát.  

**4. lépés: A FONALas tárolók számának kiszámítása**

A FONALas tárolók a feladatokhoz rendelkezésre álló Egyidejűség mennyiségét írják le.  A fonal teljes memóriájának és osztásának elvégzése a MapReduce. map. Memory használatával.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**5. lépés: Állítsa be a MapReduce. job. Maps/MapReduce. job. csökkentse**

Állítsa be a MapReduce. job. Maps/MapReduce. job parancsot, és csökkentse legalább a rendelkezésre álló tárolók számát.  További kísérletet tehet a mappers és a szűkítők számának növelésével, hogy megtudja, jobb teljesítményt érhet-e el.  Ne feledje, hogy a további leképezések további terheléssel fognak rendelkezni, így túl sok Mapper is csökkentheti a teljesítményt.  

A CPU-ütemezés és a CPU-elkülönítés alapértelmezés szerint ki van kapcsolva, így a szálak tárolóinak száma korlátozott a memóriában.

## <a name="example-calculation"></a>Példa a számításra

Tegyük fel, hogy egy 8 D14-csomópontból álló fürttel rendelkezünk, és egy I/O-igényes feladatot szeretnénk futtatni.  A következő számításokat kell végrehajtania:

**1. lépés: A futó feladatok számának meghatározása**

Ebben a példában feltételezzük, hogy az egyetlen futó feladatot felhasználjuk.  

**2. lépés: Állítsa be a MapReduce. map. Memory/MapReduce. csökkentse a memóriát**

Ebben a példában egy I/O-igényes feladatot futtatunk, és eldöntjük, hogy a térképi feladatokhoz szükséges 3GB memória elegendő lesz-e.

    mapreduce.map.memory = 3GB

**3. lépés: A fonal teljes memóriájának meghatározása**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**4. lépés: A FONALas tárolók számának kiszámítása**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**5. lépés: Állítsa be a MapReduce. job. Maps/MapReduce. job. csökkentse**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Futtatási példák

Ha szeretné bemutatni, hogy a MapReduce hogyan fut Data Lake Storage Gen2on, az alábbiakban egy olyan mintakód található, amely egy fürtön futott a következő beállításokkal:

* 16 csomópontos D14v2
* Hadoop-fürt, amely HDI 3,6-t futtat

A kiindulási pontnál Íme néhány példa a MapReduce Teragen, a Terasort és a Teravalidate futtatására szolgáló parancsokra.  Ezeket a parancsokat az erőforrások alapján módosíthatja.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate

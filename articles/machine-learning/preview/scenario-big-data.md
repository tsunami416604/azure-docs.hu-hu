---
title: "Kiszolgáló Munkaterhelés előrejelzése terabájtos adatkészleteket - Azure a |} Microsoft Docs"
description: "How Azure Machine Learning munkaterület használatával a big data a gépi tanulási modell betanításához."
services: machine-learning
documentationcenter: 
author: daden
manager: mithal
editor: daden
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ms.openlocfilehash: c7ed8e695097d0cf2f5c99f8ccf3378c4e553c3b
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="server-workload-forecasting-on-terabytes-of-data"></a>A több terabájtnyi adatot feldolgozó kiszolgálói számítási feladatok előrejelzése

Ez a cikk ismerteti, adatszakértőkön használatát Azure Machine Learning-munkaterület fejlesztéséhez megoldások, amelyek a big Data típusú adatok használata szükséges. Indítson el egy nagy adatkészlet minta az, adatok előkészítése, a szolgáltatás műszaki osztály és a gépi tanulás iterációt, és ezután bővítse a folyamat a teljes nagy adatkészletben. 

Elsajátíthatja a Machine Learning-munkaterület az alábbi főbb képességeket:
* A számítási célok között válthat. Állítsa be a különböző számítási célokat, és azok kísérletezhet használatát. Ebben a példában egy Ubuntu DSVM és az Azure HDInsight-fürtök akár használhatja a számítási célokat. Beállíthatja úgy is a számítási célokat, attól függően, hogy az erőforrások rendelkezésre állását. Különösen után kiterjesztése a Spark-fürtön a több munkavégző csomópontokhoz, használhatja a Machine Learning-munkaterület erőforrásoknak kísérlet futtatása felgyorsítása érdekében.
* Az előzmények követésének futtassa. Machine Learning munkaterület segítségével nyomon követheti a gépi tanulási modellek és más metrikákkal érdeklő teljesítményét.
* A machine learning modell operationalization. A Machine Learning munkaterületen beépített eszközei segítségével képzett gépi tanulási modell Azure tárolószolgáltatás egy webszolgáltatás telepítése. A webszolgáltatás segítségével is REST API-hívások keresztül mini kötegelt előrejelzéseket beolvasása. 
* Terabájt adatok támogatása.

> [!NOTE]
> Kódminták és egyéb kapcsolódó ebben a példában, lásd: [GitHub](https://github.com/Azure/MachineLearningSamples-BigData).
> 

## <a name="use-case-overview"></a>Használja az eset áttekintése

Az előrejelzés a munkaterhelést kiszolgáló technológia cégek, amelyek a saját infrastruktúra kezelése általános üzleti szükség. Infrastrukturális költségeinek csökkentése érdekében használja a kiszolgálókon futó szolgáltatások kell csoportosítja a gépek kevesebb futtatnak. Szolgáltatások színvonalát kiszolgálókon futó a további gépek futtatásához meg kell adni. 

Ebben a forgatókönyvben koncentrálhat munkaterhelés előrejelzés minden gépet (vagy kiszolgáló). Különösen használhatja a munkamenetadatok az egyes kiszolgálókon előre jelezni jövőbeli annak a kiszolgálónak a munkaterhelési osztály. A terhelés, az egyes kiszolgálók alacsony, közepes és magas osztályok azokat a véletlenszerű erdő osztályozó használatával besorolása [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html). Gépi tanulási módszerek és a munkafolyamat ebben a példában egyszerűen bővíthető további hasonló problémákat okozhatnak. 


## <a name="prerequisites"></a>Előfeltételek

Ez a példa futtatásához az Előfeltételek a következők:

* Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverzió érhetők el).
* Egy telepített példánya [Machine Learning-munkaterület](./overview-what-is-azure-ml.md). A program telepítéséhez, és hozzon létre egy munkaterületet, tekintse meg a [gyors üzembe helyezés a telepítési útmutató](./quickstart-installation.md).
* Windows 10 (a példában szereplő utasításokat általában azonosak macOS rendszerekhez).
* Adatok tudományos virtuális gép (DSVM) Linux (Ubuntu). Megadhat egy Ubuntu DSVM következő [ezeket az utasításokat](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-provision-vm). Azt is láthatja, [a gyors üzembe helyezés](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu). Legalább 8 maggal és 32 GB memóriát a virtuális gép használatát javasoljuk. A DSVM IP-cím, a felhasználónév és a jelszó próbálhatja ki az ebben a példában van szüksége. A következő tábla mentése a későbbi lépésekben DSVM információval:

 Mező neve| Érték |  
 |------------|------|
DSVM IP-cím | xxx|
 Felhasználónév  | xxx|
 Jelszó   | xxx|

 Használja a virtuális gép, és választhatja [Docker-motorhoz](https://docs.docker.com/engine/) telepítve.

* HDInsight Spark-fürtöt, Hortonworks Data Platform 3.6 és Spark-verzióval rendelkező 2.1.x. Látogasson el [Apache Spark-fürt létrehozása az Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) a HDInsight-fürtök létrehozása vonatkozó további információért. Az egyes munkavégző 16 maggal és 112 GB memóriát a három-munkavégző fürt használatát javasoljuk. Vagy egyszerűen kiválaszthatja a virtuális gép típusát `D12 V2` az átjárócsomópont, és `D14 V2` a munkavégző csomópont. A fürt központi telepítése nagyjából 20 percet vesz igénybe. A fürt nevét, az SSH-felhasználónév és a jelszó próbálhatja ki az ebben a példában van szüksége. Mentse a következő táblázat a későbbi lépésekben az Azure HDInsight fürt adatai:

 Mező neve| Érték |  
 |------------|------|
 Fürt neve| xxx|
 Felhasználónév  | xxx (sshuser alapértelmezés szerint)|
 Jelszó   | xxx|


* Egy Azure Storage-fiókot. Követésével [ezeket az utasításokat](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) kattintva létrehozhat egyet. Is, hozzon létre két titkos blob tárolók nevében `fullmodel` és `onemonthmodel` az ezt a tárfiókot. A tárfiók tevékenységgel menthetők a köztes számítási eredmények és a gépi tanulási modellek. A tárfiók nevét és a hozzáférési kulcsot próbálhatja ki az ebben a példában van szüksége. A következő tábla mentése a későbbi lépésekben az Azure storage-fiók információval:

 Mező neve| Érték |  
 |------------|------|
 Tárfiók neve| xxx|
 A hozzáférési kulcsot  | xxx|


Az Ubuntu DSVM és az előfeltétel-ellenőrzési lista létrehozása az Azure HDInsight-fürt számítási célokat. Számítási célpontjai a számítási erőforrással a környezetben a Machine Learning-munkaterület, amely eltérhet a munkaterületet futtató számítógépre.   

## <a name="create-a-new-workbench-project"></a>Új munkaterület-projekt létrehozása

Hozzon létre egy új projektet ebben a példában egy sablon segítségével:
1.  Nyissa meg a gépi tanulási munkaterületet.
2.  Az a **projektek** lapon jelölje be a  **+**  aláírásához, és válassza ki **új projekt**.
3.  Az a **új projekt létrehozása** ablaktáblán, töltse ki az adatokat az új projekt.
4.  Az a **keresési Projektsablonjai** keresési mezőbe, írja be **Munkaterhelés előrejelzése terabájt adatokon**, és válassza ki a sablont.
5.  Kattintson a **Létrehozás** gombra.

Létrehozhat egy munkaterület-projektet egy előre létrehozott git-tárház következő [ezeket az utasításokat](./tutorial-classifying-iris-part-1.md).  
Futtatás `git status` verziójához nyomkövetési fájlok állapotának vizsgálata.

## <a name="data-description"></a>Adatok leírása

Ebben a példában használt adatok szintetizált kiszolgáló-munkaterhelési adatok. Egy Azure Blob storage-fiókot, amely nyilvánosan hozzáférhető lévő található. A speciális tárolási fiók adatainak megtalálhatók a `dataFile` mezőjében [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json). Az adatok közvetlenül a Blob-tároló is használhatja. Ha a tárolót használja a rendszer sok felhasználó által egy időben, [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) az adatok letöltése a saját tárba. 

Az összes adat mérete körülbelül 1 TB. Minden fájl körülbelül 1 – 3 GB-tal, és a CSV fájlformátum, fejléc nélküli. Minden egyes soraiban levő adatok a terhelés, az adott kiszolgálón tranzakció jelöli. A részletes információkat az adatok séma a következőképpen történik:

Oszlopszám | Mező neve| Típus | Leírás |  
|------------|------|-------------|---------------|
1  | `SessionStart` | Dátum és idő |    Munkamenet kezdési ideje
2  |`SessionEnd`    | Dátum és idő | Munkamenet befejezése
3 |`ConcurrentConnectionCounts` | Egész szám | Egyidejű kapcsolatok száma
4 | `MbytesTransferred` | Dupla | Normalizált adatforgalmat mérete (MB)
5 | `ServiceGrade` | Egész szám |  A munkamenet szolgáltatáshoz osztály
6 | `HTTP1` | Egész szám|  Munkamenet HTTP1 vagy HTTP2 használ
7 |`ServerType` | Egész szám   |Kiszolgáló típusa
8 |`SubService_1_Load` | Dupla |   Subservice 1 betöltése
9 | `SubService_1_Load` | Dupla |  Subservice 2 betöltése
10 | `SubService_1_Load` | Dupla |     Subservice 3 betöltése
11 |`SubService_1_Load` | Dupla |  Subservice 4 betöltése
12 | `SubService_1_Load`| Dupla |      Subservice 5 betöltése
13 |`SecureBytes_Load`  | Dupla | Biztonságos bájt betöltése
14 |`TotalLoad` | Dupla | Kiszolgáló teljes terhelése
15 |`ClientIP` | Karakterlánc|    Ügyfél IP-címe
16 |`ServerIP` | Karakterlánc|    Kiszolgáló IP-címe



Vegye figyelembe, hogy a várt típusok az előző táblázatban láthatók. Hiányzó értékek és a szabálytalan Konfigurációadatok problémái miatt nem biztos, hogy az adattípusok ténylegesen van a várt módon van. Az adatfeldolgozás figyelembe kell venniük a. 


## <a name="scenario-structure"></a>A forgatókönyv struktúra

Ebben a példában a fájlokat az alábbiak szerint vannak rendszerezve.

| Fájlnév | Típus | Leírás |
|-----------|------|-------------|
| `Code` | Mappa | A mappa tartalmaz a példában a kódot. |
| `Config` | Mappa | A mappa a konfigurációs fájlok találhatók. |
| `Image` | Mappa | A lemezképek az információs fájl mentéséhez használt mappa. |
| `Model` | Mappa | A Blob storage-ból letöltött modell fájlok mentéséhez használt mappa. |
| `Code/etl.py` | Python-fájl | Adatok előkészítése és a szolgáltatás műszaki osztály használt Python fájlt. |
| `Code/train.py` | Python-fájl | A Python-fájl egy három-osztály multi-classfication modell betanításához használandó.  |
| `Code/webservice.py` | Python-fájl | A Python-fájl operationalization használatos.  |
| `Code/scoring_webservice.py` | Python-fájl |  A Python fájl data transformation használja, és a webes szolgáltatás hívása. |
| `Code/O16Npreprocessing.py` | Python-fájl | A használt scoring_webservice.py adatok előfeldolgozása Python-fájl.  |
| `Code/util.py` | Python-fájl | A Python-fájl olvasására vagy írására Azure-blobokat kódot tartalmazó.  
| `Config/storageconfig.json` | JSON-fájl | Az Azure blob-tároló, amely tárolja a köztes eredmények és a modell feldolgozása és képzési egy hónapos adatokon konfigurációs fájlját. |
| `Config/fulldata_storageconfig.json` | JSON-fájl | Az Azure blob-tároló, amely tárolja a köztes eredmények és a feldolgozás és képzési modell a teljes adatkészletet a konfigurációs fájl.|
| `Config/webservice.json` | JSON-fájl | Scoring_webservice.py konfigurációs fájlját.|
| `Config/conda_dependencies.yml` | YAM-fájl | A Conda függőségi fájlt. |
| `Config/conda_dependencies_webservice.yml` | YAM-fájl | A webszolgáltatás Conda függőségi fájlt.|
| `Config/dsvm_spark_dependencies.yml` | YAM-fájl | Ubuntu DSVM függőségi Spark-fájl. |
| `Config/hdi_spark_dependencies.yml` | YAM-fájl | A HDInsight Spark-fürt függőségi Spark-fájl. |
| `README.md` | Markdown-fájlként | Az információs markdown-fájl. |
| `Code/download_model.py` | Python-fájl | A Python fájlt a modell fájlokat letölteni az Azure blob egy helyi lemezre. |
| `Docs/DownloadModelsFromBlob.md` | Markdown-fájlként | A markdown-fájl, amely futtatásával kapcsolatos utasításokat tartalmazza `Code/download_model.py`. |



### <a name="data-flow"></a>Adatfolyam

A kód [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) adatokat tölt a nyilvánosan elérhető tárolóból (`dataFile` mezőjében [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)). Adatok előkészítése és a szolgáltatás műszaki osztály tartalmaz, és menti a köztes számítási eredmények és -modellek a saját személyes tárolóba. A kód [ `Code/train.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) betölti a köztes számítási eredmények személyes tárolójából. a több osztály besorolási modell betanítja és ír a betanított gépi tanulási modell a személyes tárolóba. 

Egy tároló egy hónapos adatkészlet és a teljes adatkészlet kísérletezhet a kísérleti kell használni. Az adatok és modellek Parquet fájlba menti, mert minden egyes fájl ténylegesen a tárolóban, több blobot tartalmazó mappa. Az eredményül kapott tároló a következőképpen néz ki:

| A BLOB előtag neve | Típus | Leírás |
|-----------|------|-------------|
| featureScaleModel | Parquet | Standard scaler modell numerikus szolgáltatások. |
| stringIndexModel | Parquet | String indexelő modell nem numerikus szolgáltatások.|
| oneHotEncoderModel|Parquet | Egy közbeni kódoló modell kategorikus szolgáltatások. |
| mlModel | Parquet | Betanított gépi tanulási modellek. |
| információ| Python körte fájl | Az átalakított adatok, beleértve a képzési start, betanításának vége, időtartam, a timestamp train-teszt felosztása és oszlopok indexelő és egy közbeni kódolást.

A fájlok és az előző táblázatban szereplő blobok operationalization érvényesek.


### <a name="model-development"></a>Modell fejlesztési

#### <a name="architecture-diagram"></a>Architektúradiagram


Az alábbi ábra bemutatja a Machine Learning-munkaterület használatával a modellezése a végpont munkafolyamat: ![architektúrája](media/scenario-big-data/architecture.PNG)

A következő szakaszokban megmutatjuk, a modell fejlesztési Machine Learning-munkaterület a távoli számítási cél funkcióinak használatával. Azt először kis mennyiségű mintaadatokat betölteni, és futtassa a parancsfájlt [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) egy Ubuntu DSVM gyors közelítés a. További korlátozhatja, hogy a munkát, mi [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) úgy, hogy gyorsabb közelítés felesleges argumentum. A végén a HDInsight-fürtök teljes adatokkal betanítása használjuk.     

A [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) fájl betölti és előkészíti az adatokat, és végrehajtja a szolgáltatás mérnöki csapathoz. Azt a két argumentumot fogad el:
* Egy konfigurációs fájl Blob tároló, a köztes számítási eredmények és -modellek tárolására.
* Egy hibakeresési config gyorsabb közelítés argumentum.

Az első argumentum `configFilename`, ahol tárolni a Blob storage-információkat, és az adatok betöltési helyének megadása a helyi konfigurációs fájlban van. Alapértelmezés szerint van [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json), és megjelenik a futtatásához egy hónapos adatok használhatók. Is magában foglalja az [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json), amely használja a teljes adatkészlet futtatásához szükséges. A tartalom a konfigurációban a következőképpen történik: 

| Mező | Típus | Leírás |
|-----------|------|-------------|
| StorageAccount | Karakterlánc | Az Azure Storage-fiók neve |
| storageContainer | Karakterlánc | A köztes eredmények tárolásához Azure Storage-fiók tároló |
| StorageKey tulajdonságát | Karakterlánc |Az Azure tárfiók_elérési_kulcsa |
| dataFile|Karakterlánc | Adatforrásfájlokat  |
| Időtartam| Karakterlánc | Az adatok az adatforrásfájlokat időtartama|

A módosítsa `Config/storageconfig.json` és `Config/fulldata_storageconfig.json` a tárfiókot, biztonságitár-kulcs és a köztes eredményeket tárolni a blob-tároló konfigurálása. Alapértelmezés szerint a blob-tároló, a futtatásához egy hónapos adatok van `onemonthmodel`, és a teljes adatkészlet futtatásához a blob tároló `fullmodel`. Ellenőrizze, hogy ezekhez a tárolókhoz két hoz létre a tárfiók. A `dataFile` mezőjét [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) konfigurálja, hogy milyen adatok betöltése a [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py). A `duration` mező konfigurálja a tartományát, az adatokat tartalmazza. Az időtartam értéke ONE_MONTH, ha az adatok betöltése az adatok hét fájlok között csak egy CSV-fájlt kell 2016. júniusi. Ha a teljes, a teljes adatkészlet (1 TB) be van töltve. Nem kell módosítani `dataFile` és `duration` a két konfigurációs fájlokban.

A második argumentum hibakeresési. FILTER_IP értékre állítaná lehetővé teszi, hogy egy gyorsabb munkamenetben. Ez a paraméter használata akkor hasznos, ha a parancsfájlok hibakeresése szeretné.

> [!NOTE]
> A következő parancsokat minden bármely argumentum változó helyére a tényleges értékével.
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>A Docker Ubuntu DSVM modell fejlesztése

#####  <a name="1-set-up-the-compute-target"></a>1. A számítási cél beállítása

Indítsa el a parancssorban a Machine Learning-munkaterület kiválasztásával **fájl** > **nyissa meg a parancssort**. Ezután futtassa: 

```az ml computetarget attach remotedocker --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password ```

A következő két fájlt a projekt aml_config mappában jönnek létre:

-  dockerdsvm.COMPUTE: Ez a fájl távoli végrehajtás cél a kapcsolat és a konfigurációs adatokat tartalmazza.
-  dockerdsvm.runconfig: ezt a fájlt egy olyan a munkaterületet üzemeltető alkalmazásban használt futtatási beállítások.

Keresse meg a dockerdsvm.runconfig, és módosítsa a konfigurációs mezők a következők:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

A projekt környezet előkészítése a következő futtatásával:

```az ml experiment prepare -c dockerdsvm```


A `PrepareEnvironment` true értéke esetén Machine Learning-munkaterület a futtatási környezetet hoz létre, amikor a feladat elküldése. `Config/conda_dependencies.yml`és `Config/dsvm_spark_dependencies.yml` a futtatási környezetet kell tartalmaznia. E két YMAL fájl szerkesztésével a Conda függőségeket, a Spark-konfiguráció és a külső függőségei bármikor módosíthatja. Ebben a példában a hozzáadott `azure-storage` és `azure-ml-api-sdk` a további Python-csomag `Config/conda_dependencies.yml`. Is hozzáadtunk `spark.default.parallelism`, `spark.executor.instances`, és `spark.executor.cores` a `Config/dsvm_spark_dependencies.yml`. 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2. Adatok előkészítése, a szolgáltatás fejlesztés a DSVM Docker

Futtassa a parancsfájlt `etl.py` DSVM Docker meg. Egy hibakeresési paraméter, amely az adott kiszolgálói IP-címekkel rendelkező betöltött adatok használja:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

Keresse meg a oldali panelen, és válassza ki **futtatása** futtatási előzményeinek megtekintéséhez `etl.py`. Láthatja, hogy a futási idő körülbelül két perc. Ha módosítania kell a kódot az új funkciókat tartalmaznak, gyorsabb iterációs biztosít, mint a második argumentum FILTER_IP biztosít. Szüksége lehet, hogy ezt a lépést többször is lefuthat a saját gépi tanulási a problémákat, az adatkészlet vizsgálatát, vagy hozzon létre új szolgáltatások meghatározásakor. 

A testre szabott korlátozás a terhelés, és, hogy milyen adatokat feldolgozni a további szűrési adatok felgyorsíthatja a iterációs folyamat a modell fejlesztés. Miközben kísérletezik, rendszeres időközönként mentse a módosításokat a kódban a Git-tárházba. Vegye figyelembe, hogy azt használja az alábbi kódot a `etl.py` a személyes tárolóba való hozzáférés engedélyezése:

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


Ezután futtassa a parancsfájlt `etl.py` DSVM Docker a hibakeresési paramétert FILTER_IP meg:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

Keresse meg a oldali panelen, és válassza ki **futtatása** futtatási előzményeinek megtekintéséhez `etl.py`. Figyelje meg, hogy a futási idő körülbelül négy perces van-e. Ebben a lépésben feldolgozott eredményét a tárolóba menti, és be töltve a train.py képzési. Emellett a string indexelő kódoló folyamatok és szabványos scalers menti a személyes tárolóba. Ezek a operationalization használhatók. 


##### <a name="3-model-training-on-dsvm-docker"></a>3. A DSVM Docker modell betanítási

Futtassa a parancsfájlt `train.py` DSVM Docker meg:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

Ez a lépés a köztes számítási eredmények betölti a futni, hanem a `etl.py`, és a gépi tanulási modellek betanítja. Ez a lépés körülbelül két percet vesz igénybe.

Sikeresen befejezte a kis adatokon kísérletezhet, ha továbbra is a kísérleti futtatnak majd a teljes adatkészlet. Elindítsa ugyanazt a kódot, majd argumentum kísérletezhet és számítási cél módosításokat.  

####  <a name="model-development-on-the-hdinsight-cluster"></a>Modell fejlesztése a HDInsight-fürt

##### <a name="1-create-the-compute-target-in-machine-learning-workbench-for-the-hdinsight-cluster"></a>1. A számítási cél Machine Learning-munkaterület létrehozása a HDInsight-fürt

```az ml computetarget attach cluster --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password```

A következő két fájlt a aml_config mappában jönnek létre:
    
-  myhdo.COMPUTE: Ez a fájl tartalmazza a távoli végrehajtás cél a kapcsolat és konfigurációs információt.
-  myhdi.runconfig: ezt a fájlt a munkaterületre alkalmazásban használt futtatási beállítások van beállítva.


Keresse meg a myhdi.runconfig, és módosítsa a konfigurációs mezők a következők:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

A projekt környezet előkészítése a következő futtatásával:

```az ml experiment prepare -c myhdi```

Ez a lépés akár hét percet is igénybe vehet.

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2. Adatok előkészítése, a szolgáltatás fejlesztés HDInsight-fürt

Futtassa a parancsfájlt `etl.py`, a HDInsight-fürt teljes adatokkal:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

Mivel ez a feladat (körülbelül két órás) viszonylag hosszú ideig tart, használhatja `-a` letiltása a kimeneti adatfolyam. Ha a feladat kész, a **futtatása előzmények**, megtekintheti az illesztőprogram, a tartományvezérlő naplókat. Ha automatikusan nagyobb fürt, újrakonfigurálhatja a konfigurációkat mindig `Config/hdi_spark_dependencies.yml` további példányok vagy magot használja. Például ha egy munkavégző – Négycsomópontos fürt, növelheti a értékének `spark.executor.instances` 5-7. Láthatja, hogy ez a lépés a kimenetét a **fullmodel** a tárfiókban lévő tároló. 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3. HDInsight-fürt tanítási modell

Futtassa a parancsfájlt `train.py` HDInsight-fürt:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

Mivel ez a feladat (körülbelül 30 percet) viszonylag hosszú ideig tart, használhatja `-a` letiltása a kimeneti adatfolyam.

#### <a name="run-history-exploration"></a>Futtassa az előzmények feltárása

Futtatási előzményei, lehetővé teszi a Machine Learning-munkaterület kísérletezhet nyomon követését. Alapértelmezés szerint azt a kísérleti időtartama követi nyomon. A konkrét példában, ha azt a teljes adatkészlet az `Code/etl.py` a kísérleti, az azt láthatja, időtartam jelentősen növeli. Adott mérőszámok követési célból is bejelentkezhet. Metrika követési engedélyezéséhez vegye fel az alábbi kódsorokat a Python-fájl vezetője:
```python
# import logger
from azureml.logging import get_azureml_logger

# initialize logger
run_logger = get_azureml_logger()
```
Íme egy példa egy adott metrika nyomon:

```python
run_logger.log("Test Accuracy", testAccuracy)
```

Keresse meg a munkaterületet üzemeltető jobb oldalsó sáv a **futtatása** megtekintéséhez a Python fájl futtatási előzményeit. A GitHub-tárházban is ugorhat. Egy új fiókirodai "AMLHistory," kezdetű névvel jön létre a parancsfájl minden egyes futtatásához a módosítások követésére. 


### <a name="operationalize-the-model"></a>Azok a modell

Ebben a szakaszban azok a modell webszolgáltatásként az előző lépésekben létrehozott. Is megismerheti, hogyan használható a webszolgáltatás terhelés előrejelzése. Gép nyelvi operationalization parancssori felületen (CLIs) használja a kód és a függőségek Docker képként csomagot, és közzéteheti a modell indexelése webszolgáltatásként. További információkért lásd: [Ez az Áttekintés](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/operationalization-overview.md).

Futtassa a CLIs használhatja a Machine Learning-munkaterület a parancssorba.  Futtathatja a CLIs Ubuntu Linux követve a [a telepítési útmutató](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/install-on-ubuntu-linux.md). 

> [!NOTE]
> Cserélje le minden argumentum változó a következő parancsokat az összes tényleges értékek. Ez a szakasz befejezéséhez készül a 40 percet vesz igénybe.
> 

Válasszon ki egy egyedi karakterlánc operationalization környezeti legyen. "[Egyedi]" karakterlánc itt választja szöveglánc használjuk.

1. A környezet operationalization, és az erőforráscsoport létrehozásához.

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   Megjegyzés: használható Tárolószolgáltatás, a környezet használatával `--cluster` a a `az ml env setup` parancsot. A gépi tanulási modell a üzembe [Azure Tárolószolgáltatás](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes). Használja [Kubernetes](https://kubernetes.io/) a telepítés, a méretezés és a felügyeleti indexelése alkalmazások automatizálásához. Ez a parancs körülbelül 20 percet vesz igénybe futtatásához. A következő segítségével ellenőrizheti, ha a telepítés sikeresen befejeződött: 

        az ml env show -g [unique]rg -n [unique]

   Állítsa be a telepítési környezet megegyezik a most létrehozott a következő futtatásával:

        az ml env set -g [unique]rg -n [unique]

2. Létrehozhat és használhat egy modell felügyeleti fiókja. A modell felügyeleti fiók létrehozásához futtassa az alábbi parancsot:

        az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   Használja a modell felügyeleti operationalization a következő futtatásával:

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   A modell felügyeleti fiók a modellek és a webes szolgáltatások kezelésére szolgál. Az Azure-portálon tekintheti meg egy új modell felügyeleti fiók létrejött. A modellek jegyzékfájlokban, Docker képek és szolgáltatásokról, amelyek a modell felügyeleti fiók használatával létrehozott látható.

3. Töltse le, és regisztrálja a modellek.

   Töltse le a modell a **fullmodel** tárolót, hogy a címtárban a kód a helyi számítógép. Ne töltse le a parquet adatfájlban a neve "vmlSource.parquet." Még nincs egy modellfájl; egy köztes számítás eredménye. A Git-tárházban szereplő szolgáltatásmodell-fájlokból is felhasználhatja. További információkért lásd: [GitHub](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md). 

   Lépjen a `Model` mappa a parancssori felület, és regisztrálja a modelleket, mint a következő:

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   Minden parancs ad Modellazonosító, a következő lépésben szükség van. Mentse a fájlt a későbbi használat érdekében.

4. A webszolgáltatás a jegyzékfájl létrehozása.

   A jegyzék a kódot a webszolgáltatás, minden a machine learning modellek és futásidejű környezet függőségeit tartalmazza. Lépjen a `Code` mappa a CLI-t, és futtassa az alábbi parancsot:

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   A kimenet a jegyzék Azonosítót ad a következő lépéssel. 

   Maradjon a `Code` könyvtárra, és webservice.py tesztelheti a következő futtatásával: 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Hozzon létre egy Docker-rendszerképet. 

        az ml image create -n [unique]image --manifest-id $manifestID

   A kimenet egy lemezkép-Azonosítót ad a következő lépés, a docker lemezképet tároló szolgáltatást használja. 

6. A webszolgáltatás üzembe helyezése a Tárolószolgáltatás-fürthöz.

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   A kimeneti biztosít egy szolgáltatást. A hitelesítési kulcs beszerzése és URL-címét kell.

7. A webszolgáltatás hívására Python-kódban mini-kötegelt pontozása céljából.

   Az alábbi parancs segítségével a hitelesítési kulcs beszerzése:

         az ml service keys realtime -i $ServiceID 

   Az alábbi parancs segítségével a szolgáltatás pontozás URL-cím beszerzése:

        az ml service usage realtime -i $ServiceID

   Módosítsa a tartalom `./Config/webservice.json` pontozás URL-cím és a hitelesítési kulcs megfelelő szolgáltatással. A "tulajdonos" ne az eredeti fájlt, és cserélje le a "xxx" rész. 
   
   Nyissa meg a projekt gyökérkönyvtárában, és tesztelje a webszolgáltatás minimális kötegelt pontozási használatával a következő:

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. A webszolgáltatás méretezni. 

   További információkért lásd: [operationalization meg az Azure Tárolószolgáltatás-fürt méretezése](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/how-to-scale.md).
 

## <a name="next-steps"></a>Következő lépések

Ez a példa emel ki, hogyan használható a Machine Learning munkaterület kell még betanítani a gépi tanulási modell a big data, és azok a betanított modell. Különösen megtudta, hogyan konfigurálja használni a különböző számítási célokat, és futtatásához előzményeinek nyomon követése a metrikák és különböző futtatásakor használja.

A kód segítségével megismerheti a kereszt-ellenőrzési és a hyper-paraméter hangolása terjeszthetők ki. Kereszt-ellenőrzési és a hyper-paraméter hangolásával kapcsolatos további tudnivalókért lásd: [a GitHub-erőforrás](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning).  

Idősorozat előrejelzés kapcsolatos további információkért lásd: [a GitHub-erőforrás](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting).

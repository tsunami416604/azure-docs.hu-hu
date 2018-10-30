---
title: Kiszolgálói számítási feladatok előrejelzése terabájtnyi adatot – Azure |} A Microsoft Docs
description: A big data, egy gépi tanulási modell betanításához az Azure Machine Learning Workbench használatával hogyan.
services: machine-learning
documentationcenter: ''
author: daden
manager: mithal
editor: daden
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: daden
ROBOTS: NOINDEX
ms.openlocfilehash: 0d3c6b78944d9365d1e7e88ed33aba852b71a9c1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232018"
---
# <a name="server-workload-forecasting-on-terabytes-of-data"></a>A több terabájtnyi adatot feldolgozó kiszolgálói számítási feladatok előrejelzése

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

Ez a cikk ismerteti, hogyan adatszakértők az Azure Machine Learning Workbench használatát a big Data-megoldások fejlesztéséhez. Indítsa el a nagyméretű mintát, iteráció az adat-előkészítési funkciófejlesztési és gépi tanulási és majd kiterjeszthetik az a folyamat a teljes nagyméretű adathalmazon. 

Megismerkedhet a Machine Learning Workbench alkalmazásban az alábbi fő lehetőségeket:
* Közötti könnyű váltás számítási céljainak. Állítsa be a különböző számítási célnak, és kísérletezés használni őket. Ebben a példában használhatja egy Ubuntu dsvm-hez és a egy Azure HDInsight-fürtöt, a számítási célokhoz. Beállíthatja, hogy a számítási célokhoz, rendelkezésre álló erőforrások függően. Különösen követően horizontális felskálázás a Spark-fürtöt, a további feldolgozó csomópontokat, használhatja a Machine Learning Workbench erőforrásai a Kísérletezési futtatások felgyorsítása érdekében.
* Futtassa a előzményeinek nyomon követése. Machine Learning Workbench használatával követheti nyomon a gépi tanulási modelleket és más érdekes mérőszám vizsgálatára.
* A gépi tanulási modellt az operacionalizáláshoz. A beépített eszközök Machine Learning Workbench használatával a betanított gépi tanulási a modellt webszolgáltatásként, amely az Azure Container Service üzembe helyezése. A webszolgáltatás segítségével REST API-hívások keresztül mini batch előrejelzés beolvasása. 
* Több terabájtnyi adat támogatása.

> [!NOTE]
> Kódminták és egyéb anyagok, ebben a példában kapcsolatos: [GitHub](https://github.com/Azure/MachineLearningSamples-BigData).
> 

## <a name="use-case-overview"></a>Használati eset – áttekintés

A számítási feladatok kiszolgálókon előrejelzés technológiai vállalatok, amelyek a saját infrastruktúra felügyelete közös üzleti szüksége. Infrastrukturális költségeinek csökkentése érdekében használja a kiszolgálókon futó szolgáltatások szerint kell csoportosítani együtt kevesebb gépeken futtathatók. A szolgáltatások színvonalát kiszolgálókon futó további gépek kell megadni. 

Ebben a forgatókönyvben összpontosíthat számítási feladatok előrejelzése minden gép (vagy kiszolgáló). Különösen használhatja a munkamenetadatok minden kiszolgálón előre jelezni a jövőbeli annak a kiszolgálónak a munkaterhelési osztály. A terhelés, az egyes kiszolgálók alacsony, közepes és magas osztályok, a véletlenszerű erdő osztályozó használatával besorolása [Apache Spark ML](https://spark.apache.org/docs/2.1.1/ml-guide.html). A machine learning-technikákat, és ebben a példában a munkafolyamat egyszerűen bővíthető más hasonló problémák. 


## <a name="prerequisites"></a>Előfeltételek

Ez a példa futtatásához az Előfeltételek a következők:

* Egy [Azure-fiók](https://azure.microsoft.com/free/) (az ingyenes próbaverziók érhető el).
* Egy telepített példánya [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md). Telepítse a programot, és hozzon létre egy munkaterületet, tekintse meg a [rövid telepítési útmutatójában](quickstart-installation.md). Ha több előfizetéssel rendelkezik, akkor az [állítsa be a kívánt előfizetést, az aktuális aktív előfizetést kell](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az_account_set).
* A Windows 10-es (az ebben a példában általában ugyanezek az utasítások érvényesek a macOS rendszerekhez).
* Egy adatelemzési virtuális gép (DSVM) Linux (Ubuntu), lehetőleg az USA keleti régiójában, ahol az adatokat keresi meg. A következő kiépíthet egy Ubuntu DSVM [ezek az utasítások](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Emellett megtekintheti [ebben a rövid útmutatóban](https://ms.portal.azure.com/#create/microsoft-ads.linux-data-science-vm-ubuntulinuxdsvmubuntu). Legalább 8 maggal és 32 GB memóriát a virtuális gép használatát javasoljuk. 

Kövesse a [utasítás](../desktop-workbench/known-issues-and-troubleshooting-guide.md#remove-vm-execution-error-no-tty-present) a virtuális Gépet a jelszó nélküli sudoer-hozzáférésének engedélyezésére vonatkozó AML Workbench.  Ha szeretné használni [SSH-alapú hitelesítés létrehozásáról és használatáról a virtuális gép AML workbenchben](experimentation-service-configuration.md#using-ssh-key-based-authentication-for-creating-and-using-compute-targets). Ebben a példában a jelszó eléri a virtuális Gépet használjuk.  Mentse az alábbi táblázat a DSVM-adatokat a későbbi lépésekben:

 Mező neve| Érték |  
 |------------|------|
A DSVM IP-cím | xxx|
 Felhasználónév  | xxx|
 Jelszó   | xxx|


 Választhatja az összes virtuális gép használata [Docker-motor](https://docs.docker.com/engine/) telepítve.

* HDInsight Spark-fürt 3.6-os verziója Hortonworks Data Platform és a Spark-verzió 2.1.x, lehetőség szerint az USA keleti régiójában, ahol az adatokat keresi meg. Látogasson el [Apache Spark-fürt létrehozása az Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters) HDInsight fürtök létrehozásával kapcsolatos további részletekért. Az egyes feldolgozói 16 maggal és 112 GB memóriával rendelkező egy három-feldolgozó fürt használatát javasoljuk. Vagy egyszerűen kiválaszthatja a virtuális gép típusa `D12 V2` a fő csomópontot és `D14 V2` a feldolgozó csomóponton. A fürt a telepítés körülbelül 20 percet vesz igénybe. A fürt nevét, SSH-felhasználónév és jelszó próbálhatja ki ebben a példában van szüksége. Mentse az alábbi táblázat a későbbi lépésekben az Azure HDInsight fürt adatai:

 Mező neve| Érték |  
 |------------|------|
 Fürt neve| xxx|
 Felhasználónév  | xxx (sshuser alapértelmezés szerint)|
 Jelszó   | xxx|


* Egy Azure Storage-fiók. Követheti [ezek az utasítások](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) hozhat létre egyet. Emellett létre két privát blob-tárolók a nevekkel `fullmodel` és `onemonthmodel` a tárfiók. A storage-fiók segítségével számítási köztes eredményeket és gépi tanulási modelleket. A tárfiók nevét és kulcsát próbálhatja ki ebben a példában van szüksége. Mentse az alábbi táblázat a következő későbbi lépésekben az Azure storage-fiók adatait:

 Mező neve| Érték |  
 |------------|------|
 Storage account name (Tárfiók neve)| xxx|
 Hozzáférési kulcs  | xxx|


Az Ubuntu dsvm-hez és a az előfeltétel-ellenőrzési lista hozta létre az Azure HDInsight-fürt is a számítási célokhoz. A számítási célok a számítási erőforrás keretén belül a Machine Learning Workbench, amely eltérhet a számítógép, amelyen fut a Workbench.   

## <a name="create-a-new-workbench-project"></a>Egy új Workbench-projekt létrehozása

Hozzon létre egy új projektet ebben a példában egy sablon segítségével:
1.  Nyissa meg a Machine Learning Workbenchet.
2.  Az a **projektek** lapon válassza ki a **+** aláírásához, és válassza ki **új projekt**.
3.  Az a **új projekt létrehozása** panelen adja meg az információkat az új projekt.
4.  Az a **projektsablonok keresése** írja be a keresőmezőbe **számítási feladatok előrejelzése több Terabájtnyi adat**, és válassza ki a sablont.
5.  Kattintson a **Létrehozás** gombra.

Létrehozhat egy Workbench-projekt előre létrehozott git-tárházat a következő [ezeket az utasításokat](./tutorial-classifying-iris-part-1.md).  
Futtatás `git status` a fájlok nyomon követése verzió állapotának vizsgálata.

## <a name="data-description"></a>Adatok leírása

Ebben a példában használt adatok szintetizált kiszolgáló-munkaterhelési adatok el. Egy Azure Blob storage-fiókot, amely USA keleti régiójában elérhető publicaly vannak tárolva. Az adott tárolási fiók adatai megtalálhatók a `dataFile` mezőjében [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json) formátumban "wasb: / /<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>". Az adatok közvetlenül a Blob storage-ból is használhatja. Ha a storage egyszerre több felhasználó használ, akkor használhatja [azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) saját színvonalú Kísérletezési a storage-bA az adatok letöltéséhez. 

Az összes adat mérete körülbelül 1 TB. Minden fájl körülbelül 1 – 3 GB, és a CSV fájlformátum, fejléc nélküli. Minden egyes sorára adatokat jelöli a terhelés egy adott kiszolgálón futó tranzakció. A részletes információkat a sémát a következőképpen történik:

Oszlopszám | Mező neve| Típus | Leírás |  
|------------|------|-------------|---------------|
1  | `SessionStart` | Dátum és idő |    Munkamenet kezdő időpontja
2  |`SessionEnd`    | Dátum és idő | Munkamenet vége
3 |`ConcurrentConnectionCounts` | Egész szám | Egyidejű kapcsolatok száma
4 | `MbytesTransferred` | Dupla | Normalizált adatátvitelre (MB)
5 | `ServiceGrade` | Egész szám |  Szolgáltatás szintű a munkamenet
6 | `HTTP1` | Egész szám|  Munkamenet HTTP1, vagy a HTTP2 használja.
7 |`ServerType` | Egész szám   |Kiszolgáló típusa
8 |`SubService_1_Load` | Dupla |   Subservice 1 betöltése
9 | `SubService_2_Load` | Dupla |  Subservice 2 betöltése
10 | `SubService_3_Load` | Dupla |     Subservice 3 betöltése
11 |`SubService_4_Load` | Dupla |  Subservice 4 betöltése
12 | `SubService_5_Load`| Dupla |      Subservice 5 betöltése
13 |`SecureBytes_Load`  | Dupla | Biztonságos bájt betöltése
14 |`TotalLoad` | Dupla | A kiszolgáló teljes terhelés
15 |`ClientIP` | Karakterlánc|    Ügyfél IP-címe
16 |`ServerIP` | Karakterlánc|    Kiszolgáló IP-címe



Vegye figyelembe, hogy a várt adattípusok alapján az előző táblázatban szerepel. Hiányzó értékeket, és szabálytalan Konfigurációadatok problémái miatt nem nem biztos, hogy az adattípusok ténylegesen vannak a várt módon. Adatok feldolgozása figyelembe kell venniük a. 


## <a name="scenario-structure"></a>A forgatókönyv-struktúra

Ebben a példában a fájlokat a következőképpen vannak rendszerezve.

| Fájlnév | Típus | Leírás |
|-----------|------|-------------|
| `Code` | Mappa | A mappa tartalmazza az összes, a példában szereplő kód. |
| `Config` | Mappa | A mappa a konfigurációs fájlokat tartalmazza. |
| `Image` | Mappa | A rendszerképeket az információs fájl mentéséhez használt mappa. |
| `Model` | Mappa | A Blob storage-ból letöltött modell fájlok mentéséhez használt mappa. |
| `Code/etl.py` | Soubor Pythonu | Az adat-előkészítési és funkciófejlesztési Python-fájlt. |
| `Code/train.py` | Soubor Pythonu | A Python-fájlt egy harmadik szintű multi-classfication modell betanításához használja.  |
| `Code/webservice.py` | Soubor Pythonu | A Python-fájlt az operacionalizáláshoz használni.  |
| `Code/scoring_webservice.py` | Soubor Pythonu |  A Python-fájlt adatátalakítás használja, és a webszolgáltatás hívásakor. |
| `Code/O16Npreprocessing.py` | Soubor Pythonu | A Python-fájlt a scoring_webservice.py az adatok előfeldolgozása használt.  |
| `Code/util.py` | Soubor Pythonu | A Python-fájlt, amely tartalmazza a kódot az Azure-blobok írásakor vagy olvasásakor.  
| `Config/storageconfig.json` | JSON-fájl | Az Azure blob-tároló, amely egy hónapig adatokat tárolja a köztes eredményeket és a modell feldolgozása és képzési konfigurációs fájljának. |
| `Config/fulldata_storageconfig.json` | JSON-fájl | A konfigurációs fájlt az Azure blob-tároló, amely tárolja a köztes eredményeket és a modell feldolgozása és képzés a teljes adatkészlet.|
| `Config/webservice.json` | JSON-fájl | A scoring_webservice.py konfigurációs fájlt.|
| `Config/conda_dependencies.yml` | YAML-fájl | A Conda-függőség fájlt. |
| `Config/conda_dependencies_webservice.yml` | YAML-fájl | A web service függőségi Conda-fájlt.|
| `Config/dsvm_spark_dependencies.yml` | YAML-fájl | Ubuntu DSVM függőségi Spark-fájlt. |
| `Config/hdi_spark_dependencies.yml` | YAML-fájl | A HDInsight Spark-fürt Spark függőségi fájlt. |
| `README.md` | Markdown-fájl | Az információs markdown-fájl. |
| `Code/download_model.py` | Soubor Pythonu | A Python-fájlt a modell fájlok letöltéséhez az Azure blob egy helyi lemezre. |
| `Docs/DownloadModelsFromBlob.md` | Markdown-fájl | A markdown-fájlban, hogyan futtathat vonatkozó utasításokat tartalmazó `Code/download_model.py`. |



### <a name="data-flow"></a>Az adatfolyam

A kód [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) adatokat tölt be a nyilvánosan elérhető tárolót (`dataFile` mezőjében [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldata_storageconfig.json)). Adat-előkészítési és funkciófejlesztési feladatok tartalmazza, és menti a köztes számítási eredményei és a modellek a saját privát tároló. A kód [ `Code/train.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/train.py) köztes számítás eredménye tölt be a személyes tárolót, betanítja a többcsoportos osztályozási modell és a személyes tárolóba ír a betanított machine learning-modellhez. 

Az egy hónapos adatkészlet és a egy másik, a teljes adathalmazon kísérletezéssel kísérletezéssel egy tárolót kell használnia. Az adatok és modellek menti a Parquet-fájlok, mert minden egyes fájl valójában a tárolóban, több blobokat tartalmazó mappa. Az eredményül kapott tárolót a következőképpen néz ki:

| Blobnév előtagja | Típus | Leírás |
|-----------|------|-------------|
| featureScaleModel | Parquet eszközökben | Standard szintű scaler modell numerikus funkciók. |
| stringIndexModel | Parquet eszközökben | A karakterlánc-indexelő modell nem numerikus funkciók.|
| oneHotEncoderModel|Parquet eszközökben | Egy gyakori kódoló modell kategorikus funkciók. |
| mlModel | Parquet eszközökben | Betanított gépi tanulási modell. |
| információ| Python pickle-fájl | Az átalakított adatok, beleértve a betanítási kezdő, képzések teljes, időtartam, jelölő időbélyegző információ tanítási és tesztelési felosztása és az indexelés és a egy gyakori kódolási oszlopok.

A fájlok és az előző táblázatban szereplő blobok az operacionalizáláshoz szolgálnak.


### <a name="model-development"></a>Modell fejlesztése

#### <a name="architecture-diagram"></a>Architektúradiagram


Az alábbi ábrán látható, a modell fejlesztése a Machine Learning Workbench használatával a végpontok közötti munkafolyamat: ![architektúra](media/scenario-big-data/architecture.PNG)

A következő szakaszokban bemutatjuk, a modell fejlesztési távoli számítási cél funkciója segítségével a Machine Learning Workbench alkalmazásban. Azt először egy kisebb mennyiségű mintaadatok betöltése, és futtassa a szkriptet [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) egy Ubuntu dsvm-hez a gyors ismétlését a. További korlátozhatja, hogy mi a munkahelyi [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) gyorsabb közelítés felesleges argumentum átadásával. A végén a egy HDInsight-fürtön az adatok teljes betanításához használjuk.     

A [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py) fájlt tölt be, és előkészíti az adatokat, és funkciófejlesztési végrehajtja. Két argumentumot fogad el:
* A Blob storage-tárolót, a köztes számítási eredmények és a modellek tárolására szolgáló konfigurációs fájl.
* A gyorsabb iteráció hibakeresési config argumentum.

Az első argumentum `configFilename`, ahol tárolni a Blob storage-adatokat, és adja meg az adatok betöltéséhez helyét egy helyi konfigurációs fájl. Alapértelmezés szerint a [ `Config/storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/storageconfig.json), és használható az egy hónapos adatok futtatni fogja. Emellett tartalmazza [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json), mert szüksége lesz a teljes adatkészlet futtatásához használandó. A tartalom a konfigurációban a következőképpen történik: 

| Mező | Típus | Leírás |
|-----------|------|-------------|
| storageAccount | Karakterlánc | Az Azure Storage-fiók neve |
| storageContainer | Karakterlánc | Az Azure Storage-fiók segítségével tárolja a köztes eredményeket tároló |
| StorageKey tulajdonságát | Karakterlánc |Az Azure Tárfiók hozzáférési kulcsát |
| dataFile|Karakterlánc | Forrásfájljainak adatok  |
| időtartam| Karakterlánc | Az adatok az adatforrásfájlokat időtartama|

A módosítsa `Config/storageconfig.json` és `Config/fulldata_storageconfig.json` konfigurálása a tárfiókot, tárkulcs és a blob-tároló tárolja a köztes eredményeket. Alapértelmezés szerint a blob-tároló futtatása egy hónapig adatok a `onemonthmodel`, és a blob-tároló, a teljes adatkészlet futtatásához `fullmodel`. Ellenőrizze, hogy ezen két tárolót hoz létre a storage-fiókban. A `dataFile` mező [ `Config/fulldata_storageconfig.json` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Config/fulldatastorageconfig.json) konfigurálja, hogy milyen adatok betöltése a [ `Code/etl.py` ](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Code/etl.py). A `duration` mezőben konfigurálja a tartomány szerepel. Az időtartam ONE_MONTH értékre van állítva, ha az adatok betöltése csak egy .csv fájlt az adatok hét fájlok között kell lennie a 2016. június. Ha a teljes, a teljes adatkészletet (1 TB) be van töltve. Nem kell módosítani `dataFile` és `duration` ezen két konfigurációs fájlokban.

A második argumentum értéke hibakeresési. Gyorsabb iteráció FILTER_IP állítja, lehetővé teszi. Ez a paraméter használata akkor hasznos, ha a parancsfájlok hibakeresése szeretné.

> [!NOTE]
> Az összes, a következő parancsokat cserélje le tényleges értékek bármely argumentum változó.
> 


#### <a name="model-development-on-the-docker-of-ubuntu-dsvm"></a>A Docker az Ubuntu DSVM modell fejlesztése

#####  <a name="1-set-up-the-compute-target"></a>1. Állítsa be a számítási célnak

Machine Learning Workbench parancssori először jelöljön ki **fájl** > **parancssor megnyitása**. Majd futtassa ezt: 

```az ml computetarget attach remotedocker --name dockerdsvm --address $DSVMIPaddress  --username $user --password $password ```

A következő két fájlt a projekt aml_config mappában jönnek létre:

-  dockerdsvm.COMPUTE: Ez a fájl egy távoli végrehajtási célként kapcsolat és a konfigurációs adatait tartalmazza.
-  dockerdsvm.runconfig: ezt a fájlt egy olyan futtatási lehetőségek, a Workbench alkalmazásban használt.

Keresse meg a dockerdsvm.runconfig, és ezek a mezők konfigurációjának módosítása a következőhöz:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/dsvm_spark_dependencies.yml

A környezet előkészítése a projekt futtatásával:

```az ml experiment prepare -c dockerdsvm```


A `PrepareEnvironment` igaz értékű, Machine Learning Workbench a futtatási környezetet hoz létre, amikor egy feladat elküldése. `Config/conda_dependencies.yml` és `Config/dsvm_spark_dependencies.yml` a futtatási környezetet kell tartalmaznia. Mindig módosíthatja a Conda-függőségeket, a Spark-konfiguráció és a Spark függőségek YMAL két fájlt szerkesztésével. Ebben a példában hozzáadtunk `azure-storage` és `azure-ml-api-sdk` , további Python-csomagok `Config/conda_dependencies.yml`. Lehetővé tettük `spark.default.parallelism`, `spark.executor.instances`, és `spark.executor.cores` a `Config/dsvm_spark_dependencies.yml`. 

#####  <a name="2-data-preparation-and-feature-engineering-on-dsvm-docker"></a>2. Adat-előkészítési és a DSVM Docker funkciófejlesztési feladatok

Futtassa a szkriptet `etl.py` a dsvm-hez a Docker. Használja a hibakeresési paramétert szűri az adott kiszolgálói IP-címmel a betöltött adatokra:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FILTER_IP```

A kiszolgálóoldali panelen keresse meg, és válassza ki **futtatása** futtatási előzményeinek megtekintéséhez `etl.py`. Figyelje meg, hogy a futási idő körülbelül két perc. Ha azt tervezi, hogy módosítania kell a kódot, többek között az új, a gyorsabb iteráció nyújtó második argumentumként FILTER_IP biztosít. Előfordulhat, hogy futtatni szeretné ezt a lépést több alkalommal a saját gépi tanulási problémák, ismerje meg az adatkészlet, vagy hozzon létre új szolgáltatások esetén. 

Milyen adatok betöltése, és további szűrési, hogy milyen adatokat feldolgozni a testre szabott korlátozás felgyorsíthatja az iteráció folyamatát modell fejlesztése. Kísérlet, mert rendszeres időközönként mentse a módosításokat a kódban, hogy a Git-tárházba. Vegye figyelembe, hogy használjuk-e a következő kódot a `etl.py` a személyes tárolóba a hozzáférés engedélyezése:

```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)

# attach the blob storage to the spark cluster or VM so that the storage can be accessed by the cluster or VM        
attach_storage_container(spark, storageAccount, storageKey)
```


Ezután futtassa a szkriptet `etl.py` a DSVM Docker, a hibakeresési paramétert FILTER_IP:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/etl.py ./Config/storageconfig.json FALSE```

A kiszolgálóoldali panelen keresse meg, és válassza ki **futtatása** futtatási előzményeinek megtekintéséhez `etl.py`. Figyelje meg, hogy a futási idő körülbelül négy percet. Ebben a lépésben feldolgozott eredményét a tárolóba mentett, és betölti a train.py képzéshez. Emellett a karakterlánc-indexelők kódoló folyamatok és standard scalers menti a személyes tárolóba. Ezek a operacionalizálás használnak. 


##### <a name="3-model-training-on-dsvm-docker"></a>3. A DSVM Docker modell betanítása

Futtassa a szkriptet `train.py` a DSVM Docker:

```az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/train.py ./Config/storageconfig.json```

Ebben a lépésben a köztes számítási eredmények tölt be, a Futtatás `etl.py`, és a egy gépi tanulási modellt betanítja. Ebben a lépésben a nagyjából két percet vesz igénybe.

Ha sikeresen befejezte a kísérletezés a kis méretű adatokon, folytathatja a Kísérletezési futtathatók a teljes adatkészletet. Első lépésként használja ugyanazt a kódot, majd argumentum kísérletezhet és számítási cél módosításokat.  

####  <a name="model-development-on-the-hdinsight-cluster"></a>Modell fejlesztése a HDInsight-fürtön

##### <a name="1-create-the-compute-target-in-machine-learning-workbench-for-the-hdinsight-cluster"></a>1. A számítási célnak a Machine Learning Workbench a HDInsight-fürt létrehozása

```az ml computetarget attach cluster --name myhdi --address $clustername-ssh.azurehdinsight.net --username $username --password $password```

A következő két fájlt a aml_config mappában jönnek létre:
    
-  myhdi.COMPUTE: Ez a fájl tartalmazza a távoli végrehajtás cél kapcsolat és a konfigurációs adatok.
-  myhdi.runconfig: ezt a fájlt a futtatási lehetőségek, a Workbench alkalmazásban használt van beállítva.


Keresse meg a myhdi.runconfig, és ezek a mezők konfigurációjának módosítása a következőhöz:

    PrepareEnvironment: true 
    CondaDependenciesFile: Config/conda_dependencies.yml 
    SparkDependenciesFile: Config/hdi_spark_dependencies.yml

A környezet előkészítése a projekt futtatásával:

```az ml experiment prepare -c myhdi```

Ebben a lépésben legfeljebb hét percet is igénybe vehet.

##### <a name="2-data-preparation-and-feature-engineering-on-hdinsight-cluster"></a>2. Adat-előkészítési és funkciófejlesztési HDInsight-fürtön

Futtassa a szkriptet `etl.py`, teljes adatokkal a HDInsight-fürtön:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/etl.py Config/fulldata_storageconfig.json FALSE```

Mivel ez a feladat tart viszonylag hosszú ideig (körülbelül két órát), használhatja `-a` letiltása a kimeneti adatfolyam. Ha a feladat befejeződött, a **futtatási előzmények**, az illesztőprogram, a vezérlő naplókat is megtekintheti. Ha nagyobb fürt, újrakonfigurálhatja az konfigurációi mindig `Config/hdi_spark_dependencies.yml` további példányok vagy magok használatára. Például ha egy feldolgozó – Négycsomópontos fürt, növelheti a értékét `spark.executor.instances` 5 – 7. Láthatja, hogy ez a lépés a kimenete a **fullmodel** a storage-fiókban lévő tárolóba. 


##### <a name="3-model-training-on-hdinsight-cluster"></a>3. HDInsight-fürtön a modell betanítása

Futtassa a szkriptet `train.py` HDInsight-fürtön:

```az ml experiment submit -a -t myhdi -c myhdi ./Code/train.py Config/fulldata_storageconfig.json```

Mivel ez a feladat tart viszonylag hosszú ideig (körülbelül 30 perc), használhatja `-a` letiltása a kimeneti adatfolyam.

#### <a name="run-history-exploration"></a>Futtassa az előzmények feltárása

Futtatási előzmények funkciója nyomon követheti a Machine Learning Workbench a kísérletezésre fordított időt. Alapértelmezés szerint azt a Kísérletezési időtartama követi nyomon. Amikor azt át a teljes adatkészletet a konkrét példában `Code/etl.py` a a kísérleti, láthatjuk, időtartam jelentősen növeli. Adott mérőszámok nyomon követési célokból is bejelentkezhet. Metrika nyomon követését lehetővé, fejét a Python-fájlt adja hozzá a következő kódsorokat:
```python
# import logger
from azureml.logging import get_azureml_logger

# initialize logger
run_logger = get_azureml_logger()
```
Íme egy példa egy bizonyos metrikát nyomon követéséhez:

```python
run_logger.log("Test Accuracy", testAccuracy)
```

A jobb oldali oldalsávon a munkaterület, keresse meg a **futtatások** egyes Python-fájlt a futtatási előzményeinek megtekintéséhez. Ha ellátogat a GitHub-adattárhoz. Egy új ág "AMLHistory," kezdetű névvel jön létre, nyomon követheti a parancsfájl minden egyes futtatásához a módosítást. 


### <a name="operationalize-the-model"></a>A modell üzembe helyezése

Ebben a szakaszban a modellt webszolgáltatásként, amely az előző lépésekben létrehozott üzembe helyezheti. Azt is megtudhatja, hogyan számítási feladatok előrejelzése a web service használatával. Gépi nyelvére operacionalizálás parancssori felületen (CLI-k) használata a kód és a Docker-rendszerképeket, a függőségek csomagolása és a modell közzététele webszolgáltatásként, amely tárolóalapú.

Machine Learning Workbench a parancssor használatával futtassa a CLI-k.  Is futtathatja a CLI-k Ubuntu Linux rendszeren a következő a [telepítési útmutató](./deployment-setup-configuration.md#using-the-cli). 

> [!NOTE]
> A következő parancsokat az összes cserélje le tényleges értékek bármely argumentum változó. Körülbelül 40 percig tart, ez a szakasz befejezéséhez.
> 

Válasszon egy egyedi karakterlánccá az operacionalizáláshoz környezet. "[Egyedi]" karakterlánc itt választott szöveglánc használjuk.

1. Az operacionalizálás megfelelő környezetet hozhat létre, és hozza létre az erőforráscsoportot.

        az ml env setup -c -n [unique] --location eastus2 --cluster -z 5 --yes

   Vegye figyelembe, hogy használhatja Tárolószolgáltatás környezet használatával `--cluster` a a `az ml env setup` parancsot. A machine learning-modellhez működésbe hozhat [az Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-intro-kubernetes). Használ [Kubernetes](https://kubernetes.io/) automatizálásához, üzembe helyezés, méretezéshez és tárolóalapú alkalmazások felügyeletéhez. Ez a parancs körülbelül 20 percet vesz igénybe futtatni. A következő segítségével ellenőrizheti, ha a telepítés sikeresen befejeződött: 

        az ml env show -g [unique]rg -n [unique]

   A telepítési környezetre állítja be csak létre a következő futtatásával:

        az ml env set -g [unique]rg -n [unique]

2. Létrehozhat és használhat egy modellkezelési fiókot. Modellkezelési fiók létrehozásához futtassa a következő:

        az ml account modelmanagement create --location  eastus2 -n [unique]acc -g [unique]rg --sku-instances 4 --sku-name S3 

   A modellkezelési operacionalizálás használata a következő futtatásával:

        az ml account modelmanagement set  -n [unique]acc -g [unique]rg  

   Modellkezelési fiók a modellek és webszolgáltatások kezelésére szolgál. Az Azure Portalon megjelenik egy új modellkezelési fiók létrehozása. Láthatja, a modellek, -jegyzékekhez, Docker-rendszerképek és szolgáltatások, amelyek jönnek létre a modellkezelési fiók használatával.

3. Töltse le, és a modellek regisztrálása.

   Töltse le a modellek a **fullmodel** tároló könyvtárában található kód a helyi gépen. Ne töltse le a parquet eszközökben adatfájl a neve "vmlSource.parquet." Már nem egy jelentésmodell fájlját; egy köztes számítás eredménye. A Git-tárházban szereplő szolgáltatásmodell-fájlokból is felhasználhatja. További információkért lásd: [GitHub](https://github.com/Azure/MachineLearningSamples-BigData/blob/master/Docs/DownloadModelsFromBlob.md). 

   Nyissa meg a `Model` mappát a parancssori felület, és regisztrálja a modellek pedig a következő:

        az ml model register -m  mlModel -n vmlModel -t fullmodel
        az ml model register -m  featureScaleModel -n featureScaleModel -t fullmodel
        az ml model register -m  oneHotEncoderModel -n  oneHotEncoderModel -t fullmodel
        az ml model register -m  stringIndexModel -n stringIndexModel -t fullmodel
        az ml model register -m  info -n info -t fullmodel

   Minden parancs kimenete egy Modellazonosító, amelyre szükség van a következő lépésben biztosít. Mentse egy szövegfájlba későbbi használatra.

4. A webszolgáltatás jegyzék létrehozásához.

   A jegyzék a kódot a webes szolgáltatás, a machine learning-modellek és futásidejű környezet függőségeit tartalmazza. Nyissa meg a `Code` mappát a parancssori felület, és futtassa az alábbi parancsot:

        az ml manifest create -n $webserviceName -f webservice.py -r spark-py -c ../Config/conda_dependencies_webservice.yml -i $modelID1 -i $modelID2 -i $modelID3 -i $modelID4 -i $modelID5

   A kimenet a következő lépés egy Jegyzékfájl megtalálható. 

   Maradjon a `Code` könyvtárra, és webservice.py tesztelheti a következő futtatásával: 

        az ml experiment submit -t dockerdsvm -c dockerdsvm webservice.py

5. Hozzon létre egy Docker-rendszerképet. 

        az ml image create -n [unique]image --manifest-id $manifestID

   A kimenet a következő lépéshez lehetővé teszi egy lemezkép-azonosító, a docker-rendszerképet használja a Container Service-ben. 

6. A webszolgáltatás üzembe helyezése a Container Service-fürthöz.

        az ml service create realtime -n [unique] --image-id $imageID --cpu 0.5 --memory 2G

   A kimenet lehetővé teszi egy azonosítót. A hitelesítési kulcs beszerzése és az URL-címét kell.

7. Hívja meg a webszolgáltatás pontszámot rendelni az mini kötegekben Python-kódban.

   Használja a következő parancsot a hitelesítési kulcs beszerzése:

         az ml service keys realtime -i $ServiceID 

   A következő parancsot használja az szolgáltatásba pontozás URL-címe:

        az ml service usage realtime -i $ServiceID

   Módosítsa a tartalmakat a `./Config/webservice.json` a pontozás URL-cím és a hitelesítési kulcs a megfelelő szolgáltatást. A "tulajdonos" tartani az eredeti fájlt, és cserélje le a "xxx" részt. 
   
   Nyissa meg a projekt gyökérkönyvtárában, és tesztelje a webszolgáltatást a minimális kötegelt pontozási a következő használatával:

        az ml experiment submit -t dockerdsvm -c dockerdsvm ./Code/scoring_webservice.py ./Config/webservice.json

8. A web service méretezhető. 

   További információkért lásd: [operacionalizálás méretezése az Azure Container Service-fürtön](how-to-scale-clusters.md).
 

## <a name="next-steps"></a>További lépések

Ebben a példában emeli ki, hogyan használja a Machine Learning Workbench alkalmazásban egy gépi tanulási modellt a big data, és a betanított modell üzembe helyezéséhez. Különösen útmutatóból megtudhatta, hogyan szolgáltatás konfigurálása és használni a különböző számítási célokhoz, és futtassa a metrikák követése előzményeit és különböző futtatások.

A kódot, és Fedezze fel a kereszt-ellenőrzési és a hyper-paraméter hangolása bővítheti. Kereszt-ellenőrzési és a hyper-paraméter hangolásával kapcsolatos további tudnivalókért lásd: [a GitHub-erőforrás](https://github.com/Azure/MachineLearningSamples-DistributedHyperParameterTuning).  

Idősorozat-előrejelzés kapcsolatos további információkért lásd: [a GitHub-erőforrás](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting).
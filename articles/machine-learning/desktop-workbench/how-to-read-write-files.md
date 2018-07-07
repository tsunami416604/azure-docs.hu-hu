---
title: Nagy mennyiségű adat fájlok olvasása és írása |} A Microsoft Docs
description: Olvasási és írási nagy méretű fájlok az Azure Machine Learning-kísérletek.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: 5a772f8792c02139e45977e207b5be4bebc63a9c
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37908237"
---
# <a name="persisting-changes-and-working-with-large-files"></a>Módosítások megőrzése, és a nagy méretű fájlok használata
Az Azure Machine Learning-Kísérletezési szolgáltatással konfigurálhatja úgy a végrehajtási célok különböző. Egyes tárolók olyan helyi, például a helyi számítógépen vagy egy Docker-tárolót a helyi számítógépen. Mások olyan távoli, például egy távoli gépen vagy egy HDInsight-fürtöt a Docker-tárolóban. További információkért lásd: [áttekintése az Azure Machine Learning-kísérletezés végrehajtási szolgáltatás](experimentation-service-configuration.md). 

Mielőtt a célhelyen hajthat végre, a számítási célnak a projektmappa fájllistájának kell másolnia. Így még a helyi végrehajtásra, amely egy helyi ideiglenes mappát használ erre a célra a kell elvégeznie. 

## <a name="execution-isolation-portability-and-reproducibility"></a>Végrehajtási elkülönítés, a hordozhatóságot és megismételhetőség
Ez a kialakítás célja az elkülönítési, megismételhetőség és hordozhatóság a végrehajtásának biztosításához. Ha ugyanezt a szkriptet kétszer hajtható végre, az ugyanazon vagy másik számítási célt, ugyanazt az eredményt kapja. Az első végrehajtás során végzett módosításokat nem lehet hatással a második végrehajtása Ezzel a kialakítással minden kellene befejezése után végrehajtott feladatok affinitás nélküli állapot nélküli számítási erőforrásként számítási célnak is kezelheti.

## <a name="challenges"></a>Problémák
Annak ellenére, hogy a terv úgy nyújt a hordozhatóság és ismételhetőség előnyeit, néhány egyedi kihívását is biztosít.

### <a name="persisting-state-changes"></a>Persisting állapotváltozások
Ha a parancsfájl módosítja a számítási környezet állapotát, a módosítások nem rögzíti a következő végrehajtásra, és azok még nem érvényesülnek vissza az ügyfélszámítógép automatikusan. 

Pontosabban a szkriptet hoz létre egy almappát, vagy egy fájlba ír, ha a fájl vagy mappa nem lesznek jelen a projektkönyvtárban végrehajtása után. A fájlokat egy ideiglenes mappába cél számítási környezetben vannak tárolva. Hibakeresési célokra használhat, de nem támaszkodhat a végleges létezésüket.

### <a name="working-with-large-files-in-the-project-folder"></a>Nagy méretű fájlokat a projektmappába használata

Ha a projektmappa fájllistájának tartalmaz minden olyan nagy méretű fájlok, díjak késés a mappa másolása a cél számítási környezetet, elején-végrehajtás során. Akkor is, ha a végrehajtás helyileg történik, a lemez továbbra is a szükségtelen forgalom van elkerülése érdekében. Ezért azt jelenleg költségplafont, 25 MB maximális projekt méretét.

## <a name="option-1-use-the-outputs-folder"></a>1. lehetőség: Használja a *kimenete* mappa
Ez a beállítás akkor előnyös, ha a következő feltételek érvényesek:
* A szkript fájlokat eredményez.
* A fájlokat, és módosítsa a minden kísérlet várható.
* Szeretné megőrizni az előzményeket, ezeket a fájlokat. 

A gyakori alkalmazási helyzetek a következők:
* A modell tanítása
* Adatkészlet létrehozása
* A modell-képzési végrehajtási részeként képfájlban grafikon 

Emellett szeretné a kimenetek összehasonlíthasson futtatja, jelölje be egy kimeneti fájl (például egy modell), amely egy korábbi készítette futtassa, és használata egy azt követő feladat (például pontozási).

Fájlok írhat nevű *kimenete* Ez az a gyökérkönyvtár viszonyítva. A mappa különleges kezelést kap a Kísérletezési szolgáltatás. Semmit a szkriptet hoz létre a mappában, például egy modellfájl, adatfájl vagy ábrázolt képfájl végrehajtása során (összefoglaló néven _összetevők_), másolja az Azure Blob storage-fiókot, amelyhez társítva van a Kísérletezési fiók a Futtatás befejeződése után. A fájlok a futtatási előzményrekordként részévé válik.

A modell tárolására szolgáló kódot egy rövid példa a *kimenete* mappa:
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
Minden olyan összetevő letöltéséhez keresse meg a **kimeneti fájlok** a Futtatás részletei lap egy adott részében futtatása az Azure Machine Learning Workbenchben. Egyszerűen válassza ki a futtató, és válassza a **letöltése** gombra. Másik lehetőségként beírhatja a `az ml asset download` parancsot a parancssori felület (CLI) ablakban.

Egy teljes példa: a `iris_sklearn.py` Python szkriptet a _Írisz osztályozása_ mintaprojektet.

## <a name="option-2-use-the-shared-folder"></a>2. lehetőség: A megosztott mappa használata
Ha nem szeretné megőrizni az egyes futtatások fájlok előzményeket, egy megosztott mappát, amely független futtatás során használatával lehet ideális megoldás a következő esetekben: 
- A szkript kell betölteni az adatokat a helyi fájlok, például a .csv-fájlok vagy a szöveg- vagy képfájl, fájlok képzési vagy vizsgálati adatként könyvtár. 
- A szkript feldolgozza a nyers adatokat, és írja meg a köztes eredményeket, például a szöveges vagy képi fájlok, amelyek az egy későbbi betanítási Futtatás featurized betanítási adatok. 
- A szkript spits ki egy modellt, és az ezt követő pontozó szkript folytattuk a munkát a modellt, és meg kell használnia, az értékeléshez. 

Fontos megjegyezni, hogy a megosztott mappa helyi a kiválasztott számítási célnak a helyén. Ezért ez a beállítás csak akkor, ha a parancsfájl futtatása, hogy a megosztott mappa tevékenységében ugyanazon az összes számítási célt működik, és a számítási célnak nem újrahasznosított futtatásai között.

A megosztott mappa funkció kihasználásával olvasni vagy írni egy külön mappába, egy környezeti változó által azonosított `AZUREML_NATIVE_SHARE_DIRECTORY`. 

### <a name="example"></a>Példa
Íme néhány Python-mintakódot a mappa megosztása a olvasása és írása egy szövegfájlba:
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', "w") as f1:
    f1.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', "r") as f2:
    text = f2.read()
```

Egy teljes példa: a *iris_sklearn_shared_folder.py* fájlt a _Írisz osztályozása_ mintaprojektet.

Ez a funkció használata előtt kell beállítani a *.compute* néhány egyszerű konfigurációk, amelyek a megcélzott végrehajtási környezet, a fájl a *aml_config* mappát. A tényleges mappa elérési útját a számítási célnak úgy dönt, és konfigurálja az érték függvényében.

### <a name="configure-local-compute-context"></a>Helyi számítási környezet konfigurálása

Ahhoz, hogy ez a funkció a helyi számítási környezetben, egyszerűen csak hozzá az *.compute* fájlt a következő sort, amely a _helyi_ környezet (általában *local.compute*).
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

Az elérési út ~/.azureml/share alapmappát alapértelmezés szerint a rendszer. Módosíthatja a bármilyen helyi abszolút elérési utat, amely elérhető a parancsfájl futtatásához. A Kísérletezési fiók neve, a munkaterület neve és a projekt neve automatikusan hozzáfűzi a alapkönyvtárának névvel, amely válik a shared könyvtár teljes elérési útja. Például a fájlok írása (és lekért) a következő elérési út, ha az előző alapértelmezett érték:

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-the-docker-compute-context-local-or-remote"></a>A Docker számítási környezetet (helyi vagy távoli) konfigurálása
Ahhoz, hogy a Docker számítási környezetek ezt a funkciót, hozzá kell adnia az alábbi két sort a helyi vagy távoli Docker *.compute* fájlt.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>A **sharedVolumes** tulajdonságot állítsa *igaz* használatakor a `AZUREML_NATIVE_SHARE_DIRECTORY` környezeti változót a megosztott mappa elérésére. Ellenkező esetben a végrehajtás sikertelen lesz.

A kód a Docker-tárolóban futó mindig látja, a megosztott mappa */azureml-share /*. A mappa, a Docker-tárolót a útvonala nem konfigurálható. Ne használja a mappa nevét a kódban. Mindig használjon inkább környezeti változó neve `AZUREML_NATIVE_SHARE_DIRECTORY` , tekintse meg ezt a mappát. A Docker-gazdagép az egyik helyi mappájába van leképezve a gép vagy a számítási környezetet. A helyi mappa alapkönyvtárának értéke konfigurálható a `nativeSharedDirectory` beállítását a *.compute* fájlt. A gazdagépen, a megosztott mappa helyi elérési útja az alapértelmezett értéket használja-e a következő:
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!NOTE]
>A helyi lemezen a megosztott mappa elérési útja megegyezik-e egy helyi számítási környezetben, vagy egy helyi Docker-számítási környezetet. Ez azt jelenti, hogy fájlokat a helyi Futtatás és a egy helyi Docker run között is megoszthatja.

Helyezze el a bemeneti adatokat közvetlenül a ezeket a mappákat és a várható, hogy a helyi vagy a Docker fut a gépen folytathatja a munkát. Ezt a mappát a helyi vagy a Docker-futtatások fájlok írni, és elvárják, fájlok abban a mappában, első megőrzött újraindulnak a még működő végrehajtási életciklusának is.

További információkért lásd: [Azure Machine Learning Workbench végrehajtási konfigurációs fájlok](experimentation-service-configuration-reference.md).

>[!NOTE]
>A `AZUREML_NATIVE_SHARE_DIRECTORY` környezeti változó nem támogatott a HDInsight számítási környezetben. Azonban könnyebbé vált a csatolt blob Storage írnak és onnan olvasnak, az Azure Blob storage abszolút elérési út explicit módon használatával ugyanaz az eredmény elérése érdekében.

## <a name="option-3-use-external-durable-storage"></a>3. lehetőség: A külső tartós tárolási használata

Külső tartós tárolási segítségével állapot megőrzése végrehajtása során. Ez a beállítás a következő esetekben hasznos:
- A bemeneti adatok tartós tárolási, amely elérhető a cél számítási környezet már tárolódik.
- A fájlok nem kell a futtatási előzmények rekordok részeként.
- A fájlok között különféle számítási környezetekben végrehajtások kell osztani.
- A fájlok stabilitást biztosít a számítási környezetet, saját maga képesnek kell lennie.

Egy ilyen megoldás, a Python- vagy PySpark-kód az Azure Blob storage használata. A következő rövid példa:

```python
from azure.storage.blob import BlockBlobService
from azure.storage.blob.models import PublicAccess
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
blob_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to Blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

Íme egy példa csatolása egy HDI Spark futásidejű bármely tetszőleges Azure Blob storage rövid:
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>Összegzés
Mivel az Azure Machine Learning parancsfájlok végrehajtja a cél számítási környezetet a teljes projektmappáról másolásával, nagy bemeneti, kimeneti és a köztes fájlok különleges gondossággal. Nagyméretű fájlok tranzakció, használhatja a speciális kimeneti mappát, a megosztott mappa, amely keresztül érhető el a `AZUREML_NATIVE_SHARE_DIRECTORY` környezeti változót, vagy külső tartós tárolási. 

## <a name="next-steps"></a>További lépések
- Tekintse át a [Azure Machine Learning Workbench végrehajtási konfigurációs fájlok](experimentation-service-configuration-reference.md) cikk.
- Tekintse meg a [Írisz osztályozása](tutorial-classifying-iris-part-1.md) oktatóprojektjében megőrizni a betanított modell kimeneti mappát használja.

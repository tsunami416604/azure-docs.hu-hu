---
title: "Nagy fájlok olvasása és írása |} Microsoft Docs"
description: "Olvasási és írási nagy fájlok az Azure Machine Learning kísérleteket."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/10/2017
ms.openlocfilehash: f5c75b95d9019c15bb402313ce7407fa9abb81d4
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="persisting-changes-and-working-with-large-files"></a>Módosítások megőrzése, és a nagy fájlok használata
Az Azure Machine Learning kísérletezhet szolgáltatással végrehajtási célok számos konfigurálhatja. Néhány célpontjai helyi, például a helyi számítógépen vagy egy Docker-tároló a helyi számítógépen. A távoli, például egy távoli gépen vagy a HDInsight-fürtöt a Docker-tároló. További információkért lásd: [áttekintés az Azure Machine Learning kísérletezhet végrehajtási szolgáltatás](experimentation-service-configuration.md). 

Ahhoz a célként megadott hajthat végre, a számítási célra a projektmappa kell másolnia. Egy helyi végrehajtását, amely egy helyi ideiglenes mappát használ erre a célra használatát, így még tegye. 

## <a name="execution-isolation-portability-and-reproducibility"></a>Végrehajtási elkülönítési adatok hordozhatóságára és reprodukálhatósági
Ez a kialakítás célja a elkülönítési, reprodukálhatósági és hordozhatóság végrehajtásának biztosítása érdekében. Ugyanazt a parancsfájlt kétszer hajtható végre, ha az ugyanazon vagy másik számítási célként, ugyanazt az eredményt kapja. Az első végrehajtása során végrehajtott módosításokat a második végrehajtása nem érinti. Ezzel a kialakítással állapotmentes számítási erőforrások, miután befejezi a végrehajtott feladatok affinitás nélküli rendelkező számítási célokat is tekinti.

## <a name="challenges"></a>Kihívásai
Annak ellenére, hogy ez a kialakítás előnyökkel adatok hordozhatóságára és ismételhetőség, néhány speciális kihívást is jelent.

### <a name="persisting-state-changes"></a>Persisting állapotváltozások
Ha a parancsfájl a számítási környezet állapotának módosítja, a módosítások nem maradnak meg a következő végrehajtásra, és azok még nem propagál vissza az ügyfélszámítógép automatikusan. 

Pontosabban Ha a parancsfájl létrehoz egy almappát, vagy menti a fájlt, adott mappa vagy fájl nem lesz a projekt könyvtárában található végrehajtása után. A fájlok számítási célkörnyezetben egy ideiglenes mappába kerülnek. Hibakeresési célra használhatja, de nem elegendő az állandó meglétét.

### <a name="working-with-large-files-in-the-project-folder"></a>A nagy fájlok projektmappában használata

Ha a projekt mappában nagy fájlok, Ön tudomásával várakozási ideje a mappához a cél számítási környezetet, elején egy végrehajtás másolását. Akkor is, ha a végrehajtás helyben történik, a lemez továbbra is szükségtelen forgalom van elkerülése érdekében. Ezért azt jelenleg cap a 25 MB maximális projekt méretét.

## <a name="option-1-use-the-outputs-folder"></a>1. lehetőség: Használata a *kimenete* mappa
Ezt a beállítást célszerű, ha a következő feltételek érvényesek:
* A parancsfájl fájlokat eredményez.
* A fájlok minden kísérlet változik várt.
* Meg szeretné tartani ezeket a fájlokat előzményeit. 

A gyakori alkalmazási esetei a következők:
* A modell betanítása
* A DataSet adatkészlet létrehozása
* A modell-képzési végrehajtásának részeként képfájlban grafikon 

Emellett összehasonlítani kívánt a kimenetek keresztül futtatja, jelölje be egy kimeneti fájl (például egy modell), amely egy előző készítette futtatni, és majd a következő feladathoz (például pontozási) használatával.

Fájlok írhat nevű *kimenete* Ez az a gyökérkönyvtár viszonyítva. A mappa különleges kezelést megkapja a kísérleti szolgáltatás. Semmit a parancsfájlt hoz létre a mappában, például egy modellfájl, adatfájlt vagy program képfájl végrehajtása során (összefoglaló néven _összetevők_), másolja az Azure Blob storage-fiók, amelyhez társítva van a kísérletezhet fiók a futtató befejeződése után. A fájlok a futtatási előzményei rekord részét képezik.

A kód lévő modellek tárolására szolgáló rövid példa a *kimenete* mappába:
```python
import os
import pickle

# m is a scikit-learn model. 
# we serialize it into a mode.plk file under the ./outputs folder.
with open(os.path.join('.', 'outputs', 'model.pkl'), 'wb') as f:    
    pickle.dump(m, f)
```
Minden összetevő tallózással is letöltheti a **kimeneti fájlok** Azure Machine Learning munkaterület futtatása egy adott futtatási Részletek lap részében. Egyszerűen jelölje be a Futtatás, majd válassza ki a **letöltése** gombra. Akkor is, megadhatja a `az ml asset download` parancsot a parancssori felület (CLI) ablakban.

Részletesebb példát, tekintse meg a `iris_sklearn.py` Python a parancsfájl a _zárolásának Iris_ mintaprojektet.

## <a name="option-2-use-the-shared-folder"></a>2. lehetőség: A megosztott mappa használata
Ha előzményekre fájlok minden egyes futtatásához nincs szüksége, független futtatása keresztül elérhető megosztott mappát használ lehet pedig a következő esetekben: 
- A parancsfájl kell betölteni az adatokat a helyi fájlok, például a .csv fájlt vagy egy könyvtárat a szöveges vagy képi fájlok képzési vagy vizsgálati adatként. 
- A parancsfájl feldolgozza a nyers adatokat, és írja a köztes eredmények, például a szöveges vagy képi fájlokat, amelyek egy későbbi képzési futtatásához használt featurized betanítási adatok. 
- A parancsfájl spits ki egy modellt, és a későbbi pontozási parancsfájlt kell vegye fel a modell és a kiértékelési használni. 

Fontos megjegyezni, hogy a megosztott mappa él, helyileg a kiválasztott számítási célponton. Ezért ez a beállítás csak akkor, ha a parancsfájl futtatása a megosztott mappa hivatkozó hajtja végre, ugyanez az összes cél számítási működik, és a számítási célja nem újrahasznosított fusson.

A megosztott mappa funkciót kihasználva, olvasni vagy írni a speciális mappába, egy környezeti változó által azonosított `AZUREML_NATIVE_SHARE_DIRECTORY`. 

### <a name="example"></a>Példa
Íme néhány mintakód Python a megosztás mappát használatával történő olvasása és írása egy szövegfájlba:
```python
import os

# write to the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'wb') as f:
    f.write(“Hello World”)

# read from the shared folder
with open(os.environ['AZUREML_NATIVE_SHARE_DIRECTORY'] + 'test.txt', 'r') as f:
    text = file.read()
```

Részletesebb információ például tekintse meg a *iris_sklearn_shared_folder.py* fájlt a _zárolásának Iris_ mintaprojektet.

Ez a funkció használata előtt kell beállítani a *.compute* néhány egyszerű konfigurációkat, amelyek megfelelnek a célzott végrehajtási környezet a fájlt a *aml_config* mappa. A tényleges mappa elérési útját a eltérőek lehetnek attól függően, hogy a számítási cél úgy dönt, és a értéket állít be.

### <a name="configure-local-compute-context"></a>Helyi számítási környezet konfigurálása

Ahhoz, hogy ez a funkció a helyi számítási környezetben, vegyük fel a *.compute* fájlt a következő sort, amely képviseli az _helyi_ környezet (általában nevű *local.compute*).
```
# local.runconfig
...
nativeSharedDirectory: ~/.azureml/share
...
```

Az elérési út ~/.azureml/share alapmappa alapértelmezés szerint a rendszer. Módosíthatja bármely helyi abszolút elérési, hogy elérhető-e a parancsfájl futtatásához. A kísérletezés fiók nevét, a munkaterület neve és a projekt neve automatikusan hozzáfűzi a alapkönyvtárának névvel, amely válik a megosztott könyvtár elérési útját. Például a fájlok írása (és lekért) a következő elérési úton, ha az előző alapértelmezett érték:

```
# on Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# on macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

### <a name="configure-the-docker-compute-context-local-or-remote"></a>A Docker számítási környezet (helyi vagy távoli) konfigurálása
Ahhoz, hogy ezt a funkciót egy Docker számítási környezetet, hozzá kell adnia az alábbi két sort a helyi vagy távoli Docker *.compute* fájlt.

```
# docker.compute
...
sharedVolumes: true
nativeSharedDirectory: ~/.azureml/share
...
```
>[!IMPORTANT]
>A **sharedVolumes** tulajdonság értékre kell állítani *igaz* használata esetén a `AZUREML_NATIVE_SHARE_DIRECTORY` környezeti változó a megosztott mappa elérésére. Egyéb esetben a végrehajtás sikertelen lesz.

A kód mindig fut a Docker-tároló látja, a megosztott mappa */azureml-share /*. A mappa elérési útja, a Docker-tároló szerinti nincs konfigurálható. Ne használja a kódban a mappa neve. Ehelyett mindig használja a környezeti változó neve `AZUREML_NATIVE_SHARE_DIRECTORY` utal, ebbe a mappába. Egy helyi mappába a Docker gazdagépen van leképezve a gép vagy a számítási környezet. A helyi mappa alapkönyvtárának értéke konfigurálható a `nativeSharedDirectory` beállítást azokban a *.compute* fájlt. A helyi a megosztott mappa, a gazdagépen, ha az alapértelmezett érték elérési út a következő:
```
# Windows
C:\users\<username>\.azureml\share\<exp_acct_name>\<workspace_name>\<proj_name>\

# macOS
/Users/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/

# Ubuntu Linux
/home/<username>/.azureml/share/<exp_acct_name>/<workspace_name>/<proj_name>/
```

>[!NOTE]
>A helyi lemezen lévő megosztott mappa elérési útja megegyezik egy helyi számítási környezet vagy egy helyi Docker számítási környezetet. Ez azt jelenti, hogy helyi futtató és a helyi Docker kísérletek között is megoszthatja.

Helyezze el a bemeneti adatok közvetlenül a ezeket a mappákat, és várja, hogy a helyi vagy a Docker fut a gépen is átvételéhez azt. Fájlok írása ebbe a mappába a helyi vagy a Docker fut, és várhatóan, fájlok beolvasása megőrzött abban a mappában, a végrehajtási életciklus fennmaradó is.

További információkért lásd: [Azure Machine Learning-munkaterület végrehajtási konfigurációs fájlok](experimentation-service-configuration-reference.md).

>[!NOTE]
>A `AZUREML_NATIVE_SHARE_DIRECTORY` HDInsight számítási környezetben nem támogatott a környezeti változó. Azonban akkor is könnyen ugyanazt az eredményt az írási és olvasási a csatolt blob Storage Azure Blob storage abszolút elérési útnak explicit módon használatával eléréséhez.

## <a name="option-3-use-external-durable-storage"></a>3. lehetőség: Külső tartós tároló használata

Állapot megőrizni a végrehajtás során külső tartós tárolót is használhatja. Ez a beállítás akkor hasznos, a következő esetekben:
- A bemeneti adatok tartós tároló, amely elérhető a cél számítási környezetben már tárolja.
- A fájlok nem kell lenniük a futtatási előzményei rekordok részét.
- A fájlok végrehajtások lehet közösen a különböző számítási környezetek között.
- A fájlok a túlélést a számítási környezet maga képesnek kell lennie.

Egy ilyen megoldás, az Azure Blob Storage tárolóban, a Python vagy PySpark kódból használni. Íme egy rövid példa:

```python
from azure.storage.blob import BlockBlobService
import glob
import os

ACCOUNT_NAME = "<your blob storage account name>"
ACCOUNT_KEY = "<account key>"
CONTAINER_NAME = "<container name>"

blob_service = BlockBlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

## Create a new container if necessary, or use an existing one
my_service.create_container(CONTAINER_NAME, fail_on_exist=False, public_access=PublicAccess.Container)

# df is a pandas DataFrame
df.to_csv('mydata.csv', sep='\t', index=False)

# Export the mydata.csv file to Blob storage.
for name in glob.iglob('mydata.csv'):
    blob_service.create_blob_from_path(CONTAINER_NAME, 'single_file.csv', name)
```

Íme egy rövid példa bármilyen tetszőleges Azure Blob Storage tárolóban csatolása egy HDI Spark futtatókörnyezetet:
```python
def attach_storage_container(spark, account, key):
    config = spark._sc._jsc.hadoopConfiguration()
    setting = "fs.azure.account.key." + account + ".blob.core.windows.net"
    if not config.get(setting):
        config.set(setting, key)
 
attach_storage_container(spark, "<storage account name>", "<storage key>”)
```

## <a name="conclusion"></a>Összegzés
Azure Machine Learning parancsfájlok úgy, hogy a cél számítási környezet teljes projektmappában hajt végre, mert nagy bemeneti, kimeneti és köztes fájlok különös gonddal. Nagy méretű fájlok tranzakciók, használhatja a speciális kimenetek mappa, a megosztott mappa, amely elérhető a `AZUREML_NATIVE_SHARE_DIRECTORY` környezeti változót vagy a külső tartós tárolást. 

## <a name="next-steps"></a>Következő lépések
- Tekintse át a [Azure Machine Learning-munkaterület végrehajtási konfigurációs fájlok](experimentation-service-configuration-reference.md) cikk.
- Lásd: hogyan a [zárolásának Iris](tutorial-classifying-iris-part-1.md) oktatóanyag projekt megőrizni a modell betanítását kimenetek mappát használja.

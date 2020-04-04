---
title: Adatok és modellek felfedezése windowsos rendszerben
titleSuffix: Azure Data Science Virtual Machine
description: Adatfeltárási és modellezési feladatok végrehajtása a Windows adatelemzési virtuális gépen.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 8da8cd7110cd17d0aadd52cce1263c7c0fcfdf5c
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632172"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Tíz dolog, amit tehetünk a Windows Data Science virtuális gép

A Windows Data Science virtuális gép (DSVM) egy hatékony adatelemzési fejlesztési környezet, ahol adatfeltárási és modellezési feladatokat hajthat végre. A környezet már számos népszerű adatelemzési eszközzel rendelkezik, amelyek megkönnyítik a helyszíni, felhőbeli vagy hibrid telepítések elemzésének megkezdését. 

A DSVM szorosan együttműködik az Azure-szolgáltatásokkal. Az Azure SQL Data Warehouse, az Azure Data Lake, az Azure Storage vagy az Azure Cosmos DB már tárolt adatok olvasására és feldolgozására képes. Más elemzési eszközök, például az Azure Machine Learning és az Azure Data Factory előnyeit is kihasználhatja.

Ebben a cikkben megtudhatja, hogyan használhatja a DSVM adatelemzési feladatok at, és más Azure-szolgáltatásokkal való együttműködés. Íme néhány dolog, amit tehetünk a DSVM:

- A Microsoft Machine Learning Server és a Python használatával adatokat fedezhet fel, és helyileg fejleszthet modelleket a DSVM-en.
- A Jupyter-jegyzetfüzetek használatával kísérletezhet az adatokkal a böngészőben a Python 2, a Python 3 és a Microsoft R használatával (a Microsoft R az R nagyvállalati használatra kész verziója, amelyet teljesítményre terveztek.)
- Az R és Python segítségével az Azure Machine Learningen készült modelleket helyezheti üzembe, így az ügyfélalkalmazások egy egyszerű webes szolgáltatásfelület használatával érhetik el a modelleket.
- Az Azure-erőforrások felügyelete az Azure Portalon vagy a PowerShell használatával.
- Bővítse tárhelyét, és ossza meg a nagyméretű adatkészleteket/kódokat az egész csapatban egy Azure Files megosztás létrehozásával a DSVM csatlakoztatható meghajtójaként.
- A GitHub használatával kódot oszthat meg a csapatával. A tárház elérése az előre telepített Git-ügyfelek használatával: Git Bash és Git GUI.
- Hozzáférhet az Azure-adatokhoz és elemzési szolgáltatásokhoz, például az Azure Blob storage-hoz, az Azure Data Lake-hez, az Azure HDInsighthoz (Hadoop), az Azure Cosmos DB-hez, az Azure SQL Data Warehouse-hoz és az Azure SQL Database-hez.
- Jelentéseket és irányítópultot hozhat létre a DSVM-re előre telepített Power BI Desktop-példány használatával, és telepítheti őket a felhőben.
- Dinamikusan méretezheti a DSVM-et, hogy megfeleljen a projekt igényeinek.
- Telepítsen további eszközöket a virtuális gépre.   

> [!NOTE]
> Az ebben a cikkben felsorolt számos adattárolási és -elemzési szolgáltatásra további használati díjak vonatkoznak. További részletek az [Azure díjszabási](https://azure.microsoft.com/pricing/) oldalán.
> 
> 

## <a name="prerequisites"></a>Előfeltételek

* Szüksége van egy Azure-előfizetésre. Akkor [iratkozzon fel egy ingyenes próbaverzió](https://azure.microsoft.com/free/).
* Az Azure Portalon az adatelemzési virtuális gép kiépítésére vonatkozó utasítások a [Virtuális gép létrehozása című](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)részben érhetők el.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Adatok feltárása és modellek fejlesztése a Microsoft Machine Learning Server rel
Használhatja a nyelvek, mint az R és a Python, hogy az adatok elemzését közvetlenül a DSVM.You can use languages like R and Python to do your data analytics right on the DSVM.

Az R, akkor egy IDE, mint RStudio, amely megtalálható a start menüben vagy az asztalon. Vagy használhatja az R Tools for Visual Studio alkalmazást is. A Microsoft további könyvtárakat biztosított a nyílt forráskódú CRAN R mellett, hogy lehetővé tegye a méretezhető elemzéseket és a párhuzamos darabolt elemzésben engedélyezett memóriaméretnél nagyobb adatok elemzését. 

A Python, használhatja az IDE, mint a Visual Studio Community Edition, amely a Python Tools for Visual Studio (PTVS) bővítmény előre telepítve van. Alapértelmezés szerint csak a Python 3.6, a root Conda környezet, a PTVS-en van konfigurálva. Az Anaconda Python 2.7 engedélyezéséhez tegye a következő lépéseket:

1. Hozzon létre egyéni környezeteket az egyes verziókhoz a **Tools** > **Python Tools** > **Python-környezetek**lapon, majd válassza a **+ Egyéni** lehetőséget a Visual Studio Community Edition alkalmazásban.
1. Adjon leírást, és állítsa be a környezeti előtag elérési útját **c:\anaconda\envs\python2** néven az Anaconda Python 2.7-hez.
1. A környezet mentéséhez válassza az **Automatikus észlelés** > **alkalmazása lehetőséget.**

A Python-környezetek létrehozásáról a [PTVS dokumentációjában](https://aka.ms/ptvsdocs) olvashat bővebben.

Most már be van állítva egy új Python-projekt létrehozására. Nyissa meg az**Új** > **project** > **Python** **fájlját,** > és válassza ki az éppen felépített Python-alkalmazás típusát. Beállíthatja, hogy az aktuális projekt Python-környezete a kívánt verzióra (Python 2.7 vagy 3.6) legyen, ha a jobb gombbal a **Python-környezetek** elemre kattint, majd a **Python-környezetek hozzáadása/eltávolítása parancsot választja.** A PTVS-ről további információt a [termék dokumentációjában](https://aka.ms/ptvsdocs)talál.

## <a name="use-jupyter-notebooks"></a>A Jupyter notebookok használata
A Jupyter notebook böngészőalapú IDE-t biztosít az adatok feltárásához és modellezéséhez. A Python 2, Python 3 vagy R (nyílt forráskódú és Microsoft R Server) jupyter-jegyzetfüzetekben is használható.

A Jupyter-jegyzetfüzet elindításához válassza a **Jupyter Notebook** ikont a **Start** menüben vagy az asztalon. A DSVM parancssorban a parancsot ```jupyter notebook``` abból a könyvtárból is futtathatja, ahol meglévő jegyzetfüzetekkel rendelkezik, vagy ahol új jegyzetfüzeteket szeretne létrehozni.  

A Jupyter indítása után meg kell jelennie egy könyvtárnak, amely néhány példajegyzetfüzetet tartalmaz, amelyek előre vannak csomagolva a DSVM-be. Ezután:

* A kód megtekintéséhez jelölje ki a jegyzetfüzetet.
* Az egyes cellák futtatásához válassza a Shift+Enter lehetőséget.
* Futtassa a teljes jegyzetfüzetet a Cell Run **(Cell** > **Run)** lehetőség kiválasztásával.
* Hozzon létre egy új jegyzetfüzetet a Jupyter ikon (bal felső sarok), a jobb oldali **Új** gomb kiválasztásával, majd a jegyzetfüzet nyelvének (más néven kernelek) kiválasztásával.   

> [!NOTE]
> Jelenleg a Python 2.7, python 3.6, R, Julia és PySpark kernelek jupyter támogatottak. Az R kernel támogatja a programozást a nyílt forráskódú R és a Microsoft R.The R kernel supports programming in both open-source R and Microsoft R.   
> 
> 

Amikor a jegyzetfüzetben van, megismerheti az adatokat, létrehozhatja a modellt, és tesztelheti a modellt a kiválasztott kódtárak használatával.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Modellek betanítása és üzembe helyezése az Azure Machine Learning használatával
Miután elkészítette és érvényesítette a modellt, a következő lépés általában az éles környezetben való üzembe helyezése. Ez a lépés lehetővé teszi, hogy az ügyfélalkalmazások valós idejű vagy kötegelt módban meghívják a modell-előrejelzéseket. Az Azure Machine Learning egy mechanizmust biztosít az R-be vagy a Pythonba beépített modell működőképessé tétele érdekében.

Amikor működőképessé teszi a modellt az Azure Machine Learningben, egy webszolgáltatás elérhetővé válik. Lehetővé teszi az ügyfelek számára, hogy rest-hívásokat, amelyek átmennek a bemeneti paraméterek et, és a modelltől származó előrejelzéseket kimenetként fogadnak.

### <a name="build-and-operationalize-python-models"></a>Python-modellek létrehozása és üzembe építése
Íme egy kódrészlet, amelyet egy Python Jupyter notebookban fejlesztettek ki, és amely egy egyszerű modellt épít a Scikit-learn könyvtár használatával:

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

A Python-modellek Azure Machine Learningben való üzembe helyezéséhez használt módszer a modell előrejelzését egy függvénybe csomagolja, és az előre telepített Azure Machine Learning Python-könyvtár által biztosított attribútumokkal díszíti. Az attribútumok az Azure Machine Learning munkaterület-azonosítóját, az API-kulcsot, valamint a bemeneti és visszatérési paramétereket jelölik.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

Az ügyfél most már hívásokat kezdeményezhet a webszolgáltatásba. A kényelmi burkolók a REST API-kérelmeket hozták létre. A webszolgáltatás felhasználásához az alábbiakat kell használnia:

```python
# Consume through web service URL and keys
from azureml import services
@services.service(url, api_key)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(float)
def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
pass

IrisPredictor(3,2,3,4)
```

> [!NOTE]
> Jelenleg az Azure Machine Learning-kódtár csak a Python 2.7 támogatott.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>R modellek építése és üzembe alakítása
Az Adatelemzési virtuális gépen vagy máshol épülő R-modelleket az Azure Machine Learningre olyan módon telepítheti, amely hasonló a Pythonhoz. A lépések a következők:

1. Hozzon létre egy settings.json fájlt a munkaterület-azonosító és a hitelesítési jogkivonat megadásához. 
2. Írjon egy burkoló a modell előrejelzési függvény.
3. Hívja ```publishWebService``` meg az Azure Machine Learning-könyvtárat a függvényburkolóban való áthaladáshoz.  

Az alábbi eljárás és kódrészletek segítségével állítsa be, építse fel, tegye közzé és használja fel a modellt webszolgáltatásként az Azure Machine Learningben.

#### <a name="set-up"></a>Beállítás

Hozzon létre egy settings.json ```.azureml``` fájlt a kezdőkönyvtár alatt megnevezett könyvtár alatt. Adja meg az Azure Machine Learning-munkaterület paramétereit.

Itt van a settings.json fájl szerkezete:

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Modell létrehozása az R-ben és közzététel az Azure Machine Learningben

```r
library(AzureML)
ws <- workspace(config="~/.azureml/settings.json")

if(!require("lme4")) install.packages("lme4")
library(lme4)
set.seed(1)
train <- sleepstudy[sample(nrow(sleepstudy), 120),]
m <- lm(Reaction ~ Days + Subject, data = train)

# Define a prediction function to publish based on the model:
sleepyPredict <- function(newdata){
        predict(m, newdata=newdata)
}

ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)
```

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Használja fel az Azure Machine Learningben üzembe helyezett modellt
A modell egy ügyfélalkalmazásból, az Azure Machine Learning-könyvtár segítségével keresse meg a közzétett webszolgáltatás név szerint. Az `services` API-hívás segítségével határozza meg a végpontot. Ezután csak `consume` hívja meg a függvényt, és adja át az adatkeretet, hogy előre jelezze.

Az alábbi kód használatával használja az Azure Machine Learning webszolgáltatásként közzétett modellt:

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# Try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

Az [R-csomagokról a Machine Learning Studio alkalmazásban](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning)talál további információt.

## <a name="manage-azure-resources"></a>Azure-erőforrások kezelése
A DSVM nem csak lehetővé teszi, hogy az elemzési megoldás helyileg a virtuális gépen. Azt is lehetővé teszi, hogy az Azure felhőplatformon szolgáltatások eléréséhez. Az Azure számos számítási, tárolási, adatelemzést és egyéb szolgáltatást kínál, amelyeket felügyelhet és elérhet a DSVM-ből.

Az Azure-előfizetés és a felhőalapú erőforrások felügyeletéhez két lehetősége van:
+ Használja a böngészőt, és nyissa meg az [Azure Portalon.](https://portal.azure.com)

+ PowerShell-parancsfájlok használata. Futtassa az Azure PowerShellt az asztalon vagy a **Start** menüben lévő parancsikonról. A részleteket a [Microsoft Azure PowerShell dokumentációjában](../../powershell-azure-resource-manager.md) találja. 

## <a name="extend-storage-by-using-shared-file-systems"></a>A tárolás kiterjesztése megosztott fájlrendszerekkel
Az adatszakértők nagy adatkészleteket, kódokat vagy más erőforrásokat oszthatnak meg a csapaton belül. A DSVM körülbelül 45 GB szabad területtel rendelkezik. A tárhely bővítéséhez használhatja az Azure Files-t, és csatlakoztathatja egy vagy több DSVM-példányhoz, vagy hozzáférhet a REST API-n keresztül. Használhatja az [Azure Portalon,](../../virtual-machines/windows/attach-managed-disk-portal.md) vagy az [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) segítségével további dedikált adatlemezek et adhat hozzá. 

> [!NOTE]
> Az Azure Files megosztás maximális területe 5 TB. Az egyes fájlok méretkorlátja 1 TB. 

Ezzel a parancsfájllal azure PowerShellben azure-fájlok megosztást hozhat létre:

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Most, hogy létrehozott egy Azure Files-megosztást, csatlakoztathatja azt az Azure bármely virtuális gépéhez. Azt javasoljuk, hogy helyezze a virtuális gép ugyanabban az Azure-adatközpontban, mint a tárfiók, a késés és az adatátviteli díjak elkerülése érdekében. Az azure PowerShell-parancsok a DSVM-en való csatlakoztatáshoz:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Most már elérheti ezt a meghajtót, mint bármely normális meghajtót a virtuális gép.

## <a name="share-code-in-github"></a>Kód megosztása a GitHubon
A GitHub egy kódtár, ahol kódmintákat és forrásokat találhat a különböző eszközökhöz a fejlesztői közösség által megosztott technológiák használatával. A Git-et használja technológiaként a kódfájlok verzióinak nyomon követésére és tárolására. A GitHub egy olyan platform is, ahol saját tárházat hozhat létre a csapat megosztott kódjának és dokumentációjának tárolásához, a verziókövetés megvalósításához és annak szabályozásához, hogy ki férhet hozzá a kód megtekintéséhez és közreműködéséhez. 

A [Git](https://help.github.com/) használatával kapcsolatos további információkért látogasson el a Git súgólapjaira. A GitHubot a csapattal való együttműködés egyik módjaként használhatja, használhatja a közösség által kifejlesztett kódot, és visszaadhat kódot a közösségnek.

A DSVM jön betöltött ügyféleszközök a parancssorban és a GUI a GitHub-tárház eléréséhez. A Git és a GitHub parancssori eszköz neve Git Bash. A Visual Studio telepítve van a DSVM-en, és rendelkezik a Git-bővítményeket. Az eszközök ikonjait a **Start** menüben és az asztalon találja.

A github-tárházból származó kód letöltéséhez használja a ```git clone``` parancsot. Ha például a Microsoft által közzétett adattárt szeretné letölteni az aktuális könyvtárba, a következő parancsot futtathatja a Git Bash alkalmazásban:

    git clone https://github.com/Azure/DataScienceVM.git

A Visual Studióban ugyanezt a klónozási műveletet is elvégezheti. Az alábbi képernyőkép bemutatja, hogyan érheti el a Git- és a GitHub-eszközöket a Visual Studióban:

![Képernyőkép a Visual Studio-ról a GitHub-kapcsolat tal](./media/vm-do-ten-things/VSGit.PNG)

További információt a Git használatával a GitHub-tárház használatával a github.com elérhető erőforrásokból. A [csaló lap](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) hasznos referencia.

## <a name="access-azure-data-and-analytics-services"></a>Azure-adat- és elemzési szolgáltatások elérése
### <a name="azure-blob-storage"></a>Azure Blob Storage
Az Azure Blob storage egy megbízható, gazdaságos felhőalapú tárolási szolgáltatás nagy és nagy adattároláshoz. Ez a szakasz azt ismerteti, hogyan helyezheti át az adatokat a Blob storage-ba, és hogyan érheti el az Azure blobban tárolt adatokat.

#### <a name="prerequisites"></a>Előfeltételek

* Hozza létre az Azure Blob tárfiókját az [Azure Portalon.](https://portal.azure.com)

   ![Képernyőkép a tárfiók létrehozásának folyamatáról az Azure Portalon](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Ellenőrizze, hogy a parancssori AzCopy ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```eszköz előre telepítve van-e: . Az azcopy.exe programot tartalmazó könyvtár már szerepel a PATH környezeti változóján, így az eszköz futtatásakor elkerülheti a teljes parancselérési út beírását. Az AzCopy eszközről további információt az [AzCopy dokumentációjában](../../storage/common/storage-use-azcopy.md)talál.
* Indítsa el az Azure Storage Explorer eszközt. Letöltheti a [Storage Explorer weblapról.](https://storageexplorer.com/) 

   ![Képernyőkép: az Azure Storage Explorer egy tárfiókhoz való hozzáféréséről](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Adatok áthelyezése virtuális gépről Egy Azure-blobba: AzCopy

Az adatok áthelyezése a helyi fájlok és a Blob storage között használhatja az AzCopy-t a parancssorban vagy a PowerShellben:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Cserélje le a **C:\myfolder mappát** a fájl tárolási útvonalára, a **mystorageaccount-ot** a Blob storage-fiók nevére, a tárolónévvel rendelkező **mycontainert** és a **tárfiók kulcsát** a Blob storage-hozzáférési kulcsra. A tárfiók hitelesítő adatait az [Azure Portalon](https://portal.azure.com)találja.

Futtassa az AzCopy parancsot a PowerShellben vagy egy parancssorból. Íme néhány példa az AzCopy parancs használatára:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Miután futtatta az AzCopy parancsot egy Azure blobba, a fájl megjelenik az Azure Storage Explorerben.

![Képernyőkép a tárfiókról, a feltöltött CSV-fájl megjelenítésével](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Adatok áthelyezése virtuális gépről Azure-blobba: Azure Storage Explorer

Az Azure Storage Explorer használatával a helyi fájlból is feltölthet adatokat:

* Adatok tárolóba való feltöltéséhez jelölje ki a céltárolót, és válassza a **Feltöltés** gombot. ![Képernyőkép az Azure Storage Explorer feltöltésgombjáról](./media/vm-do-ten-things/storage-accounts.png)
* Válassza ki a **fájlok** mappából feltöltődő három pontot (**...**) a Fájlok mező jobb oldalán, jelöljön ki egy vagy több fájlt a fájlrendszerből, és a **Feltöltés gombra** a fájlok feltöltésének megkezdéséhez. ![Képernyőkép a Fájlok feltöltése párbeszédpanelről](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Adatok olvasása Egy Azure-blobból: Machine Learning-olvasó modul

Az Azure Machine Learning Studio-ban használhatja az Adatok importálása modul adatok olvasására a blobból.

![Képernyőkép a Machine Learning Studio Adatok importálása moduljáról](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Adatok olvasása Egy Azure-blobból: Python ODBC

A BlobService-kódtár segítségével közvetlenül olvashat adatokat egy Jupyter-jegyzetfüzetben vagy egy Python-programban lévő blobból.

Először importálja a szükséges csomagokat:

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

Ezután csatlakoztassa a Blob storage-fiók hitelesítő adatait, és olvassa el az adatokat a blobból:

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

Az adatok at adatkeretként olvassa be a rendszer:

![Képernyőkép az adatok első 10 soráról](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Az Azure Data Lake Storage egy nagy méretű tárház big data-elemzési számítási feladatokhoz, és kompatibilis a Hadoop distributed file system (HDFS) rendszerrel. Együttműködik a Hadoop, a Spark és az Azure Data Lake Analytics szolgáltatással. Ebben a szakaszban megtudhatja, hogyan helyezheti át az adatokat az Azure Data Lake Storage-ba, és hogyan futtathat elemzéseket az Azure Data Lake Analytics használatával.

#### <a name="prerequisites"></a>Előfeltételek

* Hozza létre az Azure Data Lake Analytics-példányt az [Azure Portalon.](https://portal.azure.com)

   ![Képernyőkép: Data Lake Analytics-példány létrehozása az Azure Portalról](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* Az [Azure Data Lake és a Stream Analytics Tools for Visual Studio beépülő modul](https://www.microsoft.com/download/details.aspx?id=49504) már telepítve van a Visual Studio Community Edition szolgáltatásban a virtuális gépen. Miután elindítja a Visual Studio-t, és bejelentkezik az Azure-előfizetésbe, az Azure Data Analytics-fiókját és tárhelyét a Visual Studio bal oldali paneljén kell látnia.

   ![Képernyőkép a Visual Studio Data Lake eszközeinek beépülő moduljáról](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Adatok áthelyezése virtuális gépről a Data Lake-be: Azure Data Lake Explorer

Az Azure Data Lake Explorer segítségével [adatokat tölthet fel a virtuális gép helyi fájljaiból a Data Lake Storage szolgáltatásba.](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)

Az Azure Data Factory használatával adatfolyamatot is létrehozhat az Azure Data Lake-be vagy az Azure Data Lake-ből történő adatáthelyezés működőképessé [tételéhez.](https://azure.microsoft.com/services/data-factory/) [Ez](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) a cikk végigvezeti az adatfolyamatok létrehozásának lépésein.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Adatok olvasása Azure-blobból data lake-be: U-SQL

Ha az adatok az Azure Blob storage-ban találhatók, közvetlenül olvashat adatokat egy Azure-blobból egy U-SQL-lekérdezésben. Az U-SQL-lekérdezés összeállítása előtt győződjön meg arról, hogy a Blob storage-fiók kapcsolódik az Azure Data Lake-példányhoz. Nyissa meg az Azure Portalon, keresse meg az Azure Data Lake Analytics irányítópultját, válassza **az Adatforrás hozzáadása**lehetőséget, válassza ki az Azure **Storage**tárolási típusát, és csatlakoztassa az Azure-tárfiók nevét és kulcsát. Ezután hivatkozhat a tárfiókban tárolt adatokra.

![Képernyőkép az Adatforrás hozzáadása párbeszédpanelről](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

A Visual Studio-ban adatokat olvashat a Blob storage-ból, kezelheti az adatokat, mérnökként kezelheti a funkciókat, és elküldheti az eredményül kapott adatokat az Azure Data Lake vagy az Azure Blob storage-ba. Amikor a Blob storage-ban hivatkozik az adatokra, használja **a wasb://.** Amikor az Azure Data Lake-ben hivatkozik az adatokra, használja **swbhdfs://.**

A Visual Studio következő U-SQL-lekérdezéseit használhatja:

```usql
@a =
    EXTRACT medallion string,
            hack_license string,
            vendor_id string,
            rate_code string,
            store_and_fwd_flag string,
            pickup_datetime string,
            dropoff_datetime string,
            passenger_count int,
            trip_time_in_secs double,
            trip_distance double,
            pickup_longitude string,
            pickup_latitude string,
            dropoff_longitude string,
            dropoff_latitude string

    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
    USING Extractors.Csv();

@b =
    SELECT vendor_id,
    COUNT(medallion) AS cnt_medallion,
    SUM(passenger_count) AS cnt_passenger,
    AVG(trip_distance) AS avg_trip_dist,
    MIN(trip_distance) AS min_trip_dist,
    MAX(trip_distance) AS max_trip_dist,
    AVG(trip_time_in_secs) AS avg_trip_time
    FROM @a
    GROUP BY vendor_id;

OUTPUT @b   
TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
USING Outputters.Csv();

OUTPUT @b   
TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
USING Outputters.Csv();
```

A lekérdezés kiszolgálóra való elküldése után egy diagram mutatja a feladat állapotát.

![Képernyőkép a feladat állapotábrájáról](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Adatok lekérdezése a Data Lake-ben: U-SQL

Miután az adatkészlet et bevan betöltése az Azure Data Lake-ben, [az U-SQL nyelv](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) használatával lekérdezheti és felderítheti az adatokat. Az U-SQL nyelv hasonló a T-SQL-hez, de a C# egyes szolgáltatásait kombinálja, így a felhasználók testre szabott modulokat és felhasználó által definiált függvényeket írhatnak. Használhatja a parancsfájlok az előző lépésben.

A lekérdezés kiszolgálóra küldése után tripdata_summary. CsV jelenik meg az Azure Data Lake Explorer. Az adatok előnézetének megtekintéséhez kattintson a jobb gombbal a fájlra.

![Képernyőkép a Data Lake Explorer CSV-fájljáról](./media/vm-do-ten-things/USQL_create_summary.png)

A fájlinformáció a következő:

![Képernyőkép a fájl összesítő információiról](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>HDInsight Hadoop-fürtök
Az Azure HDInsight egy felügyelt Apache Hadoop, Spark, HBase és Storm szolgáltatás a felhőben. Az Azure HDInsight-fürtökkel egyszerűen dolgozhat az adatelemzési virtuális gépből.

#### <a name="prerequisites"></a>Előfeltételek

* Hozza létre az Azure Blob tárfiókját az [Azure Portalon.](https://portal.azure.com) Ez a tárfiók a HDInsight-fürtök adatainak tárolására szolgál.

   ![Képernyőkép: tárfiók létrehozása az Azure Portalról](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Az Azure HDInsight Hadoop-fürtök testreszabása az [Azure Portalról.](../team-data-science-process/customize-hadoop-cluster.md)
  
   A hdinsight-fürttel létrehozott tárfiók összekapcsolása annak létrehozásakor. Ez a tárfiók a fürtön belül feldolgozható adatok elérésére szolgál.

   ![A HDInsight-fürttel létrehozott tárfiók összekapcsolására szolgáló beállítások](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* A távoli asztali hozzáférés engedélyezése a fürt fő csomópontjához a létrehozása után. Ne feledje az itt megadott távelérési hitelesítő adatokat, mert a következő eljárásban szüksége lesz rájuk.

   ![A Távoli asztal gomb a HDInsight-fürt távoli elérésének engedélyezéséhez](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Hozzon létre egy Azure Machine Learning-munkaterületet. A Machine Learning-kísérletek et ebben a Machine Learning-munkaterületen tárolják. Válassza ki a kijelölt beállításokat a portálon, ahogy az a következő képernyőképen látható:

   ![Azure Machine Learning-munkaterület létrehozása](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Adja meg a munkaterület paramétereit.

   ![Gépi tanulási munkaterület paramétereinek megadása](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Adatok feltöltése az IPython Notebook használatával. Importálja a szükséges csomagokat, csatlakoztassa a hitelesítő adatokat, hozzon létre egy adatbázist a tárfiókban, majd töltse be az adatokat HDI-fürtökbe.

```python
# Import required packages
import pyodbc
import time as time
import json
import os
import urllib
import urllib2
import warnings
import re
import pandas as pd
import matplotlib.pyplot as plt
from azure.storage.blob import BlobService
warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


# Create the connection to Hive by using ODBC
SERVER_NAME = 'xxx.azurehdinsight.net'
DATABASE_NAME = 'nyctaxidb'
USERID = 'xxx'
PASSWORD = 'xxxx'
DB_DRIVER = 'Microsoft Hive ODBC Driver'
driver = 'DRIVER={' + DB_DRIVER + '}'
server = 'Host=' + SERVER_NAME + ';Port=443'
database = 'Schema=' + DATABASE_NAME
hiveserv = 'HiveServerType=2'
auth = 'AuthMech=6'
uid = 'UID=' + USERID
pwd = 'PWD=' + PASSWORD
CONNECTION_STRING = ';'.join(
    [driver, server, database, hiveserv, auth, uid, pwd])
connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
cursor = connection.cursor()


# Create the Hive database and tables
queryString = "create database if not exists nyctaxidb;"
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.trip
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    rate_code string,
                    store_and_fwd_flag string,
                    pickup_datetime string,
                    dropoff_datetime string,
                    passenger_count int,
                    trip_time_in_secs double,
                    trip_distance double,
                    pickup_longitude double,
                    pickup_latitude double,
                    dropoff_longitude double,
                    dropoff_latitude double)  
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.fare
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    pickup_datetime string,
                    payment_type string,
                    fare_amount double,
                    surcharge double,
                    mta_tax double,
                    tip_amount double,
                    tolls_amount double,
                    total_amount double)
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)


# Upload data from Blob storage to an HDI cluster
for i in range(1, 13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
```

Másik lehetőségként kövesse [ezt a forgatókönyvet](../team-data-science-process/hive-walkthrough.md) a NYC Taxi-adatok HDI-fürtbe való feltöltéséhez. A főbb lépések a következők:
  
* Az AzCopy segítségével letöltheti a tömörített csv-ket a nyilvános blobból a helyi mappába.
* Az AzCopy segítségével a helyi mappából feltölthet idott csv-ket egy HDI-fürtbe.
* Jelentkezzen be a Hadoop-fürt fő csomópontjára, és készüljön fel a feltáró adatok elemzésére.

Miután az adatok betöltődtek a HDI-fürtbe, ellenőrizheti az adatokat az Azure Storage Explorerben. És a nyctaxidb adatbázis jött létre a HDI klaszter.

#### <a name="data-exploration-hive-queries-in-python"></a>Adatok feltárása: Hive-lekérdezések pythonban

Mivel az adatok egy Hadoop-fürtben vannak, a pyodbc csomag segítségével csatlakozhat a Hadoop-fürtökhöz és a lekérdezési adatbázisokhoz a Hive használatával a feltárás és a szolgáltatástervezés. Megtekintheti az előfeltételi lépésben létrehozott meglévő táblákat.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![Meglévő táblák megtekintése](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Nézzük meg az egyes havi rekordok számát és a tippek gyakoriságát az utazási táblázatban:

```python
queryString = """
    select month, count(*) from nyctaxidb.trip group by month;
    """
results = pd.read_sql(queryString,connection)

%matplotlib inline

results.columns = ['month', 'trip_count']
df = results.copy()
df.index = df['month']
df['trip_count'].plot(kind='bar')
```

![Rekordok számának megnyomtatása minden hónapban](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

```python
queryString = """
    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;
    """
results = pd.read_sql(queryString, connection)

results.columns = ['tipped', 'trip_count']
df = results.copy()
df.index = df['tipped']
df['trip_count'].plot(kind='bar')
```

![A csúcsfrekvenciák nyomtatása](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Kiis számíthatja a felvételi hely és a leadási hely közötti távolságot, majd összehasonlíthatja az utazási távolsággal.

```python
queryString = """
                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                    from nyctaxidb.trip
                    where month=1
                        and pickup_longitude between -90 and -30
                        and pickup_latitude between 30 and 90
                        and dropoff_longitude between -90 and -30
                        and dropoff_latitude between 30 and 90;
            """
results = pd.read_sql(queryString, connection)
results.head(5)
```

![A felvételi és a leadási asztal felső sorai](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![A felvételi/leadási távolság és az úttávolság megrajzolása](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Most készítsünk elő egy 1 százalékkal csökkent (1 százalék) adatkészletet a modellezéshez. Ezeket az adatokat a Machine Learning olvasó modulban használhatja.

```python
queryString = """
create  table if not exists nyctaxi_downsampled_dataset_testNEW (
medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\\n'
stored as textfile;
"""
cursor.execute(queryString)
```

Most szúrja be az illesztés tartalmát az előző belső táblába.

```python
queryString = """
insert overwrite table nyctaxi_downsampled_dataset_testNEW
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
+cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
rand() as sample_key

from trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
"""
cursor.execute(queryString)
```

Egy idő után láthatja, hogy az adatok be töltésre kerültek a Hadoop-fürtökben:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![A táblázat első sorai](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Azure SQL Data Warehouse és adatbázisok
Az Azure SQL Data Warehouse egy rugalmas adattárház, amely nagyvállalati szintű SQL Server-élményt nyújtó szolgáltatás.

Az Azure SQL-adattárházat a [cikkben](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)található utasításokat követve építheti ki. Az SQL-adattárház kiépítése után ezzel a [forgatókönyvvel](../team-data-science-process/sqldw-walkthrough.md) adatfeltöltést, feltárást és modellezést hajthat végre az SQL-adattárházon belüli adatok használatával.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Az Azure Cosmos DB egy NoSQL-adatbázis a felhőben. Használhatja a JSON-hoz hasonló dokumentumokkal való munkára, valamint a dokumentumok tárolására és lekérdezésére.

Az Azure Cosmos DB dsvm-ből való eléréséhez kövesse az alábbi előfeltételeket:

1. The Azure Cosmos DB Python SDK is already installed on the DSVM. A frissítéshez ```pip install pydocumentdb --upgrade``` futtassa a parancssorból.
2. Hozzon létre egy Azure Cosmos DB-fiókot és adatbázist az [Azure Portalról.](https://portal.azure.com)
3. Töltse le az Azure Cosmos DB adatáttelepítési eszközt a [Microsoft letöltőközpontjából,](https://www.microsoft.com/download/details.aspx?id=53595) és bontsa ki az Ön által választott könyvtárba.
4. Importálja a [nyilvános blobban](https://cahandson.blob.core.windows.net/samples/volcano.json) tárolt JSON-adatokat (vulkánadatokat) az Azure Cosmos DB-be a következő parancsparaméterekkel az áttelepítési eszközbe. (Használja a dtui.exe programot abból a könyvtárból, ahol az Azure Cosmos DB adatáttelepítési eszközt telepítette.) Adja meg a forrás- és célhelyet a következő paraméterekkel:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Az adatok importálása után lépjen a Jupyter re, és nyissa meg a *DocumentDBSample*című jegyzetfüzetet. Python-kódot tartalmaz az Azure Cosmos DB eléréséhez, és néhány alapvető lekérdezési. Az Azure Cosmos DB-ről a szolgáltatás [dokumentációs oldalán olvashat bővebben.](https://docs.microsoft.com/azure/cosmos-db/)

## <a name="use-power-bi-reports-and-dashboards"></a>Power BI-jelentések és irányítópultok használata 
A Volcano JSON-fájlt az előző Azure Cosmos DB példából a Power BI Desktopban vizualizálhatja, hogy vizuális betekintést nyerjen az adatokba. A részletes lépések a [Power BI cikkében](../../cosmos-db/powerbi-visualize.md)találhatók. Itt vannak a magas szintű lépések:

1. Indítsa el a Power BI Desktopot, majd válassza az **Adatok beolvasása** lehetőséget. Adja meg az `https://cahandson.blob.core.windows.net/samples/volcano.json`URL-címet: .
2. A JSON-rekordokat listaként kell látnia. A lista átalakítása táblázattá, hogy a Power BI működhessen vele.
4. Bontsa ki az oszlopokat a kibontás (nyíl) ikon kiválasztásával.
5. Figyelje meg, hogy a hely **rekordmező.** Bontsa ki a rekordot, és csak a koordinátákat jelölje ki. **A Koordináta** egy listaoszlop.
6. Új oszlop hozzáadása a lista koordinátaoszlopának vesszővel tagolt **LatLong** oszloprá alakításához. A koordinátalista mező két elemének összefűzése a képlet ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```használatával.
7. Konvertálja a **Magasság** oszlopot decimálisra, és jelölje ki a **Bezárás** és **az Alkalmaz** gombot.

Az előző lépések helyett a következő kódot illesztheti be. Kiírja a Power BI Speciális szerkesztőjében az adatátalakítások lekérdezési nyelven történő írásához használt lépéseket.

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

Most már rendelkezik az adatokkal a Power BI adatmodelljében. A Power BI Desktop példánya a következőképpen jelenik meg:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Az adatmodell használatával megkezdheti a jelentések és vizualizációk készítését. A jelen Power [BI-cikkben](../../cosmos-db/powerbi-visualize.md#build-the-reports) leírt lépéseket követve készíthet jelentést.

## <a name="scale-the-dsvm-dynamically"></a>A DSVM dinamikus méretezése 
A DSVM-et fel-le skálázhatja a projekt igényeinek megfelelően. Ha nem kell használnia a virtuális gép az esti vagy hétvégi, leállíthatja a virtuális gép az [Azure Portalon.](https://portal.azure.com)

> [!NOTE]
> Számítási díjakat kell fizetnie, ha csak a leállítás gombot használja a virtuális gép operációs rendszeréhez.  
> 
> 

Előfordulhat, hogy kezelnie kell néhány nagyméretű elemzést, és több PROCESSZOR-, memória- vagy lemezkapacitásra van szüksége. Ha igen, a virtuális gép méretei közül választhat a CPU-magok, a mélytanulás GPU-alapú példányai, a memóriakapacitás és a lemeztípusok (beleértve az SSD-meghajtókat is), amelyek megfelelnek a számítási és költségvetési igényeknek. A virtuális gépek teljes listája, valamint az óránkénti számítási díjszabás, elérhető az [Azure virtuális gépek díjszabási](https://azure.microsoft.com/pricing/details/virtual-machines/) oldalon érhető el.

Hasonlóképpen a virtuális gép feldolgozási kapacitásának igénye is csökkenhet. (Például: egy nagyobb számítási feladatot helyezett át egy Hadoop- vagy Spark-fürtbe.) Ezután az [Azure Portalon](https://portal.azure.com) leskálázhatja a fürtöt, és a virtuálisgép-példány beállításait. 

## <a name="add-more-tools"></a>További eszközök hozzáadása
A DSVM-be előre beépített eszközök számos gyakori adatelemzési igényre képesek. Ezzel időt takaríthat meg, mert nem kell egyenként telepítenie és konfigurálnia a környezeteket. Ez is pénzt takarít meg, mert fizetni csak forrásokat, hogy használja.

A cikkben profilozott egyéb Azure-adat- és elemzési szolgáltatások használatával javíthatja az elemzési környezetet. Bizonyos esetekben további eszközökre lehet szüksége, beleértve néhány saját partnereszközt is. Teljes körű felügyeleti hozzáféréssel rendelkezik a virtuális gépen a szükséges új eszközök telepítéséhez. További, pythonban és R-ben is telepíthet olyan csomagokat, amelyek nincsenek előre telepítve. A Python esetén használhatja a ```conda``` vagya. ```pip``` Az R esetében ```install.packages()``` használhatja az R konzolt, vagy használhatja az IDE-t, és válassza **a Csomagok** > **telepítése lehetőséget**.

## <a name="deep-learning"></a>Deep learning

A keretrendszer-alapú minták mellett átfogó forgatókönyveket is kaphat, amelyek a DSVM-en lettek érvényesítve. Ezek a forgatókönyvek segítségével elindíthatja a mélytanulási alkalmazások fejlesztését olyan területeken, mint a kép és a szöveg/nyelv megértése.   


- [Neurális hálózatok futtatása különböző keretrendszerek között:](https://github.com/ilkarman/DeepLearningFrameworks)Ez a forgatókönyv bemutatja, hogyan lehet áttelepíteni a kódot az egyik keretrendszerből a másikba. Azt is bemutatja, hogyan lehet összehasonlítani a modelleket és a futásidejű teljesítményt a keretrendszerek között. 

- [Útmutató útmutató egy végponttól végpontig használható megoldás létrehozásához a képeken belüli termékek észlelésére:](https://github.com/Azure/cortana-intelligence-product-detection-from-images)A képfelismerés olyan technika, amely képes megtalálni és osztályozni az objektumokat a képeken belül. Ez a technológia rendelkezik azzal a potenciállal, hogy hatalmas jutalmakat számos valós üzleti területeken. A kiskereskedők például ezzel a technikával határozhatják meg, hogy a vevő melyik terméket vette fel a polcról. Ez az információ pedig segít a termékkészlet kezelésében. 

- [Mély tanulás audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Ez a bemutató bemutatja, hogyan kell a vonat egy mély tanulási modell audio esemény felismerésa a [városi hangok adatkészlet](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). Áttekintést nyújt a hangadatok kezeléséről is.

- [Szöveges dokumentumok besorolása](https://github.com/anargyri/lstm_han): Ez a forgatókönyv bemutatja, hogyan hozhat létre és taníthat be két neurális hálózati architektúrát: hierarchikus figyelemhálózat és hosszú rövid távú memória (LSTM) hálózat. Ezek a neurális hálózatok a Keras API-t használják a mély tanuláshoz a szöveges dokumentumok besorolásához. A Keras a három legnépszerűbb mélytanulási keretrendszer elülső része: Microsoft Cognitive Toolkit, TensorFlow és Theano.

## <a name="summary"></a>Összefoglalás
Ez a cikk néhány, a Microsoft Data Science virtuális gépen ellátható teendőt ismertetett. Sokkal több dolgot tehet, hogy a DSVM hatékony elemzési környezet.


---
title: Az adatelemzés és a modell megismerése Windows rendszeren
titleSuffix: Azure Data Science Virtual Machine
description: Adatfelderítési és-modellezési feladatok végrehajtása a Windows Data Science Virtual Machineon.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 75a685dc90db9133ee9bc5d52d046246270ea32a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497607"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Tíz dolog, amit elvégezhet a Windows Data Science Virtual Machine

A Windows Data Science Virtual Machine (DSVM) az adatelemzési és-modellezési feladatok elvégzésére szolgáló hatékony adattudományi fejlesztési környezet. A környezet már több népszerű adatelemző eszközzel is rendelkezik, amelyek megkönnyítik a helyszíni, Felhőbeli vagy hibrid üzembe helyezések elemzését. 

A DSVM szorosan együttműködik az Azure-szolgáltatásokkal. Elolvashatja és feldolgozhatja az Azure-ban már tárolt, Azure SQL Data Warehouse, Azure Data Lake, Azure Storage vagy Azure Cosmos DB rendszerű adattárakat. Más elemzési eszközök, például a Azure Machine Learning és a Azure Data Factory előnyeit is kihasználhatja.

Ebből a cikkből megtudhatja, hogyan használhatja a DSVM az adatelemzési feladatok elvégzésére és más Azure-szolgáltatásokkal való interakcióra. Íme néhány dolog, amit elvégezhet a DSVM:

- Microsoft Machine Learning Server és Python használatával megismerheti az adatelemzést és a modellek helyi fejlesztését a DSVM.
- A Jupyter notebook használatával kísérletezhet a böngészőben tárolt adataival a Python 2, a Python 3 és a Microsoft R használatával. (a Microsoft R az R nagyvállalati használatra kész, teljesítményre tervezett verziója.)
- Az R és a Python használatával létrehozott modellek üzembe helyezése Azure Machine Learning, így az ügyfélalkalmazások egy egyszerű webszolgáltatás-felületen keresztül érhetik el a modelljeiket.
- Az Azure-erőforrások felügyelete a Azure Portal vagy a PowerShell használatával.
- Kiterjesztheti tárhelyét, és megoszthatja a nagyméretű adatkészleteket/kódokat a teljes csapaton belül, ha létrehoz egy Azure Files-megosztást csatlakoztatott meghajtóként a DSVM.
- Ossza meg a kódot a csapatával a GitHub használatával. Az adattárhoz az előre telepített git-ügyfelek használatával férhet hozzá: git bash és git GUI.
- Az Azure-beli adatkezelési és elemzési szolgáltatások, például az Azure Blob Storage, az Azure Data Lake, az Azure HDInsight (Hadoop), a Azure Cosmos DB, a Azure SQL Data Warehouse és a Azure SQL Database elérése.
- Készítsen jelentéseket és irányítópultokat a DSVM előre telepített Power BI Desktop példányának használatával, és telepítse őket a felhőben.
- Dinamikusan méretezheti a DSVM, hogy megfeleljen a projekt igényeinek.
- Telepítsen további eszközöket a virtuális gépre.   

> [!NOTE]
> A további használati díjak a jelen cikkben felsorolt adattárolási és elemzési szolgáltatások esetében érvényesek. Részletekért tekintse meg az [Azure díjszabását](https://azure.microsoft.com/pricing/) ismertető oldalt.
> 
> 

## <a name="prerequisites"></a>Előfeltételek

* Szüksége van egy Azure-előfizetésre. Regisztrálhat [az ingyenes próbaverzióra](https://azure.microsoft.com/free/).
* A Azure Portal Data Science Virtual Machine üzembe helyezésére vonatkozó utasítások a [virtuális gépek létrehozásakor](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)érhetők el.


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Ismerje meg az adatgyűjtést és a modellek fejlesztését Microsoft Machine Learning Server
Használhatja az R és a Python nyelveket is, hogy az adatelemzést közvetlenül a DSVM végezze el.

Az R esetében használhat olyan IDE-t, mint a RStudio, amely a Start menüben vagy az asztalon található. Vagy használhatja az R Tools for Visual Studio alkalmazást. A Microsoft további könyvtárakat biztosított a nyílt forráskódú CRAN R-re vonatkozóan, hogy lehetővé tegye a skálázható elemzést, és hogy képes legyen elemezni a párhuzamosan tagolt elemzésekben engedélyezett memória méretét. 

A Python esetében használhat olyan IDE-t, mint a Visual Studio Community Edition, amelyen előre telepítve van a Python Tools for Visual Studio (PTVS) bővítmény. Alapértelmezés szerint csak a Python 3,6, a root Conda-környezet van konfigurálva a PTVS. A következő lépések elvégzésével engedélyezheti az anaconda Python 2,7-et:

1. Hozzon létre egyéni környezeteket minden verzióhoz az **eszközök** > **python-eszközök** > Python- **környezetek**területen, majd válassza a **+ Custom** elemet a Visual Studio Community Edition kiadásban.
1. Adjon meg egy leírást, és állítsa be a környezeti előtag elérési útját **c:\anaconda\envs\python2** for anaconda Python 2,7.
1. A környezet mentéséhez válassza az **automatikus észlelés** > **alkalmaz** lehetőséget.

A Python-környezetek létrehozásával kapcsolatos további információkért tekintse meg a [PTVS dokumentációját](https://aka.ms/ptvsdocs) .

Most, hogy létrehoz egy új Python-projektet. Lépjen a **fájl** > **új** > **projekt** > **Python** elemre, és válassza ki az Ön által felépített Python-alkalmazás típusát. Az aktuális projekt Python-környezetét a kívánt verzióra (Python 2,7 vagy 3,6) állíthatja be úgy, hogy a jobb gombbal a **Python-környezetek** lehetőségre kattint, majd kiválasztja a **Python-környezetek hozzáadása/eltávolítása**lehetőséget. A PTVS használatáról a [termékdokumentációban](https://aka.ms/ptvsdocs)talál további információt.

## <a name="use-jupyter-notebooks"></a>A Jupyter notebookok használata
A Jupyter Notebook egy böngészőalapú IDE-t biztosít az adatelemzéshez és a modellezéshez. A Jupyter-jegyzetfüzetekben a Python 2, a Python 3 vagy az R (nyílt forráskódú és Microsoft R Server) is használható.

A Jupyter Notebook elindításához kattintson a **Start** menüben vagy az asztalon található **Jupyter notebook** ikonra. A DSVM parancssorában a ```jupyter notebook``` parancs futtatásával is futtathatja azt a könyvtárat, ahol meglévő jegyzetfüzetekkel rendelkezik, vagy ahol új jegyzetfüzeteket kíván létrehozni.  

A Jupyter megkezdése után egy olyan könyvtárat kell látnia, amely néhány példát tartalmaz a DSVM előre csomagolt jegyzetfüzetek számára. Mostantól a következőket teheti:

* Válassza ki a jegyzetfüzetet a kód megtekintéséhez.
* Futtassa az egyes cellákat a SHIFT + ENTER billentyűkombináció kiválasztásával.
* Futtassa a teljes jegyzetfüzetet a **cella** > **futtatása**lehetőség kiválasztásával.
* Hozzon létre egy új jegyzetfüzetet úgy, hogy kiválasztja a Jupyter ikont (a bal felső sarokban), majd kiválasztja az **új** gombot a jobb oldalon, majd kiválasztja a jegyzetfüzet nyelvét (más néven kerneleket).   

> [!NOTE]
> A Jupyter-ben jelenleg a Python 2,7, a Python 3,6, az R, a Julia és a PySpark kernelek támogatottak. Az R kernel a nyílt forráskódú R és a Microsoft R platformon egyaránt támogatja a programozást.   
> 
> 

A jegyzetfüzetben megtekintheti az adatait, felépítheti a modellt, és tesztelheti a modellt a választott könyvtárak használatával.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Modellek betanítása és üzembe helyezése Azure Machine Learning használatával
A modell kiépítése és ellenőrzése után a következő lépés általában az éles környezetben történő üzembe helyezés. Ez a lépés lehetővé teszi az ügyfélalkalmazások számára, hogy valós idejű vagy kötegelt mód alapján meghívják a modell előrejelzéseit. Azure Machine Learning lehetővé teszi az R-vagy Python-ban épített modellek működővé tenni.

Ha Azure Machine Learningban működővé tenni a modellt, a webszolgáltatás elérhetővé válnak. Lehetővé teszi az ügyfelek számára, hogy olyan REST-hívásokat végezzenek, amelyek bemeneti paramétereket adnak át, és az előrejelzéseket a modellből kimenetként kapják meg.

### <a name="build-and-operationalize-python-models"></a>Python-modellek létrehozása és működővé tenni
Íme egy, a Python Jupyter jegyzetfüzetben kifejlesztett kódrészlet, amely egy egyszerű modellt hoz létre a Scikit-Learn könyvtár használatával:

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

A Python-modellek üzembe helyezéséhez használt módszer Azure Machine Learning becsomagolja a modell előrejelzését egy függvénybe, és az előre telepített Azure Machine Learning Python-könyvtár által megadott attribútumokkal díszíti. Az attribútumok a Azure Machine Learning-munkaterület AZONOSÍTÓját, az API-kulcsot, valamint a bemeneti és a visszatérési paramétereket jelölik.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

Az ügyfelek mostantól hívásokat indíthatnak a webszolgáltatásba. A kényelmes burkolók létrehozza a REST API kérelmeket. A webszolgáltatást a következő mintakód használatával lehet felhasználni:

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
> Jelenleg a Azure Machine Learning függvénytár csak a Python 2,7-es rendszeren támogatott.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>R-modellek létrehozása és működővé tenni
A Data Science Virtual Machinera vagy máshová épített R-modelleket olyan módon helyezheti üzembe, amely a Pythonhoz hasonló módon történik a Azure Machine Learning. A lépések a következők:

1. Hozzon létre egy Settings. JSON fájlt a munkaterület-azonosító és a hitelesítési jogkivonat megadásához. 
2. Írjon egy burkolót a modell előrejelzési függvényéhez.
3. Hívja meg ```publishWebService``` a Azure Machine Learning könyvtárban, hogy átadja a függvény burkolójának.  

A következő eljárás és kódrészletek használatával beállíthatja, létrehozhatja, közzéteheti és felhasználhatja a modelleket webszolgáltatásként a Azure Machine Learningban.

#### <a name="set-up"></a>Beállítás

Hozzon létre egy Settings. JSON fájlt egy ```.azureml``` nevű könyvtárban a saját kezdőkönyvtár alatt. Adja meg a paramétereket a Azure Machine Learning munkaterületen.

A Settings. JSON fájl szerkezete:

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Hozzon létre egy modellt az R-ben, és tegye közzé Azure Machine Learning

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

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>A Azure Machine Learning üzembe helyezett modell felhasználása
Ha a modellt egy ügyfélalkalmazás alapján kívánja használni, a Azure Machine Learning-függvénytár használatával keresse meg a közzétett webszolgáltatás nevét. A végpont meghatározásához használja a `services` API-hívást. Ezután hívja meg az `consume` függvényt, és adja át az adatkeretet az előrejelzéshez.

Használja a következő kódot a Azure Machine Learning webszolgáltatásként közzétett modell felhasználásához:

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

További információ az [R-csomagokról: Machine learning Studio](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning).

## <a name="manage-azure-resources"></a>Azure-erőforrások kezelése
A DSVM nem csupán azt teszi lehetővé, hogy az elemzési megoldást helyileg hozza létre a virtuális gépen. Emellett lehetővé teszi a szolgáltatások elérését az Azure Cloud platformon. Az Azure számos számítási, tárolási, adatelemzési és egyéb szolgáltatást biztosít, amelyeket felügyelhet és elérhet a DSVM.

Az Azure-előfizetések és a felhőalapú erőforrások felügyeletéhez két lehetőség közül választhat:
+ Használja a böngészőt, és lépjen a [Azure Portal](https://portal.azure.com).

+ PowerShell-parancsfájlok használata. Azure PowerShell futtatása az asztal egyik parancsikonjára vagy a **Start** menüből. A részletekért tekintse meg a [Microsoft Azure PowerShell dokumentációját](../../powershell-azure-resource-manager.md) . 

## <a name="extend-storage-by-using-shared-file-systems"></a>Tárterület kiterjesztése megosztott fájlrendszerek használatával
Az adatszakértők megoszthatnak nagy adatkészleteket, kódokat vagy más erőforrásokat a csapaton belül. A DSVM körülbelül 45 GB szabad területtel rendelkezik. A tárterület bővítéséhez Azure Files használhat, és csatlakoztathatja egy vagy több DSVM-példányhoz, vagy elérheti azt egy REST API keresztül. A [Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) is használhatja, vagy a [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) használatával további dedikált adatlemezeket adhat hozzá. 

> [!NOTE]
> A Azure Files-megosztás maximális mérete 5 TB. Az egyes fájlok maximális mérete 1 TB. 

Azure Files-megosztás létrehozásához használja a Azure PowerShell parancsfájlt:

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

Most, hogy létrehozott egy Azure Files-megosztást, csatlakoztathatja az Azure-ban található bármely virtuális géphez. Javasoljuk, hogy a virtuális gépet a Storage-fiókkal megegyező Azure-adatközpontba helyezze, hogy elkerülje a késést és az adatátviteli díjakat. Az Azure PowerShell alábbi parancsokkal csatlakoztathatja a meghajtót a DSVM:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Most már elérheti a meghajtót, ahogy a virtuális gép bármely normál meghajtóján lenne.

## <a name="share-code-in-github"></a>Kód megosztása a GitHubban
A GitHub egy olyan kódrészlet, amelyben a fejlesztői közösség által megosztott technológiák használatával különböző eszközökhöz használhat programkódokat és forrásokat. A git technológiát használja a programkódok verzióinak nyomon követésére és tárolására. A GitHub egy olyan platform is, ahol saját tárházat hozhat létre a csapat megosztott kódjának és dokumentációjának tárolásához, a verziókövetés megvalósításához, valamint azt, hogy ki férhet hozzá a kód megtekintéséhez és használatához. 

A git használatával kapcsolatos további információkért látogasson el a [GitHub Súgó oldalaira](https://help.github.com/) . Használhatja a GitHubot a csapatával való együttműködésre, a Közösség által fejlesztett kód használatára, valamint a kód visszavonására a Közösséghez.

A DSVM a parancssorban és a grafikus felhasználói felületen is betöltődik a GitHub-tárház eléréséhez. A git és a GitHub működéséhez használt parancssori eszköz git bash néven is ismert. A Visual Studio telepítve van a DSVM, és a git-bővítmények. Ezen eszközök ikonjai a **Start** menüben és az asztalon találhatók.

Ha egy GitHub-tárházból szeretne kódot letölteni, használja a ```git clone``` parancsot. Például a Microsoft által az aktuális könyvtárba közzétett adatelemzési tárház letöltéséhez futtassa a következő parancsot a git Bashben:

    git clone https://github.com/Azure/DataScienceVM.git

A Visual Studióban ugyanezt a klónozási műveletet végezheti el. Az alábbi képernyőfelvételen a git-és GitHub-eszközök a Visual Studióban való elérését mutatja be:

![A Visual Studio képernyőképe a megjelenő GitHub-kapcsolatban](./media/vm-do-ten-things/VSGit.PNG)

A git használatával kapcsolatban további információkat talál a GitHub-tárház használatáról a github.com elérhető erőforrásokból. A [Cheat Sheet](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) hasznos hivatkozás.

## <a name="access-azure-data-and-analytics-services"></a>Hozzáférés az Azure-beli és az elemzési szolgáltatásokhoz
### <a name="azure-blob-storage"></a>Azure Blob Storage
Az Azure Blob Storage egy megbízható, gazdaságos felhőalapú tárolási szolgáltatás, amely nagy és kicsi adatmennyiséget biztosít. Ez a szakasz azt ismerteti, hogyan helyezhetők át az adattárolók a blob Storage-ba, és hogyan érhetik el az Azure-blobokban tárolt

#### <a name="prerequisites"></a>Előfeltételek

* Hozza létre az Azure Blob Storage-fiókot a [Azure Portal](https://portal.azure.com).

   ![Képernyőfelvétel a Storage-fiók létrehozási folyamatáról a Azure Portal](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Ellenőrizze, hogy a parancssori AzCopy eszköz előre van-e telepítve: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. A azcopy. exe fájlt tartalmazó könyvtár már a PATH környezeti változóban van, így az eszköz futtatásakor nem lehet beírni a teljes parancs elérési útját. A AzCopy eszközzel kapcsolatos további információkért tekintse meg a [AzCopy dokumentációját](../../storage/common/storage-use-azcopy.md).
* Indítsa el a Azure Storage Explorer eszközt. A [Storage Explorer weboldaláról](https://storageexplorer.com/)tölthető le. 

   ![A Storage-fiók elérésének Azure Storage Explorer képernyőképe](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Adatok áthelyezése egy virtuális gépről egy Azure-blobba: AzCopy

Ha az adatátvitelt a helyi fájlok és a blob Storage között szeretné áthelyezni, a parancssorban vagy a PowerShellben is használhatja a AzCopy-t:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Cserélje le a **C:\MyFolder** -t a fájl tárolásának elérési útjára, a **mystorageaccount** a blob Storage-fiók nevével, a **mycontainer** és a **Storage-fiók kulcsát** a blob Storage-hozzáférési kulcsával. A Storage-fiók hitelesítő adatait a [Azure Portalban](https://portal.azure.com)találja.

Futtassa a AzCopy parancsot a PowerShellben vagy egy parancssorból. Íme néhány példa a AzCopy parancs használatára:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Miután futtatta a AzCopy parancsot egy Azure-blobba való másolásra, a fájl megjelenik Azure Storage Explorerban.

![Képernyőfelvétel a Storage-fiókról, a feltöltött CSV-fájl megjelenítése](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Adatok áthelyezése egy virtuális gépről egy Azure-blobba: Azure Storage Explorer

Az adatok a virtuális gépen lévő helyi fájlból is tölthetők fel Azure Storage Explorer használatával:

* Az adatok tárolóba való feltöltéséhez válassza ki a tárolót, és kattintson a **feltöltés** gombra.![képernyőkép a Azure Storage Explorer feltöltés gombról](./media/vm-do-ten-things/storage-accounts.png)
* A **fájlok** mező jobb oldalán válassza a három pontot ( **..** .), válassza ki a fájlrendszerből feltölteni kívánt fájlokat, majd válassza a **feltöltés** lehetőséget a fájlok feltöltésének megkezdéséhez. a fájlok feltöltése párbeszédpanel![képernyőképe](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Adatok beolvasása egy Azure-blobból: Machine Learning olvasó modul

A Azure Machine Learning Studioban az adatok importálása modul használatával olvashatja el a blob adatait.

![Képernyőkép az Adatimportálási modulról Machine Learning Studio](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Adatok beolvasása egy Azure-blobból: Python ODBC

Az adatok közvetlenül egy Jupyter-jegyzetfüzetben vagy egy Python-programban való beolvasásához használhatja a BlobService-függvénytárat.

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

Ezután dugja be a blob Storage-fiók hitelesítő adatait, és olvassa el az adatokat a blobból:

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

Az adatgyűjtés adatkeretként történik:

![Képernyőfelvétel az első 10 adatsorról](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
A Azure Data Lake Storage big data elemzési számítási feladatokhoz használható nagy kapacitású-tárház, amely kompatibilis a Hadoop elosztott fájlrendszerával (HDFS). A Hadoop, a Spark és a Azure Data Lake Analytics is működik. Ebből a szakaszból megtudhatja, hogyan helyezheti át az információkat a Azure Data Lake Storageba, és hogyan futtathatja az elemzéseket Azure Data Lake Analytics használatával.

#### <a name="prerequisites"></a>Előfeltételek

* Hozza létre a Azure Data Lake Analytics példányt a [Azure Portalban](https://portal.azure.com).

   ![Képernyőkép Data Lake Analytics példány létrehozásáról a Azure Portal](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* A [Visual Studio beépülő modul Azure Data Lake és stream Analytics eszközei](https://www.microsoft.com/download/details.aspx?id=49504) már telepítve vannak a Visual Studio Community Edition-ben a virtuális gépen. Miután elindította a Visual studiót, és bejelentkezett az Azure-előfizetésbe, a Visual Studio bal oldali paneljén megjelenik az Azure-beli adatelemzési fiók és a tárterület.

   ![Képernyőfelvétel a Visual Studióban található Data Lake eszközök beépülő modulról](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Adatok áthelyezése egy virtuális gépről Data Lakeba: Azure Data Lake Explorer

A Azure Data Lake Explorer segítségével [feltöltheti az adatait a virtuális gép helyi fájljaiból a Data Lake Storageba](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

Létrehozhat egy adatfolyamatot is, amellyel működővé tenni az adatátvitelt Azure Data Lakeba vagy a [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)használatával. [Ez a cikk](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) végigvezeti az adatfolyamatok felépítésének lépésein.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Adatok beolvasása egy Azure-blobból a Data Lakeba: U-SQL

Ha az adatok az Azure Blob Storage-ban találhatók, közvetlenül is beolvashatja az Azure-Blob adatait egy U-SQL-lekérdezésben. A U-SQL-lekérdezés létrehozása előtt győződjön meg arról, hogy a blob Storage-fiók a Azure Data Lake-példányhoz van csatolva. Nyissa meg a Azure Portal, keresse meg Azure Data Lake Analytics irányítópultját, válassza az **adatforrás hozzáadása**lehetőséget, válassza ki az **Azure Storage**tárolási típusát, és csatlakoztassa az Azure Storage-fiók nevét és kulcsát. Ezután hivatkozhat a Storage-fiókban tárolt adatértékekre.

![Az adatforrás hozzáadása párbeszédpanel képernyőképe](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

A Visual Studióban az adatok a blob Storage-ból, az adatok kezeléséhez, a szolgáltatások fejlesztéséhez, valamint az eredményül kapott adatok Azure Data Lake vagy Azure Blob Storage szolgáltatásba való elküldéséhez is beolvashatók. Ha a blob Storage-ban lévő adathivatkozásra hivatkozik, használja a **wasb://** . Ha Azure Data Lakeban lévő adathivatkozást használ, használja a **swbhdfs://** .

A Visual Studióban a következő U-SQL-lekérdezéseket használhatja:

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

Miután elküldte a lekérdezést a kiszolgálónak, a diagram megjeleníti a feladatok állapotát.

![Képernyőfelvétel a feladatok állapotának diagramról](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Adatlekérdezés Data Lakeban: U-SQL

Az adatkészletnek a Azure Data Lakeba való betöltését követően a [U-SQL nyelv](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) használatával kérdezheti le és vizsgálhatja meg az adatokat. Az U-SQL nyelv a T-SQL-hez hasonló, de néhány funkciót C# kombinál, hogy a felhasználók egyéni modulokat és felhasználó által definiált függvényeket tudjanak írni. A szkripteket az előző lépésben használhatja.

Miután elküldte a lekérdezést a kiszolgálónak, a tripdata_summary. A CSV a Azure Data Lake Explorerben jelenik meg. Az adatmegjelenítést úgy is megtekintheti, hogy a jobb gombbal a fájlra kattint.

![Képernyőfelvétel a CSV-fájlról Data Lake Explorerben](./media/vm-do-ten-things/USQL_create_summary.png)

Ekkor megjelenik a fájl adatai:

![A fájl összegző információinak képernyőképe](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>HDInsight Hadoop-fürtök
Az Azure HDInsight egy felügyelt Apache Hadoop-, Spark-, HBase-és Storm-szolgáltatás a felhőben. Az Azure HDInsight-fürtökkel könnyedén dolgozhat a Data Science Virtual Machine.

#### <a name="prerequisites"></a>Előfeltételek

* Hozza létre az Azure Blob Storage-fiókot a [Azure Portal](https://portal.azure.com). Ezt a Storage-fiókot használjuk a HDInsight-fürtökre vonatkozó adattároláshoz.

   ![Képernyőkép a Storage-fiók létrehozásáról a Azure Portal](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Azure HDInsight Hadoop fürtök testreszabása a [Azure Portal](../team-data-science-process/customize-hadoop-cluster.md).
  
   Csatolja a HDInsight-fürttel létrehozott Storage-fiókot a létrehozásakor. Ez a Storage-fiók a fürtön belül feldolgozható adatok elérésére szolgál.

   ![A HDInsight-fürttel létrehozott Storage-fiók összekapcsolásának kijelölése](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* A létrehozás után engedélyezze Távoli asztal a fürt fő csomópontjának elérését. Jegyezze meg az itt megadott távelérési hitelesítő adatokat, mert az ezt követő eljárásban szüksége lesz rájuk.

   ![Távoli asztal gomb a HDInsight-fürt távoli elérésének engedélyezéséhez](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Hozzon létre egy Azure Machine Learning munkaterületet. A Machine Learning kísérleteket ebben a Machine Learning-munkaterületen tárolja a rendszer. Válassza ki a Kiemelt beállításokat a portálon, az alábbi képernyőképen látható módon:

   ![Azure Machine Learning-munkaterület létrehozása](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Adja meg a munkaterület paramétereit.

   ![Machine Learning munkaterület paramétereinek megadása](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Adatok feltöltése a IPython notebook használatával. Importálja a szükséges csomagokat, csatlakoztassa a hitelesítő adatokat, hozzon létre egy adatbázist a Storage-fiókban, majd töltse be az adatokat a HDI-fürtökbe.

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

Azt is megteheti, hogy [ezt a bemutatót](../team-data-science-process/hive-walkthrough.md) követve feltölthet egy NYC-taxi-adatát a HDI-fürtbe. A főbb lépések a következők:
  
* A AzCopy használatával töltse le a tömörített CSV a nyilvános blobból a helyi mappájába.
* A AzCopy használatával töltse fel a kibontott CSV a helyi mappából a HDI-fürtbe.
* Jelentkezzen be a Hadoop-fürt fő csomópontjára, és készüljön fel a felderítő adatelemzésre.

Miután betöltötte az adatait a HDI-fürtbe, megtekintheti az adatait Azure Storage Explorerban. És a nyctaxidb-adatbázis létre lett hozva a HDI-fürtben.

#### <a name="data-exploration-hive-queries-in-python"></a>Adatfeltárás: struktúra-lekérdezések a Pythonban

Mivel az adatai egy Hadoop-fürtben vannak, a pyodbc-csomaggal kapcsolódhat a Hadoop-fürtökhöz és a lekérdezési adatbázisokhoz a kaptár használatával a feltárás és a szolgáltatások fejlesztéséhez. Megtekintheti az előfeltételként létrehozott meglévő táblákat.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![Meglévő táblák megtekintése](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Nézzük meg az egyes hónapokban lévő rekordok számát, illetve a kimondott vagy nem az utazási táblázatban szereplő gyakoriságot:

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

![Rekordok számának ábrázolása minden hónapban](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

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

![Tipp gyakoriságának ábrázolása](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Kiszámíthatja a felvételi hely és a legördülő hely közötti távolságot is, majd összehasonlíthatja azt az utazási távolsággal.

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

![A pickup és a drop-off tábla legfelső sora](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Felvétel/legördülő lista távolsága az utazási távolságig](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Most Készítsünk elő egy Downsampled (1 százalék) adatkészletet modellezéshez. Ezeket az adatMachine Learning olvasó modulban is használhatja.

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

Most szúrja be a csatlakozás tartalmát az előző belső táblába.

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

Egy idő után láthatja, hogy a rendszer betöltötte az Hadoop-fürtökben lévő összes információt:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![A tábla adatainak legfelső sora](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

#### <a name="read-data-from-hdi-by-using-azure-machine-learning-studio-classic-reader-module"></a>Adatok beolvasása a HDI-ből Azure Machine Learning Studio (klasszikus): olvasó modul használatával

A Hadoop-fürtben található adatbázis eléréséhez használhatja a Azure Machine Learning Studio (klasszikus) olvasó modulját is. Csatlakoztassa a HDI-fürtök és az Azure Storage-fiók hitelesítő adatait, hogy lehetővé váljon a gépi tanulási modellek létrehozása a HDI-fürtökben található adatbázis használatával.

![Olvasó modul tulajdonságai](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

Ezután megtekintheti a pontszámmal ellátható adatkészletet:

![Pontozásos adatkészlet megtekintése](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Azure SQL Data Warehouse és adatbázisok
Azure SQL Data Warehouse egy rugalmas adattárház-szolgáltatás, amely nagyvállalati szintű SQL Server-felülettel rendelkezik.

Az Azure SQL-adattárházat a [jelen cikk](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)utasításait követve építheti ki. Az SQL-adattárház üzembe helyezése után ezzel az [útmutatóval](../team-data-science-process/sqldw-walkthrough.md) az adatok feltöltését, feltárását és modellezését végezheti el az SQL-adattárházban lévő adatok használatával.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB egy NoSQL-adatbázis a felhőben. Használhatja a JSON-hoz hasonló dokumentumokkal, valamint a dokumentumok tárolására és lekérdezésére.

A következő előfeltételek végrehajtásával érheti el Azure Cosmos DB a DSVM:

1. A Azure Cosmos DB Python SDK már telepítve van a DSVM. A frissítéshez futtassa ```pip install pydocumentdb --upgrade``` parancsot a parancssorból.
2. Hozzon létre egy Azure Cosmos DB fiókot és adatbázist a [Azure Portal](https://portal.azure.com).
3. Töltse le a Azure Cosmos DB adatáttelepítési eszközt a [Microsoft letöltőközpontból](https://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) , és bontsa ki a kívánt könyvtárat.
4. Egy [nyilvános blobban](https://cahandson.blob.core.windows.net/samples/volcano.json) tárolt JSON-adatok (vulkáni adatok) importálása Azure Cosmos DBba az áttelepítési eszközhöz a következő parancs-paraméterekkel. (Használja a dtui. exe fájlt abban a könyvtárban, ahol a Azure Cosmos DB adatáttelepítési eszközt telepítette.) Adja meg a forrás és a cél helyét a következő paraméterekkel:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Az adat importálása után nyissa meg a Jupyter, és nyissa meg a *DocumentDBSample*című jegyzetfüzetet. Python-kódot tartalmaz a Azure Cosmos DB eléréséhez, és elvégezheti az alapszintű lekérdezéseket. A Azure Cosmos DBról további információt a szolgáltatás [dokumentációs oldalának](https://docs.microsoft.com/azure/cosmos-db/)webhelyén talál.

## <a name="use-power-bi-reports-and-dashboards"></a>Power BI jelentések és irányítópultok használata 
Megjelenítheti a vulkáni JSON-fájlt az előző Azure Cosmos DB például Power BI Desktop, hogy vizuális elemzéseket nyerjen az adatokból. A részletes lépések a [Power bi cikkben](../../cosmos-db/powerbi-visualize.md)találhatók. A magas szintű lépések:

1. Nyissa meg Power BI Desktop és válassza **az adatlekérdezés**lehetőséget. Az URL-címet a következőképpen adhatja meg: https://cahandson.blob.core.windows.net/samples/volcano.json.
2. Ekkor megjelenik a listaként importált JSON-rekordok listája. Alakítsa át a listát egy táblázatba, hogy Power BI képes legyen vele dolgozni.
4. Bontsa ki az oszlopokat a Expand (nyíl) ikon kiválasztásával.
5. Figyelje meg, hogy a hely egy **rekord** mező. Bontsa ki a rekordot, és csak a koordinátákat válassza ki. A **koordináta** egy lista oszlop.
6. Adjon hozzá egy új oszlopot a lista koordináta oszlopának egy vesszővel tagolt **LatLong** oszlopba való átalakításához. Összefűzi a két elemet a koordináta-lista mezőben a következő képlet használatával: ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Alakítsa át a **jogosultságszint-emelési** oszlopot decimális értékre, és válassza a **Bezárás** és **alkalmazás** gombokat.

A fenti lépések helyett illessze be a következő kódot. Az adatátalakítások lekérdezési nyelven való írásához a Power BI Speciális szerkesztőban használt lépéseket.

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

Most már rendelkezik a Power BI adatmodellben lévő adataival. A Power BI Desktop példányának a következőképpen kell megjelennie:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Jelentéseket és vizualizációkat hozhat létre az adatmodell használatával. A következő cikkben ismertetett lépéseket követve hozhat létre egy jelentést: [Power bi](../../cosmos-db/powerbi-visualize.md#build-the-reports) .

## <a name="scale-the-dsvm-dynamically"></a>A DSVM dinamikus méretezése 
A DSVM vertikális fel-és leskálázásával is teljesítheti a projekt igényeit. Ha a virtuális gépet nem kell esti vagy hétvégeken használni, leállíthatja a virtuális gépet a [Azure Portalról](https://portal.azure.com).

> [!NOTE]
> Ha csak a virtuális gép operációs rendszerének leállítás gombját használja, a számítási költségeket számítjuk fel.  
> 
> 

Előfordulhat, hogy néhány nagy méretű elemzést kell kezelnie, és több PROCESSZORt, memóriát vagy lemezterületet kell használnia. Ha igen, a számítási és a költségvetési igényeknek megfelelő méretű virtuálisgép-méretek közül választhat a CPU-magok, a GPU-alapú példányok a Deep learning, a memória kapacitása és a lemez típusa (beleértve a SSD-meghajtókat is) tekintetében. A virtuális gépek teljes listája, valamint óradíjas számítási díjszabása az [Azure Virtual Machines díjszabási](https://azure.microsoft.com/pricing/details/virtual-machines/) oldalán érhető el.

Hasonlóképpen, a virtuális gépek feldolgozási kapacitásának csökkentése is csökkenhet. (Például: jelentős számítási feladatokat helyezett át egy Hadoop vagy Spark-fürtre.) Ezután lekicsinyítheti a fürtöt a [Azure Portal](https://portal.azure.com) , és a virtuálisgép-példány beállításait megtekintheti. 

## <a name="add-more-tools"></a>További eszközök hozzáadása
A DSVM beépített eszközök számos gyakori adatelemzési igényt tudnak kezelni. Ezzel időt takaríthat meg, mert nem kell egyenként telepítenie és konfigurálnia a környezeteket. Pénzt takarít meg, mivel csak a felhasznált erőforrásokért kell fizetnie.

A cikkben ismertetett egyéb Azure-adatelemzési és-elemzési szolgáltatások segítségével javíthatja az elemzési környezetét. Bizonyos esetekben további eszközökre lehet szükség, beleértve néhány saját partneri eszközt is. A virtuális gépen teljes körű rendszergazdai hozzáféréssel rendelkezik a szükséges új eszközök telepítéséhez. További csomagokat is telepíthet a Pythonban és az R-ben, amelyek nincsenek előre telepítve. A Python esetében ```conda``` vagy ```pip```is használható. Az R esetében használhatja a ```install.packages()``` az R-konzolon, vagy használhatja az IDE-t, majd kiválaszthatja a **csomagokat** > **csomagok telepítése**lehetőséget.

## <a name="deep-learning"></a>Deep learning

A keretrendszeren alapuló mintákon kívül olyan átfogó útmutatók is beszerezhetők, amelyek ellenőrzése megtörtént a DSVM. Ezek a forgatókönyvek megkönnyítik a mélyreható tanulási alkalmazások fejlesztését olyan tartományokban, mint például a képek és a szövegek és a nyelvek megismerése.   


- [Neurális hálózatok futtatása különböző keretrendszerekben](https://github.com/ilkarman/DeepLearningFrameworks): Ez a bemutató bemutatja, hogyan lehet áttelepíteni a kódot az egyik keretrendszerről a másikra. Azt is bemutatja, hogyan lehet összehasonlítani a modelleket és a futásidejű teljesítményt a keretrendszerek között. 

- [Útmutató a képeken belüli termékek észlelésére szolgáló teljes körű megoldás](https://github.com/Azure/cortana-intelligence-product-detection-from-images)létrehozásához: a képészlelés egy olyan technika, amely képes objektumokat megkeresni és osztályozni a képeken belül. Ez a technológia jelentős előnyökkel bír számos valós üzleti tartományban. A kiskereskedők például használhatják ezt a technikát annak meghatározására, hogy az ügyfél melyik terméket választotta fel a polcról. Ezek az információk segítenek a termékek leltározásának kezelésében. 

- [Mély tanulás a hanggal](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): ez az oktatóanyag azt mutatja be, hogyan lehet betanítani a Hangesemények észlelésére szolgáló mélyreható tanulási modellt a [városi hangok adatkészletében](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). Emellett áttekintést nyújt a hangadatokkal való munkáról.

- [Szöveges dokumentumok besorolása](https://github.com/anargyri/lstm_han): Ez a bemutató azt mutatja be, hogyan lehet két neurális hálózati architektúrát létrehozni és betanítani: hierarchikus figyelmet a hálózatra és a hosszú távú memória-(LSTM-) hálózatra. Ezek a neurális hálózatok a kerasz API-t használják a mélyreható tanuláshoz a szöveges dokumentumok besorolásához. A kerasz a legnépszerűbb mélyreható tanulási keretrendszerek: Microsoft Cognitive Toolkit, TensorFlow és theano.

## <a name="summary"></a>Összefoglalás
Ez a cikk néhány olyan dolgot ismertetett, amelyet a Microsoft Data Science Virtual Machine végezhet el. Számos további dolgot tehet a DSVM hatékony elemzési környezetének elvégzéséhez.


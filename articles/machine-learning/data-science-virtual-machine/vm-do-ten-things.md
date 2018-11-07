---
title: Tíz dolog teheti meg az adatelemző virtuális gép az Azure-ban |} A Microsoft Docs
description: Hajtsa végre a különféle adatáttekintés és modellezés a feladat az adatelemző virtuális gép.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
editor: cgronlun
ms.assetid: 145dfe3e-2bd2-478f-9b6e-99d97d789c62
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: df9edfee9d8a6a0736a040924bac736cfcb3633c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250917"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Tíz dolog, amire alkalmas a Windows Data Science virtuális gépen

A Windows adatelemzési virtuális gép (DSVM) egy hatékony adatelemzési fejlesztési környezetet, amely lehetővé teszi, hogy különböző adatok feltárása és modellezés feladatok elvégzésére. A környezet már tartalmaz beépített és az számos népszerű elemzési eszközök, amelyek megkönnyítik a gyors használatbavétele az elemzést, a helyszíni, Felhőbeli vagy hibrid telepítési. A dsvm-hez szorosan együttműködik számos Azure-szolgáltatásokkal, és olvashatja, és már az Azure-ban, az Azure SQL Data Warehouse, az Azure Data Lake, az Azure Storage vagy az Azure Cosmos DB-ben tárolt adatokat. Azt is használhatja például az Azure Machine Learning és az Azure Data Factory más elemzési eszközökből.

Ebben a cikkben megtudhatja hogyan használható a dsvm-hez különböző adattudományi tevékenység, és más Azure-szolgáltatásokkal kommunikálni. Az alábbiakban néhány, a DSVM végezhet el:

1. Adatok feltárása és modellek helyileg, a dsvm-hez a Microsoft ML Server Python használatával
2. Az adatok egy böngészőben, a Python 2, a Python 3, a Microsoft R teljesítmény készült R kész vállalati verzióját egy kísérletezhet a Jupyter notebook használata
3. R és Python használatával az Azure Machine Learning, az ügyfél alkalmazásai is elérhetik a modellek egyszerű webes szolgáltatás felületen létrehozott modellek üzembe helyezése
4. Felügyelheti az Azure-erőforrások Azure portal vagy a Powershell használatával
5. Bővítheti a tárhely és megoszthatja a nagyméretű adatkészletek / hozzon létre egy Azure File storage a dsvm-hez csatlakoztatható meghajtóként teljes munkatársaival code
6. Kód megosztásra a csapatával GitHub használatával, és az előre telepített Git-ügyfél használatával – Git bash eszközt, grafikus Git a tárház eléréséhez.
7. Hozzáférjen a különböző Azure adatok és analitika szolgáltatásokhoz hasonlóan az Azure blob storage-ban az Azure Data Lake, az Azure HDInsight (Hadoop), az Azure Cosmos DB, Azure SQL Data Warehouse és az adatbázisok
8. Jelentések és irányítópultok használata a Power BI Desktop a dsvm-hez előre telepítve és üzembe helyezheti őket a felhőben
9. A dsvm-hez projekt igény szerint dinamikusan méretezhető
10. További eszközök telepítése a virtuális gépen   

> [!NOTE]
> További használati díjak vonatkoznak a cikkben szereplő további adatok tárolási és -elemzési szolgáltatásaiba számos esetében. Tekintse meg a [Azure díjszabását](https://azure.microsoft.com/pricing/) a lapot.
> 
> 

**Előfeltételek**

* Azure-előfizetés szükséges. Iratkozzon fel az ingyenes próbaverzióra [Itt](https://azure.microsoft.com/free/).
* Utasítások üzembe helyezés egy adatelemző virtuális gép az Azure Portal webhelyen érhetők el [hoz létre virtuális gépet](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).

## <a name="1-explore-data-and-develop-models-using-microsoft-ml-server-or-python"></a>1. Adatok feltárása és fejlesztése a Microsoft ML Server vagy a Python használatával
Például az R és Python nyelvek használatával az adatok elemzését közvetlenül a dsvm-hez.

Az R például az RStudio, amely megtalálható a start menüből vagy az asztal vagy R Tools for Visual Studio IDE is használhatja. A Microsoft közzétett további kódtárak pedig felül a nyílt-forrás/CRAN-R méretezhető elemzéseket és nagyobb, mint a memória méretét engedélyezett módon párhuzamos darabolt elemzési adatok elemzéséhez. 

A Python például a Visual Studio Community Edition, amely rendelkezik a Python Tools for Visual Studio (PTVS) bővítmény előre telepítve van egy IDE is használhatja. Csak a Python 3.6, alapértelmezés szerint a legfelső szintű conda-környezet projektek készítése PTVS van konfigurálva. Anaconda Python 2.7-es engedélyezéséhez kövesse az alábbi lépéseket kell:

* Az egyéni környezet minden verzió létrehozása **eszközök** -> **Python Tools** -> **Python-környezetek** és kattintás a " **+ Egyéni**"az a Visual Studio Community Edition
* Adjon meg egy leírást, és állítsa be a környezet előtag elérési, mert *c:\anaconda\envs\python2* Anaconda Python 2.7
* Kattintson a **automatikus észlelése** , majd **alkalmaz** mentéséhez a környezetben.

Itt látható, hogy az egyéni környezet beállítása néz ki a Visual Studióban.

![Projektek készítése PTVS-telepítő](./media/vm-do-ten-things/PTVSSetup.png)

Tekintse meg a [PVTS dokumentációban](https://aka.ms/ptvsdocs) további részleteket a Python-környezetek létrehozása.

Most hozzon létre egy új Python-projektet állíthatók be. Navigáljon a **fájl** -> **új** -> **projekt** -> **Python** és típusának kiválasztása Python-alkalmazást hoz létre. A kívánt verziót (Python 2.7-es és 3.6) az aktuális projekt Python-környezetet a jobb gombbal kattintva állíthatja a **Python-környezetek**lehetőségre kattint **hozzáadása/eltávolítása Python-környezetek**, majd kiválasztotta a kívánt környezetre. A termék-projektek készítése PTVS használatáról további információkat talál [dokumentáció](https://aka.ms/ptvsdocs).

## <a name="2-using-a-jupyter-notebook-to-explore-and-model-your-data-with-python-or-r"></a>2. Ismerje meg, és modellezheti az adatokat, a Python vagy R Jupyter Notebook használata
A Jupyter Notebook egy hatékony környezetet biztosít, a böngésző alapú "IDE" adatáttekintés és modellezés a rendszer. A Jupyter Notebook Python 2, a Python 3 vagy az R (nyílt forráskódú és a Microsoft R Server) használhatja.

A Jupyter Notebook indításához kattintson a start menü ikonra / asztali ikonra nevű **Jupyter Notebook**. A DSVM-parancssorban is futtathatja a parancsot ```jupyter notebook``` a címtárból, ha van meglévő jegyzetfüzeteket, vagy létre szeretné hozni az Új jegyzetfüzet.  

Jupyter megkezdése után megjelenik egy könyvtárat, amely tartalmazza, amelyek a dsvm-hez az előrecsomagolt néhány példa-jegyzetfüzeteket. Most a következőket teheti:

* Kattintson a notebook kód megtekintéséhez.
* Hajtsa végre az egyes cellákban lenyomásával **a SHIFT + ENTER**.
* A teljes jegyzetfüzet futtatásához kattintson a **cella** -> **futtatása**
* A Jupyter (bal felső sarokban) ikonra kattintva, majd kattintson az Új jegyzetfüzet létrehozása **új** gombra a jobb oldalon, és a notebook nyelv (más néven kernelekkel) lehetőséget választja.   

> [!NOTE]
> Jelenleg a Jupyter Python 2.7-es, a Python 3.6-os, az R, a Julia és a PySpark kernelt támogatottak. Az R-kernel programozási támogatja mind nyílt forráskódú R, valamint a nagy teljesítményű Microsoft R.   
> 
> 

Miután a notebook feltárhatja az adatokat, a modell, a teszteli a modellt, a választott kódtárak használatával.

## <a name="3-build-models-using-r-or-python-and-operationalize-them-using-azure-machine-learning"></a>3. A R vagy Python és elindítható őket az Azure Machine Learning modellek készítése
Miután létrehozott és a modell érvényesítése a következő lépés az általában éles üzembe helyezés. Ez lehetővé teszi az ügyfél alkalmazásokat kell elindítani a modell-előrejelzéseket, a valós idejű és kötegelt módban történik. Az Azure Machine Learning lehetővé teszi a beépített R vagy Python modell üzembe helyezése.

Ha Ön a modell, az Azure Machine Learning üzembe helyezése, egy webszolgáltatás, amely lehetővé teszi az ügyfelek, amelyek a bemeneti paraméterek átadása, illetve a modell kimenetként előrejelzéseket fogadhat REST-hívásokat van közzétéve.   

> [!NOTE]
> Ha Ön rendelkezik még nem írta az Azure Machine Learning, szerezheti be egy ingyenes munkaterületet, vagy a standard munkaterületre funkcionáló a [Azure Machine Learning Studio](https://studio.azureml.net/) kezdőlapját, és kattintson az "első lépések."   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Buildelés és üzembe helyezése Python modellek
A következő kódrészlettel, amely egy Python Jupyter Notebookot, amely egy egyszerű modellt, a SciKit-további kódtár használatával fejlesztett.

    #IRIS classification
    from sklearn import datasets
    from sklearn import svm
    clf = svm.SVC()
    iris = datasets.load_iris()
    X, y = iris.data, iris.target
    clf.fit(X, y)

A használt központi telepítése a python-modellek Azure Machine Learning burkolja az előrejelzési modell egy függvény és decorates, az előre telepített Azure Machine Learning python-kódtár által biztosított az attribútumokat, amelyek az Azure Machine Learning jelöl munkaterület azonosítója, az API-kulcs és a bemeneti és a visszatérési paraméterek.  

    from azureml import services
    @services.publish(workspaceid, auth_token)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(int) #0, or 1, or 2
    def predictIris(sep_l, sep_w, pet_l, pet_w):
     inputArray = [sep_l, sep_w, pet_l, pet_w]
    return clf.predict(inputArray)

Egy ügyfél hajtsa végre a webszolgáltatás-hívások. Nincsenek kényelmi burkolókat, amely a REST API-kérelmek létrehozására. Itt látható mintakód a webes szolgáltatás felhasználásához.

    # Consume through web service URL and keys
    from azureml import services
    @services.service(url, api_key)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(float)
    def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

    IrisPredictor(3,2,3,4)


> [!NOTE]
> Az Azure Machine Learning-kódtár csak jelenleg támogatott a Python 2.7-t.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Buildelés és üzembe helyezése az R-modellek
R-modellek a Data Science virtuális gépen vagy máshol be az Azure Machine Learning beépített hogyan történik a Pythonhoz készült hasonló módon telepítheti. A lépések a következők:

* Hozzon létre egy fájlt a settings.json adja meg a munkaterület-Azonosítót és a hitelesítési jogkivonat 
* az írási egy burkoló a modell előre jelezni a függvény számára.
* hívás ```publishWebService``` adja át a függvény burkoló az Azure Machine Learning-könyvtárban található.  

Ez az eljárás és kód kódrészletek, amelyek segítségével állítsa be, létrehozása, közzététel és modell felhasználása az Azure Machine Learning webszolgáltatásként.

#### <a name="setup"></a>Beállítás

* Hozzon létre egy settings.json fájlt nevű könyvtárat a ```.azureml``` alatt a kezdőkönyvtárban, és adja meg a paramétereket az Azure Machine Learning-munkaterület:

fájlstruktúra Settings.JSON:

    {"workspace":{
    "id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
    "authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
    }}


#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Modell létrehozása az R, és közzé kell tennie az Azure Machine Learningben
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

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Az Azure Machine Learning-ban üzembe helyezett modell felhasználása
A modell a ügyfélalkalmazás feldolgozható, megkeresheti a közzétett webes szolgáltatás nevének használatával használjuk az Azure Machine Learning-kódtár az `services` határozza meg a végpont az API-hívás. Majd egyszerűen a `consume` funkciót, és adja át a kell elvégezni, ha az adathalmaz.
A következő kódot az Azure Machine Learning-webszolgáltatásként közzétett modell felhasználása szolgál.

    library(AzureML)
    library(lme4)
    ws <- workspace(config="~/.azureml/settings.json")

    s <-  services(ws, name = "sleepy lm")
    s <- tail(s, 1) # use the last published function, in case of duplicate function names

    ep <- endpoints(ws, s)

    # OK, try this out, and compare with raw data
    ans = consume(ep, sleepstudy)$ans

További információ az Azure Machine Learning R-könyvtár ismertetése található [Itt](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf).

## <a name="4-administer-your-azure-resources-using-azure-portal-or-powershell"></a>4. Felügyelheti az Azure-erőforrások Azure portal vagy a Powershell használatával
A dsvm-hez nem csak lehetővé teszi, hogy helyileg hozhat létre egy elemzési megoldást a virtuális gépen, de lehetővé teszi, hogy hozzáférjen olyan szolgáltatásokhoz a Microsoft Azure-felhőben. Az Azure biztosít több számítási, tárolási, elemzési adatszolgáltatások és más szolgáltatások felügyelete és érheti el a dsvm-hez a.

Felügyelheti az Azure-előfizetés és a felhőbeli erőforrások a böngészőjében, és mutasson a [az Azure portal](https://portal.azure.com). Azure Powershell segítségével is felügyelheti az Azure-előfizetés és a egy parancsfájl erőforrásai.
Azure Powershell egy parancsikont az asztalon vagy a start menü "Microsoft Azure Powershell." című futtathatók Tekintse meg [a Microsoft Azure Powershell-dokumentáció](../../powershell-azure-resource-manager.md) hogyan felügyelete az Azure-előfizetés és a Windows Powershell-parancsfájlok használatával erőforrások további tájékoztatást.

## <a name="5-extend-your-storage-space-with-a-shared-file-system"></a>5. A tárolóhely egy megosztott fájlrendszert a kiterjesztése
Az adatszakértők megoszthatja a nagyméretű adathalmazok, kódok vagy egyéb erőforrások a csapaton belüli. A DSVM magát körülbelül 45GB-nyi szabad terület van. Kiterjeszti a storage, használhatja az Azure File Storage-szolgáltatás, és vagy csatlakoztassa azt egy vagy több DSVM-példányt, vagy elérheti azokat egy REST API-n keresztül.  Is [az Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) vagy [Azure PowerShell-lel](../../virtual-machines/windows/attach-disk-ps.md) külön dedikált adatlemezek hozzáadása. 

> [!NOTE]
> Az Azure File Service fájlmegosztás maximális lemezterület 5 TB-os, az egyes fájlok maximális mérete pedig 1 TB-ot. 
> 
> 

Azure Powershell segítségével hozzon létre egy Azure File Service-megosztást. Íme a szkript futtatásához az Azure-szolgáltatás fájlmegosztás létrehozása az Azure PowerShell-lel.

    # Authenticate to Azure.
    Connect-AzureRmAccount
    # Select your subscription
    Get-AzureRmSubscription –SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
    # Create a new resource group.
    New-AzureRmResourceGroup -Name <dsvmdatarg>
    # Create a new storage account. You can reuse existing storage account if you wish.
    New-AzureRmStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
    # Set your current working storage account
    Set-AzureRmCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

    # Create a Azure File Service Share
    $s = New-AzureStorageShare <<teamsharename>>
    # Create a directory under the FIle share. You can give it any name
    New-AzureStorageDirectory -Share $s -Path <directory name>
    # List the share to confirm that everything worked
    Get-AzureStorageFile -Share $s


Most, hogy létrehozott egy Azure-fájlmegosztást, csatlakoztathatja azt az Azure-ban minden virtuális gépen. Azt javasoljuk, hogy a virtuális gép szerepel-e a tárfiók, késés és az adatátviteli díjak elkerülése érdekében ugyanabban az Azure az adatközpontban. Az alábbiakban a a meghajtót csatlakoztatni a dsvm-hez, amely futtatható az Azure Powershell-parancsok.

    # Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
    cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

    # Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
    net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>


Most már hozzáférhet a meghajtó, mint bármilyen normál meghajtó a virtuális gépen.

## <a name="6-share-code-with-your-team-using-github"></a>6. Kód megosztásra a csapatával GitHub használatával
A GitHub az egy megkeresésének számos mintakód és források a különböző eszközök használatával különböző technológiákkal, a fejlesztői Közösség által megosztott kódtárban. Használ a Git, a technológia nyomon követheti, és tárolja a kódot fájlok verzióival. A GitHub az is platform, amellyel hozható létre a saját tárház tárolására, a csoport megosztott kód és dokumentáció, megvalósítása a verziókezeléshez, és azt is szabályozzák, megtekintése és közreműködés kód hozzáféréssel. Látogasson el a [GitHub súgóoldalak](https://help.github.com/) a Git használatával kapcsolatban bővebb információt. A módszerek együttműködhet a csapatával, a Közösség által fejlesztett kóddal és kód vissza hozzájárulnak a Közösség egyik GitHub is használhatja.

A dsvm-hez már származik az ügyféleszközök elől betöltött egyaránt jól grafikus felhasználói felület GitHub-adattár eléréséhez, parancssori. A Git és a GitHub parancssori eszköz a Git Bash nevezzük. A dsvm-hez telepítve a Visual Studio a Git-bővítmények rendelkezik. Ezekkel az eszközökkel a start menüben, és a desktop indítási-ikonokat találhat meg.

A kód letöltése a GitHub-adattárból, használhatja a ```git clone``` parancsot. Például, töltse le az aktuális könyvtárban, a Microsoft által közzétett adatelemzési adattár futtathatja a következő parancs Miután ```git-bash```.

    git clone https://github.com/Azure/DataScienceVM.git

A Visual Studióban érdemes ugyanazt a Klónozási műveletet. Az alábbi képernyőfelvételhez bemutatja a Git és a GitHub, Visual Studio eszközök elérése.

![Git a Visual Studióban](./media/vm-do-ten-things/VSGit.PNG)

További információ a Git segítségével dolgozhat a GitHub-adattárban több erőforrás elérhető a github.com találja. A [adatlap](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) hasznos hivatkozás.

## <a name="7-access-various-azure-data-and-analytics-services"></a>7. Különböző Azure adat- és elemzési szolgáltatások eléréséhez.
### <a name="azure-blob"></a>Azure-blob
Az Azure blob egy megbízható, gazdaságos felhőalapú tárolás, a kis- és big Data típusú adatok. Ez a szakasz ismerteti, hogyan viheti át adatok Azure Blob- és hozzáférés, egy Azure-blobban tárolt adatok.

**Előfeltétel**

* **Hozzon létre az Azure Blob storage-fiókjában [az Azure portal](https://portal.azure.com).**

![Create_Azure_Blob](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Győződjön meg arról, hogy az előre telepített parancssori AzCopy eszköz a következő címen található ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. A könyvtárra, amelyben az azcopy.exe már szerepel a PATH környezeti változóba, ha nem szeretné beírni a parancs teljes elérési útja, ez az eszköz futtatásakor. További információ az AzCopy eszközt, tekintse meg [AzCopy dokumentációját](../../storage/common/storage-use-azcopy.md)
* Az Azure Storage Explorer eszköz elindításához. Le is tölthetők: [Microsoft Azure Storage Explorer](http://storageexplorer.com/). 

![AzureStorageExplorer_v4](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

**Adatok áthelyezése Azure Blob virtuális gépről: AzCopy**

Adatok áthelyezése a helyi fájlok és a blob storage között, használhatja az AzCopy parancssori vagy a PowerShell használatával:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Cserélje le **C:\myfolder** a elérési utat, ahová a fájlt tárolja, **mystorageaccount** a blob storage-fiók neve, a **mycontainer** a tároló nevének **tárfiókkulcs** , a blob storage hozzáférési kulccsal. Annak a tárfiók hitelesítő adatait a [az Azure portal](https://portal.azure.com).

![StorageAccountCredential_v2](./media/vm-do-ten-things/StorageAccountCredential_v2.png)

AzCopy-parancs futtatása, a PowerShellben vagy a parancssorból. Íme néhány példa a használatra az AzCopy-parancsot:

    # Copy *.sql from local machine to a Azure Blob
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

    # Copy back all files from Azure Blob container to Local machine

    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S



Az Azure-blobba másolja az AzCopy-parancs futtatása után láthatja a fájl megjelenik-e az Azure Storage Explorerben kis türelmet kérünk.

![AzCopy_run_finshed_Storage_Explorer_v3](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

**Adatok áthelyezése Azure Blob virtuális gépről: Azure Storage Explorerrel**

A virtuális gépen az Azure Storage Explorer használatával is feltölthetők a helyi fájlokból való adatokat:

* Adatok feltöltése a tárolóba, válassza ki a céltárolót, és kattintson a **feltöltése** gomb.![ Töltse fel a Storage Explorerben](./media/vm-do-ten-things/storage-accounts.png)
* Kattintson a **...**  jobb oldalán a **fájlok** jelölje ki egy vagy több fájlokat a fájlrendszerből, és kattintson a **feltöltése** a fájlok feltöltése a kezdéshez.![ A blob-fájlok feltöltése](./media/vm-do-ten-things/upload-files-to-blob.png)

**Olvassa el az adatokat az Azure-Blobból: Machine Learning-olvasó modul**

Az Azure Machine Learning Studióban, használhat egy **adatok importálása modullal** adatokat olvasni a blobot.

![AML_ReaderBlob_Module_v3](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

**Olvassa el az adatokat az Azure-Blobból: Python ODBC**

Használhat **BlobService** könyvtár adatolvasási közvetlenül a Jupyter Notebook vagy a Python program található blob.

Először importálja a szükséges csomagokat:

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

Ezután beépülő modul az Azure Blob-fiókja hitelesítő adataival, és beolvashatja az adatokat a Blob:

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

    #unzipping downloaded files if needed
    #with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
    #    z.extractall(LOCALDIRECTORY)

    df1 = pd.read_csv(LOCALFILE, header=0)
    df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
    print 'the size of the data is: %d rows and  %d columns' % df1.shape

Az adatok az olvasható adatok keretet másként:

![IPNB_data_readin](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Az Azure Data Lake Storage egy nagy kapacitású adattár a big data-elemzési számítási feladatok és a Hadoop elosztott fájlrendszer (HDFS) kompatibilis. A Hadoop, Spark- és az Azure Data Lake Analytics működik. Ebben a szakaszban megtudhatja hogyan adatok áthelyezése az Azure Data Lake Store-ba, és az Azure Data Lake Analytics használatával elemzések futtatására.

**Előfeltétel**

* Hozzon létre az Azure Data Lake Analytics a [az Azure portal](https://portal.azure.com).

![Azure_Data_Lake_Create_v2](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* A **Azure Data Lake Tools** a **Visual Studio** ezen található [hivatkozás](https://www.microsoft.com/download/details.aspx?id=49504) már telepítve van a Visual Studio Community Edition, amely a virtuális gépen. Ha Visual Studio indítása, és a bejelentkezés az Azure-előfizetés, megtekintheti az Azure Data Analytics-fiók és a storage, a bal oldali panelen, a Visual Studio.

![Azure_Data_Lake_PlugIn_v2](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

**Adatok áthelyezése a Data Lake-Virtuálisgép: Azure Data Lake Explorerrel**

Használhat **Azure Data Lake Explorerrel** feltölthet adatokat a helyi fájlokat a virtuális gépen a Data Lake storage.

![Azure_Data_Lake_UploadData](./media/vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

Egy adatfolyamat üzembe helyezés az adatmozgás, vagy az Azure Data Lake használatával is létrehozható a [Azure Data Factory(ADF)](https://azure.microsoft.com/services/data-factory/). Ebben [cikk](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) folyamatok végigvezeti az adatok létrehozásának lépésein.

**Adatok olvasása az Azure-Blobokból a Data Lake-: U-SQL**

Ha az adatok Azure Blob storage-ban található, is közvetlenül beolvashatja az adatokat az Azure storage-blobból az U-SQL-lekérdezésben. A U-SQL-lekérdezések összeállítása, előtt ellenőrizze, a blob storage-fiók az Azure Data Lake van csatolva. Lépjen a **az Azure portal**, keresse meg az Azure Data Lake Analytics-irányítópultot, kattintson a **adatforrás hozzáadása**, válassza ki a tárolási típust **Azure Storage** és a beépülő modul az Azure Storage-fiók Nevére és kulcsára. Ezután megtörténik a storage-fiókban tárolt adatokra hivatkoznak.

![Adja meg a storage-fiók és a kulcs](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

A Visual Studióban olvassa el az adatokat blob storage-ból, hajtsa végre az egyes adatkezelés, Funkciók tervezése és kimeneti adatok vagy az Azure Data Lake, vagy az Azure Blob Storage. Ha az adatok blob storage-ban hivatkozik, használja **wasb: / /**; Ha az adatokat az Azure Data Lake használata hivatkozik **swbhdfs: / /**

![Adatkeretek](./media/vm-do-ten-things/USQL_Read_Blob_v2.PNG)

Az alábbi U-SQL-lekérdezéseket használhat a Visual Studióban:

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



A lekérdezés elküldésekor a kiszolgálóhoz, miután a feladat állapota bemutató ábra. Ez jelenik meg.

![Feladatábra állapota](./media/vm-do-ten-things/USQL_Job_Status.PNG)

**A Data Lake adatlekérdezés: U-SQL**

Miután az adatkészlet az Azure Data Lake be lett töltve, [U-SQL nyelv](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) a lekérdezésre és vizsgálódásra az adatokat. U-SQL nyelv hasonlít a T-SQL, de egyesíti az egyes szolgáltatásokat a C#, hogy a felhasználók írhat, testre szabott modulok, a felhasználó által definiált függvények és stb. Az előző lépésben a parancsfájlokat használhat.

A lekérdezés után elküldésekor tripdata_summary-kiszolgálóra. Hamarosan a fürt megosztott kötetei szolgáltatás található **Azure Data Lake Explorerrel**, akkor előfordulhat, hogy adatok előnézetének megtekintéséhez kattintson a jobb gombbal a fájlt.

![Az Azure Data Lake Explorerrel fájl](./media/vm-do-ten-things/USQL_create_summary.png)

A fájl adatainak megtekintése:

![Fájl összegzése](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>HDInsight Hadoop-fürtök
Az Azure HDInsight egy felügyelt Apache Hadoop, Spark, HBase és Storm szolgáltatás a felhőben. Használhatja egyszerűen az adatelemző virtuális gép az Azure HDInsight-fürtökkel.

**Előfeltétel**

* Hozzon létre az Azure Blob storage-fiókjában [az Azure portal](https://portal.azure.com). Ez a tárfiók HDInsight-fürtök adatok tárolására szolgál.

![Az Azure Blob storage-fiók létrehozása](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Testre szabhatja az Azure HDInsight Hadoop-fürtök a [Azure Portalon](../team-data-science-process/customize-hadoop-cluster.md)
  
  * A HDInsight-fürt létrehozásakor létrehozott storage-fiók összekapcsolása. Ehhez a tárfiókhoz való hozzáféréshez szükséges adatokat a fürtön belül feldolgozható szolgál.

![HDInsight-fürthöz létrehozott storage-fiók összekapcsolása](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Engedélyezése **távelérési** az átjárócsomóponthoz, a fürt létrehozása után. Az itt megadott távelérési hitelesítő adatok megjegyzése későbbi eljárás alkalmával a rendszer igény szerint.

![Távoli hozzáférés engedélyezése](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Az Azure Machine Learning-munkaterület létrehozása. A Machine Learning-kísérletek találhatók a Machine Learning-munkaterületet. A kiemelt lehetőségek kiválasztása a portálon, az alábbi képernyőképen látható módon:

![Azure Machine Learning-munkaterület létrehozása](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Ezután adja meg a paramétereket a munkaterület

![Adja meg a Machine Learning-munkaterület paraméterek](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* IPython Notebook használata az adatok feltöltése. Először importálja a szükséges csomagokat, hitelesítő adatok beépülő modul, egy adatbázis létrehozása a tárfiókban található, majd betölteni az adatokat a HDI-fürt.

        #Import required Packages
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


        #Create the connection to Hive using ODBC
        SERVER_NAME='xxx.azurehdinsight.net'
        DATABASE_NAME='nyctaxidb'
        USERID='xxx'
        PASSWORD='xxxx'
        DB_DRIVER='Microsoft Hive ODBC Driver'
        driver = 'DRIVER={' + DB_DRIVER + '}'
        server = 'Host=' + SERVER_NAME + ';Port=443'
        database = 'Schema=' + DATABASE_NAME
        hiveserv = 'HiveServerType=2'
        auth = 'AuthMech=6'
        uid = 'UID=' + USERID
        pwd = 'PWD=' + PASSWORD
        CONNECTION_STRING = ';'.join([driver,server,database,hiveserv,auth,uid,pwd])
        connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
        cursor=connection.cursor()


        #Create Hive database and tables
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


        #Upload data from blob storage to HDI cluster
        for i in range(1,13):
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);"%(i,i)
            cursor.execute(queryString)
            queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);"%(i,i)  
            cursor.execute(queryString)


* Másik lehetőségként kövesse ezt [forgatókönyv](../team-data-science-process/hive-walkthrough.md) NYC Taxi-ba való adatfeltöltést HDI-fürt. Fő lépései a következők:
  
  * Az AzCopy: tömörített CSV letöltése nyilvános blob a helyi mappába
  * Az AzCopy: töltse fel a kicsomagolt fürt megosztott kötetei szolgáltatás a helyi mappából a HDI-fürt
  * Jelentkezzen be a Hadoop-fürt fő csomópontjának és feltáró jellegű adatok elemzése előkészítése

Miután az adatok betöltése a HDI-fürt, ellenőrizheti az adatok az Azure Storage Explorerben. És a egy HDI-fürt létrehozott adatbázis nyctaxidb rendelkezik.

**Az adatok feltárása: a Python Hive-lekérdezések**

Mivel az adatokat a Hadoop-fürtöt, a Hive használatával feltárása és jellemzőkiemelés Hadoop-fürtök és a lekérdezés adatbázishoz való csatlakozáshoz használhatja a pyodbc csomagot. A létező táblák az előfeltételként szükséges lépésben létrehozott tekintheti meg.

    queryString = """
        show tables in nyctaxidb2;
        """
    pd.read_sql(queryString,connection)


![Meglévő táblák megtekintése](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Nézzük meg a rekordok száma havonta és gyakoriságát Formabontó vagy nem a trip táblázatban:

    queryString = """
        select month, count(*) from nyctaxidb.trip group by month;
        """
    results = pd.read_sql(queryString,connection)

    %matplotlib inline

    results.columns = ['month', 'trip_count']
    df = results.copy()
    df.index = df['month']
    df['trip_count'].plot(kind='bar')


![A bejegyzések száma havonta a diagram](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

    queryString = """
        SELECT tipped, COUNT(*) AS tip_freq
        FROM
        (
            SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
            FROM nyctaxidb.fare
        )tc
        GROUP BY tipped;
        """
    results = pd.read_sql(queryString,connection)

    results.columns = ['tipped', 'trip_count']
    df = results.copy()
    df.index = df['tipped']
    df['trip_count'].plot(kind='bar')


![Tipp gyakoriságok diagram](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Is számítási felvételi helye és dropoff hely közötti távolságot, és hasonlítsa össze a trip távolság.

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
    results = pd.read_sql(queryString,connection)
    results.head(5)


![Felvétel és dropoff tábla](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

    results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                       'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
    df = results.loc[results['trip_distance']<=100] #remove outliers
    df = df.loc[df['direct_distance']<=100] #remove outliers
    plt.scatter(df['direct_distance'], df['trip_distance'])


![Utazás távolság begyűjtés/dropoff távolság ábrázolása](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Most hozzunk készítse el lefelé-mintát (1 %) az adatok a modellezési. Machine Learning-olvasó modul is használhatja ezeket az adatokat.

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

        --- now insert contents of the join into the preceding internal table

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

Idővel tekintheti meg az adatok be van töltve a Hadoop-fürtök:

    queryString = """
        select * from nyctaxi_downsampled_dataset limit 10;
        """
    cursor.execute(queryString)
    pd.read_sql(queryString,connection)


![Adattábla](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

**Adatok olvasása a Machine Learning segítségével HDI: Adatolvasó modulja**

Is használhatja a **olvasó** modul a Machine Learning Studio a Hadoop-fürtöt az adatbázis eléréséhez. Csatlakoztassa a HDI-fürt és az Azure Storage-fiók létrehozása a machine learning modellek adatbázist használ a HDI-fürt engedélyezése a hitelesítő adatait.

![Olvasó modul tulajdonságai](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

Pontozott adatkészlet is megtekinthetők:

![Pontozott adatkészlet megtekintése](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse--databases"></a>Az Azure SQL Data Warehouse és adatbázisok
Az Azure SQL Data Warehouse egy rugalmas adattárház nagyvállalati szintű SQL Server kezelőfelülettel szolgáltatás.

A jelen megadott utasítások szerint helyezheti üzembe a az Azure SQL Data Warehouse [cikk](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Miután az Azure SQL Data Warehouse üzembe, ezzel [forgatókönyv](../team-data-science-process/sqldw-walkthrough.md) adatok feltöltése feltárás és -adatok az SQL Data Warehouse belül modellezési tennie.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Az Azure Cosmos DB egy NoSQL-adatbázis a felhőben. Ez lehetővé teszi, hogy hasonló JSON-dokumentumok kezelése, és lehetővé teszi, hogy tárolja és kérdezheti le a.

Az alábbi lépések az Előfeltételek – Azure Cosmos DB érhetnek el a dsvm-hez:

1. Az Azure Cosmos DB Python SDK-t a dsvm-hez már telepítve van (Futtatás ```pip install pydocumentdb --upgrade``` frissítése a parancssor használatával)
2. Az Azure Cosmos DB-fiók és a egy adatbázis létrehozása [Azure Portalon](https://portal.azure.com)
3. "Az Azure Cosmos DB áttelepítési eszköz" letöltését [Itt](https://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) és a egy szabadon választott könyvtárba való kinyeréséhez
4. A tárolt JSON-adatok (mexikói adatok) importálása egy [nyilvános blob](https://cahandson.blob.core.windows.net/samples/volcano.json) az a következő parancs paraméterei, az áttelepítési eszköz (a Cosmos DB áttelepítési eszköz telepítési könyvtárából dtui.exe) Cosmos DB-be. Adja meg a forrás és cél helyét ezekkel a paraméterekkel:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Az importált adatokat, ugorjon a Jupyter, és nyissa meg a notebook nevű *DocumentDBSample* , amely tartalmazza a python-kód Azure Cosmos DB eléréséhez, és néhány alapszintű lekérdezést. További információ a Cosmos DB a szolgáltatás felkeresésével [dokumentációs oldalon](https://docs.microsoft.com/azure/cosmos-db/).

## <a name="8-build-reports-and-dashboard-using-the-power-bi-desktop"></a>8. Hozhat létre jelentéseket és irányítópultokat a Power BI Desktop használatával
A Mexikói JSON-fájlt az előző Cosmos DB példából visual elemezheti az adatokat a Power BI-ban jelenítheti meg. Részletes lépéseket érhetők el a [Power BI-cikk](../../cosmos-db/powerbi-visualize.md). Magas szintű lépései a következők:

1. Nyissa meg a Power BI Desktop és az "adatok lekérése." Adja meg az URL-cím: https://cahandson.blob.core.windows.net/samples/volcano.json
2. Megjelenik a lista importált JSON-bejegyzések
3. A lista átalakítása egy táblát, így a Power BI is ugyanaz
4. Bontsa ki az oszlopot a Kibontás ikonra (a "a balra mutató nyílra és a egy jobbra mutató nyílra" ikonra a jobb oldali oszlop rendelkezőt) kattintva
5. Figyelje meg, hogy a hely a "Rekord" mező. Bontsa ki a bejegyzést, és válassza ki a csak a koordinátákat. Koordináta egy listaoszlop
6. Adjon hozzá egy olyan új oszlop a koordináta oszlopa alakítható át egy vesszővel tagolt külön LatLong oszlopban összefűzi a két elemet a koordináta lista mezőjében a következő képlettel ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Végül konvertálja a ```Elevation``` oszlop tizedes tört, és válassza a **Bezárás** és **alkalmaz**.

Helyett az előző lépésekben, illessze be a következő kódot, hogy parancsfájlok vette az a speciális szerkesztőben a Power bi Szolgáltatásban, amely lehetővé teszi, hogy a adatátalakítások írhat egy lekérdezési nyelvet használt lépéseket.

    let
        Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
        #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
        #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
        #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
        #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
        #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
    in
        #"Changed Type"



Most már az adatok a Power BI-adatmodellben. A Power BI desktopban a következőképpen jelenik meg:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Megkezdheti a jelentések és Vizualizációk használata az adatmodell létrehozásához. A lépések a jelen [Power BI-cikk](../../cosmos-db/powerbi-visualize.md#build-the-reports) hozhat létre egy jelentést. A kimenet egy olyan jelentés, az alábbihoz hasonló.

![A Power BI Desktop jelentés nézet – Power BI-összekötő](./media/vm-do-ten-things/power_bi_connector_pbireportview2.png)

## <a name="9-dynamically-scale-your-dsvm-to-meet-your-project-needs"></a>9. A dsvm-hez projekt igény szerint dinamikusan méretezhető
Felfelé és a DSVM projekt igény szerint méretezhető. Ha nem szeretné használni a virtuális gép az esténként vagy hétvégeken, akkor csak leállíthatja a virtuális gépről a [az Azure portal](https://portal.azure.com).

> [!NOTE]
> Számítási díjak merülhetnek fel a virtuális gép csak az operációs rendszer leállítási gomb használatakor.  
> 
> 

Ha szeretné kezelni a nagy méretű elemzést és nagyobb CPU-és/vagy memória-és/vagy lemez kapacitásra van szüksége annak CPU-magok, a példányok GPU-alapú Virtuálisgép-méretek széles választéka deep learning, a memória-kapacitás és a lemeztípusok (beleértve az SSD-meghajtókat) a számítási és költségvetési szükségleteinek megfelelő. Virtuális gépek teljes listáját és azok óránkénti számítási díjak érhető el a [Azure Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/) lapot.

Hasonlóképpen ha csökkenti a virtuális gép feldolgozási kapacitást szükség (például: egy nagyobb munkaterhelés helyezte át a Hadoop és Spark-fürt), a fürt vertikális a [az Azure portal](https://portal.azure.com) és a VM-példány a gépház. Íme egy képernyőkép.

![Virtuális gép példány beállításai](./media/vm-do-ten-things/VMScaling.PNG)

## <a name="10-install-additional-tools-on-your-virtual-machine"></a>10. További eszközök telepítése a virtuális gépen
A dsvm-hez, amely a common data analytics kell számos előre beépített több eszköz közül. Ezzel időt takarít idő lehetőleg ne kelljen telepítése és konfigurálása a környezetek egyenként valamint pénzt takaríthat meg, csak azért kell fizetnie erőforrások használatát.

Használhat más az elemzési környezet növelése érdekében ez a cikk a profilkészítés során létrehozott Azure data és analitikai szolgáltatásokat. Bizonyos esetekben az igényeinek további eszközöket, beleértve néhány harmadik fél szellemi tulajdont képező eszköz lehet szükség. A virtuális gépen telepíteni kell az új eszközök teljes rendszergazdai hozzáféréssel rendelkezik. A Python és előre nem telepített R további csomagokat is telepíthet. Pythonhoz készült használhat ```conda``` vagy ```pip```. Az R használata a ```install.packages()``` az R-konzol vagy használni az IDE, és válassza a "**csomagok** -> **csomagok telepítése...** ".

## <a name="summary"></a>Összegzés
Ezek a mindössze néhány a lehetőség a Microsoft Data Science virtuális gépen. Nincsenek számos további lehetőség legyen a hatékony elemzési környezet.


---
title: "Tíz lehetősége van az adatok tudományos virtuális gépet az Azure |} Microsoft Docs"
description: "Hajtsa végre a virtuális gép adattudomány különböző adatok feltárása és modellezési feladat."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 145dfe3e-2bd2-478f-9b6e-99d97d789c62
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: gokuma;weig;bradsev
ms.openlocfilehash: 9aafd6300bebbb5505aa4ed707cac9716c8e6aa1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="ten-things-you-can-do-on-the-data-science-virtual-machine"></a>Tíz dolog, amire alkalmas az adatelemző virtuális gép

A Microsoft Data tudományos virtuális gép (DSVM) olyan hatékony tudományos fejlesztési környezet, amely lehetővé teszi a különböző adatok feltárása és modellezési feladatok elvégzéséhez. A környezet már rendelkezik a beépített és csomagolt eszközökkel számos népszerű adatok elemzés, amely megkönnyíti a helyszíni, elemzéshez gyors megkezdéséhez felhőalapú vagy hibrid környezetekhez. A DSVM szorosan együttműködik olyan sok Azure-szolgáltatást, és képes olvasni és feldolgozni az adatokat az Azure, az Azure SQL Data Warehouse, az Azure Data Lake, az Azure Storage, vagy az Azure Cosmos Adatbázisba tárolt. Azt is kihasználhatják a más analytics eszközök, például az Azure Machine Learning és az Azure Data Factory.

Ebben a cikkben azt végigvezetik Önt a DSVM segítségével különböző adatok tudományos feladatok elvégzésére, és más Azure-szolgáltatásokkal kommunikálni. Íme néhány a DSVM végezhet el:

1. Adatok vizsgálatát, és helyileg a használatával a Microsoft R Server, Python DSVM modellek fejlesztése
2. Méretezhetőség és teljesítmény készült R egy vállalati készen verzióját az adatok egy böngésző használatával Python 2, Python 3, Microsoft R kísérletezhet Jupyter notebook használatával
3. Azok a modellek használatával készített R és Python az Azure Machine Learning szolgáltatást az ügyfélalkalmazások számára a szolgáltatások egyszerű webes felületen keresztül modellek
4. Felügyelheti az Azure-erőforrások Azure-portálon vagy a Powershell használatával
5. A tárhely kiterjesztése és nagy méretű adatkészletek megosztása / hozzon létre egy Azure File storage a DSVM csatlakoztatható meghajtóként között a teljes csoport kód
6. Kód megoszthatja a Githubon használatával munkatársaival, és hozzáférni a tárházat a előre telepített Git ügyfelek - Git Bash Git grafikus felhasználói felület használatával.
7. Hozzáférjen a különböző Azure adatok és analitikák szolgáltatásokhoz hasonlóan az Azure blob storage Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, Azure SQL Data Warehouse & adatbázisok
8. Jelentéseket és a Power BI Desktop előre telepítve a DSVM használatával hozhat létre, és telepítheti azokat a felhő
9. A projekt igényeknek megfelelő DSVM dinamikusan méretezése
10. A virtuális gép további eszközök telepítése   

> [!NOTE]
> A cikkben szereplő további adatok tárolási és elemzési szolgáltatások számos további használati díjak érvényesek. Tekintse meg a [Azure díjszabása](https://azure.microsoft.com/pricing/) a lapot.
> 
> 

**Előfeltételek**

* Azure-előfizetés szükséges. Regisztrálhat egy ingyenes próbaverzióra [Itt](https://azure.microsoft.com/free/).
* Az Azure portálon található adatok tudományos virtuális géphez történő üzembe helyezéséhez utasításokat érhetők el [egy virtuális gép létrehozása](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).

## <a name="1-explore-data-and-develop-models-using-microsoft-r-server-or-python"></a>1. Adatok vizsgálatát, és a Microsoft R Server vagy a Python modellek fejlesztése
Ehhez a adatelemzés jogosultsággal a DSVM a használhatja például R és Python nyelveket.

Az R egy IDE "8.0 fordulat R vállalati" nevű, a start menüből vagy az asztalon található is használhatja. A Microsoft közzétett további könyvtárak fölött a nyitott forrás/CRAN-R méretezhető elemzés és elemezhetik a párhuzamos darabolt elemzés végrehajtásával engedélyezett memória mérete nagyobb képességét engedélyezéséhez. A choice hasonló egy R IDE is telepíthet [Rstudióból](https://www.rstudio.com/products/rstudio-desktop/).

A Python például a Visual Studio Community Edition, amely a Python Tools for Visual Studio (PTVS) bővítmény előre telepítve van egy IDE használhatják. Alapértelmezés szerint csak egy alapszintű Python 2.7 van konfigurálva a PTVS (nélkül analytics függvénytárat, például a SciKit, Pandas). Ahhoz, hogy Anaconda Python 2.7-es és 3.5-ös verzióját, tegye a következőket kell:

* Hozzon létre egyéni környezetek egyes verzióihoz való **eszközök** -> **Python Tools** -> **Python-környezetek** , majd kattintson " **+ Egyéni**"a a Visual Studio 2015 Community Edition
* Adjon meg leírást, majd állítsa be a környezet előtag elérési utakat *c:\anaconda* Anaconda Python 2.7 vagy *c:\anaconda\envs\py35* a Anaconda Python 3.5
* Kattintson a **az automatikus észlelés** , majd **alkalmaz** mentéséhez a környezetben.

Ez az egyéni környezetben való telepítés néz a Visual Studióban.

![PTVS-telepítő](./media/vm-do-ten-things/PTVSSetup.png)

Tekintse meg a [PVTS dokumentációban](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) további részleteket a Python-környezetek létrehozása.

Most hozzon létre egy új Python-projektet állíthatók be. Navigáljon a **fájl** -> **új** -> **projekt** -> **Python** és típusának kiválasztása Python alkalmazást hoz létre. A Python-környezetben a jelenlegi projekt állíthat be a kívánt verziójával (2.7 vagy 3.5-ös Anaconda): kattintson a jobb gombbal a **Python-környezetben**, jelölje be **hozzáadása Python-környezetek**, majd válassza ki a projekt rendelje hozzá a kívánt környezetre. A termék PTVS kezelésével kapcsolatos további információ található [dokumentáció](https://github.com/Microsoft/PTVS/wiki) lap.

## <a name="2-using-a-jupyter-notebook-to-explore-and-model-your-data-with-python-or-r"></a>2. Jupyter Notebook használatával történő vizsgálatát, és az adatok R és Python
A Jupyter Notebook egy hatékony környezetben, amely egy webböngésző-alapú "IDE" biztosít az adatok feltárása és modellezési. Jupyter Notebook Python 2, Python 3 vagy R (nyílt forráskódú és a Microsoft R Server) használhatja.

A Jupyter Notebook kattintson a start menü ikonra indítása / asztali ikon című **Jupyter Notebook**. A DSVM a is tallózással megkereshet "https://localhost:9999 /" Jupiter jegyzetfüzet eléréséhez. Ha azt kéri a jelszót, használja a megjelenő utasításokat a ***hogyan hozzon létre egy erős jelszót a Jupyter notebook kiszolgálón*** szakasza a [a Microsoft Data tudományos virtuális gép kiépítéséhez](provision-vm.md) az a témakörhöz, és hozzon létre egy erős jelszót a Jupyter notebook eléréséhez. 

A notebook megnyitása után meg kell jelennie egy könyvtárat, amely tartalmazza azokat a DSVM előcsomagolt van néhány példa jegyzetfüzetek. Most már a következőket teheti:

* Kattintson a notebook kód.
* Minden cella végrehajtása billentyűkombináció lenyomásával **a SHIFT + ENTER**.
* Futtassa a teljes jegyzetfüzet található kattintva **cella** -> **futtatása**
* létrehozhat egy újat (bal felső sarokban) Jupyter ikonra kattint, majd **új** jobb és lehetőséget választja a notebook nyelv (más néven kernelek) gombra.   

> [!NOTE]
> Jelenleg támogatott Python 2.7, Python 3.5-ös és R. Az R kernel programozási támogatja mind nyílt forráskódú R, valamint a vállalati méretezhető Microsoft R Server.   
> 
> 

Miután belépett a notebook ismerje meg az adatokat, a modell létrehozása, a modell teszteléséhez szalagtárak tetszőleges.

## <a name="3-build-models-using-r-or-python-and-operationalize-them-using-azure-machine-learning"></a>3. R vagy Python és használatával Operationalize őket az Azure Machine Learning modellek létrehozása
Miután létrehozott és ellenőrizni a modell a következő lépés az általában éles környezetben telepítheti. Ez lehetővé teszi az ügyfél a modell előrejelzéseket egy valós idejű vagy kötegelt módban alapon a meghívni kívánt alkalmazásokat. Az Azure Machine Learning egy modellt a beépített R vagy Python azok mechanizmust biztosít.

Azok az az Azure Machine Learning modellje, amikor egy webszolgáltatás-bővítmény tesz elérhetővé, amely lehetővé teszi az ügyfelek, amelyek bemeneti paraméterek át és előrejelzéseket fogadása a modellből kimenetek REST-hívásokat.   

> [!NOTE]
> Ha nem még feliratkozott az Azure Machine Learning, ezt úgy szerezheti be egy szabad munkaterület vagy egy szabványos munkaterület látogasson el a [Azure Machine Learning Studio](https://studio.azureml.net/) kezdőlapját, majd kattintson az "első lépések".   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Buildelés és azok Python modellek
Ez egy egyszerű modell a SciKit további szalagtárat használó épülő Python Jupyter Notebook létre kódrészletét.

    #IRIS classification
    from sklearn import datasets
    from sklearn import svm
    clf = svm.SVC()
    iris = datasets.load_iris()
    X, y = iris.data, iris.target
    clf.fit(X, y)

A használt központi telepítése a python modellek Azure Machine Learning becsomagolja a függvénynek a modell előrejelzését és decorates azt a előre telepített Azure Machine Learning python Library attribútumokkal, az Azure Machine Learning jelöl munkaterület-Azonosítót, az API-kulcs és a bemeneti és a visszatérési paramétert.  

    from azureml import services
    @services.publish(workspaceid, auth_token)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(int) #0, or 1, or 2
    def predictIris(sep_l, sep_w, pet_l, pet_w):
     inputArray = [sep_l, sep_w, pet_l, pet_w]
    return clf.predict(inputArray)

Egy ügyfél hajtsa végre a webszolgáltatás hívásokat. Nincsenek kényelmi burkolók, amely a REST API-kérés összeállításához. Itt látható mintakód a webszolgáltatás felhasználásához.

    # Consume through web service URL and keys
    from azureml import services
    @services.service(url, api_key)
    @services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
    @services.returns(float)
    def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
    pass

    IrisPredictor(3,2,3,4)


> [!NOTE]
> Az Azure Machine Learning könyvtár csak jelenleg támogatott a Python 2.7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Buildelés és azok R modellek
Az adatok tudományos virtuális gép vagy máshol alakzatot Azure Machine Learning olyan módon, hogy hogyan történik a Python hasonlít a beépített R modellek is telepíthet. A lépéseket:

* Hozzon létre egy settings.json fájlt arra, hogy a munkaterület azonosítója és a hitelesítési token a következő kód mintában látható módon.
* a burkoló írni, ehhez a a modell előrejelzése függvény.
* hívás ```publishWebService``` felelt meg a függvény burkoló az Azure Machine Learning-könyvtárban.  

Ez az eljárás és kód kódtöredékek, amelyek segítségével állítsa be, felépítéséhez, közzétételét és felhasználását a modell az Azure Machine Learning webszolgáltatásként.

#### <a name="setup"></a>Beállítás
1. A Machine Learning R telepítéséhez írja be a ```install.packages("AzureML")``` fordulat R vállalati 8.0 IDE vagy az R IDE.
2. Töltse le a RTools [Itt](https://cran.r-project.org/bin/windows/Rtools/). A zip-segédprogram az elérési út (és nevesített zip.exe) azok az R csomag Machine Learning be van szüksége.
3. Hozzon létre egy settings.json fájlt nevű alatt ```.azureml``` a kezdőkönyvtár alatt, és adjon meg a paraméterek az Azure Machine Learning-munkaterület:

Settings.JSON fájlstruktúra:

    {"workspace":{
    "id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
    "authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
    }}


#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Az R egy modell létrehozása, és tegye közzé az Azure Machine Learning
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

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>A modell Azure Machine Learning telepített felhasználása
A modell a ügyfélalkalmazás felhasználását, megkeresheti a közzétett webes szolgáltatás neve használatával használjuk az Azure Machine Learning könyvtár a `services` API-hívás a végpont meghatározásához. Ezt követően csak hívja a `consume` fázisban kell elvégezni, ha az adatok keret és működik.
A következő kódot az Azure Machine Learning webszolgáltatás közzétett modell felhasználásához szolgál.

    library(AzureML)
    library(lme4)
    ws <- workspace(config="~/.azureml/settings.json")

    s <-  services(ws, name = "sleepy lm")
    s <- tail(s, 1) # use the last published function, in case of duplicate function names

    ep <- endpoints(ws, s)

    # OK, try this out, and compare with raw data
    ans = consume(ep, sleepstudy)$ans

További információ az Azure Machine Learning R könyvtárban található [Itt](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf).

## <a name="4-administer-your-azure-resources-using-azure-portal-or-powershell"></a>4. Felügyelheti az Azure-erőforrások Azure-portálon vagy a Powershell használatával
A DSVM nem csak lehetővé teszi a virtuális gép létrehozásához, helyileg a elemzési megoldások, de is lehetővé teszi a Microsoft Azure felhőbe szolgáltatások eléréséhez. Azure több számítási, tárolási, analytics adatszolgáltatások és egyéb szolgáltatások, amelyek felügyeletéhez, és a DSVM hozzáférést biztosít.

Felügyelheti az Azure előfizetés és a felhőalapú erőforrások a böngészővel, és mutasson a [Azure-portálon](https://portal.azure.com). Azure Powershell segítségével is felügyelheti az Azure-előfizetés és az erőforrások egy parancsfájl segítségével.
Azure Powershell futtathatja egy parancsikont az asztalon vagy a start menü "Microsoft Azure Powershell" című témakört. Tekintse meg [Microsoft Azure Powershell dokumentációs](../../powershell-azure-resource-manager.md) hogyan felügyelhető az Azure-előfizetés és a Windows Powershell-parancsfájlokkal erőforrások olvashat.

## <a name="5-extend-your-storage-space-with-a-shared-file-system"></a>5. A tárolóhely egy megosztott fájlrendszerrel kiterjesztése
Adatszakértőkön megoszthatja nagy adatkészletek, kód vagy a csapaton belüli más erőforrásokat. Maga a DSVM készül 70GB szabad lemezterület van. A tárolási kiterjesztéséhez használhatja a Azure szolgáltatás, és vagy csatlakoztassa azt a DSVM vagy -e férni a REST API-n keresztül.   

> [!NOTE]
> A maximális lemezterület a Azure Fájlszolgáltatás megosztás 5TB, és egyes fájl maximális mérete 1 TB-os.   
> 
> 

Azure Powershell használatával hozzon létre egy Azure Fájlszolgáltatás megosztást. Ez a parancsfájl futtatásához Azure PowerShell egy Azure szolgáltatás fájlmegosztás létrehozásához.

    # Authenticate to Azure.
    Login-AzureRmAccount
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


Most, hogy létrehozta az Azure fájlmegosztások, a virtuális gép az Azure-ban csatlakoztathatja azt. Erősen ajánlott, hogy a virtuális gép van, ugyanazon Azure adatközpontba, ahol a késés és az adatok átvitel díjak elkerülése érdekében a tárfiók. Az alábbiakban a parancsok a meghajtót csatlakoztatni a DSVM, amely az Azure Powershell futtathatja.

    # Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
    cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

    # Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
    net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>


Most már hozzáférhet a meghajtó mint bármely normál meghajtót a virtuális Gépen.

## <a name="6-share-code-with-your-team-using-github"></a>6. Kód megosztása a csapat GitHub használatával
GitHub tárháza kód megkeresésének mintakód és a források számos különböző eszközök különböző technológiákkal, a fejlesztői Közösség által megosztott használatával. Az Git a technológia segítségével nyomon követheti és a kód fájlok tárolásához. GitHub egyben a platform saját tárház tárolja a csoport megosztott kód és dokumentáció, verziója vezérléséhez, és is meghatározhatja, megtekintése és kód közre hozzáféréssel rendelkező létrehozásához használható. Látogasson el a [GitHub súgóoldalak](https://help.github.com/) további információt a Git használatával. GitHub együttműködni a csapatával, használja a Közösség által fejlesztett és kód vissza hozzájárulnak a közösségi módjai egyikét használhatja.

A DSVM már betöltött az ügyféleszközök elől a előre parancssori, mint a GitHub-adattár eléréséhez jól grafikus felhasználói Felülettel is. A parancssori eszköz segítségével megismerheti a Git és GitHub elnevezése a Git bash eszközt. A Visual Studio, a DSVM telepítve van a Git-bővítmények. Kezdeti ikonok az egyes eszközök a start menüben és az asztalon található.

Kód letöltése a GitHub-tárházban, használja a ```git clone``` parancsot. Ha például a jelenlegi könyvtárba, amely a Microsoft által kiadott tudományos adattárház letöltésére is futtassa a következő parancsot, Miután belépett ```git-bash```.

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

A Visual Studio a Klónozási műveletet végezhet. Az alábbi képernyőfelvételen bemutatja, hogyan érhetők el a Git szoftver, a GitHub, a Visual Studio eszközök.

![A Visual Studio Git](./media/vm-do-ten-things/VSGit.PNG)

A Git használatával együttműködni a GitHub-tárházban több forrásanyag is elérhető a github.com további információt talál. A [adatlap](https://training.github.com/kit/downloads/github-git-cheat-sheet.pdf) hasznos hivatkozás.

## <a name="7-access-various-azure-data-and-analytics-services"></a>7. Hozzáférjen a különböző Azure adatok és analitikák szolgáltatásokhoz
### <a name="azure-blob"></a>Azure-blob
Az Azure blob a kis és nagy adatok megbízható, gazdaságos felhőtárhelyére. Ossza meg velünk keresse meg, hogyan viheti át adatok Azure-Blob és a hozzáférési adatok tárolódnak az Azure Blob.

**Előfeltétel**

* **Az az Azure Blob storage-fiók létrehozása [Azure-portálon](https://portal.azure.com).**

![Create_Azure_Blob](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Győződjön meg arról, hogy a telepített parancssori AzCopy eszköz a következő címen található ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Ha nem szeretné beírni a parancs teljes elérési útja, ez az eszköz futtatásakor a PATH környezeti változóhoz azcopy.exe tartalmazó könyvtár adhat hozzá. AzCopy eszközről további információért tekintse meg [AzCopy dokumentációját](../../storage/common/storage-use-azcopy.md)
* Indítsa el az Azure Tártallózó eszközt. Letölthető a [Microsoft Azure Tártallózó](http://storageexplorer.com/). 

![AzureStorageExplorer_v4](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

**Helyezze át az adatokat a virtuális gép Azure Blob: AzCopy**

Adatok között a helyi fájlokat és a blob-tároló áthelyezéséhez használhatja AzCopy parancssori vagy a PowerShell használatával:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Cserélje le **C:\myfolder** az elérési útra, ahol a fájl található, **mystorageaccount** a blob storage fiók nevével **mycontainer** a tároló nevére **tárfiók kulcsa** számára a blob tárelérési kulcsát. A tárfiók hitelesítő adatainak a található [Azure-portálon](https://portal.azure.com).

![StorageAccountCredential_v2](./media/vm-do-ten-things/StorageAccountCredential_v2.png)

AzCopy parancs futtatása, a PowerShell vagy a parancssorból. Íme néhány példa használati AzCopy parancs:

    # Copy *.sql from local machine to a Azure Blob
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

    # Copy back all files from Azure Blob container to Local machine

    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S



Egy Azure-blobot másolni a AzCopy parancs futtatása után megjelenik a fájl jön létre az Azure Tártallózó hamarosan.

![AzCopy_run_finshed_Storage_Explorer_v3](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

**Helyezze át az adatokat a virtuális gép Azure Blob: Azure Tártallózó**

A virtuális gép Azure Tártallózó is feltöltheti a helyi fájl adatait:

* Adatok feltöltése a tárolóhoz, válassza ki a cél-tároló, majd kattintson a **feltöltése** gomb.![ A Tártallózó feltöltése](./media/vm-do-ten-things/storage-accounts.png)
* Kattintson a **...**  jobb oldalán a **fájlok** mezőben adja meg egy vagy több fájl feltöltése a fájlrendszerből, és kattintson a **feltöltése** a fájlok feltöltése a kezdéshez.![ A blob-fájl feltöltése](./media/vm-do-ten-things/upload-files-to-blob.png)

**Adatokat olvasni az Azure Blob: gépi tanulás olvasó modul**

Az Azure Machine Learning Studióban használhatja egy **és adatokat importálhat modul** adatokat olvasni a blob.

![AML_ReaderBlob_Module_v3](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

**Adatokat olvasni az Azure Blob: Python ODBC**

Használhat **BlobService** erőforrástár-adatok olvasása közvetlenül a Jupyter Notebook vagy Python programban blob.

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

Majd a beépülő modul az Azure Blob-fiók hitelesítő adatait, és adatokat olvasni Blob:

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

Az adatok az olvasható adatok keretként:

![IPNB_data_readin](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Az Azure Data Lake Storage egy kapacitású adattár a big data elemzés számítási feladatokat és a Hadoop elosztott fájlrendszerrel (HDFS) kompatibilis. A Hadoop ökoszisztémájának és az Azure Data Lake Analytics is működik. Megmutatjuk, hogyan adatok áthelyezése az Azure Data Lake Store rendszerbe, és futtassa a analytics az Azure Data Lake Analytics használatának.

**Előfeltétel**

* Hozzon létre az Azure Data Lake Analytics a [Azure-portálon](https://portal.azure.com).

![Azure_Data_Lake_Create_v2](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* A **Azure Data Lake Tools** a **Visual Studio** ezzel található [hivatkozás](https://www.microsoft.com/download/details.aspx?id=49504) már telepítve van a Visual Studio Community Edition egyben a virtuális gépen. Visual Studio kezdő, és az Azure-előfizetése van bejelentkezve, megtekintheti az Azure Data Analytics-fiók és a bal oldali panel a Visual Studio tárolás.

![Azure_Data_Lake_PlugIn_v2](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

**Virtuális gép a Data Lake-tárolt adatok mozgatása: Azure Data Lake-kezelővel**

Használhat **Azure Data Lake Explorer** feltölteni az adatokat a virtuális gépen a helyi fájlokból Data Lake-tárhelyre.

![Azure_Data_Lake_UploadData](./media/vm-do-ten-things/Azure_Data_Lake_UploadData.PNG)

Adatok csővezeték productionize az adatmozgás, vagy a Azure Data Lake használatával is készíthet a [Azure adatok Factory(ADF)](https://azure.microsoft.com/services/data-factory/). Jelenleg hivatkozik, ez [cikk](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) végigvezeti a lépés az adatok folyamatok.

**Az Azure Blob-adatok olvasása a Data Lake-: U-SQL**

Ha az adatok Azure Blob Storage tárolóban található, közvetlenül olvashatja adatokat az Azure storage-blobból az U-SQL-lekérdezésben. Előtt a U-SQL-lekérdezés létrehozása, győződjön meg arról, hogy a blob storage-fiók kapcsolódik az Azure Data Lake. Lépjen **Azure-portálon**, az Azure Data Lake Analytics irányítópulton található, majd **adatforrás hozzáadása**, tárolási típust választja, a **Azure Storage** , és csatlakoztassa az Azure Storage-fiók nevét és a kulcs. Ezután megtörténik a tárfiókban tárolt adatokra hivatkoztak.

![Adja meg a tárfiók és a kulcs](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

A Visual Studio adatokat olvasni a blob-tároló, hajtsa végre az egyes adatkezelési, jellemzőkiemelés és kimeneti vagy az Azure Data Lake, vagy az Azure Blob Storage a kapott adatokat. Ha az adatokat a blob Storage tárolóban hivatkozik, **wasb: / /**; Ha az Azure Data Lake, használja az adatok hivatkozik **swbhdfs: / /**

![Adatok keret](./media/vm-do-ten-things/USQL_Read_Blob_v2.PNG)

Az alábbi U-SQL-lekérdezések Visual Studio segítségével:

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



A lekérdezés elküldése a kiszolgálóra, után ábra: a feladat állapotának jelenik meg.

![Feladat állapota diagramja](./media/vm-do-ten-things/USQL_Job_Status.PNG)

**A Data Lake adatait: U-SQL**

Miután a dataset az Azure Data Lake van okozhatnak, [U-SQL nyelv](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) lekérdezése, és áttekintheti az adatokat. U-SQL nyelv T-SQL hasonló, de egyesíti az egyes szolgáltatások, a C#, hogy a felhasználók írhat, egyéni modulokat, felhasználó által definiált függvények és stb. Az előző lépésben a parancsfájlokat használhat.

A lekérdezés után tripdata_summary-kiszolgálóra küldött. Fürt megosztott kötetei szolgáltatás található hamarosan **Azure Data Lake Explorer**, akkor előfordulhat, hogy az adatok villámnézetének kattintson a jobb gombbal a fájl.

![Az Azure Data Lake Explorer fájl](./media/vm-do-ten-things/USQL_create_summary.png)

A fájl adatainak megtekintéséhez:

![Fájl összegzése](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>HDInsight Hadoop-fürtök
Az Azure HDInsight egy olyan felügyelt Apache Hadoop, a Spark, a HBase és a Storm szolgáltatás, a felhő. Az adatok tudományos virtuális gépről Azure HDInsight-fürtök egyszerűen dolgozhat.

**Előfeltétel**

* Az az Azure Blob storage-fiók létrehozása [Azure-portálon](https://portal.azure.com). Ez a tárfiók a HDInsight-fürtök az adatok tárolására szolgál.

![Azure Blob storage-fiók létrehozása](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Testre szabhatja az Azure HDInsight Hadoop-fürtök a [Azure-portálon](../team-data-science-process/customize-hadoop-cluster.md)
  
  * A storage-fiók létrehozásakor a HDInsight-fürthöz létrehozott kell kapcsolni. Ez a tárfiók eléréséhez szükséges adatok, amelyek a fürtön belül dolgozhatók szolgál.

![HDInsight-fürthöz létrehozott tárfiók csatolása](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Engedélyeznie kell az **távelérési** az átjárócsomóponthoz, a fürt létrehozása után. Az itt megadott (eltérnek a a létrehozásakor a fürthöz megadott) távoli hozzáférési hitelesítő adatok megjegyzése: a következő eljárással kell.

![Távelérés engedélyezése](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Hozzon létre egy Azure Machine Learning munkaterülettel. A gépi tanulási kísérletekhez a Machine Learning-munkaterület vannak tárolva. A kijelölt beállítások kiválasztása a portálon, az alábbi képernyőfelvételen látható módon:

![Azure Machine Learning-munkaterület létrehozása](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Adja meg a paraméterek a munkaterület

![Adja meg a Machine Learning-munkaterület paraméterek](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Töltse fel az adatok IPython Notebook használatával. Először importálja a szükséges csomagokat, a beépülő modul hitelesítő adatokat, egy adatbázis létrehozása a tárfiókban lévő, majd HDI-fürtök adatok betöltése.

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


* Ehelyett kövesse a [forgatókönyv](../team-data-science-process/hive-walkthrough.md) NYC Taxi adatokat feltölteni HDI-fürtnek. Fő lépések az alábbiak:
  
  * AzCopy: zip CSV letöltése nyilvános blob a helyi mappájába
  * AzCopy: tömörítetlen fürt megosztott kötetei szolgáltatás a helyi mappából HDI-fürtnek való feltöltése
  * Jelentkezzen be a Hadoop-fürt átjárócsomópontjához, és készítse elő a felderítő adatelemzés

Miután az adatok betöltése a HDI-fürtöt, ellenőrizheti a Azure Tártallózó adatai. És egy adatbázis nyctaxidb HDI-fürtöt létrehozni.

**Az adatok feltárása: a Python Hive-lekérdezések**

Mivel az adatokat a Hadoop-fürttel, használhatja a pyodbc csomag Hive eszközzel feltárása és jellemzőkiemelés Hadoop-fürtök és a lekérdezés adatbázishoz való kapcsolódáshoz. A létező táblák az előfeltételként szükséges lépésben létrehozott tekintheti meg.

    queryString = """
        show tables in nyctaxidb2;
        """
    pd.read_sql(queryString,connection)


![Meglévő táblák megtekintése](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Nézzük rekordok száma havonta és gyakoriságát Formabontó vagy a út táblában nem szereplő:

    queryString = """
        select month, count(*) from nyctaxidb.trip group by month;
        """
    results = pd.read_sql(queryString,connection)

    %matplotlib inline

    results.columns = ['month', 'trip_count']
    df = results.copy()
    df.index = df['month']
    df['trip_count'].plot(kind='bar')


![Bejegyzések száma havonta a ábrázolása](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

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


![Tipp gyakoriságot ábrázolása](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Azt is számítási felvételi helye és dropoff helye közötti távolság, és hasonlítsa össze a út távolságot.

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


![A felvételi és dropoff tábla](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

    results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                       'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
    df = results.loc[results['trip_distance']<=100] #remove outliers
    df = df.loc[df['direct_distance']<=100] #remove outliers
    plt.scatter(df['direct_distance'], df['trip_distance'])


![A felvételi/dropoff távolság út távolságra ábrázolása](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Most tegyük készítse el lefelé-mintát (1 %) adatokat a modellezési. A Machine Learning-olvasó modul használhatjuk ezeket az adatokat.

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

Egy kis idő után megjelenik az adatok betöltése a Hadoop-fürtök:

    queryString = """
        select * from nyctaxi_downsampled_dataset limit 10;
        """
    cursor.execute(queryString)
    pd.read_sql(queryString,connection)


![Az adatok tábla](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

**A Machine Learning segítségével HDI-adatok olvasása: olvasó modul**

Is használhatja a **olvasó** modul a Machine Learning Studio Hadoop-fürt-adatbázisának eléréséhez. Csatlakoztassa a HDI-fürtök és a build ing gépi tanulási modellek adatbázist használ a HDI-fürtök engedélyezése Azure Storage-fiók hitelesítő adatait.

![Olvasó modul tulajdonságai](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

A pontozott adatkészletet is megtekinthetők:

![Pontozott dataset megtekintése](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse--databases"></a>Az Azure SQL Data Warehouse & adatbázisok
Az SQL Data Warehouse az egy rugalmas data warehouse az SQL Server révén vállalati szintű szolgáltatásként.

A jelen utasításokat követve építhető ki az Azure SQL Data Warehouse [cikk](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Miután az Azure SQL Data Warehouse kiépítése ezzel [forgatókönyv](../team-data-science-process/sqldw-walkthrough.md) adatok feltöltését, feltárása és az SQL Data warehouse adatainak használatával modellezési.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos-adatbázis egy NoSQL-adatbázis a felhőben. Lehetővé teszi például a JSON-dokumentumok kezelése, és lehetővé teszi tárolja, és a dokumentumok lekérdezése.

A következő lépések szükséges Azure Cosmos DB elérje a DSVM kell.

1. A DocumentDB Python SDK telepítése (Futtatás ```pip install pydocumentdb``` parancssorból)
2. Hozzon létre egy Azure Cosmos DB fiókot és az adatbázis [Azure-portálon](https://portal.azure.com)
3. "Azure Cosmos DB áttelepítési eszköz" letölthető [Itt](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) és bontsa ki az Ön által választott könyvtárra
4. Adatimportálás JSON (mexikói adatok) tárolja egy [nyilvános blob](https://cahandson.blob.core.windows.net/samples/volcano.json) be a következő parancsparaméterek az áttelepítési eszköz (a könyvtárból, amelyre telepítette a Cosmos DB áttelepítési eszköz dtui.exe) Cosmos DB. Adja meg a forrás és cél helyét, ezekkel a paraméterekkel:
   
    /s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/; AccountKey = [[kulcs]; adatbázis mexikói /t.Collection:volcano1 =

Az adatok importálása után nyissa meg Jupyter, és nyissa meg a notebook című *DocumentDBSample* tartalmazó python kódját szeretné a hozzáférést a DocumentDB és néhány alapvető lekérdezése. További kapcsolatos Cosmos DB érhetők el a szolgáltatás [dokumentációs oldal](https://docs.microsoft.com/azure/cosmos-db/).

## <a name="8-build-reports-and-dashboard-using-the-power-bi-desktop"></a>8. Jelentések és a Power BI Desktop használatával hozhat létre.
Ossza meg velünk megjelenítheti a Power BI-ban az előző Cosmos DB példában ahhoz, hogy az adatok vizuális betekintést bekerül mexikói JSON-fájlt. Részletes utasítások találhatók a [Power BI-cikk](../../cosmos-db/powerbi-visualize.md). Magas szintű lépései a következők:

1. Nyissa meg a Power BI Desktopban, és hajtsa végre az "Adatok beolvasása". Adja meg az URL-CÍMÉRE: https://cahandson.blob.core.windows.net/samples/volcano.json
2. Láthatja, hogy a lista formájában importált JSON-bejegyzések
3. A lista átalakítása táblázattá, a Power BI dolgozzanak ugyanazzal a
4. Bontsa ki az oszlopok (egy jobb oldalán a oszlop "balra nyíl billentyűt és a jobbra mutató nyílra" ikonnal) található Kibontás ikonra kattintva
5. Figyelje meg, hogy a helye "Rekord" mező. Bontsa ki a bejegyzést, és válassza ki a csak a koordináták százalékosan. Koordináta egy listaoszlop
6. Vegyen fel egy új oszlopot a koordináta oszlopa alakítani egy vesszővel külön LatLong oszlop hozzáfűzésével a két elemet a koordináta lista mezőben a következő képlettel ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Végül alakítsa át a ```Elevation``` oszlop decimális és válassza ki a **Bezárás** és **alkalmaz**.

Helyett az előző lépésekben, illessze be az alábbi kódot, hogy a parancsfájlok a speciális szerkesztőben a Power BI, amely lehetővé teszi egy lekérdezési nyelv írja be a adatátalakítást lépéseire ki.

    let
        Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
        #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
        #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
        #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
        #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
        #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
    in
        #"Changed Type"



Most már rendelkezik az adatokat a Power BI adatmodell. A Power BI desktop megjelenjen-e az alábbiak szerint:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Megkezdheti a jelentések és az adatmodell használatával képi megjelenítések készítéséhez. Ezen lépések követésével [Power BI-cikk](../../cosmos-db/powerbi-visualize.md#build-the-reports) jelentés létrehozásához. A végeredménynek olyan jelentést, amely a következőképpen néznek.

![A Power BI Desktop jelentés nézet – Power BI connector](./media/vm-do-ten-things/power_bi_connector_pbireportview2.png)

## <a name="9-dynamically-scale-your-dsvm-to-meet-your-project-needs"></a>9. A projekt igényeknek megfelelő DSVM dinamikusan méretezése
A projekt igényeknek megfelelő DSVM fel és le méretezheti. Ha nem szeretné használni a virtuális Gépet a este vagy a hétvégeken, csak leállíthat a virtuális Gépet a [Azure-portálon](https://portal.azure.com).

> [!NOTE]
> Ön tudomásával költségeket, ha csak az operációs rendszer Leállítás gombját használja a virtuális Gépen.  
> 
> 

Ha néhány nagy méretű elemzési kezelnek, és a Virtuálisgép-méretek mag, a memória kapacitás és a lemeztípusok (beleértve az SSD-meghajtókon) a számítási és a költségvetési igényeinek megfelelő CPU széles választéka található további CPU és/vagy a memóriát és/vagy a lemez kapacitását kell módosítania. Virtuális gépek együtt az óránkénti számítási árképzési teljes listája megtalálható a [Azure Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/) lap.

Hasonlóképpen ha csökkenti a virtuális gép feldolgozási kapacitás szükség (például: egy nagyobb munkaterhelés áthelyezte a Hadoop vagy egy Spark-fürt), le a fürt méretezheti a [Azure-portálon](https://portal.azure.com) és a beállításokat a virtuális gép példány. Íme egy képernyőkép.

![Virtuálisgép-példány beállítások](./media/vm-do-ten-things/VMScaling.PNG)

## <a name="10-install-additional-tools-on-your-virtual-machine"></a>10. A virtuális gép további eszközök telepítése
Úgy véljük, képesek a közös adatok analytics igényeinek, és számos kell mentési idő lehetőleg ne kelljen telepítse és konfigurálja a környezetek egyenként címre, és mentse azt pénz által használt fizető csak az erőforrások több eszközt csomagolását azt.

Kihasználhatja a más Azure adatok és analitikák-szolgáltatások ebben a cikkben javítása érdekében a analytics környezet csatolást. Tudjuk, hogy bizonyos esetekben az igényeinek szükség lehet további eszközeit, beleértve az egyes védett külső eszközök. A virtuális gépen telepíteni kell az új eszközök teljes rendszergazdai hozzáféréssel rendelkezik. A Python és előre nem telepített R további csomagokat is telepíthet. Python-hez is használhatja ```conda``` vagy ```pip```. Az R használhatja a ```install.packages()``` az R-konzol vagy használja az IDE, és válassza a "**csomagok** -> **csomagok telepítése...** ".

## <a name="summary"></a>Összefoglalás
Ezek közé tartoznak a csak a Microsoft Data tudományos virtuális gép által elvégezhető műveletekből. Számos dolgot további hatékony analytics környezet elérhetővé teheti.


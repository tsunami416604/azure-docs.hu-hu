---
title: Oktatóanyag az Azure Machine Learning előzetes verziójának funkcióiról – Parancssori felület | Microsoft Docs
description: Ez az oktatóanyag az Írisz osztályozásának a parancssori felületről történő elvégzéséhez szükséges lépéseket mutatja be.
services: machine-learning
author: jpe316
ms.author: jordane
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 10/15/2017
ms.openlocfilehash: 10fe861682da6c1d1ac701a565cef11f9b44cd1e
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2018
ms.locfileid: "41924830"
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>Oktatóanyag: Írisz osztályozása a parancssori felület segítségével
Az Azure Machine Learning-szolgáltatások (előzetes verzió) az adatszakértők számára az adatok előkészítéséhez, a kísérletek kidolgozásához és a modellek felhőszinten való üzembe helyezéséhez létrehozott átfogó, integrált és fejlett adatelemzési megoldások.

Ebből az oktatóanyagból elsajátíthatja, hogyan használhatja a parancssori felületi (CLI) eszközöket az Azure Machine Learning előzetes verzió funkcióiban a következőkre: 
> [!div class="checklist"]
> * Kísérletezési fiók beállítása és munkaterület létrehozása
> * Projekt létrehozása
> * Kísérlet elküldése több számítási célnak
> * Betanított modell előléptetése és regisztrálása
> * Webszolgáltatás üzembe helyezése új adatok pontozásához

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:
- Hozzáférés Azure-előfizetéshez és engedélyek erőforrások létrehozására az előfizetésben. 
  
  Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

- Az [Azure Machine Learning-szolgáltatások telepítését és elindítását ismertető rövid útmutatóban](../service/quickstart-installation.md) leírt módon telepített Azure Machine Learning Workbench alkalmazás. 

  >[!IMPORTANT]
  >Ne hozza létre az Azure Machine Learning-szolgáltatásfiókokat, mivel ezt ebben a cikkben fogja elvégezni a parancssori felületről.
 
## <a name="getting-started"></a>Első lépések
Az Azure Machine Learning parancssori felülete (CLI) lehetővé teszi a teljes körű adatelemzési munkafolyamathoz szükséges összes feladat elvégzését. A következőképpen érheti el a parancssori felületi eszközöket:

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>1. lehetőség: Az Azure ML parancssori felületének elindítása az Azure ML Workbench bejelentkezési párbeszédpaneljéről
Amikor először indítja el és jelentkezik be az Azure ML Workbenchbe, és még nincs hozzáférése kísérletezési fiókhoz, a következő képernyő jelenik meg:

![nem található fiók](media/tutorial-iris-azure-cli/no_account_found.png)

Kattintson a párbeszédpanelen a **parancssori ablak** hivatkozásra a parancssori ablak megnyitásához.

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>2. lehetőség: Az Azure ML parancssori felületének elindítása az Azure ML Workbench alkalmazásból
Ha már hozzáféréssel rendelkezik egy kísérletezési fiókhoz, sikeresen bejelentkezhet. Ezután a **Fájl** --> **Parancssor megnyitása** lehetőségre kattintva megnyithatja a parancssori ablakot.

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>3. lehetőség: Az Azure ML parancssori felületének engedélyezése egy tetszőleges parancssori ablakban
Az Azure ML parancssori felülete bármely parancssori ablakból engedélyezhető. Ehhez indítson el egy parancsablakot, és írja be a következő parancsokat:

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
A módosítás véglegesítéséhez Windows rendszeren a `SETX` parancsot használhatja. macOS rendszeren használja a `setenv` parancsot.

>[!TIP]
>A kedvenc terminálablakában a fenti környezeti változók beállításával engedélyezheti az Azure CLI-t.

## <a name="step-1-log-in-to-azure"></a>1. lépés Jelentkezzen be az Azure-ba
Az első lépés a parancssori felület megnyitása az AMLWorkbench alkalmazásból (Fájl > Parancssor megnyitása). Ez biztosítja, hogy a megfelelő Python-környezettel rendelkezik, és hogy az ML parancssori felületi parancsai elérhetőek. 

Most beállíthatja a CLI-ben a megfelelő környezetet az Azure-erőforrások eléréséhez és kezeléséhez.
 
```azure-cli
# log in
$ az login

# list all subscriptions
$ az account list -o table

# set the current subscription
$ az account set -s <subscription id or name>
```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>2. lépés Új Azure Machine Learning-kísérletezési fiók és -munkaterület létrehozása

Ebben a lépésben létrehoz egy új kísérletezési fiókot és egy új munkaterületet. A kísérletezési fiókokkal és munkaterületekkel kapcsolatos további részletekért lásd: [Azure Machine Learning-fogalmak](overview-general-concepts.md).

> [!NOTE]
> A kísérletezési fiókokhoz tárfiókra van szükség, amely a kísérletezési futtatások kimeneteit tárolja. A tárfiók nevének globálisan egyedinek kell lennie az Azure-ban, mert egy URL-cím tartozik hozzá. Ha nem ad meg egy meglévő tárfiókot, a rendszer a kísérletezési fiók nevével létrehoz egy új tárfiókot. Ügyeljen arra, hogy egyedi nevet használjon, különben a következőhöz hasonló hibaüzenet jelenik meg: _„A(z) \<tárfiók_neve> nevű tárfiók már használatban van.”_ Másik lehetőségként a `--storage` argumentummal megadhat egy meglévő tárfiókot.

```azure-cli
# create a resource group 
$ az group create --name <resource group name> --location <supported Azure region>

# create a new experimentation account with a new storage account
$ az ml account experimentation create --name <experimentation account name> --resource-group <resource group name>

# create a new experimentation account with an existing storage account
$ az ml account experimentation create --name <experimentation account name>  --resource-group <resource group name> --storage <storage account Azure Resource ID>

# create a workspace in the experimentation account
az ml workspace create --name <workspace name> --account <experimentation account name> --resource-group <resource group name>
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>2.a lépés (nem kötelező) Munkaterület megosztása egy munkatárssal
Itt megismerheti, hogyan oszthatja meg egy munkaterület elérését egy munkatárssal. A kísérletezési fiókokhoz vagy projektekhez való hozzáférés is ezekkel a lépésekkel osztható meg. Csak az Azure-erőforrásazonosító beszerzésének módját kell frissíteni.

```azure-cli
# find the workspace Azure Resource ID
$az ml workspace show --name <workspace name> --account <experimentation account name> --resource-group <resource group name>

# add Bob to this workspace as a new owner
$az role assignment create --assignee bob@contoso.com --role owner --scope <workspace Azure Resource ID>
```

> [!TIP]
> A fenti parancs `bob@contoso.com` elemének érvényes Azure AD-identitásnak kell lennie abban a könyvtárban, amelyhez a jelenlegi előfizetés tartozik.

## <a name="step-3-create-a-new-project"></a>3. lépés Új projekt létrehozása
A következő lépés egy új projekt létrehozása. Többféleképpen is elkezdhet egy új projektet.

### <a name="create-a-new-blank-project"></a>Új üres projekt létrehozása

```azure-cli
# create a new project
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path>
```

### <a name="create-a-new-project-with-a-default-project-template"></a>Új projekt létrehozása alapértelmezett projektsablonnal
Létrehozhat egy új projektet egy alapértelmezett sablonnal.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>Felhőbeli Git-adattárhoz társított új projekt létrehozása
Létrehozhat egy VSTS (Visual Studio Team Services) Git-adattárhoz társított új projektet. A rendszer minden kísérlet elküldésekor a távoli Git-adattárba menti a teljes projektmappáról készült pillanatfelvételt. További részletekért tekintse meg a [Git-adattár és az Azure Machine Learning Workbench-projekt együttes használatát ismertető](using-git-ml-project.md) cikket.

> [!NOTE]
> Az Azure Machine Learning csak a VSTS-ben létrehozott üres Git-adattárakat támogatja.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --repo <VSTS repo URL>
```
> [!TIP]
> Ha „Az adattár URL-címe valószínűleg érvénytelen vagy a felhasználónak nincs hozzáférése” hibaüzenet jelenik meg, létrehozhat egy biztonsági tokent a VSTS-ben (a _Security_ (Biztonság), _Add personal access tokens_ (Személyes hozzáférési tokenek hozzáadása) menüben), és a `--vststoken` argumentumot használhatja a projekt létrehozásakor. 

### <a name="sample_create"></a>Új projekt létrehozása mintából
Ebben a példában egy új projektet hoz létre egy mintaprojektet használva sablonként.

```azure-cli
# List the project samples, find the Classifying Iris sample
$ az ml project sample list

# Create a new project from the sample
az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```
A projekt létrehozása után a `cd` paranccsal lépjen a projektkönyvtárba.

## <a name="step-4-run-the-training-experiment"></a>4. lépés A tanítási kísérlet futtatása 
A következő lépések azt feltételezik, hogy rendelkezik az Írisz mintát tartalmazó projekttel (tekintse meg az [új projekt online mintából való létrehozását ismertető](#sample_create) cikket).

### <a name="prepare-your-environment"></a>A környezet előkészítése 
Az Írisz mintához telepítenie kell a matplotlib kódtárat.

```azure-cli
$ pip install matplotlib
```

###  <a name="submit-the-experiment"></a>A kísérlet elküldése

```azure-cli
# Execute the file
$ az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>A kísérlet iterálása csökkenő regularizációs arányokkal
Némi kreativitással egyszerűen lehet olyan Python-szkriptet összeállítani, amely különböző regularizációs arányú kísérleteket küld el. (Előfordulhat, hogy szerkesztenie kell a fájlt, hogy a megfelelő projektútvonalra mutasson.)

```azure-cli
$ python run.py
```

## <a name="step-5-view-run-history"></a>5. lépés Futtatási előzmények megtekintése
A következő parancs megjeleníti az összes korábbi futtatást. 

```azure-cli
$ az ml history list -o table
```
Az előző parancs futtatása megjeleníti a projekthez tartozó összes futtatás listáját. Láthatjuk, hogy a pontosság és a regularizációs arány metrikái is fel vannak sorolva. Így könnyen azonosítható a legjobb futtatás a listából.

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>5.a lépés A futtatások által létrehozott melléklet megtekintése 
Egy adott futtatáshoz társított melléklet megtekintéséhez a futtatási előzmények info parancsát használhatja. Az előző listában keresse meg egy adott futtatás futtatási azonosítóját.

```azure-cli
$ az ml history info --run <run id> --artifact driver_log
```

A futtatások összetevőinek letöltéséhez a következő parancsot használhatja:

```azure-cli
# Stream a given attachment 
$ az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>6. lépés Futtatás összetevőinek előléptetése 
Az egyik futtatás jobb AUC-vel rendelkezik, ezért ezt kell használni az éles környezetben üzembe helyezni kívánt pontozási webszolgáltatás létrehozásához. Ehhez először elő kell léptetnie az összetevőket egy eszközbe.

```azure-cli
$ az ml history promote --run <run id> --artifact-path outputs/model.pkl --name model.pkl
```

Ezzel létrejön egy `assets` mappa a projektkönyvtárban egy `model.pkl.link` fájllal. Ezzel a hivatkozásfájllal lehet előléptetett eszközre hivatkozni.

## <a name="step-7-download-the-files-to-be-operationalized"></a>7. lépés Az üzembe helyezendő fájlok letöltése
Töltse le az előléptetett modellt, hogy annak segítségével előrejelzési webszolgáltatást hozzon létre. 

```azure-cli
$ az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-set-up-your-model-management-environment"></a>8. lépés A modell felügyeleti környezetének beállítása 
Hozzon létre egy környezetet webszolgáltatások üzembe helyezéséhez. A webszolgáltatást a Docker használatával futtathatja a helyi számítógépen, vagy üzembe helyezheti egy ACS-fürtön nagy léptékű műveletekhez. 

```azure-cli
# Create new local operationalization environment
$ az ml env setup -l <supported Azure region> -n <env name>
# Once setup is complete, set your environment for current context
$ az ml env set -g <resource group name> -n <env name>
```

## <a name="step-9-create-a-model-management-account"></a>9. lépés Modellkezelési fiók létrehozása 
Egy modellkezelési fiókra van szükség éles környezetben a modellek üzembe helyezéséhez és nyomon követéséhez. 

```azure-cli
$ az ml account modelmanagement create -n <model management account name> -g <resource group name> -l <supported Azure region>
```

## <a name="step-10-create-a-web-service"></a>10. lépés Webszolgáltatás létrehozása
Hozzon létre egy olyan webszolgáltatást, amely az üzembe helyezett modell használatával előrejelzést nyújt. 

```azure-cli
$ az ml service create realtime -m asset_download/model.pkl -f score_iris.py -r python –n <web service name>
```

## <a name="step-11-run-the-web-service"></a>11. lépés A webszolgáltatás futtatása
Az előző lépés eredményeként kapott webszolgáltatás-azonosítóval hívja meg és tesztelje a webszolgáltatást. 

```azure-cli
# Get web service usage information 
$ az ml service usage realtime -i <web service id>

# Call the web service with the run command:
$ az ml service run realtime -i <web service id> -d <input data>
```

## <a name="step-12-deleting-all-the-resources"></a>12. lépés Az összes erőforrás törlése 
Az oktatóanyag befejezéséhez törölje az összes létrehozott erőforrást, ha nem szeretne tovább dolgozni rajtuk. 

Ehhez törölje az erőforrásokat tartalmazó erőforráscsoportot. 

```azure-cli
az group delete --name <resource group name>
```

## <a name="next-steps"></a>További lépések
Ebből az oktatóanyagból megtanulta, hogyan használhatja az Azure Machine Learninget a következőkre: 
> [!div class="checklist"]
> * Kísérletezési fiók beállítása, munkaterület létrehozása
> * Projektek létrehozása
> * Kísérletek elküldése több számítási célnak
> * Betanított modell előléptetése és regisztrálása
> * Modellkezelési fiók létrehozása a modellkezeléshez
> * Környezet létrehozása webszolgáltatások üzembe helyezéséhez
> * Webszolgáltatás üzembe helyezése és pontozás új adatokkal

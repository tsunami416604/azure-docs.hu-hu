---
title: "Útmutató a cikk az Azure Machine Learning előzetes verziójú funkciók - parancssori felülettel |} Microsoft Docs"
description: "Az oktatóanyag lépésein végighaladva a lépéseket egy Iris besorolás-végpont a parancssori felület végrehajtásához szükséges."
services: machine-learning
author: ahgyger
ms.author: ahgyger, ritbhat
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 453c774c97b77dd7829a50fa5e5668d06f817a1d
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2017
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>Oktatóanyag: Zárolásának Iris a parancssori felület használatával
Az Azure Machine Learning-szolgáltatások (előzetes verzió) az adatszakértők számára az adatok előkészítéséhez, a kísérletek kidolgozásához és a modellek felhőszinten való üzembe helyezéséhez létrehozott átfogó, integrált és fejlett adatelemzési megoldás.

Ebben az oktatóanyagban elsajátíthatja, az Azure Machine Learning előzetes verziójú funkciók a parancssori felület (CLI) eszközzel: 
> [!div class="checklist"]
> * Kísérletezhet fiókot és egy munkaterület létrehozása
> * A projekt létrehozása
> * Küldje el a kísérletre, ha több számítási cél
> * Lépteti elő, és regisztrálja a betanított modell
> * Új adatok pontozása céljából egy webszolgáltatás-bővítmény telepítése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés és az adott előfizetés az erőforrások létrehozásához szükséges engedélyek hozzáférés szükséges. 
- Következő Azure Machine Learing munkaterület alkalmazást telepíteni kell a [telepítése és gyors üzembe helyezés létrehozása](quickstart-installation.md). 

  >[!NOTE]
  >Csak akkor kell telepíteni az Azure Machine Learning-munkaterület helyileg. Csak a jogosult telepítése Azure Machine Learning munkaterületet, a fiók létrehozása óta szakaszok kövesse és lépéseket végzi a cikkben parancssor új projekt létrehozása kell.
 
## <a name="getting-started"></a>Bevezetés
Az Azure Machine Learning parancssori felület (CLI) lehetővé teszi egy végpontok közötti adatelemezési munkafolyamatot a szükséges műveletek elvégzéséhez. A parancssori eszközök az alábbi módon érheti el:

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>1. lehetőség. Indítsa el az Azure ML CLI Azure ML munkaterület bejelentkezést párbeszédpanelről
Azure ML munkaterület és a bejelentkezéshez először indítása során, és egy olyan kísérletezhet fiókot már nem rendelkezik, a következő képernyő jelenik meg:

![a fiók nem található](media/tutorial-iris-azure-cli/no_account_found.png)

Kattintson a **parancssori ablakban** hivatkozás a párbeszédpanelen, a parancssori ablak megnyitása.

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>2. lehetőség. Indítsa el az Azure ML CLI Azure ML munkaterület alkalmazásból
Ha már rendelkezik egy olyan kísérletezhet fiókot, bejelentkezhet sikeresen megtörtént. Azt követően meg tudja nyitni a parancssori ablakban kattintva **fájl** --> **nyissa meg a fájl törlése Rákérdezés** menü.

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>3. lehetőség. Azure ML CLI egy tetszőleges parancssori ablakban engedélyezése
Azure ML CLI bármely parancssori ablakban is engedélyezheti. Egyszerűen indítsa el a parancsablakot, és adja meg a következő parancsokat:

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
A módosítás végleges, segítségével `SETX` Windows rendszeren. MacOS, használhatja `setenv`.

>[!TIP]
>Az Azure parancssori felület a fenti környezeti változók beállításával engedélyezheti a kedvenc terminálablakot.

## <a name="step-1-log-in-to-azure"></a>1. lépés Jelentkezzen be az Azure-ba
Az első lépés az, hogy nyissa meg a parancssori felület a AMLWorkbench alkalmazásból (Fájl > Nyissa meg a parancssorba). Ezzel biztosítja, használjuk a megfelelő python-környezetben, és vezetünk a gépi tanulás parancssori felület parancsait érhető el. 

Igazolnia kell a megfelelő környezet beállítása a parancssori felület férhessen hozzá és felügyelhesse az Azure-erőforrások a majd.
 
```azure-cli
# log in
$ az login

# list all subscriptions
$ az account list -o table

# set the current subscription
$ az account set -s <subscription id or name>
```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>2. lépés Hozzon létre egy új Azure Machine Learning kísérletezhet fiók és munkaterület
Először hozzon létre egy új kísérletezhet fiókot és egy új munkaterületet. Lásd: [Azure Machine Learning fogalmak](overview-general-concepts.md) kísérletezhet fiókok és munkaterületekkel kapcsolatos további részletekért.

> [!NOTE]
> Kísérletezhet fiókok szükséges egy tárfiókot, amely a kísérlet futtatása kimenetének tárolására szolgál. A tárfiók neve nem lehet globálisan egyedi az Azure-ban, mert nincs társítva egy url. Meglévő tárfiók nem adja meg, ha a kísérleti fióknév segítségével hozzon létre egy új tárfiókot. Ügyeljen arra, hogy az egyedi név, vagy hiba lép fel, mint _"nevű tárfiók \<storage_account_name > már használatban van."_ Másik lehetőségként használhatja a `--storage` automatikusan megadhatja azokat a meglévő tárfiók argumentum.

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

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>Lépés (opcionális) 2.a munkaterület megosztása a munkatárs tagja
Itt azt megismerkedhet a munkaterületek elérését megosztása olyan munkatárs. Fájlmegosztás elérését kísérletezhet fiókkal vagy egy projekt lépéseit változatlan marad. Csak a vonható az Azure erőforrás-azonosító kell frissíteni.

```azure-cli
# find the workspace Azure Resource ID
$az ml workspace show --name <workspace name> --account <experimentation account name> --resource-group <resource group name>

# add Bob to this workspace as a new owner
$az role assignment create --assignee bob@contoso.com --role owner --scope <workspace Azure Resource ID>
```

> [!TIP]
> `bob@contoso.com`a fenti parancsban egy érvényesnek kell lennie az Azure AD identity a könyvtárban, ahol a jelenlegi előfizetés tartozik.

## <a name="step-3-create-a-new-project"></a>3. lépés Új projekt létrehozása
A következő lépés, hogy hozzon létre egy új projektet. Ismerkedés az új projekt számos módon lehet.

### <a name="create-a-new-blank-project"></a>Üres új projekt létrehozása

```azure-cli
# create a new project
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path>
```

### <a name="create-a-new-project-with-a-default-project-template"></a>Hozzon létre egy új projektet egy alapértelmezett projekt sablon
Létrehozhat egy új projektet az alapértelmezett sablont.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>Git-tárház felhő társított új projekt létrehozása
Vannak, létrehozható egy új projektet egy VSTS (a Visual Studio Team Service) Git-tárház társított. Minden alkalommal, amikor a kísérlet nyújtják, a teljes projektmappa pillanatképe elkötelezte magát a távoli Git-tárház. Lásd: [használatával Git-tárház Azure Machine Learning-munkaterület projekttel](using-git-ml-project.md) további részleteket.

> [!NOTE]
> Az Azure Machine Learning csak üres Git repók VSTS létrehozott támogatja.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --repo <VSTS repo URL>
```
> [!TIP]
> Ha "tárház URL-címe valószínűleg érvénytelen vagy a felhasználó nem fér hozzá" hibaüzenetet kap, létrehozhat egy biztonsági jogkivonatot VSTS (alatt _biztonsági_, _adja hozzá a személyes hozzáférési jogkivonatok_ menü), és használja a `--vststoken`argumentum a projekt létrehozásakor. 

### <a name="sample_create"></a>A mintából új projekt létrehozása
Ebben a példában azt egy minta-projekt használata sablonként új projekt létrehozása.

```azure-cli
# List the project samples, find the Classifying Iris sample
$ az ml project sample list

# Create a new project from the sample
az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```
Ha a projekt létrehozása a `cd` parancs használatával adja meg a projekt könyvtárában.

## <a name="step-4-run-the-training-experiment"></a>Futtassa a tanítási kísérletet 4. lépés 
Az alábbi lépések azt feltételezik, hogy rendelkezik-e a Iris minta a projekthez (lásd: [hozzon létre egy új projektet egy online mintából](#sample_create)).

### <a name="prepare-your-environment"></a>A környezet előkészítése 
A Iris minta matplotlib telepítése szükséges.

```azure-cli
$ pip install matplotlib
```

###  <a name="submit-the-experiment"></a>Küldje el a kísérlet

```azure-cli
# Execute the file
$ az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>A csökkenő exportügyletek díjszabás kísérletbe többször
Az egyes kreativitást használata egyszerű hozzáfoghat a Python-parancsfájl, amely elküldi a kísérletek különböző exportügyletek aránnyal. (Előfordulhat, hogy a megfelelő projekt elérési útja mutasson a fájl szerkesztéséhez.)

```azure-cli
$ python run.py
```

## <a name="step-5-view-run-history"></a>5. lépés Futtatási előzmények megtekintése
Az előző fut hajtotta végre a következő parancs sorolja fel. 

```azure-cli
$ az ml history list -o table
```
A fenti parancs futtatása a projekthez tartozó összes futtatása listája jelenik meg. Láthatja, hogy a pontosság és exportügyletek arány metrikák túl szerepel-e. Ez ellenőrizze akkor könnyedén azonosíthatja legjobb futni a listából.

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>Lépés hozta létre a megadott kísérletek melléklet 5.a megtekintése 
A megadott futtató társított melléklet megtekintéséhez azt paranccsal a adatait a futtatási előzményei. Egy futtatási azonosítója egy adott futtató a fenti listában található.

```azure-cli
$ az ml history info --run <run id> --artifact driver_log
```

Az összetevők letöltését futtató, alábbi parancsot használhatja:

```azure-cli
# Stream a given attachment 
$ az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>6. lépés Egy futtató összetevők előléptetése 
Az fut, a Microsoft által készített egyike jobb AUC, ezért szeretnénk éles központi telepítése egy pontozási webszolgáltatás létrehozására használható. Ehhez először kell lépteti elő az összetevők egy adategységbe.

```azure-cli
$ az ml history promote --run <run id> --artifact-path outputs/model.pkl --name model.pkl
```

Ezzel létrehoz egy `assets` a projektkönyvtárban mappa egy `model.pkl.link` fájlt. A hivatkozásfájl előléptetett eszköz hivatkozni.

## <a name="step-7-download-the-files-to-be-operationalized"></a>7. lépés Töltse le a fájlokat kell operationalized
Most kell az előléptetett modell letöltése, tehát használhatjuk ezeket az előrejelzés webszolgáltatás létrehozásához. 

```azure-cli
$ az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-setup-your-model-management-environment"></a>8. lépés. A modell felügyeleti környezet beállítása 
Webszolgáltatások üzembe helyezése környezet létrehozása azt. A webszolgáltatás Docker használatával a helyi számítógépen futtatható azt. Vagy telepíteni kell egy ACS-fürthöz, jelentősen méretezhető műveletekhez. 

```azure-cli
# Create new local operationalization environment
$ az ml env setup -l <supported Azure region> -n <env name>
# Once setup is complete, set your environment for current context
$ az ml env set -g <resource group name> -n <env name>
```

## <a name="step-9-create-a-model-management-account"></a>9. lépés. A modell felügyeleti fiók létrehozása 
A modell felügyeleti fiók szükséges telepíteni, és nyomon követheti a modellek éles környezetben. 

```azure-cli
$ az ml account modelmanagement create -n <model management account name> -g <resource group name> -l <supported Azure region>
```

## <a name="step-10-create-a-web-service"></a>10. lépés. Webszolgáltatás létrehozása
Majd létrehozhatunk egy webszolgáltatás, amelyet a modellel helyeztünk üzembe előrejelzéshez adja vissza. 

```azure-cli
$ az ml service create realtime -m asset_download/model.pkl -f score.py -r python –n <web service name>
```

## <a name="step-10-run-the-web-service"></a>10. lépés. A webszolgáltatás futtatása
A webes szolgáltatás azonosítója az előző lépésben kimenetében, azt segítségével a webszolgáltatás hívására és tesztelik azt. 

```azure-cli
# Get web service usage infomration
$ az ml service usage realtime -i <web service id>

# Call the web service with the run command:
$ az ml service run realtime -i <web service id> -d <input data>
```

## <a name="deleting-all-the-resources"></a>Az erőforrások törlése 
Ez az oktatóanyag befejezéseként létrehoztunk, összes erőforrást törli, hacsak nem szeretné tovább dolgozik a háttérben! 

Ehhez az szükséges, azt egyszerűen törölje a csoportot az erőforrások rendelkezik. 

```azure-cli
az group delete --name <resource group name>
```

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtanulhatta az Azure Machine Learning előzetes verziójú funkciók a használatára 
> [!div class="checklist"]
> * Kísérletezhet fiókot, munkaterület létrehozása
> * Projektek létrehozása
> * Küldje el több cél számára számítási kísérletek
> * Lépteti elő, és regisztrálja a betanított modell
> * A modell felügyeleti modell felügyeleti fiók létrehozása
> * Egy webszolgáltatás-bővítmény telepítése környezet létrehozása
> * Telepítsen egy webszolgáltatás és az új adatokat pontszám
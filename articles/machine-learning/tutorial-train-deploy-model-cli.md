---
title: Modellek betanítása és üzembe helyezése a CLI-ből
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja az Azure CLI gépi tanulási bővítményét egy modell betanításához, regisztrálásához és üzembe helyezéséhez a parancssorból.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 1cafc311c842cd5bc17fefe34eacbdfc99b7147a
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617722"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Oktatóanyag: A CLI-ből modellbevonattal és üzembe helyezéssel
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben az oktatóanyagban az Azure CLI gépi tanulási bővítményt használja egy modell betanításához, regisztrálásához és üzembe helyezéséhez.

Az oktatóanyag Python-betanítási parancsfájljai [scikit-learn](https://scikit-learn.org/) használatával tanítják be az alapmodellt. Ez az oktatóanyag nem a parancsfájlok vagy a modell, hanem a folyamat a CLI az Azure Machine Learning használata.

További információ a következő műveletek elmisztika a műveletről:

> [!div class="checklist"]
> * A gépi tanulási bővítmény telepítése
> * Azure Machine Learning-munkaterület létrehozása
> * A modell betanításához használt számítási erőforrás létrehozása
> * A modell betanításához használt adatkészlet meghatározása és regisztrálása
> * Betanítási sorozat indítása
> * Modell regisztrálása és letöltése
> * A modell telepítése webszolgáltatásként
> * Pontszám adatok a webszolgáltatás

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki még ma [az Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

* A **helyi környezetből**származó CLI-parancsok használatához a helyi környezetből kell használnia a [CLI parancsot.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

    Ha az [Azure Cloud Shell,](https://azure.microsoft.com//features/cloud-shell/)a CLI érhető el a böngészőn keresztül, és él a felhőben.

## <a name="download-the-example-project"></a>A példaprojekt letöltése

Ehhez az oktatóanyaghoz töltse le a [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) projektet. A könyvtárban `examples/cli-train-deploy` lévő fájlokat az oktatóanyag lépései használják.

A fájlok helyi másolatának beletöltéséhez [töltsön le egy .zip archívumot,](https://github.com/microsoft/MLOps/archive/master.zip)vagy használja a következő Git parancsot a tárház klónozásához:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Betanítási fájlok

A `examples/cli-train-deploy` projekt könyvtára a következő fájlokat tartalmazza, amelyek a modell betanításakor használatosak:

* `.azureml\mnist.runconfig`: Egy __futtatott konfigurációs__ fájl. Ez a fájl határozza meg a modell betanításához szükséges futásidejű környezetet. Ebben a példában is csatlakoztatja a modell betanításához használt adatokat a betanítási környezetbe.
* `scripts\train.py`: A betanítási parancsfájl. Ez a fájl beprogramozja a modellt.
* `scripts\utils.py`: A betanítási parancsfájl által használt segítő fájl.
* `.azureml\conda_dependencies.yml`: Meghatározza a betanítási parancsfájl futtatásához szükséges szoftverfüggőségeket.
* `dataset.json`: Az adatkészlet definíciója. Az MNIST-adatkészlet regisztrálására szolgál az Azure Machine Learning-munkaterületen.

### <a name="deployment-files"></a>Telepítési fájlok

A tárház a következő fájlokat tartalmazza, amelyek a betanított modell webszolgáltatásként való üzembe helyezéséhez használatosak:

* `aciDeploymentConfig.yml`: __Központi telepítési konfigurációs__ fájl. Ez a fájl határozza meg a modellhez szükséges üzemeltetési környezetet.
* `inferenceConfig.json`: __Következtetéskonfigurációs__ fájl. Ez a fájl határozza meg a szoftver környezet által használt szolgáltatás pontszám adatokat a modell.
* `score.py`: Egy python-parancsfájl, amely elfogadja a bejövő adatokat, a modell használatával pontozza azt, majd választ ad vissza.
* `scoring-env.yml`: A modell és `score.py` a parancsfájl futtatásához szükséges conda-függőségek.
* `testdata.json`: Az üzembe helyezett webszolgáltatás tesztelésére használható adatfájl.

## <a name="connect-to-your-azure-subscription"></a>Csatlakozás az Azure-előfizetéshez

Számos módon hitelesítheti magát az Azure-előfizetésa a CLI-ből. A legalapvetőbb az, hogy interaktívan hitelesíti a böngésző segítségével. Az interaktív hitelesítéshez nyisson meg egy parancssort vagy terminált, és használja a következő parancsot:

```azurecli-interactive
az login
```

Ha a CLI megnyithatja az alapértelmezett böngészőt, akkor megnyitja, és betölti a bejelentkezési oldalt. Ellenkező esetben meg kell nyitnia egy böngészőt, és követnie kell a parancssorban található utasításokat. Az utasítások az [https://aka.ms/devicelogin](https://aka.ms/devicelogin) engedélyezési kód böngészését és megadását foglalják magukban.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

## <a name="install-the-machine-learning-extension"></a>A gépi tanulási bővítmény telepítése

A gépi tanulási bővítmény telepítéséhez használja a következő parancsot:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Ha arról kap üzenetet, hogy a bővítmény már telepítve van, a következő paranccsal frissíthet a legújabb verzióra:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport az Azure platform on-k on-k ban található erőforrások alapvető tárolója. Az Azure Machine Learning használatával végzett munka során az erőforráscsoport tartalmazza az Azure Machine Learning-munkaterületet. A munkaterület által használt egyéb Azure-szolgáltatásokat is tartalmaz. Ha például a modellbe egy felhőalapú számítási erőforrás használatával tanítja be, az erőforrás az erőforráscsoportban jön létre.

__Új erőforráscsoport létrehozásához__használja a következő parancsot. Cserélje `<resource-group-name>` le az erőforráscsoporthoz használandó névvel. Cserélje `<location>` le az erőforráscsoporthoz használandó Azure-régióra:

> [!TIP]
> Válassza ki azt a régiót, ahol az Azure Machine Learning elérhető. További információt a [Régiónként elérhető Termékek](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)című témakörben talál.

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

A parancs válasza hasonló a következő JSON-hoz:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Az erőforráscsoportokkal való munkáról az [az csoport című](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)témakörben talál további információt.

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Új munkaterület létrehozásához használja a következő parancsot. Cserélje `<workspace-name>` le a munkaterülethez használni kívánt névvel. Csere `<resource-group-name>` az erőforráscsoport nevére:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

A parancs kimenete hasonló a következő JSON-hoz:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>Helyi projekt csatlakoztatása munkaterülethez

Terminálon vagy parancssorból használja a következő parancsokat, amelyek könyvtárakat váltanak a `cli-train-deploy` könyvtárra, majd csatlakozzon a munkaterülethez:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

A parancs kimenete hasonló a következő JSON-hoz:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Ez a `.azureml/config.json` parancs létrehoz egy fájlt, amely a munkaterülethez való csatlakozáshoz szükséges információkat tartalmazza. Az oktatóanyagban használt többi parancs ezt a `az ml` fájlt fogja használni, így nem kell hozzáadnia a munkaterületet és az erőforráscsoportot az összes parancshoz.

## <a name="create-the-compute-target-for-training"></a>A számítási cél létrehozása a képzéshez

Ez a példa egy Azure Machine Learning Compute-fürt öthasználatával tanítja be a modellt. Új számítási fürt létrehozásához használja a következő parancsot:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

A parancs kimenete hasonló a következő JSON-hoz:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Ez a parancs létrehoz egy `cpu-cluster`új számítási cél nevű, legfeljebb négy csomópont. A kiválasztott virtuális gép mérete egy virtuális gép egy GPU-erőforrást biztosít. A virtuális gép méretéről további információt a [Virtuálisgép-típusok és -méretek] című témakörben talál.

> [!IMPORTANT]
> A számítási cél neve`cpu-cluster` (ebben az esetben) fontos; a következő szakaszban `.azureml/mnist.runconfig` használt fájl hivatkozik rá.

## <a name="define-the-dataset"></a>Az adatkészlet meghatározása

Modell betanításához a betanítási adatokat egy adatkészlet használatával adhat meg. Ha a CLI-ből szeretne adatkészletet létrehozni, meg kell adnia egy adatkészlet-definíciós fájlt. A `dataset.json` tárházban megadott fájl új adatkészletet hoz létre az MNIST-adatok használatával. Az általa létrehozott `mnist-dataset`adatkészlet neve .

Az adatkészlet `dataset.json` fájllal történő regisztrálásához használja a következő parancsot:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

A parancs kimenete hasonló a következő JSON-hoz:

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```

> [!IMPORTANT]
> Másolja a bejegyzés `id` értékét a következő szakaszban használt módon.

Az adatkészletek átfogóbb sablonjának megtekintéséhez használja a következő parancsot:

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Hivatkozás az adatkészletre

Ahhoz, hogy az adatkészlet elérhető legyen a betanítási környezetben, hivatkoznia kell rá a runconfig fájlból. A `.azureml/mnist.runconfig` fájl a következő YAML-bejegyzéseket tartalmazza:

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

Módosítsa a bejegyzés `id` értékét úgy, hogy megfeleljen az adatkészlet regisztrálásakor visszaadott értéknek. Ez az érték az adatok betöltésére szolgál a számítási cél betanítása során.

Ez a YAML a következő műveleteket eredményezi a képzés során:

* Az adatkészletet (az adatkészlet azonosítója alapján) csatlakoztatja a betanítási környezetben, és `mnist` tárolja a csatlakoztatási pont elérési útját a környezeti változóban.
* Az argumentum használatával átadja az adatok (csatlakoztatási pont) `--data-folder` helyét a betanítási környezeten belül a parancsfájlnak.

A runconfig fájl a betanítási futtatás által használt környezet konfigurálásához használt információkat is tartalmaz. Ha megvizsgálja ezt a fájlt, látni fogja, `cpu-compute` hogy hivatkozik a korábban létrehozott számítási cél. Azt is felsorolja a csomópontok száma, amelyeket a betanítás (`"nodeCount": "4"`), és tartalmaz egy `"condaDependencies"` szakaszt, amely felsorolja a python-csomagok futtatásához szükséges a betanítási parancsfájl.

> [!TIP]
> Bár lehetőség van manuálisan létrehozni egy runconfig fájlt, a `generate-runconfig.py` példában szereplő tárat a tárházban található fájl használatával hozták létre. Ez a fájl hivatkozást kap a regisztrált adatkészletre, létrehoz egy futtatást programozott módon, majd megőrzi azt fájlba.

A konfigurációs fájlok futtatásáról a [Számítási célok beállítása és használata a modellbetanításhoz](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)című témakörben talál további információt. A teljes JSON-referencia a [runconfigschema.json fájlban.](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)

## <a name="submit-the-training-run"></a>A betanítási sorozat beküldése

A `cpu-cluster` számítási cél betanítási futtatásának elindításához használja a következő parancsot:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

Ez a parancs a kísérlet`myexperiment`( . A kísérlet a futtatással kapcsolatos információkat a munkaterületen tárolja.

A `-c mnist` paraméter adja `.azureml/mnist.runconfig` meg a fájlt.

A `-t` paraméter egy JSON-fájlban tárolja a futtatásra mutató hivatkozást, és a következő lépésekben a modell regisztrálásához és letöltéséhez lesz használva.

A betanítási folyamatok futtatásakor a távoli számítási erőforrás betanítási munkamenetéből származó információkat továbbítja. Az információ egy része hasonló a következő szöveghez:

```output
Predict the test set
Accuracy is 0.9185
```

Ez a szöveg a betanítási parancsfájlból naplózódik, és megjeleníti a modell pontosságát. Más modellek eltérő teljesítménymutatókkal rendelkeznek.

Ha megvizsgálja a betanítási parancsfájlt, észre fogja venni, hogy az `outputs/sklearn_mnist_model.pkl`alfa-értéket is használja, amikor tárolja a betanított modellt.

A modell a `./outputs` számítási cél azon könyvtárába lett mentve, ahol be volt tanítva. Ebben az esetben az Azure Machine Learning Compute-példány az Azure-felhőben. A betanítási folyamat automatikusan `./outputs` feltölti a könyvtár tartalmát a számítási célból, ahol a betanítás történik az Azure Machine Learning-munkaterületre. A kísérlet részeként tárolják (ebben`myexperiment` a példában).

## <a name="register-the-model"></a>Regisztrálja a modellt

Ha a modellt közvetlenül a kísérletben tárolt verzióból szeretné regisztrálni, használja a következő parancsot:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Ez a parancs `outputs/sklearn_mnist_model.pkl` a betanítási futtatás által létrehozott `mymodel`fájlt új modellregisztrációként regisztrálja. A `--assets-path` kísérlet egy görbére hivatkozik. Ebben az esetben a kísérlet és a `runoutput.json` futtatási információk a betanítási parancs által létrehozott fájlból töltődnek be. A `-t registeredmodel.json` létrehoz egy JSON-fájlt, amely a parancs által létrehozott új regisztrált modellre hivatkozik, és a regisztrált modellekkel használható más CLI-parancsok használják.

A parancs kimenete hasonló a következő JSON-hoz:

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>Modellverziószámozás

Jegyezze fel a modellhez visszaadott verziószámot. A verzió minden alkalommal növekszik, amikor új modellt regisztrál ezzel a névvel. Például letöltheti a modellt, és helyi fájlból regisztrálhatja a következő parancsokkal:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

Az első parancs letölti a regisztrált modellt az aktuális könyvtárba. A fájlnév `sklearn_mnist_model.pkl`a , amelyre a modell regisztrálásakor hivatkozott fájl hivatkozik. A második parancs regisztrálja`-p "sklearn_mnist_model.pkl"`a helyi modellt ( )`mymodel`az előző regisztrációval megegyező névvel ( ). Ezúttal a JSON-adatok 2-ként listázza a verziót.

## <a name="deploy-the-model"></a>A modell üzembe helyezése

Modell üzembe helyezéséhez használja a következő parancsot:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> Figyelmeztetést kaphat a "Nem sikerült ellenőrizni a LocalWebservice létezését" vagy "Nem sikerült létrehozni a Docker-ügyfelet". Ezt nyugodtan figyelmen kívül hagyhatja, mivel nem helyi webszolgáltatást telepít.

Ez a parancs egy `myservice`új, a korábban regisztrált modell 1-es verzióját használó szolgáltatást telepít.

A `inferenceConfig.yml` fájl amodell következtetésként való használatával kapcsolatos információkat tartalmaz. Például hivatkozik a bejegyzés`score.py`parancsfájl ( ) és a szoftver függőségek.

A fájl szerkezetéről a [Következtetés konfigurációs sémában](reference-azure-machine-learning-cli.md#inference-configuration-schema)talál további információt. A bejegyzési parancsfájlokkal kapcsolatos további információkért lásd: Modellek telepítése az Azure Machine Learning használatával című [témakörben.](how-to-deploy-and-where.md#prepare-to-deploy)

A `aciDeploymentConfig.yml` leírás a szolgáltatás üzemeltetéséhez használt központi telepítési környezet. A központi telepítési konfiguráció a központi telepítéshez használt számítási típusra jellemző. Ebben az esetben egy Azure Container-példány t használ. További információt a [Központi telepítés konfigurációs sémája című témakörben talál.](reference-azure-machine-learning-cli.md#deployment-configuration-schema)

A központi telepítési folyamat befejezése néhány percet vesz igénybe.

> [!TIP]
> Ebben a példában az Azure Container instances használatos. Az ACI-ba történő központi telepítések automatikusan létrehozzák a szükséges ACI-erőforrást. Ha úgy volt, hogy ehelyett az Azure Kubernetes szolgáltatás üzembe helyezése, hozzon létre `az ml model deploy` egy AKS-fürt et előre, és adja meg a parancs részeként. Az AKS-re való üzembe helyezés például [a Modell üzembe helyezése egy Azure Kubernetes-szolgáltatásfürtre című témakört.](how-to-deploy-azure-kubernetes-service.md)

Néhány perc elteltével a következő JSON-hoz hasonló információkat ad vissza:

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>A pontozási URI

A `scoringUri` központi telepítésből visszaadott a REST-végpont egy webszolgáltatásként telepített modell. Ezt az URI-t a következő paranccsal is beszerezheti:

```azurecli-interactive
az ml service show -n myservice
```

Ez a parancs ugyanazt a JSON-dokumentumot adja vissza, beleértve a `scoringUri`.

A REST-végpont segítségével adatokat küldhet a szolgáltatásnak. Az adatokat küldő ügyfélalkalmazás létrehozásáról a [Webszolgáltatásként telepített Azure Machine Learning-modell felhasználása](how-to-consume-web-service.md) című témakörben talál további információt.

### <a name="send-data-to-the-service"></a>Adatok küldése a szolgáltatásnak

Bár létrehozhat egy ügyfélalkalmazást a végpont hívásához, a gépi tanulási CLI egy olyan segédprogramot biztosít, amely tesztügyfélként működhet. A következő paranccsal adatokat küldhet a `testdata.json` fájlban a szolgáltatásnak:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Ha a PowerShellt használja, használja inkább a következő parancsot:
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

A parancs válasza hasonló `[ 3 ]`a hoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

> [!IMPORTANT]
> A létrehozott erőforrások előfeltételként is használhatók más Azure Machine Learning-oktatóanyagokban vagy -útmutatókban.

### <a name="delete-deployed-service"></a>Telepített szolgáltatás törlése

Ha továbbra is használni szeretné az Azure Machine Learning-munkaterületet, de a költségek csökkentése érdekében meg szeretne szabadulni az üzembe helyezett szolgáltatástól, használja a következő parancsot:

```azurecli-interactive
az ml service delete -n myservice
```

Ez a parancs egy JSON-dokumentumot ad vissza, amely a törölt szolgáltatás nevét tartalmazza. A szolgáltatás törlése több percig is eltarthat.

### <a name="delete-the-training-compute"></a>A betanítási számítás törlése

Ha továbbra is használni szeretné az Azure Machine Learning-munkaterületet, `cpu-cluster` de meg szeretne szabadulni a betanításhoz létrehozott számítási céltól, használja a következő parancsot:

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

Ez a parancs egy JSON-dokumentumot ad vissza, amely a törölt számítási cél azonosítóját tartalmazza. A számítási cél törlése több percig is eltarthat.

### <a name="delete-everything"></a>Mindent töröljön

Ha nem tervezi a létrehozott erőforrások használatát, törölje őket, hogy ne kelljen további költségeket fizetnie.

Az erőforráscsoport és a dokumentumban létrehozott összes Azure-erőforrás törléséhez használja a következő parancsot. Cserélje `<resource-group-name>` le a korábban létrehozott erőforráscsoport nevére:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>További lépések

Ebben az Azure Machine Learning-oktatóanyagban a gépi tanulási CLI-t használta a következő feladatokhoz:

> [!div class="checklist"]
> * A gépi tanulási bővítmény telepítése
> * Azure Machine Learning-munkaterület létrehozása
> * A modell betanításához használt számítási erőforrás létrehozása
> * A modell betanításához használt adatkészlet meghatározása és regisztrálása
> * Betanítási sorozat indítása
> * Modell regisztrálása és letöltése
> * A modell telepítése webszolgáltatásként
> * Pontszám adatok a webszolgáltatás

A CLI használatával kapcsolatos további információkért [lásd: A CLI bővítmény használata az Azure Machine Learninghez című témakörben.](reference-azure-machine-learning-cli.md)

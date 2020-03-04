---
title: Modellek betanítása és üzembe helyezése a parancssori felületről
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Machine learning-bővítményt az Azure CLI-hez a parancssorból származó modellek betanításához, regisztrálásához és üzembe helyezéséhez.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 761011cba71c3907994616904cc854003abda7ee
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78245136"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Oktatóanyag: modellek betanítása és üzembe helyezése a parancssori felületről
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben az oktatóanyagban az Azure CLI Machine learning-bővítményét használja a modellek betanításához, regisztrálásához és üzembe helyezéséhez.

Az oktatóanyagban szereplő Python-betanítási szkriptek a [scikit-Learn](https://scikit-learn.org/) paranccsal egy alapszintű modellt tanítanak. Ennek az oktatóanyagnak a középpontjában nem a parancsfájlok vagy a modell szerepel, de a CLI-vel a Azure Machine Learning használatával dolgozhat.

Ismerje meg, hogyan végezheti el a következő műveleteket:

> [!div class="checklist"]
> * A Machine learning-bővítmény telepítése
> * Azure Machine Learning-munkaterület létrehozása
> * A modell betanításához használt számítási erőforrás létrehozása
> * A modell betanításához használt adatkészlet meghatározása és regisztrálása
> * Tanítási Futtatás elindítása
> * Modell regisztrálása és letöltése
> * A modell üzembe helyezése webszolgáltatásként
> * Az adatpontszámok a webszolgáltatás használatával

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Ha a jelen dokumentumban a CLI-parancsokat a **helyi környezetből**szeretné használni, szüksége lesz az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)-re.

    Ha a [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)használja, a CLI a böngészőn keresztül érhető el, és a felhőben él.

## <a name="download-the-example-project"></a>A példában szereplő projekt letöltése

Ebben az oktatóanyagban töltse le a [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) projektet. Az oktatóanyag lépései a `examples/cli-train-deploy` könyvtárban található fájlokat használják.

A fájlok helyi másolatának lekéréséhez [töltse le a. zip archívumot](https://github.com/microsoft/MLOps/archive/master.zip), vagy használja a következő git-parancsot a tárház klónozásához:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Fájlok betanítása

A projekt `examples/cli-train-deploy` könyvtára a következő fájlokat tartalmazza, amelyeket a rendszer a modellek betanításakor használ:

* `.azureml\mnist.runconfig`: __futtatási konfigurációs__ fájl. Ez a fájl határozza meg a modell betanításához szükséges futásidejű környezetet. Ebben a példában a modellnek a képzési környezetbe való betanításához használt adatfeldolgozást is csatlakoztatja.
* `scripts\train.py`: a betanítási szkript. Ez a fájl a modellt is betanítja.
* `scripts\utils.py`: a betanítási szkript által használt segítő fájl.
* `.azureml\conda_dependencies.yml`: a betanítási parancsfájl futtatásához szükséges szoftver-függőségeket határozza meg.
* `dataset.json`: az adatkészlet definíciója. A MNIST-adatkészlet Azure Machine Learning munkaterületen való regisztrálásához használatos.

### <a name="deployment-files"></a>Központi telepítési fájlok

A tárház a következő fájlokat tartalmazza, amelyek segítségével a betanított modellt webszolgáltatásként helyezheti üzembe:

* `aciDeploymentConfig.yml`: __telepítési konfigurációs__ fájl. Ez a fájl határozza meg a modellhez szükséges üzemeltetési környezetet.
* `inferenceConfig.yml`: __következtetési konfigurációs__ fájl. Ez a fájl határozza meg, hogy a szolgáltatás milyen Szoftverkörnyezet használatával szerzi be az adatmodellt.
* `score.py`: A beérkező adatok fogadására szolgáló Python-szkript, a modell használatával szerzi be, majd visszaadja a választ.
* `scoring-env.yml`: a modell és a `score.py` parancsfájl futtatásához szükséges Conda függőségek.
* `testdata.json`: az üzembe helyezett webszolgáltatás tesztelésére használható adatfájl.

## <a name="connect-to-your-azure-subscription"></a>Csatlakozás az Azure-előfizetéshez

Az Azure-előfizetések több módon is hitelesíthetők a parancssori felületről. A legalapvetőbb az interaktív hitelesítés böngésző használatával. Az interaktív hitelesítéshez nyisson meg egy parancssort vagy terminált, és használja a következő parancsot:

```azurecli-interactive
az login
```

Ha a CLI megnyithatja az alapértelmezett böngészőt, akkor megnyitja, és betölti a bejelentkezési oldalt. Ellenkező esetben meg kell nyitnia egy böngészőt, és követnie kell a parancssor utasításait. Az utasítások a [https://aka.ms/devicelogin](https://aka.ms/devicelogin) böngészését és az engedélyezési kód beírását foglalják magukban.

## <a name="install-the-machine-learning-extension"></a>A Machine learning-bővítmény telepítése

A Machine learning-bővítmény telepítéséhez használja a következő parancsot:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Ha egy üzenet jelenik meg, amely szerint a bővítmény már telepítve van, a következő paranccsal frissíthet a legújabb verzióra:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport az Azure platformon található erőforrások alapszintű tárolója. A Azure Machine Learning használatakor az erőforráscsoport a Azure Machine Learning munkaterületet fogja tartalmazni. Emellett a munkaterület által használt egyéb Azure-szolgáltatásokat is tartalmazni fogja. Ha például egy felhőalapú számítási erőforrás használatával betanítja a modellt, akkor az erőforrást az erőforráscsoport hozza létre.

__Új erőforráscsoport létrehozásához__használja a következő parancsot. Cserélje le a `<resource-group-name>` nevet az erőforráscsoport számára használni kívánt névre. Cserélje le a `<location>`t az erőforráscsoporthoz használni kívánt Azure-régióra:

> [!TIP]
> Válassza ki azt a régiót, ahol a Azure Machine Learning elérhető. További információ: [régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

A parancs válasza hasonló a következő JSON-fájlhoz:

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

További információ az erőforráscsoportok használatáról: [az Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Új munkaterület létrehozásához használja a következő parancsot. Cserélje le a `<workspace-name>`t a munkaterülethez használni kívánt névre. Cserélje le a `<resource-group-name>` az erőforráscsoport nevére:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

A parancs kimenete a következő JSON-hoz hasonló:

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

## <a name="connect-local-project-to-workspace"></a>Helyi projekt összekötése a munkaterülettel

Egy terminálról vagy parancssorból az alábbi parancsokkal módosíthatja a címtárakat a `cli-train-deploy` könyvtárba, majd csatlakozhat a munkaterületéhez:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

A parancs kimenete a következő JSON-hoz hasonló:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Ez a parancs létrehoz egy `.azureml/config.json` fájlt, amely a munkaterületéhez való kapcsolódáshoz szükséges információkat tartalmazza. Az oktatóanyagban használt többi `az ml` parancs ezt a fájlt fogja használni, így nem kell hozzáadnia a munkaterületet és az erőforráscsoportot az összes parancshoz.

## <a name="create-the-compute-target-for-training"></a>Számítási cél létrehozása képzéshez

Ez a példa egy Azure Machine Learning számítási fürtöt használ a modell betanításához. Új számítási fürt létrehozásához használja a következő parancsot:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

A parancs kimenete a következő JSON-hoz hasonló:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Ez a parancs létrehoz egy `cpu`nevű új számítási célt, amely legfeljebb négy csomóponttal rendelkezik. A kiválasztott virtuálisgép-méret GPU-erőforrással rendelkező virtuális gépet biztosít. A virtuális gép méretével kapcsolatos információkért lásd: [VM-típusok és méretek].

> [!IMPORTANT]
> A számítási cél neve (ebben az esetben`cpu`) fontos; a következő szakaszban használt `.azureml/mnist.runconfig` fájl hivatkozik rá.

## <a name="define-the-dataset"></a>Az adatkészlet meghatározása

A modellek betanításához adatkészletek segítségével megadhatja a betanítási adatokat. Adatkészlet létrehozásához a parancssori felületről meg kell adnia egy adatkészlet-definíciós fájlt. A tárházban megadott `dataset.json` fájl egy új adatkészletet hoz létre a MNIST-adatokat használva. Az általa létrehozott adatkészlet neve `mnist-dataset`.

Az adatkészlet `dataset.json` fájl használatával történő regisztrálásához használja a következő parancsot:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

A parancs kimenete a következő JSON-hoz hasonló:

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
> Másolja a `id` bejegyzés értékét, ahogy azt a következő szakaszban használják.

Az adatkészletek átfogóbb sablonjának megjelenítéséhez használja a következő parancsot:
```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Az adatkészlet hivatkozása

Ahhoz, hogy az adatkészlet elérhető legyen a betanítási környezetben, hivatkozni kell rá a runconfig fájlra. A `.azureml/mnist.runconfig` fájl a következő YAML-bejegyzéseket tartalmazza:

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

Módosítsa a `id` bejegyzés értékét úgy, hogy az megfeleljen az adatkészlet regisztrálásakor visszaadott értéknek. Ezzel az értékkel tölthetők be az adatok a számítási célra a betanítás során.

Ez a YAML a következő műveleteket eredményezi a betanítás során:

* Csatlakoztatja az adatkészletet (az adatkészlet azonosítója alapján) a képzési környezetben, és a `mnist` környezeti változóban lévő csatlakoztatási pont elérési útját tárolja.
* A `--data-folder` argumentum használatával átadja az adatelérési pont (Mount Point) helyét a betanítási környezeten belül a parancsfájlnak.

A runconfig-fájl a betanítási Futtatás által használt környezet konfigurálásához használt információkat is tartalmaz. Ha megvizsgálja ezt a fájlt, látni fogja, hogy a korábban létrehozott `cpu-compute` számítási célra hivatkozik. Azt is felsorolja, hogy hány csomópontot kell használni a Betanításkor (`"nodeCount": "4"`), és tartalmaz egy `"condaDependencies"` szakaszt, amely felsorolja a betanítási parancsfájl futtatásához szükséges Python-csomagokat.

> [!TIP]
> Habár manuálisan is létrehozhat egy runconfig-fájlt, az ebben a példában szereplő `generate-runconfig.py` a tárházban foglalt fájl használatával jött létre. Ez a fájl a regisztrált adatkészletre mutató hivatkozást kap, létrehoz egy futtatási konfiguráció programozott módon, majd megőrzi a fájlt.

A konfigurációs fájlok futtatásával kapcsolatos további információkért lásd: [számítási célok beállítása és használata a modell betanításához](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli). A teljes JSON-referenciáért tekintse meg a [runconfigschema. JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)fájlt.

## <a name="submit-the-training-run"></a>A betanítási Futtatás elküldése

A `cpu-compute` számítási célra való betanítás indításához használja a következő parancsot:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

Ez a parancs megadja a kísérlet nevét (`myexperiment`). A kísérlet a munkaterületen lévő futtatással kapcsolatos adatokat tárolja.

A `-c mnist` paraméter a `.azureml/mnist.runconfig` fájlt adja meg.

A `-t` paraméter egy JSON-fájlban lévő futtatásra mutató hivatkozást tárol, és a következő lépésekben fogja használni a modell regisztrálásához és letöltéséhez.

A betanítási folyamat során a a távoli számítási erőforráson keresztül továbbítja az adatokat a betanítási munkamenetből. Az adatok egy része az alábbi szöveghez hasonló:

```text
Predict the test set
Accuracy is 0.9185
```

Ezt a szöveget a betanítási szkript naplózza, és megjeleníti a modell pontosságát. Más modellek esetében a teljesítmény mérőszámai eltérőek lesznek.

Ha megvizsgálja a betanítási szkriptet, megfigyelheti, hogy az alfa értéket is használja, amikor a betanított modellt `outputs/sklearn_mnist_model.pkl`ba tárolja.

A modellt a rendszer a `./outputs` könyvtárba mentette a számítási célhelyen, ahol azt betanítták. Ebben az esetben a Azure Machine Learning számítási példány az Azure-felhőben. A betanítási folyamat automatikusan feltölti a `./outputs` könyvtár tartalmát a számítási célhelyről, ahol a képzés bekerül a Azure Machine Learning munkaterületre. A kísérlet részeként tárolódik (`myexperiment` ebben a példában).

## <a name="register-the-model"></a>Regisztrálja a modellt

A modell közvetlenül a kísérletben tárolt verzióból való regisztrálásához használja a következő parancsot:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Ez a parancs regisztrálja a betanítási Futtatás által létrehozott `outputs/sklearn_mnist_model.pkl` fájlt `mymodel`nevű új modell-regisztrációként. A `--assets-path` egy kísérlet útvonalára hivatkozik. Ebben az esetben a kísérlet és a futtatási információ betöltődik a betanítási parancs által létrehozott `runoutput.json` fájlból. A `-t registeredmodel.json` létrehoz egy JSON-fájlt, amely hivatkozik a parancs által létrehozott új regisztrált modellre, és más CLI-parancsok használják, amelyek regisztrált modellekkel működnek.

A parancs kimenete a következő JSON-hoz hasonló:

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

### <a name="model-versioning"></a>Modell verziószámozása

Jegyezze fel a modell visszaadott verziószámát. A verzió minden alkalommal nő, amikor új modellt regisztrál ehhez a névvel. Például letöltheti a modellt, és a következő parancsokkal regisztrálhat egy helyi fájlból:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

Az első parancs letölti a regisztrált modellt az aktuális könyvtárba. A fájl neve `sklearn_mnist_model.pkl`, amely a modell regisztrálása során hivatkozott fájl. A második parancs regisztrálja a helyi modellt (`-p "sklearn_mnist_model.pkl"`) ugyanazzal a névvel, mint a korábbi regisztrációval (`mymodel`). Ez alkalommal a visszaadott JSON-adatérték a 2-es verziót sorolja fel.

## <a name="deploy-the-model"></a>A modell üzembe helyezése

Modell üzembe helyezéséhez használja a következő parancsot:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

> [!NOTE]
> A "sikertelen LocalWebservice létezésének ellenőrzésével" kapcsolatos figyelmeztetés jelenhet meg. Ezt nyugodtan figyelmen kívül hagyhatja, mert nem telepít helyi webszolgáltatást.

Ez a parancs egy `myservice`nevű új szolgáltatást helyez üzembe a korábban regisztrált modell 1. verziójának használatával.

A `inferenceConfig.yml` fájl információkkal szolgál arról, hogyan használható a modell a következtetésekhez. Például hivatkozik a bejegyzési parancsfájlra (`score.py`) és a szoftver függőségeire. 

A fájl struktúrájával kapcsolatos további információkért tekintse meg a [következtetési konfigurációs sémát](reference-azure-machine-learning-cli.md#inference-configuration-schema). A beléptetési parancsfájlokkal kapcsolatos további információkért lásd: [modellek üzembe helyezése a Azure Machine learning](how-to-deploy-and-where.md#prepare-to-deploy).

A `aciDeploymentConfig.yml` ismerteti a szolgáltatás üzemeltetéséhez használt központi telepítési környezetet. A központi telepítési konfiguráció a központi telepítéshez használt számítási típusra vonatkozik. Ebben az esetben egy Azure Container-példányt használunk. További információ: a [központi telepítés konfigurációs sémája](reference-azure-machine-learning-cli.md#deployment-configuration-schema).

Az üzembe helyezési folyamat befejeződése előtt több percet is igénybe vehet.

> [!TIP]
> Ebben a példában Azure Container Instances van használatban. Az ACI-hoz készült üzembe helyezések automatikusan létrehozzák a szükséges ACI-erőforrást. Ha ehelyett az Azure Kubernetes Service-ben kellene üzembe helyeznie, létre kell hoznia egy AK-fürtöt az idő előtt, és meg kell adnia a `az ml model deploy` parancs részeként. Az AK-ra való üzembe helyezésre példa: [modell üzembe helyezése Azure Kubernetes Service-fürtön](how-to-deploy-azure-kubernetes-service.md).

Néhány perc elteltével a rendszer a következő JSON-hoz hasonló adatokat adja vissza:

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

A központi telepítés által visszaadott `scoringUri` egy webszolgáltatásként üzembe helyezett modell REST-végpontja. Ezt az URI-t a következő parancs használatával is lekérheti:

```azurecli-interactive
az ml service show -n myservice
```

Ez a parancs ugyanazt a JSON-dokumentumot adja vissza, amely tartalmazza a `scoringUri`is.

A REST-végpont használatával adatok küldhetők a szolgáltatásba. Az adatokat a szolgáltatásnak küldő ügyfélalkalmazás létrehozásával kapcsolatos információkért lásd: [webszolgáltatásként üzembe helyezett Azure Machine learning-modell felhasználása](how-to-consume-web-service.md)

### <a name="send-data-to-the-service"></a>Adatküldés a szolgáltatásba

Amíg létrehozhat egy ügyfélalkalmazás a végpont meghívásához, a Machine learning parancssori felület olyan segédprogramot biztosít, amely tesztelő ügyfélként működhet. Használja az alábbi parancsot a `testdata.json` fájlban tárolt adatküldéshez a szolgáltatásba:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Ha a PowerShellt használja, használja inkább az alábbi parancsot:
>
> ```powershell
> az ml service run -n myservice -d `@testdata.json
> ```

A parancs válasza hasonló a `[ 3 ]`hoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

> [!IMPORTANT]
> A létrehozott erőforrások előfeltételként is használhatók más Azure Machine Learning-oktatóanyagokban vagy -útmutatókban.

### <a name="delete-deployed-service"></a>Telepített szolgáltatás törlése

Ha azt tervezi, hogy továbbra is az Azure Machine Learning munkaterületet használja, de a költségek csökkentése érdekében szeretné megszabadulni a telepített szolgáltatástól, használja a következő parancsot:

```azurecli-interactive
az ml service delete -n myservice
```

Ez a parancs egy JSON-dokumentumot ad vissza, amely tartalmazza a törölt szolgáltatás nevét. A szolgáltatás törlése több percet is igénybe vehet.

### <a name="delete-the-training-compute"></a>A betanítási számítás törlése

Ha azt tervezi, hogy továbbra is használja a Azure Machine Learning munkaterületet, de szeretné megszabadulni a betanításhoz létrehozott `cpu-compute` számítási célnak, használja a következő parancsot:

```azurecli-interactive
az ml computetarget delete -n cpu
```

Ez a parancs egy JSON-dokumentumot ad vissza, amely tartalmazza a törölt számítási cél AZONOSÍTÓját. Több percet is igénybe vehet, amíg a számítási cél törölve lett.

### <a name="delete-everything"></a>Mindent törölni

Ha nem tervezi a létrehozott erőforrások használatát, törölje őket, így nem jár további költségekkel.

Az erőforráscsoport és az ebben a dokumentumban létrehozott összes Azure-erőforrás törléséhez használja a következő parancsot. Cserélje le a `<resource-group-name>`t a korábban létrehozott erőforráscsoport nevére:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Következő lépések

Ebben az Azure Machine Learning oktatóanyagban a Machine learning parancssori felületét használta a következő feladatokhoz:

> [!div class="checklist"]
> * A Machine learning-bővítmény telepítése
> * Azure Machine Learning-munkaterület létrehozása
> * A modell betanításához használt számítási erőforrás létrehozása
> * A modell betanításához használt adatkészlet meghatározása és regisztrálása
> * Tanítási Futtatás elindítása
> * Modell regisztrálása és letöltése
> * A modell üzembe helyezése webszolgáltatásként
> * Az adatpontszámok a webszolgáltatás használatával

További információ a parancssori felület használatáról: [a CLI-bővítmény használata a Azure Machine Learninghoz](reference-azure-machine-learning-cli.md).

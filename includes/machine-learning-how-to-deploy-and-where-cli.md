---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 38b346b4296ea72f964717220adf762e8d0d6d91
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542785"
---
## <a name="prerequisites"></a>Előfeltétel

- Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](../articles/machine-learning/how-to-manage-workspace.md).
- Egy modell. Ha nem rendelkezik betanított modellel, az [oktatóanyagban](https://aka.ms/azml-deploy-cloud)szereplő modell-és függőségi fájlokat is használhatja.
- A [Machine learning szolgáltatás Azure parancssori felületének (CLI) bővítménye](../articles/machine-learning/reference-azure-machine-learning-cli.md)


## <a name="connect-to-your-workspace"></a>Csatlakozás a munkaterülethez

Az [előfizetési környezet beállításához](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)kövesse az Azure CLI dokumentációjának utasításait.

Ezután tegye a következőket:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

lehetőségre kattintva megtekintheti a munkaterületeket.

## <a name="register-your-model"></a><a id="registermodel"></a>A modell regisztrálása

A regisztrált modell a modellt alkotó egy vagy több fájl logikai tárolója. Ha például több fájlban tárolt modell van, akkor egyetlen modellként regisztrálhatja őket a munkaterületen. A fájlok regisztrálása után letöltheti vagy telepítheti a regisztrált modellt, és megkapja az összes regisztrált fájlt.

> [!TIP]
> Modell regisztrálása esetén megadja a Felhőbeli hely (betanítási Futtatás) vagy egy helyi könyvtár elérési útját. Ez az elérési út csak a feltöltéshez szükséges fájlok megkeresése a regisztrációs folyamat részeként. Nem kell megegyeznie a bejegyzési parancsfájlban használt elérési úttal. További információ: [a modell fájljainak megkeresése a belépési parancsfájlban](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models).

A gépi tanulási modellek regisztrálva vannak a Azure Machine Learning munkaterületen. A modell Azure Machine Learning vagy máshonnan is származhat. Modell regisztrálása esetén a modellre vonatkozó metaadatokat is megadhat. A `tags` modell-regisztrációra alkalmazott és a szótárak használatával szűrheti a modelleket `properties` .

Az alábbi példák bemutatják, hogyan regisztrálhat egy modellt.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Modell regisztrálása egy Azure ML betanítási futtatásból

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](machine-learning-service-install-extension.md)]

A `--asset-path` paraméter a modell Felhőbeli helyét jelöli. Ebben a példában egyetlen fájl elérési útját használjuk. Ha több fájlt szeretne szerepeltetni a modell regisztrációjában, állítsa `--asset-path` a fájlokat tartalmazó mappa elérési útjára.

### <a name="register-a-model-from-a-local-file"></a>Modell regisztrálása helyi fájlból

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Ha több fájlt szeretne szerepeltetni a modell regisztrációjában, állítsa `-p` a fájlokat tartalmazó mappa elérési útjára.

A szolgáltatással kapcsolatos további információkért `az ml model register` olvassa el a [dokumentációt](/cli/azure/ext/azure-cli-ml/ml/model).

## <a name="define-an-entry-script"></a>Bejegyzési parancsfájl definiálása

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>Következtetési konfiguráció definiálása

[!INCLUDE [inference config](machine-learning-service-inference-config.md)]

A következő parancs bemutatja, hogyan helyezhet üzembe egy modellt a parancssori felület használatával:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Ebben a példában a konfiguráció a következő beállításokat adja meg:

* A modellhez Python szükséges
* A [beléptetési parancsfájl](#define-an-entry-script), amely a központilag telepített szolgáltatásnak küldött webes kérelmek kezelésére szolgál.
* A következtetéshez szükséges Python-csomagokat ismertető Conda-fájl

További információ a következtetési konfigurációval rendelkező egyéni Docker-rendszerképek használatáról: [modell üzembe helyezése egyéni Docker-rendszerkép használatával](../articles/machine-learning/how-to-deploy-custom-docker-image.md).

## <a name="choose-a-compute-target"></a>Számítási cél kiválasztása

[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Telepítési konfiguráció definiálása

A központi telepítési konfigurációhoz elérhető lehetőségek a kiválasztott számítási céltól függően eltérőek.

[!INCLUDE [aml-local-deploy-config](machine-learning-service-local-deploy-config.md)]

További információ: az [ml Model Deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) dokumentáció.

## <a name="deploy-your-model"></a>A modell üzembe helyezése

Most már készen áll a modell üzembe helyezésére.

### <a name="using-a-registered-model"></a>Regisztrált modell használata

Ha regisztrálta a modellt a Azure Machine Learning munkaterületen, cserélje le a "mymodel: 1" kifejezést a modell nevével és annak verziószámával.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Helyi modell használata

Ha nem szeretné regisztrálni a modellt, akkor a inferenceconfig.js"sourceDirectory" paraméterének megadásával megadhat egy helyi könyvtárat a modell kiszolgálásához.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```
## <a name="delete-resources"></a>Erőforrások törlése

Telepített webszolgáltatás törléséhez használja a következőt: `az ml service <name of webservice>` .

Ha törölni szeretne egy regisztrált modellt a munkaterületről, használja a következőt:`az ml model delete <model id>`

További információ a [webszolgáltatások törléséről](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) és [a modellek törléséről](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete)
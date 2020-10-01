---
title: A Triton-t (előzetes verzió) kiszolgáló nagy teljesítményű modell
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan helyezhet üzembe egy modellt a Triton következtetési kiszolgálóval Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 09/23/2020
ms.topic: conceptual
ms.openlocfilehash: 17260c3890df0bd78b1503a046ff39ab173712be
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91622064"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Nagy teljesítményű kiszolgálás a Triton inferenc Serverrel (előzetes verzió) 

Ismerje meg, hogyan használhatja az [NVIDIA Triton-alapú kiszolgáló](https://developer.nvidia.com/nvidia-triton-inference-server) szolgáltatást a modell következtetéséhez használt webszolgáltatás teljesítményének növelésére.

A modell üzembe helyezésének egyik módja webszolgáltatásként használható. Például egy üzembe helyezés az Azure Kubernetes Service-ben vagy a Azure Container Instances. Alapértelmezés szerint a Azure Machine Learning egy többszálú, *általános célú* webes keretrendszert használ a webszolgáltatások üzembe helyezéséhez.

A Triton egy *következtetésre optimalizált*keretrendszer. A GPU-k jobb kihasználtságát és költséghatékonyabb következtetéseket biztosít. A kiszolgálóoldali, a beérkező kéréseket kötegbe állítja, és elküldi ezeket a kötegeket a következtetésekhez. A kötegelt feldolgozás jobban kihasználja a GPU-erőforrásokat, és a Triton teljesítményének kulcsfontosságú része.

> [!IMPORTANT]
> A Triton használata a Azure Machine Learning szolgáltatásból való üzembe helyezéshez jelenleg __előzetes__verzióban érhető el. Előfordulhat, hogy az előzetes verzió funkcióit nem fedi le az ügyfélszolgálat. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> A dokumentumban szereplő kódrészletek szemléltető célokat szolgálnak, és nem mutatnak teljes megoldást. A következő példában a [Triton végpontok közötti mintáit Azure Machine Learningban](https://aka.ms/aml-triton-sample)tekintheti meg.

## <a name="prerequisites"></a>Előfeltételek

* Egy **Azure-előfizetés**. Ha még nem rendelkezik ilyennel, próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).
* Ismerje meg, [Hogyan és hol helyezheti üzembe a modelleket Azure Machine learning használatával](how-to-deploy-and-where.md) .
* A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py) **vagy** az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) és a [Machine learning bővítmény](reference-azure-machine-learning-cli.md).
* A Docker egy működő telepítése a helyi teszteléshez. A Docker telepítésével és ellenőrzésével kapcsolatos információkért lásd: [Tájolás és beállítás](https://docs.docker.com/get-started/) a Docker dokumentációjában.

## <a name="architectural-overview"></a>Az architektúra áttekintése

Mielőtt a Tritont a saját modelljére próbálja használni, fontos tisztában lennie azzal, hogyan működik együtt a Azure Machine Learning, és hogyan hasonlítja össze az alapértelmezett központi telepítéssel.

**Alapértelmezett üzembe helyezés Triton nélkül**

* Több [Gunicorn](https://gunicorn.org/) -feldolgozó is megkezdi a bejövő kérések egyidejű kezelését.
* Ezek a dolgozók az előfeldolgozást, a modell meghívását és a feldolgozás utáni folyamatokat kezelik. 
* A következtetési kérelmek a __pontozási URI__-t használják. Például: `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Normál, nem Triton, üzembe helyezési architektúra diagramja":::

**Konfiguráció központi telepítése a Tritontal**

* Több [Gunicorn](https://gunicorn.org/) -feldolgozó is megkezdi a bejövő kérések egyidejű kezelését.
* A rendszer továbbítja a kéréseket a **Triton-kiszolgálónak**. 
* A Triton a GPU-kihasználtság maximalizálása érdekében a kötegekben dolgozza fel a kérelmeket.
* Az ügyfél a __pontozási URI__ -t használja a kérések elvégzéséhez. Például: `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inferenceconfig-deploy.png" alt-text="Normál, nem Triton, üzembe helyezési architektúra diagramja":::

A Triton a modell üzembe helyezéséhez használt munkafolyamat a következő:

1. Győződjön meg arról, hogy a Triton képes kiszolgálni a modellt.
1. Ellenőrizze, hogy küldhet-e kéréseket a Triton által üzembe helyezett modellbe.
1. A Triton-specifikus kód beépítése a pénzmosás-alapú üzembe helyezésbe.

## <a name="optional-define-a-model-config-file"></a>Választható Modell konfigurációs fájljának definiálása

A modell konfigurációs fájlja azt mutatja be, hogy a Triton hány bemenetet vár, és hogy milyen dimenziók lesznek a bemenetek. A konfigurációs fájl létrehozásával kapcsolatos további információkért lásd: [modell konfigurálása](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html) az NVIDIA dokumentációjában.

> [!TIP]
> A `--strict-model-config=false` Triton következtetési kiszolgálójának indításakor a lehetőséget használjuk, ami azt jelenti, hogy nem kell megadnia egy `config.pbtxt` fájlt a ONNX vagy TensorFlow modellekhez.
> 
> További információ erről a lehetőségről: [generált modell konfigurálása](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html#generated-model-configuration) az NVIDIA dokumentációjában.

## <a name="directory-structure"></a>Könyvtár szerkezete

Azure Machine Learning-alapú modell regisztrálása esetén egyéni fájlokat vagy címtár-struktúrát is regisztrálhat. A Triton használatához a modell regisztrációjának olyan címtár-struktúrára kell mutatnia, amely tartalmazza a nevű könyvtárat `triton` . Ennek a könyvtárnak a általános szerkezete a következő:

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
                - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> Ez a címtár-struktúra a Triton-modell tárháza, és a modell (ek) hoz való együttműködéshez szükséges. További információ: [Triton Model repositorys](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_repository.html) az NVIDIA dokumentációjában.

## <a name="test-with-triton-and-docker"></a>Tesztelés a Triton és a Docker révén

Ha tesztelni szeretné a modelljét, hogy az a Triton használatával fusson, használhatja a Docker-t. Az alábbi parancsok lekérik a Triton-tárolót a helyi számítógépre, majd elindítják a Triton-kiszolgálót:

1. A Triton-kiszolgáló rendszerképének a helyi számítógépre való lekéréséhez használja a következő parancsot:

    ```bash
    docker pull nvcr.io/nvidia/tritonserver:20.09-py3
    ```

1. A Triton-kiszolgáló elindításához használja a következő parancsot. Cserélje le a helyére a `<path-to-models/triton>` modelleket tartalmazó Triton Model adattár elérési útját:

    ```bash
    docker run --rm -ti -v<path-to-models/triton>:/models nvcr.io/nvidia/tritonserver:20.09-py3 tritonserver --model-repository=/models --strict-model-config=false
    ```

    > [!IMPORTANT]
    > Ha Windows rendszert használ, előfordulhat, hogy a parancs első futtatásakor a rendszer kérni fogja, hogy engedélyezze a hálózati kapcsolatokat ehhez a folyamathoz. Ha igen, válassza a hozzáférés engedélyezése lehetőséget.

    Az indítás után a következő szöveghez hasonló információk kerülnek a parancssorba:

    ```bash
    I0923 19:21:30.582866 1 http_server.cc:2705] Started HTTPService at 0.0.0.0:8000
    I0923 19:21:30.626081 1 http_server.cc:2724] Started Metrics Service at 0.0.0.0:8002
    ```

    Az első sorban a webszolgáltatás címe látható. Ebben az esetben ez `0.0.0.0:8000` megegyezik a következővel: `localhost:8000` .

1. Az állapot-végpont eléréséhez használjon egy olyan segédprogramot, mint például a curl.

    ```bash
    curl -L -v -i localhost:8000/v2/health/ready
    ```

    Ez a parancs a következőhöz hasonló adatokat ad vissza. Megjegyzés: a `200 OK` ; Ez az állapot azt jelenti, hogy a webkiszolgáló fut.

    ```bash
    *   Trying 127.0.0.1:8000...
    * Connected to localhost (127.0.0.1) port 8000 (#0)
    > GET /v2/health/ready HTTP/1.1
    > Host: localhost:8000
    > User-Agent: curl/7.71.1
    > Accept: */*
    >
    * Mark bundle as not supporting multiuse
    < HTTP/1.1 200 OK
    HTTP/1.1 200 OK
    ```

Az alapszintű állapot-ellenőrzések után létrehozhat egy ügyfelet, amely az adatoknak a Tritonba való küldését is elküldheti. Az ügyfelek létrehozásával kapcsolatos további információkért tekintse meg az NVIDIA dokumentációjában található [ügyfél-példákat](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/client_example.html) . [A Triton githubon](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples)is vannak Python-minták.

A Triton a Docker használatával történő futtatásával kapcsolatos további információkért lásd: a [Triton futtatása egy GPU-val rendelkező rendszeren](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-with-a-gpu) , valamint a [Triton futtatása GPU nélküli rendszeren](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-without-a-gpu).

## <a name="register-your-model"></a>A modell regisztrálása

Most, hogy ellenőrizte, hogy a modell a Triton-rel működik, regisztrálja Azure Machine Learning-mel. A modell regisztrálása a Azure Machine Learning munkaterületen tárolja a modell fájljait, és a Python SDK-val és az Azure CLI-vel való üzembe helyezéskor használatos.

Az alábbi példák bemutatják, hogyan regisztrálhatók a modell (ek):

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.model import Model

model = Model.register(
    model_path=os.path.join("..", "triton"),
    model_name="bidaf_onnx",
    tags={'area': "Natural language processing", 'type': "Question answering"},
    description="Question answering model from ONNX model zoo",
    workspace=ws
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml model register --model-path='triton' \
--name='bidaf_onnx' \
--workspace-name='<my_workspace>'
```
---

<a id="processing"></a>

## <a name="add-pre-and-post-processing"></a>Előzetes és utólagos feldolgozás hozzáadása

Miután ellenőrizte, hogy a webszolgáltatás működik-e, megadhatja az előzetes és a feldolgozás utáni kódot egy _bejegyzési parancsfájl_definiálásával. A fájl neve `score.py` . A beléptetési parancsfájlokkal kapcsolatos további információkért lásd: [bejegyzési parancsfájl definiálása](how-to-deploy-and-where.md#define-an-entry-script).

A két fő lépés egy Triton HTTP-ügyfél inicializálása a `init()` metódusban, valamint az ügyfélnek a függvényben való meghívása `run()` .

### <a name="initialize-the-triton-client"></a>A Triton-ügyfél inicializálása

Az alábbi példához hasonló kódot adjon meg a `score.py` fájlhoz. A Triton a Azure Machine Learning a localhost, a 8000-es portra vár. Ebben az esetben a localhost az üzemelő példány Docker-rendszerképében található, nem pedig a helyi gépen lévő port:

> [!TIP]
> A `tritonhttpclient` pip-csomag a kurátori környezet részét képezi `AzureML-Triton` , így nem kell megadnia pip-függőségként.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Pontozási parancsfájl módosítása a Tritonba való híváshoz

Az alábbi példa bemutatja, hogyan lehet dinamikusan kérelmezni a modell metaadatait:

> [!TIP]
> A Triton-ügyféllel betöltött modellek metaadatait dinamikusan kérheti le a `.get_model_metadata` Triton-ügyfél metódusának használatával. Tekintse meg a [minta jegyzetfüzetet](https://aka.ms/triton-aml-sample) a használatáról.

```python
input = tritonhttpclient.InferInput(input_name, data.shape, datatype)
input.set_data_from_numpy(data, binary_data=binary_data)

output = tritonhttpclient.InferRequestedOutput(
         output_name, binary_data=binary_data, class_count=class_count)

# Run inference
res = triton_client.infer(model_name,
                          [input]
                          request_id='0',
                          outputs=[output])

```

<a id="redeploy"></a>

## <a name="redeploy-with-an-inference-configuration"></a>Újbóli üzembe helyezés a következtetési konfigurációval

A következtetési konfiguráció lehetővé teszi egy belépési parancsfájl használatát, valamint a Azure Machine Learning üzembe helyezési folyamatát a Python SDK vagy az Azure CLI használatával.

> [!IMPORTANT]
> Meg kell adnia a `AzureML-Triton` [kurátori környezetet](./resource-curated-environments.md).
>
> A Python-kód például `AzureML-Triton` egy másik, nevű környezetbe klónozott `My-Triton` . Az Azure CLI-kód ezt a környezetet is használja. A környezetek klónozásával kapcsolatos további információkért tekintse meg a [Environment. Clone ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true#clone-new-name-) hivatkozást.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import LocalWebservice
from azureml.core import Environment
from azureml.core.model import InferenceConfig


local_service_name = "triton-bidaf-onnx"
env = Environment.get(ws, "AzureML-Triton").clone("My-Triton")

for pip_package in ["nltk"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(
    entry_script="score_bidaf.py",  # This entry script is where we dispatch a call to the Triton server
    source_directory=os.path.join("..", "scripts"),
    environment=env
)

local_config = LocalWebservice.deploy_configuration(
    port=6789
)

local_service = Model.deploy(
    workspace=ws,
    name=local_service_name,
    models=[model],
    inference_config=inference_config,
    deployment_config=local_config,
    overwrite=True)

local_service.wait_for_deployment(show_output = True)
print(local_service.state)
# Print the URI you can use to call the local deployment
print(local_service.scoring_uri)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!TIP]
> A következtetési beállítások létrehozásával kapcsolatos további információkért lásd: a [következtetések konfigurációs sémája](./reference-azure-machine-learning-cli.md#inference-configuration-schema).

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

---

Az üzembe helyezés befejezése után megjelenik a pontozási URI. Ebben a helyi telepítésben a következő lesz: `http://localhost:6789/score` . Ha a felhőbe telepíti a szolgáltatást, az az [ml Service show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext_azure_cli_ml_az_ml_service_show) CLI paranccsal kérheti le a pontozási URI-t.

További információ arról, hogyan hozhat létre olyan ügyfelet, amely a pontozási URI-ra vonatkozó következtetéseket küld: [webszolgáltatásként üzembe helyezett modell felhasználása](how-to-consume-web-service.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy továbbra is használja a Azure Machine Learning munkaterületet, de szeretné megszabadulni a telepített szolgáltatástól, használja az alábbi lehetőségek egyikét:

# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml service delete -n triton-densenet-onnx
```

---

## <a name="next-steps"></a>További lépések

* [Lásd: a Triton végpontok közötti mintái Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Ismerje meg a [Triton-ügyfelek példáit](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples)
* A Triton-beli [következtetési kiszolgáló dokumentációjának](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/index.html) beolvasása
* [Sikertelen üzembe helyezés hibáinak megoldása](how-to-troubleshoot-deployment.md)
* [Üzembe helyezés az Azure Kubernetes Service-ben](how-to-deploy-azure-kubernetes-service.md)
* [Webszolgáltatás frissítése](how-to-deploy-update-web-service.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)

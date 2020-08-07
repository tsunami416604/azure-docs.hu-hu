---
title: Docker-telepítés – hibaelhárítás
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy miként lehet megkerülni, megoldani és elhárítani a Common Docker-telepítési hibákat az Azure Kubernetes szolgáltatással, és Azure Container Instances a Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4, devx-track-python
ms.openlocfilehash: 3f8a3c705878e212e6a26670e20b5a81a3f2a6ba
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904377"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>Az Azure Kubernetes Service-szel és Azure Container Instances-mel kapcsolatos modellek Docker-telepítésének hibáinak megoldása 

Megtudhatja, hogyan oldhatja fel az általános Docker-telepítési hibákat a Azure Container Instances (ACI) és az Azure Kubernetes szolgáltatással (ak) a Azure Machine Learning használatával.

## <a name="prerequisites"></a>Előfeltételek

* Egy **Azure-előfizetés**. Ha még nem rendelkezik ilyennel, próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).
* A [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)-vel.
* A [Azure Machine learning CLI-bővítménye](reference-azure-machine-learning-cli.md).
* Helyi hibakereséshez rendelkeznie kell egy működő Docker-telepítéssel a helyi rendszeren.

    A Docker-telepítés ellenőrzéséhez használja a parancsot `docker run hello-world` egy terminálról vagy parancssorból. A Docker telepítésével vagy a Docker-hibák elhárításával kapcsolatos információkért tekintse meg a [Docker dokumentációját](https://docs.docker.com/).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>A Machine learning-modellek Docker-telepítésének lépései

Azure Machine Learning-modell telepítésekor a rendszer számos feladatot hajt végre.

A modell üzembe helyezésének ajánlott módja a [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API használata egy [környezeti](how-to-use-environments.md) objektum használatával bemeneti paraméterként. Ebben az esetben a szolgáltatás alapszintű Docker-rendszerképet hoz létre az üzembe helyezési fázis során, és egyetlen hívásban csatlakoztatja a szükséges modelleket. Az alapszintű üzembe helyezési feladatok a következők:

1. Regisztrálja a modellt a munkaterület-modell beállításjegyzékében.

2. Következtetési konfiguráció meghatározása:
    1. Hozzon létre egy [környezeti](how-to-use-environments.md) objektumot a környezeti YAML fájlban megadott függőségek alapján, vagy használja a beszerzett környezetek egyikét.
    2. Hozzon létre egy következtetési konfigurációt (InferenceConfig-objektumot) a környezet és a pontozási parancsfájl alapján.

3. A modell üzembe helyezése az Azure Container instance (ACI) szolgáltatásban vagy az Azure Kubernetes szolgáltatásban (ak).

További információ a folyamatról a [modellkezelés](concept-model-management-and-deployment.md) bevezetésében.

## <a name="before-you-begin"></a>Előkészületek

Ha bármilyen problémába ütközik, az első lépés az, hogy az üzembe helyezési feladatot (előző Leírás) a probléma elkülönítése érdekében egyedi lépésekbe bontsa.

Feltételezve, hogy az új/ajánlott telepítési módszert használja a [Model. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API-val egy [környezeti](how-to-use-environments.md) objektummal bemeneti paraméterként, a kód három fő lépésből bontható:

1. A modell regisztrálása. Íme néhány mintakód:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Következtetési konfiguráció megadása a központi telepítéshez:

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Telepítse a modellt az előző lépésben létrehozott következtetési konfiguráció használatával:

    ```python
    from azureml.core.webservice import AciWebservice


    # deploy the model
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
    aci_service = Model.deploy(workspace=ws,
                           name='my-service',
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aci_config)
    aci_service.wait_for_deployment(show_output=True)
    ```

Miután megszakította az üzembe helyezés folyamatát az egyes feladatokra, megvizsgáljuk a leggyakoribb hibákat.

## <a name="debug-locally"></a>Helyi hibakeresés

Ha olyan problémák merülnek fel, amelyek a modell ACI-vagy AK-beli üzembe helyezésével kapcsolatosak, próbálja meg helyi webszolgáltatásként telepíteni. A helyi webszolgáltatás használatával egyszerűbbé válik a problémák elhárítása. A modellt tartalmazó Docker-rendszerkép le van töltve, és elindult a helyi rendszeren.

A [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) -tárházban talál egy [helyi telepítési jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) egy futtatható-példa megismeréséhez.

> [!WARNING]
> A helyi webszolgáltatások üzembe helyezése éles környezetben nem támogatott.

A helyileg történő üzembe helyezéshez módosítsa a kód használatát a `LocalWebservice.deploy_configuration()` telepítési konfiguráció létrehozásához. Ezután `Model.deploy()` a használatával telepítheti a szolgáltatást. A következő példa egy modellt helyez üzembe a modell változójában helyi webszolgáltatásként:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Ha saját Conda-specifikációs YAML határoz meg, akkor a azureml alapértelmezett értékeit kell megadnia a >= 1.0.45 verzióval pip-függőségként. Ez a csomag tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat.

Ezen a ponton a megszokott módon dolgozhat a szolgáltatással. Az alábbi kód például a szolgáltatásnak küldött adatokat mutatja be:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

A Python-környezet testreszabásával kapcsolatos további információkért lásd: [környezetek létrehozása és kezelése képzéshez és üzembe helyezéshez](how-to-use-environments.md). 

### <a name="update-the-service"></a>A szolgáltatás frissítése

A helyi tesztelés során előfordulhat, hogy frissítenie kell a `score.py` fájlt a naplózás hozzáadásához, vagy a felderített problémák megoldására tett kísérletet. A fájl módosításainak újratöltéséhez használja a következőt: `score.py` `reload()` . A következő kód például újratölti a szolgáltatáshoz tartozó parancsfájlt, majd adatokat küld neki. Az adatgyűjtés a frissített `score.py` fájllal történik:

> [!IMPORTANT]
> A `reload` metódus csak helyi központi telepítések esetén érhető el. A központi telepítés másik számítási célra való frissítésével kapcsolatos információkért lásd: [a webszolgáltatások frissítése](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> A parancsfájl a `InferenceConfig` szolgáltatás által használt objektum által megadott helyről lesz újratöltve.

A modell, a Conda-függőségek vagy a telepítési konfiguráció módosításához használja az [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-)t. A következő példa frissíti a szolgáltatás által használt modellt:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>A szolgáltatás törlése

A szolgáltatás törléséhez használja a [delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--)t.

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>A Docker-napló ellenőrzése

A Service objektumból kinyomtathatja a Docker-motor részletes naplófájljait. Megtekintheti az ACI-, AK-és helyi központi telepítések naplóját. Az alábbi példa bemutatja, hogyan lehet kinyomtatni a naplókat.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
Ha úgy látja, hogy a sor többször is `Booting worker with pid: <pid>` előfordul a naplókban, az azt jelenti, hogy nincs elég memória a feldolgozó elindításához.
A hibát a következő értékének növelésével kezelheti: `memory_gb``deployment_config`
 
## <a name="container-cannot-be-scheduled"></a>A tároló nem ütemezhető

Egy szolgáltatás Azure Kubernetes szolgáltatásbeli számítási célra való telepítésekor a Azure Machine Learning megkísérli a szolgáltatás ütemezését a kért mennyiségű erőforrással. Ha 5 perc elteltével nem érhetők el csomópontok a fürtben a megfelelő mennyiségű erőforrással, a telepítés sikertelen lesz az üzenettel `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Ezt a hibát a további csomópontok hozzáadásával, a csomópontok SKU-jának módosításával vagy a szolgáltatás erőforrás-követelményeinek módosításával kezelheti. 

A hibaüzenet általában azt jelzi, hogy melyik erőforrásra van szüksége – például ha megjelenik egy hibaüzenet, amely azt jelzi, hogy `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` a szolgáltatás a GPU-t igényli, és a fürtben három olyan csomópont van, amely nem rendelkezik elérhető GPU-val. Ez több csomópont hozzáadásával oldható meg, ha GPU SKU-t használ, váltson GPU-t támogató SKU-ra, ha nem a GPU-k használatára van szüksége, és nem módosítja a környezetet.  

## <a name="service-launch-fails"></a>A szolgáltatás indítása sikertelen

A rendszerkép sikeres felépítése után a rendszer megkísérli a tároló indítását a telepítési konfiguráció alapján. A tároló indítási folyamatának részeként a `init()` rendszer meghívja a pontozási parancsfájl függvényét. Ha a függvény nem észlelt kivételeket `init()` tartalmaz, előfordulhat, hogy a **CrashLoopBackOff** hiba jelenik meg a hibaüzenetben.

A naplók ellenőrzéséhez használja a [Docker-napló vizsgálata](#dockerlog) szakaszban található információkat.

## <a name="function-fails-get_model_path"></a>A függvény sikertelen: get_model_path ()

Gyakran előfordul, `init()` hogy a pontozási parancsfájl függvényében a [model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) függvényt egy modell fájljának vagy a tárolóban található Model Files mappa megkeresésére hívja meg. Ha a modell fájlja vagy mappája nem található, a függvény sikertelen lesz. A hiba hibakeresésének legegyszerűbb módja a következő Python-kód futtatása a Container shellben:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Ez a példa megjeleníti a helyi elérési utat (ahhoz képest `/var/azureml-app` ) a tárolóban, ahol a pontozási parancsfájl a modell fájljának vagy mappájának megkeresésére vár. Ezt követően ellenőrizheti, hogy a fájl vagy mappa valóban a várt-e.

Ha a naplózási szint HIBAKERESÉSét állítja be, akkor további információk is naplózhatók, ami hasznos lehet a hiba azonosításához.

## <a name="function-fails-runinput_data"></a>A függvény sikertelen: Futtatás (input_data)

Ha a szolgáltatás sikeresen telepítve van, de összeomlik a pontozási végpontba való adatküldés során, a függvényben hozzáadhat egy Error befogási utasítást, `run(input_data)` hogy a részletes hibaüzenetet adja vissza helyette. Például:

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Megjegyzés**: Ha a hívásból hibaüzeneteket ad vissza, `run(input_data)` csak hibakeresési célokat kell végrehajtania. Biztonsági okokból az éles környezetben nem adhat vissza hibaüzeneteket.

## <a name="http-status-code-502"></a>HTTP-állapotkód 502

A 502 állapotkód azt jelzi, hogy a szolgáltatás kivételt okozott, vagy összeomlott a `run()` score.py fájl metódusában. A cikkben található információk segítségével a fájl hibakeresését folytathatja.

## <a name="http-status-code-503"></a>HTTP-állapotkód 503

Az Azure Kubernetes szolgáltatás központi telepítései támogatják az automatikus skálázást, amely lehetővé teszi a replikák hozzáadását a további terhelések támogatásához. Az automatikus skálázás azonban úgy van kialakítva, hogy kezelje a terhelés **fokozatos** változásait. Ha a kérések másodpercenként nagy számú tüskéket kapnak, az ügyfelek a 503-as HTTP-állapotkódot kapják meg.

Két olyan dolog van, amely segíthet megelőzni a 503-es állapotkódot:

* Módosítsa a kihasználtsági szintet, amelynél az automatikus skálázás új replikákat hoz létre.
    
    Alapértelmezés szerint az automatikus skálázási cél kihasználtsága 70%-ra van állítva, ami azt jelenti, hogy a szolgáltatás legfeljebb 30%-os terhelést képes kezelni a másodpercenkénti kérésekben (RPS). A kihasználtsági célt úgy állíthatja be, `autoscale_target_utilization` hogy alacsonyabb értéket állít be.

    > [!IMPORTANT]
    > Ez a változás nem eredményezi a replikák *gyorsabb*létrehozását. Ehelyett alacsonyabb kihasználtsági küszöbértékben jönnek létre. Ahelyett, hogy megvárná, amíg a szolgáltatás 70%-ot nem használ, az érték 30%-ra való módosítása a replikákat a 30%-os kihasználtság esetén hozza létre.
    
    Ha a webszolgáltatás már használja a jelenlegi maximális replikákat, és továbbra is a 503-es állapotkódot látja, növelje az `autoscale_max_replicas` értéket a replikák maximális számának növeléséhez.

* Módosítsa a replikák minimális számát. A minimális replikák növelése nagyobb készletet biztosít a bejövő tüskék kezeléséhez.

    A replikák minimális számának növeléséhez állítsa `autoscale_min_replicas` magasabb értékre. A szükséges replikák kiszámításához használja a következő kódot, és cserélje le az értékeket a projekthez tartozó értékekre:

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Ha az új minimális replikánál nagyobb kérelmeket kap, akkor a 503s újra megjelenhet. Ha például a szolgáltatás felé irányuló forgalom növekszik, akkor előfordulhat, hogy növelnie kell a minimális replikákat.

A és a for beállításával kapcsolatos további információkért `autoscale_target_utilization` `autoscale_max_replicas` `autoscale_min_replicas` tekintse meg a [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) -modul referenciáját.

## <a name="http-status-code-504"></a>HTTP-állapotkód 504

A 504 állapotkód azt jelzi, hogy a kérelem túllépte az időkorlátot. Az alapértelmezett időkorlát 1 perc.

A felesleges hívások eltávolításához módosítsa a score.py, vagy próbálja meg felgyorsítani a szolgáltatást. Ha ezek a műveletek nem orvosolják a problémát, a jelen cikkben található információk segítségével a score.py-fájl hibakeresését végezheti el. A kód nem válaszoló állapotban vagy végtelen hurokban is lehet.

## <a name="advanced-debugging"></a>Speciális hibakeresés

Bizonyos esetekben előfordulhat, hogy interaktívan kell hibakeresést végeznie a modell üzembe helyezésében található Python-kóddal. Ha például a bejegyzési parancsfájl meghibásodik, és az ok nem határozható meg további naplózással. A Visual Studio Code és a debugpy használatával a Docker-tárolón belül futó kód is csatolható. További információkért tekintse meg az [interaktív hibakeresést a vs Code útmutatóban](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments).

## <a name="next-steps"></a>Következő lépések

További információk az üzembe helyezésről:

* [Üzembe helyezés és hol](how-to-deploy-and-where.md)
* [Oktatóanyag: a betanítási & modellek üzembe helyezése](tutorial-train-models-with-aml.md)

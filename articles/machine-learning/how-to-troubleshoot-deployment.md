---
title: Webszolgáltatás központi telepítésének hibakeresése
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy miként lehet megkerülni, megoldani és elhárítani az általános Docker-telepítési hibákat az Azure Kubernetes Service és a Azure Container Instances használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: jmartens
ms.date: 11/02/2020
ms.topic: troubleshooting
ms.custom: contperfq4, devx-track-python, deploy
ms.openlocfilehash: dfbfea22738e6aeb0df31ad941b2ff10e53795a4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311297"
---
# <a name="troubleshoot-model-deployment"></a>Modell központi telepítésének megoldása

Megtudhatja, hogyan oldhatja fel az általános Docker-telepítési hibákat a Azure Container Instances (ACI) és az Azure Kubernetes szolgáltatással (ak) a Azure Machine Learning használatával.

## <a name="prerequisites"></a>Előfeltételek

* Egy **Azure-előfizetés**. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).
* A [Azure Machine learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* Az [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)-vel.
* A [Azure Machine learning CLI-bővítménye](reference-azure-machine-learning-cli.md).
* Helyi hibakereséshez rendelkeznie kell egy működő Docker-telepítéssel a helyi rendszeren.

    A Docker-telepítés ellenőrzéséhez használja a parancsot `docker run hello-world` egy terminálról vagy parancssorból. A Docker telepítésével vagy a Docker-hibák elhárításával kapcsolatos információkért tekintse meg a [Docker dokumentációját](https://docs.docker.com/).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>A Machine learning-modellek Docker-telepítésének lépései

Ha Azure Machine Learningon nem helyi számítási feladatokra helyezi üzembe a modellt, a következő dolgok történnek:

1. A InferenceConfig lévő környezetek objektumban megadott Docker a rendszer elküldi a felhőbe, a forrás könyvtára tartalmával együtt.
1. Ha egy korábban létrehozott rendszerkép nem érhető el a tároló beállításjegyzékében, egy új Docker-rendszerkép jön a felhőben, és a munkaterület alapértelmezett tárolójának beállításjegyzékében tárolódik.
1. A rendszer letölti a Docker-rendszerképet a tároló-beállításjegyzékből a számítási célra.
1. A munkaterület alapértelmezett blob-tárolója csatlakoztatva van a számítási célhoz, így hozzáférést biztosít a regisztrált modellekhez
1. A webkiszolgáló inicializálása a bejegyzési parancsfájl függvényének futtatásával történik `init()`
1. Ha a telepített modell megkapja a kérelmet, a `run()` függvény kezeli ezt a kérelmet.

Helyi központi telepítés használata esetén a fő különbség az, hogy a tároló rendszerképe a helyi gépre épül, ezért a helyi telepítéshez szükséges Docker-nek kell lennie.

Ezeknek a magas szintű lépéseknek a megértése segít megérteni, hogy hol történnek a hibák.

## <a name="get-deployment-logs"></a>Üzembe helyezési naplók beolvasása

A hibák hibakeresésének első lépése a telepítési naplók beszerzése. Először kövesse az [itt található utasításokat](how-to-deploy-and-where.md#connect-to-your-workspace) a munkaterülethez való kapcsolódáshoz.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Az üzembe helyezett webszolgáltatásból származó naplók beszerzéséhez tegye a következőket:

```bash
az ml service get-logs --verbose --workspace-name <my workspace name> --name <service name>
```

# <a name="python"></a>[Python](#tab/python)


Feltételezve, hogy van egy nevű objektuma `azureml.core.Workspace` `ws` , a következőket teheti:

```python
print(ws.webservices)

# Choose the webservice you are interested in

from azureml.core import Webservice

service = Webservice(ws, '<insert name of webservice>')
print(service.get_logs())
```

---

## <a name="debug-locally"></a>Helyi hibakeresés

Ha a modell ACI-vagy AK-beli üzembe helyezése során problémák merülnek fel, telepítse helyi webszolgáltatásként. Helyi webszolgáltatás használatával könnyebben háríthatók el a problémák.

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

Ha saját Conda-specifikációs YAML határoz meg, sorolja fel a azureml alapértelmezett verzióját (>= 1.0.45) pip-függőségként. Ez a csomag a modell webszolgáltatásként való üzemeltetéséhez szükséges.

Ezen a ponton a megszokott módon dolgozhat a szolgáltatással. A következő kód az adatok szolgáltatásba küldését mutatja be:

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

A modell, a Conda-függőségek vagy a telepítési konfiguráció módosításához használja az [Update ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueupdate--args-)t. A következő példa frissíti a szolgáltatás által használt modellt:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>A szolgáltatás törlése

A szolgáltatás törléséhez használja a [delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--)t.

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> A Docker-napló ellenőrzése

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

Amikor szolgáltatást helyez üzembe az Azure Kubernetes Service egyik számítási céljára, az Azure Machine Learning megkísérli a kért mennyiségű erőforrással ütemezni a szolgáltatást. Ha a fürtben nincsenek elérhető csomópontok a megfelelő mennyiségű erőforrás 5 perc után, akkor a telepítés sikertelen lesz. A hibaüzenet a következő: `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00` . Ezt a hibát a további csomópontok hozzáadásával, a csomópontok SKU-jának módosításával vagy a szolgáltatás erőforrás-követelményeinek módosításával kezelheti. 

A hibaüzenet általában azt jelzi, hogy melyik erőforrásra van szüksége – például ha megjelenik egy hibaüzenet, amely azt jelzi, hogy `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` a szolgáltatás a GPU-t igényli, és a fürtben három olyan csomópont van, amely nem rendelkezik elérhető GPU-val. Ez több csomópont hozzáadásával oldható meg, ha GPU SKU-t használ, váltson GPU-t támogató SKU-ra, ha nem a GPU-k használatára van szüksége, és nem módosítja a környezetet.  

## <a name="service-launch-fails"></a>A szolgáltatás indítása sikertelen

A rendszerkép sikeres felépítése után a rendszer megkísérli a tároló indítását a telepítési konfiguráció alapján. A tároló indítási folyamatának részeként a `init()` rendszer meghívja a pontozási parancsfájl függvényét. Ha a függvény nem észlelt kivételeket `init()` tartalmaz, előfordulhat, hogy a **CrashLoopBackOff** hiba jelenik meg a hibaüzenetben.

A naplók ellenőrzéséhez használja a [Docker-napló vizsgálata](#dockerlog) szakaszban található információkat.

## <a name="function-fails-get_model_path"></a>A függvény sikertelen: get_model_path ()

Gyakran előfordul, `init()` hogy a pontozási parancsfájl függvényében a [Model.get_model_path ()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) függvényt a rendszer megkeresi a modell fájlját vagy a tárolóban a modell fájljainak mappáját. Ha a modellfájl vagy -mappa nem található, a függvény meghiúsul. A hiba hibakeresésének legegyszerűbb módja a következő Python-kód futtatása a Container shellben:

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

**Megjegyzés** : Ha a hívásból hibaüzeneteket ad vissza, `run(input_data)` csak hibakeresési célokat kell végrehajtania. Biztonsági okokból az éles környezetben nem adhat vissza hibaüzeneteket.

## <a name="http-status-code-502"></a>502-es HTTP-állapotkód

A 502 állapotkód azt jelzi, hogy a szolgáltatás kivételt okozott, vagy összeomlott a `run()` score.py fájl metódusában. A cikkben található információk segítségével a fájl hibakeresését folytathatja.

## <a name="http-status-code-503"></a>503-as HTTP-állapotkód

Az Azure Kubernetes szolgáltatás központi telepítései támogatják az automatikus skálázást, amely lehetővé teszi a replikák hozzáadását a további terhelések támogatásához. Az automatikus skálázás a terhelés **fokozatos** változásainak kezelésére szolgál. Ha a kérések másodpercenként nagy számú tüskéket kapnak, az ügyfelek a 503-as HTTP-állapotkódot kapják meg. Annak ellenére, hogy az autoskálázás gyorsan működik, a további tárolók létrehozásához nagy mennyiségű időt vesz igénybe.

A vertikális felskálázásra vagy lefelé irányuló döntések az aktuális tároló-replikák kihasználtságán alapulnak. A foglalt replikák (egy kérelem feldolgozása) száma osztva az aktuális replikák teljes számával. Ha ez a szám meghaladja a számot `autoscale_target_utilization` , a rendszer több replikát hoz létre. Ha alacsonyabb, a replikák csökkennek. A replikák hozzáadására vonatkozó döntések lelkesek és gyorsak (körülbelül 1 másodperc). A replikák eltávolítására vonatkozó döntések konzervatívak (körülbelül 1 perc). Alapértelmezés szerint az automatikus skálázási cél kihasználtsága **70%-ra** van állítva, ami azt jelenti, hogy a szolgáltatás **legfeljebb 30%** -os terhelést képes kezelni a másodpercenkénti kérésekben (RPS).

Két olyan dolog van, amely segíthet megelőzni a 503-es állapotkódot:

> [!TIP]
> Ezt a két megközelítést külön vagy kombinálva lehet használni.

* Módosítsa a kihasználtsági szintet, amelynél az automatikus skálázás új replikákat hoz létre. A kihasználtsági célt úgy állíthatja be, `autoscale_target_utilization` hogy alacsonyabb értéket állít be.

    > [!IMPORTANT]
    > Ez a változás nem eredményezi a replikák *gyorsabb* létrehozását. Ehelyett alacsonyabb kihasználtsági küszöbértékben jönnek létre. Ahelyett, hogy megvárná, amíg a szolgáltatás 70%-ot nem használ, az érték 30%-ra való módosítása a replikákat a 30%-os kihasználtság esetén hozza létre.
    
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

A és a for beállításával kapcsolatos további információkért `autoscale_target_utilization` `autoscale_max_replicas` `autoscale_min_replicas` tekintse meg a [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py) -modul referenciáját.

## <a name="http-status-code-504"></a>504-es HTTP-állapotkód

A 504 állapotkód azt jelzi, hogy a kérelem túllépte az időkorlátot. Az alapértelmezett időkorlát 1 perc.

A felesleges hívások eltávolításához módosítsa a score.py, vagy próbálja meg felgyorsítani a szolgáltatást. Ha ezek a műveletek nem orvosolják a problémát, a jelen cikkben található információk segítségével a score.py-fájl hibakeresését végezheti el. A kód nem válaszoló állapotban vagy végtelen hurokban is lehet.

## <a name="advanced-debugging"></a>Speciális hibakeresés

Előfordulhat, hogy interaktív hibakeresést kell végeznie a modell üzemelő példányában található Python-kódon. Ha például a bejegyzési parancsfájl meghibásodik, és az ok nem határozható meg további naplózással. A Visual Studio Code és a debugpy használatával a Docker-tárolón belül futó kód is csatolható.

További információkért tekintse meg az [interaktív hibakeresést a vs Code útmutatóban](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments).

## <a name="model-deployment-user-forum"></a>[Modell üzembe helyezésének felhasználói fóruma](/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Következő lépések

További információk az üzembe helyezésről:

* [Üzembe helyezés és hol](how-to-deploy-and-where.md)
* [Oktatóanyag: a betanítási & modellek üzembe helyezése](tutorial-train-models-with-aml.md)
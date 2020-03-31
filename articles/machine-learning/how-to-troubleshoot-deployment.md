---
title: Üzembehelyezési hibaelhárítási útmutató
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan gazdálkodhat, oldhat meg és háríthat el általános Docker-telepítési hibákat az Azure Kubernetes-szolgáltatásés az Azure Machine Learning használatával az Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: fab46f7d7ae74ad643ce3f122b27b0dc767f5a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399684"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Az Azure Kubernetes-szolgáltatás és az Azure Container Instances üzembe helyezésének hibaelhárítása

Ismerje meg, hogyan kerüli meg vagy oldhatja meg a Docker gyakori telepítési hibáit az Azure Container Instances (ACI) és az Azure Kubernetes Service (AKS) használatával az Azure Machine Learning használatával.

Amikor egy modellt telepít az Azure Machine Learningben, a rendszer számos feladatot hajt végre.

A modell üzembe helyezésének ajánlott és legnaprakészebb megközelítése a [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API-n keresztül [történik, amely egy környezeti](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) objektumot használ bemeneti paraméterként. Ebben az esetben a szolgáltatás létrehoz egy alap docker-rendszerképet az üzembe helyezési szakaszban, és a szükséges modelleket egy hívásban csatlakoztatja. Az alapvető telepítési feladatok a következők:

1. Regisztrálja a modellt a munkaterületmodell beállításjegyzékében.

2. Következtetési konfiguráció definiálása:
    1. Hozzon létre egy [környezeti](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) objektumot a környezetben megadott yaml fájl alapján, vagy használja az egyik beszerzett környezetünket.
    2. Hozzon létre egy következtetési konfigurációt (InferenceConfig objektum) a környezet és a pontozási parancsfájl alapján.

3. Telepítse a modellt az Azure Container Instance (ACI) szolgáltatásvagy az Azure Kubernetes service (AKS) szolgáltatásba.

Erről a folyamatról a [Modellkezelés](concept-model-management-and-deployment.md) bevezetésben olvashat bővebben.

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure-előfizetés**. Ha nem rendelkezik ilyen, próbálja ki az [Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)
* Az [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Az [Azure Machine Learning CLI-bővítménye.](reference-azure-machine-learning-cli.md)
* A helyi hibakereséshez a helyi rendszeren működő Docker-telepítéssel kell rendelkeznie.

    A Docker telepítésének ellenőrzéséhez `docker run hello-world` használja a parancsot egy terminálról vagy parancssorból. A Docker telepítéséről és a Docker-hibák elhárításáról a [Docker dokumentációjában](https://docs.docker.com/)olvashat.

## <a name="before-you-begin"></a>Előkészületek

Ha bármilyen probléma lép fel, az első teendő az, hogy a telepítési feladat (előző leírt) lebontása a probléma elkülönítéséhez szükséges egyes lépésekre.

Feltéve, hogy az új/ajánlott üzembe helyezési módszert használja a [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) API-n keresztül [egy környezeti](https://docs.microsoft.com/azure/machine-learning/service/how-to-use-environments) objektummal bemeneti paraméterként, a kód három fő lépésre bontható fel:

1. Regisztrálja a modellt. Itt van néhány minta kód:

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. A központi telepítéshez a következtetéskonfiguráció meghatározása:

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

Miután a telepítési folyamatot különálló feladatokra bontotta, megvizsgálhatjuk a leggyakoribb hibákat.

## <a name="debug-locally"></a>Hibakeresés helyileg

Ha problémákat tapasztal egy modell aci vagy AKS üzembe helyezésével, próbálja meg helyi webszolgáltatásként telepíteni. A helyi webszolgáltatás használata megkönnyíti a problémák elhárítását. A modellt tartalmazó Docker-rendszerkép letöltődik, és elindul a helyi rendszeren.

> [!WARNING]
> A helyi webszolgáltatás-telepítések nem támogatottak éles környezetben.

Helyi telepítéshez módosítsa a kódot, `LocalWebservice.deploy_configuration()` hogy hozzon létre egy központi telepítési konfigurációt. Ezután `Model.deploy()` használja a szolgáltatás üzembe helyezéséhez. A következő példa helyi webszolgáltatásként telepít egy modellt (a modellváltozóban található):

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

Kérjük, vegye figyelembe, hogy ha saját conda specifikációt, YAML-t határoz meg, akkor az azureml-defaults-t >= 1.0.45 verzióval kell felsorolnia pip-függőségként. Ez a csomag tartalmazza a modell webszolgáltatásként való üzemeltetéséhez szükséges funkciókat.

Ezen a ponton a szokásos módon dolgozhat a szolgáltatással. A következő kód például bemutatja az adatok küldését a szolgáltatásnak:

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

A Python-környezet testreszabásáról további információt a [Környezetek létrehozása és kezelése betanításhoz és üzembe helyezéshez című témakörben talál.](how-to-use-environments.md) 

### <a name="update-the-service"></a>A szolgáltatás frissítése

Előfordulhat, hogy a helyi tesztelés `score.py` során frissítenie kell a fájlt a naplózás hozzáadásához, vagy meg kell kísérelnie a felfedezett problémák megoldását. A fájl módosítási `score.py` módosításának `reload()`újratöltéséhez használja a használatát. Például a következő kód újratölti a szolgáltatás parancsfájlját, majd adatokat küld neki. Az adatok pontozott a `score.py` frissített fájl:

> [!IMPORTANT]
> A `reload` metódus csak helyi telepítések esetén érhető el. A központi telepítés másik számítási célra történő frissítéséről a [Telepítési modellek](how-to-deploy-and-where.md#update)frissítési szakaszában talál tájékoztatást.

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> A parancsfájl újratöltődik a `InferenceConfig` szolgáltatás által használt objektum által megadott helyről.

A modell, a Conda-függőségek vagy a központi telepítési konfiguráció módosításához használja a [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-)( frissítés) szolgáltatást. A következő példa frissíti a szolgáltatás által használt modellt:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>A szolgáltatás törlése

A szolgáltatás törléséhez használja a [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--)parancsot.

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a>A Docker-napló vizsgálata

A szolgáltatásobjektumból részletes Docker-motornapló-üzeneteket nyomtathat ki. Megtekintheti az ACI, Az AKS és a Helyi központi telepítések naplóját. A következő példa bemutatja, hogyan kell kinyomtatni a naplókat.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>A szolgáltatás indítása sikertelen

A lemezkép sikeres létrehozása után a rendszer megkísérli elindítani a tárolót a központi telepítési konfiguráció használatával. A tároló indítási folyamatának `init()` részeként a rendszer a pontozási parancsfájl ban lévő függvényt hívja meg. Ha a függvényben nincsenek `init()` kivételek, a hibaüzenetben **a CrashLoopBackOff** hiba jelenhet meg.

Használja a [Docker-napló vizsgálata](#dockerlog) szakaszban található információkat a naplók ellenőrzéséhez.

## <a name="function-fails-get_model_path"></a>A függvény sikertelen: get_model_path()

Gyakran előfordul, `init()` hogy a függvény a pontozási parancsfájl, [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) függvény hívják, hogy keresse meg a modell fájlt, vagy egy mappát a modell fájlokat a tárolóban. Ha a modellfájl vagy mappa nem található, a függvény sikertelen lesz. A hiba hibakeresésének legegyszerűbb módja az alábbi Python-kód futtatása a tárolóhéjban:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Ez a példa kinyomtatja a `/var/azureml-app`helyi elérési utat (ehhez viszonyítva) abban a tárolóban, ahol a pontozási parancsfájl a modellfájl vagy -mappa megtalálását várja. Ezután ellenőrizheti, hogy a fájl vagy mappa valóban ott van-e, ahol várható.

Ha a naplózási szintet DEBUG-ra állítja, további információk naplózása lehet, ami hasznos lehet a hiba azonosításához.

## <a name="function-fails-runinput_data"></a>A függvény sikertelen: run(input_data)

Ha a szolgáltatás telepítése sikeresen megtörtént, de összeomlik, amikor adatokat küld a pontozási `run(input_data)` végpontra, hozzáadhat hibabefogási utasítást a függvényben, hogy az részletes hibaüzenetet adjon vissza. Példa:

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

**Megjegyzés:** A `run(input_data)` hívásból származó hibaüzenetek visszaadása csak hibakereséscéljából történhet. Biztonsági okokból éles környezetben nem szabad ilyen módon hibaüzeneteket visszaadni.

## <a name="http-status-code-502"></a>HTTP-állapotkód 502

Az 502-es állapotkód azt jelzi, hogy a `run()` szolgáltatás kivételt tett, vagy összeomlott a score.py fájl metódusában. A cikkben található információk segítségével hibakeresést folytatanak a fájlban.

## <a name="http-status-code-503"></a>HTTP-állapotkód 503

Az Azure Kubernetes-szolgáltatás központi telepítései támogatják az automatikus skálázást, amely lehetővé teszi a replikák hozzáadását a további terhelés támogatásához. Az automatikus méretező azonban a terhelés **fokozatos** változásainak kezelésére szolgál. Ha másodpercenként nagy ugrásokat kap a kérelmekben, az ügyfelek 503-as HTTP-állapotkódot kaphatnak.

Két dolog segíthet megelőzni az 503-as állapotkódokat:

* Módosítsa azt a kihasználtsági szintet, amelyen az automatikus skálázás új replikákat hoz létre.
    
    Alapértelmezés szerint az automatikus skálázás célkihasználtság van beállítva 70%, ami azt jelenti, hogy a szolgáltatás képes kezelni a kérelmek másodpercenkénti (RPS) legfeljebb 30%-os kiugrások. A kihasználtsági célt alacsonyabb `autoscale_target_utilization` értékre állításával állíthatja be.

    > [!IMPORTANT]
    > Ez a módosítás nem eredményezi a kópiák *gyorsabb*létrehozását. Ehelyett alacsonyabb kihasználtsági küszöbértékkel jönnek létre. Ahelyett, hogy megvárná, amíg a szolgáltatás 70%-os kihasználása, az érték módosítása 30%-os okoz replikák jön létre, ha 30%-os kihasználtság történik.
    
    Ha a webszolgáltatás már használja az aktuális maximális replikákat, és még `autoscale_max_replicas` mindig 503 állapotkódokat lát, növelje az értéket a replikák maximális számának növeléséhez.

* Módosítsa a kópiák minimális számát. A minimális replikák növelése nagyobb készletet biztosít a bejövő csúcsok kezeléséhez.

    A replikák minimális számának `autoscale_min_replicas` növeléséhez állítsa magasabb értékre. A szükséges replikákat a következő kód dal számíthatja ki, amely a projektre jellemző értékekre cseréli az értékeket:

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
    > Ha az új minimális replikák által kezelhetőnél nagyobb kérési csúcsokat kap, előfordulhat, hogy ismét 503-as tanéveket kap. Például a szolgáltatás forgalmának növekedésével szükség lehet a minimális replikák növelésére.

A `autoscale_target_utilization`beállítással és `autoscale_max_replicas`az `autoscale_min_replicas` [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) modul hivatkozási útmutatójával kapcsolatos további információkért.

## <a name="http-status-code-504"></a>HTTP 504-es állapotkód

Az 504-es állapotkód azt jelzi, hogy a kérelem időkimenőt ért. Az alapértelmezett időhosszabbítás 1 perc.

Növelheti az időtúllépést, vagy megpróbálhatja felgyorsítani a szolgáltatást a score.py módosításával a szükségtelen hívások eltávolításához. Ha ezek a műveletek nem oldják meg a problémát, a cikkben található információk alapján hibakeresésre használja a score.py fájlt. A kód lehet lógott állapotban vagy végtelen ciklusban.

## <a name="advanced-debugging"></a>Speciális hibakeresés

Bizonyos esetekben előfordulhat, hogy interaktívan hibakeresést kell, hogy a modell központi telepítésében található Python-kódot. Ha például a bejegyzésparancsfájl hibás, és az ok nem határozható meg további naplózással. A Visual Studio Code és a Python Tools for Visual Studio (PTVSD) használatával csatolhatja a Docker-tárolóban futó kódot.

> [!IMPORTANT]
> Ez a hibakeresési módszer nem `Model.deploy()` működik `LocalWebservice.deploy_configuration` a modell helyi használata és telepítése során. Ehelyett létre kell hoznia egy lemezképet a [Model.package()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-) metódushasználatával.

A helyi webszolgáltatás-telepítések a helyi rendszeren működő Docker-telepítést igényelnek. A Docker használatával kapcsolatos további információkért tekintse meg a [Docker dokumentációját.](https://docs.docker.com/)

### <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

1. A Python Tools for Visual Studio (PTVSD) telepítéséhez a helyi VS Code fejlesztői környezetben a következő paranccsal:

    ```
    python -m pip install --upgrade ptvsd
    ```

    A PTVSD VS-kóddal való használatáról a [Távoli hibakeresés](https://code.visualstudio.com/docs/python/debugging#_remote-debugging)című témakörben talál további információt.

1. Ha a VS Code-ot a Docker-lemezképpel való kommunikációra szeretné konfigurálni, hozzon létre egy új hibakeresési konfigurációt:

    1. A VS-kód lapon válassza a __Debug menüt,__ majd a __Konfigurációk megnyitása parancsot.__ Megnyílik __egy launch.json__ nevű fájl.

    1. Az __launch.json__ fájlban keresse meg `"configurations": [`a tartalmazó sort, és utána szúrja be a következő szöveget:

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Ha a konfigurációk szakaszban már vannak más bejegyzések, a beszúrt kód után adjon hozzá vesszőt (,) .

        Ez a szakasz az 5678-as port használatával csatlakozik a Docker-tárolóhoz.

    1. Mentse a __launch.json__ fájlt.

### <a name="create-an-image-that-includes-ptvsd"></a>PTVSD-t tartalmazó kép létrehozása

1. Módosítsa a központi telepítés conda-környezetét, hogy az tartalmazza a PTVSD-t. A következő példa bemutatja `pip_packages` a hozzáadást a paraméter használatával:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.45', 'ptvsd'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. A PTVSD indításához és a kapcsolatra való várakozáshoz a `score.py` szolgáltatás indításakor adja hozzá a következőt a fájl tetejére:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Hozzon létre egy lemezképet a környezet definíciója alapján, és húzza le a lemezképet a helyi beállításjegyzékbe. A hibakeresés során előfordulhat, hogy a lemezképfájljait újra meg kell változtatnia anélkül, hogy újra létre kellene hoznia azokat. Ha egy szövegszerkesztőt (vim) szeretne telepíteni `Environment.docker.base_image` a `Environment.docker.base_dockerfile` Docker-lemezképbe, használja a és a tulajdonságokat:

    > [!NOTE]
    > Ebben a példában feltételezi, hogy `ws` az Azure `model` Machine Learning-munkaterületre mutat, és ez az üzembe helyezett modell. A `myenv.yml` fájl az 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    A lemezkép létrehozása és letöltése után a kép elérési útja (beleértve a tárházat, a nevet és a címkét, amely ebben az esetben egyben kivonata is) a következőhöz hasonló üzenetben jelenik meg:

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. A képpel való munka megkönnyítése érdekében a következő paranccsal adjon hozzá címkét. Cserélje `myimagepath` le az előző lépés helyértékére.

    ```bash
    docker tag myimagepath debug:1
    ```

    A további lépésekhez a helyi lemezképre `debug:1` hivatkozhat a teljes kép elérési útja helyett.

### <a name="debug-the-service"></a>Hibakeresés a szolgáltatásban

> [!TIP]
> Ha időtúllépést állít be a PTVSD-kapcsolathoz a fájlban, az `score.py` időtúllépés lejárta előtt csatlakoztatnia kell a VS Code-ot a hibakeresési munkamenethez. Indítsa el a VS-kódot, nyissa meg a helyi példányát, `score.py`állítson be egy töréspontot, és állítsa készen az indulásra, mielőtt az ebben a szakaszban leírt lépéseket használná.
>
> A hibakeresésről és a töréspontok beállításáról a [Hibakeresés](https://code.visualstudio.com/Docs/editor/debugging)című témakörben talál további információt.

1. Ha docker-tárolót szeretne elindítani a rendszerkép használatával, használja a következő parancsot:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Ha a VS-kódot a tárolón belül szeretné csatolni a PTVSD-hez, nyissa meg a VS Code-ot, és használja az F5 gombot, vagy válassza a __Debug__lehetőséget. Amikor a rendszer kéri, válassza ki az __Azure Machine Learning: Docker Debug__ konfiguráció. Az oldalsávon is kiválaszthatja a hibakeresési ikont, az __Azure Machine Learning: Docker Debug__ bejegyzést a Hibakeresési legördülő menüből, majd a zöld nyíllal csatolhatja a hibakeresőt.

    ![A hibakeresési ikon, a hibakeresés indítása gomb és a konfigurációválasztó](./media/how-to-troubleshoot-deployment/start-debugging.png)

Ezen a ponton a VS-kód csatlakozik a PTVSD-hez a Docker-tárolón belül, és megáll a korábban beállított töréspontnál. Most már lépésben a kódot, mert fut, változók megtekintése, stb.

A VS Code python-hibakereséssel kapcsolatos további tudnivalókért olvassa el [a Python-kód hibakeresése című témakört.](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>A tárolófájlok módosítása

A lemezképfájljainak módosításához csatolhatja a futó tárolóhoz, és végrehajthatja a bash rendszerhéjat. Innen a vim segítségével szerkesztheti a fájlokat:

1. A futó tárolóhoz való csatlakozáshoz és a tárolóban lévő bash rendszerhéj elindításához használja a következő parancsot:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. A szolgáltatás által használt fájlok megkereséséhez használja a következő parancsot a tárolóban `/var/azureml-app`lévő bash rendszerhéjból, ha az alapértelmezett könyvtár eltér a következőtől:

    ```bash
    cd /var/azureml-app
    ```

    Innen a vim segítségével szerkesztheti `score.py` a fájlt. A vim használatával kapcsolatos további tudnivalókért olvassa el a Vim szerkesztő használata című [témakört.](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html)

1. A tároló módosításai általában nem maradnak meg. A módosítások mentéséhez használja a következő parancsot, mielőtt kilépne a fenti lépésből (azaz egy másik héjban) megkezdett rendszerhéjból:

    ```bash
    docker commit debug debug:2
    ```

    Ez a parancs létrehoz `debug:2` egy új, a szerkesztéseket tartalmazó képet.

    > [!TIP]
    > A módosítások életbe lépése előtt le kell állítania az aktuális tárolót, és el kell kezdenie használni az új verziót.

1. Ügyeljen arra, hogy a tárolóban lévő fájlokon végzett módosítások szinkronban maradjanak a VS Code által használt helyi fájlokkal. Ellenkező esetben a hibakereső-élmény nem a várt módon fog működni.

### <a name="stop-the-container"></a>A tároló leállítása

A tároló leállításához használja a következő parancsot:

```bash
docker stop debug
```

## <a name="next-steps"></a>További lépések

További információk az üzembe helyezésről:

* [Hogyan és hol kell telepíteni](how-to-deploy-and-where.md)
* [Oktatóanyag: A & a modellek üzembe helyezése](tutorial-train-models-with-aml.md)

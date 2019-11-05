---
title: Üzembehelyezési hibaelhárítási útmutató
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy miként lehet megkerülni, megoldani és elhárítani a Common Docker-telepítési hibákat az Azure Kubernetes szolgáltatással, és Azure Container Instances a Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 10/25/2019
ms.custom: seodec18
ms.openlocfilehash: 3a79c95d627bbdec3a91a1d048a48ff061b308ca
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489364"
---
# <a name="troubleshooting-azure-machine-learning-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Az Azure Kubernetes Service és a Azure Container Instances üzemelő példány hibaelhárítása Azure Machine Learning

Megtudhatja, hogyan használhatja a Docker-telepítési hibákat a Azure Container Instances (ACI) és az Azure Kubernetes szolgáltatással (ak) a Azure Machine Learning használatával.

Azure Machine Learning-modell telepítésekor a rendszer számos feladatot hajt végre. A központi telepítési feladatok a következők:

1. Regisztrálja a modellt a munkaterület-modell beállításjegyzékében.

2. Hozzon létre egy Docker-rendszerképet, beleértve a következőket:
    1. Töltse le a regisztrált modellt a beállításjegyzékből. 
    2. Hozzon létre egy Docker egy Python-környezettel a környezeti YAML fájlban megadott függőségek alapján.
    3. Adja hozzá a modell fájljait és a Docker megadott pontozási szkriptet.
    4. Hozzon létre egy új Docker-rendszerképet a Docker használatával.
    5. Regisztrálja a Docker-rendszerképet a munkaterülethez társított Azure Container Registry.

    > [!IMPORTANT]
    > A programkódtól függően a rendszerkép létrehozása automatikusan történik a bevitel nélkül.

3. Telepítse a Docker-rendszerképet az Azure Container instance (ACI) szolgáltatásba vagy az Azure Kubernetes szolgáltatásba (ak).

4. Indítson el egy új tárolót (vagy tárolót) ACI-ban vagy AK-ban. 

További információ a folyamatról a [modellkezelés](concept-model-management-and-deployment.md) bevezetésében.

## <a name="before-you-begin"></a>Előzetes teendők

Ha bármilyen problémába ütközik, az első lépés az, hogy az üzembe helyezési feladatot (előző Leírás) a probléma elkülönítése érdekében egyedi lépésekbe bontsa.

Ha a [webszolgáltatás. Deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) API-t, vagy a [webservice. deploy_from_model ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) API-t használja, akkor az üzembe helyezés a feladatok során is hasznos lehet, mivel mindkét függvény egyetlen műveletként hajtja végre a fenti lépéseket. Általában ezek az API-k kényelmesek, de segít megszüntetni az alábbi API-hívásokkal végzett hibaelhárítási lépéseket.

1. Regisztrálja a modellt. Íme néhány mintakód:

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Hozza létre a rendszerképet. Íme néhány mintakód:

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      entry_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. A rendszerkép üzembe helyezése szolgáltatásként. Íme néhány mintakód:

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

Miután megszakította az üzembe helyezés folyamatát az egyes feladatokra, megvizsgáljuk a leggyakoribb hibákat.

## <a name="image-building-fails"></a>A rendszerkép kiépítése sikertelen

Ha nem lehet felépíteni a Docker-rendszerképet, a [rendszerkép. wait_for_creation ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) vagy a [Service. wait_for_deployment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) hívása meghiúsul, és néhány olyan hibaüzenetet jelez, amely bizonyos nyomokat is biztosít. További részleteket a rendszerkép-létrehozási napló hibáiról is talál. Alább látható egy példa, amely bemutatja, hogyan derítheti fel a rendszerkép-létrehozási napló URI-ját.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print(img.name, img.version, img.image_build_log_uri)
```

A képnapló URI-ja egy, az Azure Blob Storage-ban tárolt naplófájlra mutató SAS URL-cím. Egyszerűen másolja ki és illessze be az URI-t egy böngészőablakba, és töltse le és tekintse meg a naplófájlt.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault hozzáférési szabályzat és Azure Resource Manager sablonok

A rendszerkép létrehozása a Azure Key Vault hozzáférési házirendjének problémája miatt is sikertelen lehet. Ez a helyzet akkor fordulhat elő, ha Azure Resource Manager sablonnal hozza létre a munkaterületet és a kapcsolódó erőforrásokat (beleértve Azure Key Vault), többször is. Például a sablon többszöri használata ugyanazzal a paraméterekkel, mint a folyamatos integráció és üzembe helyezési folyamat részeként.

A sablonokon keresztül a legtöbb erőforrás-létrehozási művelet idempotens, de Key Vault törli a hozzáférési házirendeket a sablon használatakor. A hozzáférési házirendek törlése megszakítja a hozzáférést a Key Vault az azt használó meglévő munkaterületekhez. Ez az állapot hibákat eredményez, amikor új rendszerképeket próbál létrehozni. A következő példák a kapott hibákra mutatnak:

__Portál__:
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__SDK__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

A probléma elkerüléséhez a következő módszerek egyikét javasoljuk:

* A sablont ne telepítse többször ugyanarra a paraméterekre. Vagy törölje a meglévő erőforrásokat, mielőtt a sablon használatával újra létrehozza őket.
* Vizsgálja meg a Key Vault hozzáférési házirendeket, majd használja ezeket a házirendeket a sablon `accessPolicies` tulajdonságának beállításához.
* Ellenőrizze, hogy a Key Vault erőforrás már létezik-e. Ha igen, ne hozza létre újra a sablonon keresztül. Hozzáadhat például egy olyan paramétert, amely lehetővé teszi, hogy letiltsa a Key Vault erőforrás létrehozását, ha az már létezik.

## <a name="debug-locally"></a>Helyi hibakeresés

Ha olyan problémák merülnek fel, amelyek a modell ACI-vagy AK-beli üzembe helyezésével kapcsolatosak, próbálja meg helyiként telepíteni. A helyi használata megkönnyíti a problémák elhárítását. A modellt tartalmazó Docker-rendszerkép le van töltve, és elindult a helyi rendszeren.

> [!IMPORTANT]
> A helyi központi telepítések működéséhez a helyi rendszeren működő Docker-telepítés szükséges. Helyi telepítése előtt a Docker-nek futnia kell. A Docker telepítésével és használatával kapcsolatos információkért lásd: [https://www.docker.com/](https://www.docker.com/).

> [!WARNING]
> A helyi központi telepítések éles környezetben nem támogatottak.

A helyileg történő üzembe helyezéshez módosítsa a kódot a `LocalWebservice.deploy_configuration()` használatára a telepítési konfiguráció létrehozásához. Ezután a `Model.deploy()` használatával telepítse a szolgáltatást. A következő példa egy modellt helyez üzembe (a `model` változóban) helyiként:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime="python",
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the  endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the  is available on
print(service.port)
```

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

### <a name="update-the-service"></a>A szolgáltatás frissítése

A helyi tesztelés során előfordulhat, hogy frissítenie kell a `score.py` fájlt a naplózás hozzáadásához, vagy megkísérelni a felderített problémák megoldását. A `score.py` fájl változásainak újratöltéséhez használja a `reload()`. A következő kód például újratölti a szolgáltatáshoz tartozó parancsfájlt, majd adatokat küld neki. A rendszer a frissített `score.py` fájl alapján szerzi be az adatgyűjtést:

> [!IMPORTANT]
> A `reload` metódus csak helyi központi telepítések esetén érhető el. További információ a központi telepítés más számítási célra való frissítéséről: a [modellek üzembe helyezésének](how-to-deploy-and-where.md#update)frissítése szakasz.

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> A parancsfájl a szolgáltatás által használt `InferenceConfig` objektum által megadott helyről lesz újratöltve.

A modell, a Conda-függőségek vagy a telepítési konfiguráció módosításához használja az [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-)t. A következő példa frissíti a szolgáltatás által használt modellt:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>A szolgáltatás törlése

A szolgáltatás törléséhez használja a [delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--)t.

### <a id="dockerlog"></a>A Docker-napló ellenőrzése

A Service objektumból kinyomtathatja a Docker-motor részletes naplófájljait. Megtekintheti az ACI-, AK-és helyi központi telepítések naplóját. Az alábbi példa bemutatja, hogyan lehet kinyomtatni a naplókat.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>A szolgáltatás indítása sikertelen

A rendszerkép sikeres felépítése után a rendszer megkísérli a tároló indítását a telepítési konfiguráció alapján. A tároló indítási folyamatának részeként a rendszer meghívja a pontozási parancsfájl `init()` függvényét. Ha a `init()` függvény nem észlelt kivételeket tartalmaz, előfordulhat, hogy a **CrashLoopBackOff** hiba jelenik meg a hibaüzenetben.

A naplók ellenőrzéséhez használja a [Docker-napló vizsgálata](#dockerlog) szakaszban található információkat.

## <a name="function-fails-get_model_path"></a>A függvény sikertelen: get_model_path ()

Gyakran előfordul, hogy a pontozási parancsfájl `init()` függvényében a [Model. Get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) függvényt a rendszer megkeresi egy modellel vagy egy, a tárolóban található modell-fájl mappáját. Ha a modell fájlja vagy mappája nem található, a függvény sikertelen lesz. A hiba hibakeresésének legegyszerűbb módja a következő Python-kód futtatása a Container shellben:

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Ez a példa kiírja a helyi elérési utat (a `/var/azureml-app`hoz képest) abban a tárolóban, ahol a pontozási parancsfájl a modell fájljának vagy mappájának megkeresésére vár. Ezt követően ellenőrizheti, hogy a fájl vagy mappa valóban a várt-e.

Ha a naplózási szint HIBAKERESÉSét állítja be, akkor további információk is naplózhatók, ami hasznos lehet a hiba azonosításához.

## <a name="function-fails-runinput_data"></a>A függvény sikertelen: Futtatás (input_data)

Ha a szolgáltatás sikeresen telepítve van, de összeomlik a pontozási végpontra történő adatküldés során, akkor a `run(input_data)` függvényben hozzáadhat egy Error befogási utasítást, hogy a részletes hibaüzenetet adja vissza helyette. Példa:

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

**Megjegyzés**: a `run(input_data)` hívással kapcsolatos hibaüzeneteket csak hibakeresési célra lehet elvégezni. Biztonsági okokból az éles környezetben nem adhat vissza hibaüzeneteket.

## <a name="http-status-code-503"></a>HTTP-állapotkód 503

Az Azure Kubernetes szolgáltatás központi telepítései támogatják az automatikus skálázást, amely lehetővé teszi a replikák hozzáadását a további terhelések támogatásához. Az automatikus skálázás azonban úgy van kialakítva, hogy kezelje a terhelés **fokozatos** változásait. Ha a kérések másodpercenként nagy számú tüskéket kapnak, az ügyfelek a 503-as HTTP-állapotkódot kapják meg.

Két olyan dolog van, amely segíthet megelőzni a 503-es állapotkódot:

* Módosítsa a kihasználtsági szintet, amelynél az automatikus skálázás új replikákat hoz létre.
    
    Alapértelmezés szerint az automatikus skálázási cél kihasználtsága 70%-ra van állítva, ami azt jelenti, hogy a szolgáltatás legfeljebb 30%-os terhelést képes kezelni a másodpercenkénti kérésekben (RPS). A kihasználtsági célt úgy állíthatja be, hogy a `autoscale_target_utilization` alacsonyabb értékre állítja be.

    > [!IMPORTANT]
    > Ez a változás nem eredményezi a replikák *gyorsabb*létrehozását. Ehelyett alacsonyabb kihasználtsági küszöbértékben jönnek létre. Ahelyett, hogy megvárná, amíg a szolgáltatás 70%-ot nem használ, az érték 30%-ra való módosítása a replikákat a 30%-os kihasználtság esetén hozza létre.
    
    Ha a már használja a jelenlegi maximális replikákat, és továbbra is a 503-es állapotkódot látja, növelje a `autoscale_max_replicas` értéket a replikák maximális számának növeléséhez.

* Módosítsa a replikák minimális számát. A minimális replikák növelése nagyobb készletet biztosít a bejövő tüskék kezeléséhez.

    A replikák minimális számának növeléséhez állítsa a `autoscale_min_replicas` értéket magasabb értékre. A szükséges replikák kiszámításához használja a következő kódot, és cserélje le az értékeket a projekthez tartozó értékekre:

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

A `autoscale_target_utilization`, `autoscale_max_replicas`és `autoscale_min_replicas` beállításával kapcsolatos további információkért tekintse meg a [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) -modul referenciáját.


## <a name="advanced-debugging"></a>Speciális hibakeresés

Bizonyos esetekben előfordulhat, hogy interaktívan kell hibakeresést végeznie a modell üzembe helyezésében található Python-kóddal. Ha például a bejegyzési parancsfájl meghibásodik, és az ok nem határozható meg további naplózással. A Visual Studio Code és a Python Tools for Visual Studio (PTVSD) használatával csatlakoztathatja a Docker-tárolón belül futó kódot.

> [!IMPORTANT]
> Ez a hibakeresési módszer nem működik, ha a `Model.deploy()` és `LocalWebservice.deploy_configuration` használatával helyileg helyezi üzembe a modellt. Ehelyett létre kell hoznia egy rendszerképet a [ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) osztály használatával. 
>
> A helyi központi telepítések működéséhez a helyi rendszeren működő Docker-telepítés szükséges. Helyi telepítése előtt a Docker-nek futnia kell. A Docker telepítésével és használatával kapcsolatos információkért lásd: [https://www.docker.com/](https://www.docker.com/).

### <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

1. A Python Tools for Visual Studio (PTVSD) a helyi VS Code-fejlesztési környezetbe való telepítéséhez használja a következő parancsot:

    ```
    python -m pip install --upgrade ptvsd
    ```

    További információ a PTVSD és a VS Code használatával kapcsolatban: [távoli hibakeresés](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Ha a VS Code-t úgy szeretné konfigurálni, hogy kommunikáljon a Docker-lemezképpel, hozzon létre egy új hibakeresési konfigurációt:

    1. A VS Code-ból válassza a __hibakeresés__ menüt, majd válassza a __konfigurációk megnyitása__lehetőséget. Megnyílik egy __Launch. JSON__ nevű fájl.

    1. A __Launch. JSON__ fájlban keresse meg a `"configurations": [`tartalmazó sort, majd szúrja be a következő szöveget:

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
        > Ha már vannak más bejegyzések a konfigurációk szakaszban, adjon hozzá egy vesszőt (,) a beszúrt kód után.

        Ez a szakasz a Docker-tárolóhoz csatlakozik a 5678-es porton keresztül.

    1. Mentse a __Launch. JSON__ fájlt.

### <a name="create-an-image-that-includes-ptvsd"></a>PTVSD tartalmazó rendszerkép létrehozása

1. Módosítsa az üzemelő példány Conda-környezetét, hogy az tartalmazza a PTVSD-t. Az alábbi példa a `pip_packages` paraméterrel való hozzáadását mutatja be:

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 
    
    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.17', 'ptvsd'])
    
    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. A PTVSD elindításához és a szolgáltatás indításakor való várakozáshoz adja hozzá a következőt a `score.py` fájl elejéhez:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. A hibakeresés során érdemes lehet módosításokat végezni a rendszerképben anélkül, hogy újból létre kellene hoznia. Ha egy szövegszerkesztőt (VIM) szeretne telepíteni a Docker-rendszerképbe, hozzon létre egy `Dockerfile.steps` nevű új szövegfájlt, és használja a következőt a fájl tartalmához:

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    A szövegszerkesztő lehetővé teszi a Docker-rendszerképben lévő fájlok módosítását a módosítások teszteléséhez új rendszerkép létrehozása nélkül.

1. A `Dockerfile.steps` fájlt használó rendszerképek létrehozásához használja a `docker_file` paramétert a rendszerkép létrehozásakor. Az alábbi példa bemutatja, hogyan teheti meg ezt:

    > [!NOTE]
    > Ez a példa azt feltételezi, hogy `ws` a Azure Machine Learning munkaterületre mutat, és az `model` az üzembe helyezett modell. A `myenv.yml` fájl az 1. lépésben létrehozott Conda-függőségeket tartalmazza.

    ```python
    from azureml.core.image import Image, ContainerImage
    image_config = ContainerImage.image_configuration(runtime= "python",
                                 execution_script="score.py",
                                 conda_file="myenv.yml",
                                 docker_file="Dockerfile.steps")

    image = Image.create(name = "myimage",
                     models = [model],
                     image_config = image_config, 
                     workspace = ws)
    # Print the location of the image in the repository
    print(image.image_location)
    ```

A rendszerkép létrehozása után megjelenik a rendszerkép helye a beállításjegyzékben. A hely az alábbi szöveghez hasonló:

```text
myregistry.azurecr.io/myimage:1
```

Ebben a példában a beállításjegyzék neve `myregistry`, és a rendszerkép neve `myimage`. A rendszerkép verziója `1`.

### <a name="download-the-image"></a>A rendszerkép letöltése

1. Nyisson meg egy parancssort, egy terminált vagy egy másik rendszerhéjat, és használja a következő [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) -parancsot a Azure Machine learning munkaterületet tartalmazó Azure-előfizetésben való hitelesítéshez:

    ```azurecli
    az login
    ```

1. A rendszerképet tartalmazó Azure Container Registry (ACR) hitelesítéséhez használja a következő parancsot. Cserélje le a `myregistry`t a rendszerkép regisztrálásakor visszaadott értékre:

    ```azurecli
    az acr login --name myregistry
    ```

1. A rendszerkép helyi Docker-re való letöltéséhez használja a következő parancsot. Cserélje le a `myimagepath`t a rendszerkép regisztrálásakor visszaadott helyre:

    ```bash
    docker pull myimagepath
    ```

    A rendszerkép elérési útjának a `myregistry.azurecr.io/myimage:1`hoz hasonlónak kell lennie. Ahol a `myregistry` a beállításjegyzékben, `myimage` a rendszerkép, és a `1` a rendszerkép verziója.

    > [!TIP]
    > Az előző lépésből való hitelesítés nem tart örökké. Ha a hitelesítési parancs és a lekérési parancs között elég hosszú ideig várakozik, a rendszer hitelesítési hibát fog kapni. Ha ez történik, végezze el a hitelesítést.

    A letöltés befejezéséhez szükséges idő a internetkapcsolat sebességétől függ. A folyamat során a letöltési állapot jelenik meg. A letöltés befejeződése után a `docker images` paranccsal ellenőrizheti, hogy letöltötte-e a fájlt.

1. Ahhoz, hogy könnyebben működjön a rendszerképpel, a következő paranccsal adhat hozzá egy címkét. Cserélje le a `myimagepath` értéket a 2. lépésben szereplő Location értékre.

    ```bash
    docker tag myimagepath debug:1
    ```

    A többi lépésnél a teljes rendszerkép elérési útja helyett `debug:1` a helyi rendszerképet.

### <a name="debug-the-service"></a>A szolgáltatás hibakeresése

> [!TIP]
> Ha a `score.py` fájlban beállította a PTVSD-kapcsolat időtúllépését, az időkorlát lejárta előtt csatlakoztatnia kell a VS Code-ot a hibakeresési munkamenethez. Indítsa el a VS Code-ot, nyissa meg `score.py`helyi példányát, állítson be egy töréspontot, és készen áll arra, hogy az ebben a szakaszban ismertetett lépések használata előtt lépjen fel.
>
> A töréspontok hibakeresésével és beállításával kapcsolatos további információkért lásd: [hibakeresés](https://code.visualstudio.com/Docs/editor/debugging).

1. Ha egy Docker-tárolót a rendszerkép használatával szeretne elindítani, használja a következő parancsot:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Ha a VS Code-t a tárolón belül PTVSD szeretné csatolni, nyissa meg a VS Code-ot, és használja az F5 billentyűt, __vagy válassza a__ Ha a rendszer kéri, válassza ki a __Azure Machine learning: Docker hibakeresési__ konfigurációját. Azt is megteheti, hogy kijelöli a hibakeresés ikont az oldalsó sávban, a __Azure Machine learning: Docker debug__ bejegyzést a hibakeresés legördülő menüből, majd a zöld nyíl használatával csatlakoztathatja a hibakeresőt.

    ![A hibakeresés ikon, a hibakeresés elindítása gomb és a konfigurációs választó](media/how-to-troubleshoot-deployment/start-debugging.png)

Ezen a ponton a VS Code a Docker-tárolón belül csatlakozik a PTVSD-hez, és a korábban megadott törésponton leáll. Most már megkezdheti a kód futtatását, megtekintheti a változókat stb.

A VS Code a Python hibakereséséhez való használatával kapcsolatos további információkért lásd [a Python-kód hibakeresését](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019)ismertető témakört.

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>A tároló fájljainak módosítása

A rendszerképben lévő fájlok módosításához csatolhatja a futó tárolót, és végrehajthat egy bash-rendszerhéjat. Innen a Vim használatával szerkesztheti a fájlokat:

1. A futó tárolóhoz való kapcsolódáshoz és egy bash-rendszerhéj a tárolóban történő indításához használja a következő parancsot:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. A szolgáltatás által használt fájlok megkereséséhez használja a következő parancsot a tárolóban található bash rendszerhéjból:

    ```bash
    cd /var/azureml-app
    ```

    Innen a Vim használatával szerkesztheti a `score.py` fájlt. A Vim használatával kapcsolatos további információkért lásd: [a Vim-szerkesztő használata](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. A tároló módosításai általában nem maradnak meg. A módosítások mentéséhez használja a következő parancsot, mielőtt kilép a fenti lépésben elindított rendszerhéjból (azaz egy másik rendszerhéjban):

    ```bash
    docker commit debug debug:2
    ```

    Ez a parancs létrehoz egy `debug:2` nevű új rendszerképet, amely tartalmazza a módosításokat.

    > [!TIP]
    > A módosítások érvénybe léptetéséhez le kell állítania az aktuális tárolót, és meg kell kezdenie az új verzió használatát.

1. Ügyeljen arra, hogy a tárolóban lévő fájlok módosításai szinkronban legyenek a VS Code által használt helyi fájlokkal. Ellenkező esetben a hibakereső felhasználói felülete nem a várt módon fog működni.

### <a name="stop-the-container"></a>A tároló leállítása

A tároló leállításához használja a következő parancsot:

```bash
docker stop debug
```

## <a name="next-steps"></a>További lépések

További információk az üzembe helyezésről:

* [Üzembe helyezés és hol](how-to-deploy-and-where.md)
* [Oktatóanyag: a betanítási & modellek üzembe helyezése](tutorial-train-models-with-aml.md)

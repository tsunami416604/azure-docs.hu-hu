---
title: Üzembehelyezési hibaelhárítási útmutató
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan kerülő megoldása és a Docker telepítési kapcsolatos gyakori hibák elhárítása Azure Kubernetes Service-ben és az Azure Container Instances szolgáltatásban az Azure Machine Learning szolgáltatás használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 07/09/2018
ms.custom: seodec18
ms.openlocfilehash: e0f4b024d717c08df3514df057abf89d55be1dc9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707043"
---
# <a name="troubleshooting-azure-machine-learning-service-azure-kubernetes-service-and-azure-container-instances-deployment"></a>Az Azure Machine Learning szolgáltatás Azure Kubernetes Service-ben és az Azure Container Instances üzembe helyezés hibaelhárítása

Ismerje meg, hogyan megkerüléséhez, vagy a Docker Azure Container Instances (ACI) és az Azure Machine Learning szolgáltatás használatával az Azure Kubernetes Service (AKS) gyakori üzembehelyezési hibák megoldásához.

Amikor üzembe helyezéséhez az Azure Machine Learning szolgáltatáshoz, a rendszer számos feladatot hajt végre. A központi telepítésének feladatai a következők:

1. A munkaterület-modell beállításjegyzék regisztrálja a modellt.

2. Elkészíthet egy Docker-rendszerképet, többek között:
    1. Töltse le a regisztrált modell a beállításjegyzékből. 
    2. Hozzon létre egy docker-fájlban, a Python-környezetet, a függőségeket, adja meg, ha a környezet yaml-fájl alapján.
    3. Adja hozzá a szolgáltatásmodell-fájlokból és a pontozó szkript Önnek kell letöltenie a docker-fájlban.
    4. Hozhat létre egy új Docker-rendszerképet a docker-fájl használatával.
    5. Regisztrálja a Docker-rendszerképet az Azure Container Registry társítva a munkaterülethez.

    > [!IMPORTANT]
    > Attól függően, a kód lemezképkészítés automatikusan történik az adatbevitel nélkül.

3. A Docker-rendszerkép üzembe helyezése, Azure Container Instance (ACI) szolgáltatásban vagy az Azure Kubernetes Service (AKS).

4. Az aci Szolgáltatásban vagy az AKS Start fel egy új tárolót (vagy tárolók). 

További információ a folyamatot a [Modellkezelési](concept-model-management-and-deployment.md) bemutatása.

## <a name="before-you-begin"></a>Előkészületek

Ha bármilyen problémát tapasztal,-e az első teendő a szolgáltatástelepítési feladat felosztania (előző ismertetett) az egyes lépéseket a probléma.

Az üzembe helyezés ossza feladatok akkor hasznos, ha használja a [Webservice.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) API-t, vagy [Webservice.deploy_from_model()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) API-t, mindkét függvényt, hajtsa végre a fenti lépéseket, hogy egy egyetlen művelettel. Általában az API-kból kényelmesek, de ez segít érdekében a lépéseket a azáltal, hogy azokat a hibaelhárítása során az alábbi API-hívások.

1. Regisztrálja a modellt. Íme néhány mintakódját:

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. A rendszerkép létrehozásához. Íme néhány mintakódját:

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

3. A lemezkép telepítése szolgáltatásként. Íme néhány mintakódját:

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

Miután rendelkezik, az üzembe helyezési folyamat az egyes tevékenységek lebontva, hogy megtekinthessük leggyakoribb hibák.

## <a name="image-building-fails"></a>Lemezkép létrehozása sikertelen

A Docker-rendszerkép nem hozható létre, ha a [image.wait_for_creation()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) vagy [service.wait_for_deployment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) bizonyos hibaüzenetek által nyújtott néhány a keresőmotorok a hívás sikertelen. A kép build naplóból hibákkal kapcsolatos további részleteket is talál. Az alábbiakban látható néhány mintakódját felderítése a rendszerkép build napló uri.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```

A kép napló uri-ja egy SAS URL-t, az Azure blob storage szolgáltatásban tárolt naplófájl. Egyszerűen másolja be az URI-t egy böngészőablakban, és töltse le és a napló megtekintése.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Az Azure Key Vault hozzáférési szabályzattal és Azure Resource Manager-sablonok

A rendszerkép összeállítását a hozzáférési szabályzat az Azure Key vaulttal kapcsolatos probléma miatt is meghiúsulhat. Ez a helyzet akkor fordulhat elő, ha egy Azure Resource Manager-sablon használatával hozza létre a munkaterületet és a kapcsolódó erőforrások (beleértve az Azure Key Vault), több alkalommal. Például használja a sablon többször ugyanazokat a paramétereket egy folyamatos integrációt és üzembe helyezési folyamat részeként.

A legtöbb erőforrás-létrehozási műveletet sablonok idempotensek, de a Key Vault törli a hozzáférési szabályzatok minden alkalommal, amikor a sablont használ. A hozzáférési szabályzatok szünetek a Key Vault elérése bármely meglévő munkaterület által használt törlése. Ez az állapot hibát eredményez, amikor megpróbál létrehozni új képek. Példák, amely megkapja a hibák a következők:

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

__PARANCSSORI FELÜLET__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

Ez a probléma elkerülése érdekében javasoljuk, hogy a következő módszerek egyikét:

* Nem telepíthető a sablon egynél többször ugyanazokat a paramétereket. Vagy törölje a meglévő erőforrások újra létrehozhatja őket a sablon használata előtt.
* Vizsgálja meg a kulcstartó-hozzáférési szabályzatok, és ezek a szabályzatok segítségével állítsa be a `accessPolicies` a tulajdonsága.
* Ellenőrizze, hogy a Key Vault erőforrás már létezik. Ha igen, nem hozza létre újra, a sablon segítségével. Például adjon hozzá egy paramétert, amely lehetővé teszi, hogy tiltsa le a Key Vault-erőforrást, ha már létezik.

## <a name="debug-locally"></a>Helyi hibakeresése

Ha problémába ütközik az ACI vagy az AKS üzembe helyezéséhez, próbálkozzon a helyi webszolgáltatásként üzembe helyezni. A helyi webszolgáltatás segítségével megkönnyíti a problémák elhárításához. A Docker-rendszerképet, amely tartalmazza a modell letöltése és a helyi rendszeren elindult.

> [!IMPORTANT]
> Helyi webszolgáltatások üzembe helyezéséhez egy működő, a helyi rendszeren Docker-telepítés szükséges. Docker futnia kell egy helyi webszolgáltatás üzembe helyezése előtt. Telepítése és a Docker használatával kapcsolatos tudnivalókat lásd: [ https://www.docker.com/ ](https://www.docker.com/).

> [!WARNING]
> Üzemi forgatókönyvek esetén nem támogatottak a helyi webszolgáltatások üzembe helyezéséhez.

Helyi üzembe helyezés, módosítsa a kód használatához `LocalWebservice.deploy_configuration()` üzembe helyezési konfiguráció létrehozásához. Ezután `Model.deploy()` a szolgáltatás üzembe helyezéséhez. A következő példa telepíti a modell (szerepel a `model` változó) webszolgáltatásként, amely helyi:

```python
from azureml.core.model import InferenceConfig,Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Ezen a ponton a szolgáltatással a szokásos módon használhatja. Ha például a következő kód bemutatja a szolgáltatás adatokat küldő:

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>A szolgáltatás frissítése

Helyi tesztelése során szükség lehet frissíteni a `score.py` fájlt naplózás hozzáadása, vagy olyan problémák, amelyek megismert feloldásához. Töltse be újra a módosításokat, a `score.py` fájlt, használja `reload()`. A következő kód például a szolgáltatás a szkript betölti, és adatokat küld. Az adatok sorolódik használatával a frissített `score.py` fájlt:

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> A parancsfájl a megadott helyen újbóli a `InferenceConfig` a szolgáltatás által használt objektum.

A modell, a Conda-függőségeket vagy a telepítési konfiguráció módosításához használja [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). Az alábbi példa frissíti a modellt, a szolgáltatás által használt:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>A szolgáltatás törlése

A szolgáltatás törléséhez használja [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a> Vizsgálja meg a Docker-naplót

Kinyomtathatja a részletes Docker engine naplóüzenetek a szolgáltatás-objektumból. A napló az ACI, az AKS és helyi központi telepítések tekintheti meg. Az alábbi példa bemutatja, hogyan nyomtatni a naplókat.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Szolgáltatás indítása sikertelen lesz.

A rendszerkép sikeresen létrejött, miután a rendszer megkísérli az üzembe helyezési konfiguráció tároló indítása. Tároló indítása folyamat részeként a `init()` függvényt a pontozó szkript hív a rendszer. Ha a nem kezelt kivételek a `init()` működni, előfordulhat, hogy látható **CrashLoopBackOff** hiba a hibaüzenetben.

Az adatokra a [vizsgálja meg a Docker-naplót](#dockerlog) szakaszban tekintse meg a naplókat.

## <a name="function-fails-getmodelpath"></a>Függvény futása: get_model_path()

Gyakran előfordul, a a `init()` függvényt a pontozó szkript [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) függvény egy modell fájlt vagy mappát a modell fájlok található a tároló neve. Ha a modell fájl vagy mappa nem található, a függvény sikertelen lesz. A legegyszerűbben úgy, hogy ez a hiba hibakeresési, hogy futtassa az alábbi a tároló shellben a Python-kód:

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

Ebben a példában kiírja a helyi elérési útja (viszonyítva `/var/azureml-app`) a tárolóban, ahol a modell fájl vagy mappa megkereséséhez a pontozó szkript feltételez. Ezután már ellenőrizhető, ha a fájl vagy mappa valóban ahol azt kellene lennie.

A naplózási szint beállítása a DEBUG vezethet be kell jelentkeznie, további információt, amely lehet hasznos, ha a hiba azonosítása.

## <a name="function-fails-runinputdata"></a>Függvény futása: run(input_data)

Ha a szolgáltatás sikeres üzembe helyezése, de azt meg ezeket az adatokat a pontozási végpontjához összeomlik, hiba, valamint rögzíti az utasítás is hozzáadhat a `run(input_data)` függvényt, hogy a részletes hibaüzenet Ehelyett adja vissza. Példa:

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

**Megjegyzés**: Adatszolgáltató hibaüzenetek a `run(input_data)` hibakeresési célú csak hívást kell elvégezni. Biztonsági okokból meg nem adja vissza hibaüzenetek ezzel a módszerrel az éles környezetben.

## <a name="http-status-code-503"></a>HTTP-állapotkód: 503-as

Azure Kubernetes Service-környezetek támogatja az automatikus skálázást, amely lehetővé teszi a további terhelés hozzáadandó replikákat. Azonban a méretező tervezték **fokozatos** terhelés változásaira. Ha nagy ugrásszerűen kapja a kérések másodpercenkénti, az ügyfelek HTTP-állapotkód: 503-as jelenhet meg.

Két dolog segít megakadályozni, hogy 503-as állapotkód esetében:

* A változás a kihasználtsága, mely az automatikus skálázás új replikákat hoz létre.
    
    Alapértelmezés szerint az automatikus skálázás célkihasználtság van beállítva a 70 %-ra, ami azt jelenti, hogy a szolgáltatás képes kezelni a kérelmek / másodperc (RPS), akár 30 %-os adatforgalmi csúcsokhoz. A kihasználtsági célértéket beállítást módosíthatja a `autoscale_target_utilization` egy alacsonyabb értékre.

    > [!IMPORTANT]
    > Ez a változás nem okoz létrehozandó replikák *gyorsabban*. Ehelyett létrehozásuk alacsonyabb kihasználtság küszöbértékkel. Várnia, amíg a szolgáltatás 70 %-os használt fel, az érték 30 %-ának módosítása hatására replikák hozhatók létre 30 %-os kihasználtság esetén.
    
    Ha a webszolgáltatás már használja a jelenlegi maximális replikákat, és továbbra is látja az 503-as állapotkódok, növelje a `autoscale_max_replicas` érték megadásával növelheti a replikák maximális száma.

* Replikák minimális számának módosításához. A minimális replikák növelése biztosít nagyobb készlettel kezelje a bejövő adatforgalmi csúcsokhoz.

    Replikák minimális száma növelése érdekében állítsa `autoscale_min_replicas` értéke. Kiszámíthatja a szükséges replikákat az alábbi kód használatával értékeket cserélje le a konkrét értékek a projekthez:

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
    > Ha kérelem ugrásszerűen nagyobb, mint amit az új minimális replikák képes kezelni, 503s újra is megjelenhet. Például adatforgalom a szolgáltatás növeli, szükség lehet a minimális replikák növelése érdekében.

További információ a beállításra `autoscale_target_utilization`, `autoscale_max_replicas`, és `autoscale_min_replicas` , lásd: a [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) modul-hivatkozás.


## <a name="advanced-debugging"></a>Fejlett hibakeresés

Bizonyos esetekben szükség lehet a modell-üzembehelyezés található Python-kód interaktív módon hibakeresése. Például ha a bejegyzés parancsprogram futtatása sikertelen, és azért nem határozza meg további naplózás. A Visual Studio Code és a Python Tools for Visual Studio (PTVSD) használ, a kód a Docker-tárolóban futó is csatlakoztatható.

> [!IMPORTANT]
> Ez a módszer a hibakeresés használata esetén nem működik `Model.deploy()` és `LocalWebservice.deploy_configuration` modell üzembe helyezése helyi. Ehelyett, létre kell hoznia egy lemezkép használatával a [ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) osztály. 
>
> Helyi webszolgáltatások üzembe helyezéséhez egy működő, a helyi rendszeren Docker-telepítés szükséges. Docker futnia kell egy helyi webszolgáltatás üzembe helyezése előtt. Telepítése és a Docker használatával kapcsolatos tudnivalókat lásd: [ https://www.docker.com/ ](https://www.docker.com/).

### <a name="configure-development-environment"></a>A fejlesztési környezet konfigurálása

1. Telepíti a Python Tools for Visual Studio (PTVSD) a VS Code helyi fejlesztőkörnyezetet, használja a következő parancsot:

    ```
    python -m pip install --upgrade ptvsd
    ```

    A VS Code való használatához készült PTVSD további információkért lásd: [távoli hibakeresés](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. A Docker-rendszerkép kommunikálni a VS Code konfigurálása, hozzon létre egy új hibakeresési konfigurációt:

    1. A VS Code, válassza ki a __Debug__ menüben, majd __nyissa meg a konfigurációk__. Nevű fájl __launch.json__ nyílik meg.

    1. Az a __launch.json__ fájlt és keresse meg a tartalmazó sort `"configurations": [`, és helyezze be a következő szöveg után azt:

        ```json
        {
            "name": "Azure Machine Learning service: Docker Debug",
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
        > Ha már más bejegyzéseket a konfigurációk szakaszban, adjon hozzá egy vessző (,) beszúrt kód után.

        Ez a szakasz csatolja a Docker-tároló port 5678 használatával.

    1. Mentse a __launch.json__ fájlt.

### <a name="create-an-image-that-includes-ptvsd"></a>Hozzon létre egy rendszerképet, amely tartalmazza az PTVSD

1. Módosítsa a conda-környezet az üzembe helyezéshez, hogy a PTVSD tartalmazza. A következő példa bemutatja, hogy hozzáadná a használatával a `pip_packages` paramétert:

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

1. PTVSD elindításához, és várjon, amíg a kapcsolat a szolgáltatás indulásakor, adja hozzá a következő felső részén a `score.py` fájlt:

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Hibakeresés során érdemes módosítania kell a fájlt a képen nélkül hozza létre újból. Egy szövegszerkesztőben (vim) telepítése a Docker-rendszerképet, hozzon létre egy új szöveges fájlt `Dockerfile.steps` és a fájl tartalmát a következő használja:

    ```text
    RUN apt-get update && apt-get -y install vim
    ```

    Egy szövegszerkesztőben lehetővé teszi, hogy a fájlokat egy új lemezkép létrehozása nélkül tesztelheti a módosításokat a docker-rendszerkép belül módosíthatja.

1. Hozzon létre egy rendszerképet, amely használja, a `Dockerfile.steps` fájlt, használja a `docker_file` paraméter egy kép létrehozásakor. A következő példa ezt mutatja be:

    > [!NOTE]
    > Ez a példa feltételezi, hogy `ws` mutat, az Azure Machine Learning-munkaterületet, és hogy `model` a modell üzembe helyezéséhez. A `myenv.yml` fájl tartalmazza a 1. lépésben létrehozott conda-függőségeket.

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

A lemezkép létrehozása után megjelenik a kép helyen a beállításjegyzékben. A hely az alábbi szöveghez hasonlít:

```text
myregistry.azurecr.io/myimage:1
```

Szöveg ebben a példában a beállításjegyzék neve a következő `myregistry` , és a lemezkép neve `myimage`. A rendszerkép verziószáma `1`.

### <a name="download-the-image"></a>A kép letöltése

1. Nyisson meg egy parancssort, a terminált vagy más rendszerhéj, és használja a következő [Azure CLI-vel](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) paranccsal próbál hitelesítést az Azure-előfizetéshez, amely tartalmazza az Azure Machine Learning-munkaterület:

    ```azurecli
    az login
    ```

1. Hitelesítést végezni, az Azure Container Registry (ACR), amely tartalmazza a rendszerkép, használja a következő parancsot. Cserélje le `myregistry` egy adott vissza, ha a kép regisztrálva:

    ```azurecli
    az acr login --name myregistry
    ```

1. Töltse le a rendszerképet a helyi Docker, használja a következő parancsot. Cserélje le `myimagepath` helyét adja vissza, ha a kép regisztrálva:

    ```bash
    docker pull myimagepath
    ```

    A lemezkép-elérési útnak kell lennie hasonló `myregistry.azurecr.io/myimage:1`. Ahol `myregistry` a regisztrációs adatbázis `myimage` a lemezkép és `1` a lemezkép-verzió.

    > [!TIP]
    > Az előző lépésben a hitelesítés nem tartja az utolsó. Ha elég hosszú az hitelesítést és a lekéréses parancsot között várni, kapni fog egy hitelesítési hiba. Ha ez történik, hitelesítse magát újra.

    A letöltés befejezéséhez szükséges idő az internetkapcsolat sebességétől függ. A letöltés állapota megjelenik a folyamat során. A letöltés befejezése után is használhatja a `docker images` paranccsal ellenőrizheti, hogy azt töltött le.

1. Egyszerűbb legyen a lemezképpel működik, használja a következő parancsot egy címke hozzáadása. Cserélje le `myimagepath` 2. lépés a hely értékkel.

    ```bash
    docker tag myimagepath debug:1
    ```

    A helyi rendszerképet, olvassa el a lépéseket a többi `debug:1` helyett a teljes lemezkép elérési útja értéket.

### <a name="debug-the-service"></a>A szolgáltatás hibakeresése

> [!TIP]
> Ha be van-e a PTVSD kapcsolat időtúllépés a `score.py` fájl, csatlakoznia kell a VS Code a hibakeresési munkamenet az időkorlát lejárta előtt. Indítsa el a VS Code, nyissa meg a helyi példányának `score.py`, állítson be egy töréspontot, és ebben a szakaszban ismertetett lépések előtt fel kell azt.
>
> A Hibakeresés és töréspontok beállításával további információkért lásd: [Debugging](https://code.visualstudio.com/Docs/editor/debugging).

1. Indítsa el a Docker-tároló lemezképet használja, használja a következő parancsot:

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. A VS Code PTVSD csatolni a tárolóban, nyissa meg a VS Code és használatához a kulcs, vagy válassza ki az F5 __Debug__. Amikor a rendszer kéri, válassza ki a __Azure Machine Learning szolgáltatás: Docker-hibakeresési__ konfigurációja. Az oldalsó sáv közül is választhat a hibakeresés ikon a __Azure Machine Learning szolgáltatás: Docker-hibakeresési__ a hibakeresési legördülő menüre, majd társítsa a hibakeresőt, a zöld nyíl bejegyzés.

    ![A hibakeresés ikon, a kezdő hibakeresési gomb, valamint a konfiguráció-választó](media/how-to-troubleshoot-deployment/start-debugging.png)

Ezen a ponton a VS Code PTVSD csatlakozik a Docker-tárolóban, és a korábban megadott töréspont megáll. Most már elolvasásával a kódot futtatja, megtekintheti a változókat, és így tovább.

A Python hibakeresése a VS Code használatával további információkért lásd: [a Python-kód hibaelhárítása](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>A tároló fájl módosítása

A kép fájlok módosítja, csatlakoztatni a futó tárolót, és hajtsa végre a bash felületet. Itt vim fájlok szerkesztésére használhatja:

1. Csatlakozás a futó tárolót, és indítsa el a bash felületet, a tárolóban, a következő paranccsal:

    ```bash
    docker exec -it debug /bin/bash
    ```

1. A szolgáltatás által használt fájlokat megkereséséhez használja a következő parancs a bash felületet, a tárolóban:

    ```bash
    cd /var/azureml-app
    ```

    Itt az vim segítségével módosíthatja a `score.py` fájlt. Vim használatával kapcsolatos további információkért lásd: [a Vim szerkesztővel](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Egy tároló módosításai általában nem megőrzött. A fenti lépésben lépések menteni a módosításokat, használja a következő parancsot a rendszerhéj Kilépés előtt (azt jelenti, egy másik rendszerhéj):

    ```bash
    docker commit debug debug:2
    ```

    Ez a parancs létrehoz egy új rendszerképet nevű `debug:2` , amely tartalmazza a módosításokat.

    > [!TIP]
    > Szüksége lesz az aktuális tárolóban leállítására és elindítására a módosítások érvénybe lépéséhez az új verziót használja.

1. Győződjön meg arról, hogy a végzett módosítások a tárolóban lévő fájlok szinkronizálása a VS Code használó a helyi fájlok. Ellenkező esetben a hibakeresőt élmény fog nem működnek megfelelően.

### <a name="stop-the-container"></a>Állítsa le a tároló

A tároló leállításához használja a következő parancsot:

```bash
docker stop debug
```

## <a name="next-steps"></a>További lépések

További információk az üzembe helyezésről:

* [Hogyan helyezhet üzembe, és ahol](how-to-deploy-and-where.md)
* [Oktatóanyag: Be & modellek üzembe helyezése](tutorial-train-models-with-aml.md)

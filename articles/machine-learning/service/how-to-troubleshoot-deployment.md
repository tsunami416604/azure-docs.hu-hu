---
title: Hibaelhárítási útmutató Azure Machine Learning szolgáltatás a központi telepítés
description: Ismerje meg, hogyan megkerülő megoldásként megoldásában, és a Docker telepítési kapcsolatos gyakori hibák elhárítása AKS és Azure Machine Learning szolgáltatás használatával ACI.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.openlocfilehash: ed6fed58aac236f143345a9504ece21aceae09d2
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865369"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Az Azure Machine Learning szolgáltatás AKS és az aci Szolgáltatásban üzemelő példányainak hibaelhárítása

Ebben a cikkben, megtudhatja, hogyan megkerüléséhez vagy oldja meg a közös Docker telepítési hibák az Azure Container Instances (ACI) és az Azure Kubernetes Service (AKS) az Azure Machine Learning szolgáltatás használatával.

Amikor üzembe helyezéséhez az Azure Machine Learning szolgáltatáshoz, a rendszer számos feladatot hajt végre. Toto je sekvence, az események összetett, és néha problémák merülnek fel. A központi telepítésének feladatai a következők:

1. A munkaterület-modell beállításjegyzék regisztrálja a modellt.

2. Elkészíthet egy Docker-rendszerképet, többek között:
    1. Töltse le a regisztrált modell a beállításjegyzékből. 
    2. Hozzon létre egy docker-fájlban, a Python-környezetet, a függőségeket, adja meg, ha a környezet yaml-fájl alapján.
    3. Adja hozzá a szolgáltatásmodell-fájlokból és a pontozó szkript Önnek kell letöltenie a docker-fájlban.
    4. Hozhat létre egy új Docker-rendszerképet a docker-fájl használatával.
    5. Regisztrálja a Docker-rendszerképet az Azure Container Registry társítva a munkaterülethez.

3. A Docker-rendszerkép üzembe helyezése, Azure Container Instance (ACI) szolgáltatásban vagy az Azure Kubernetes Service (AKS).

4. Az aci Szolgáltatásban vagy az AKS Start fel egy új tárolót (vagy tárolók). 

További információ a folyamatot a [Modellkezelési](concept-model-management-and-deployment.md) bemutatása.

## <a name="before-you-begin"></a>Előkészületek

Ha bármilyen problémát tapasztal,-e az első teendő a szolgáltatástelepítési feladat felosztania (előző ismertetett) az egyes lépéseket a probléma. 

Ez különösen akkor hasznos, ha használja az a `Webservice.deploy` API-t, vagy `Webservice.deploy_from_model` API-t, mivel ezekhez a függvényekhez csoportba foglalhatók a fent említett egyetlen művelettel a lépéseket. API-k általában igen kényelmes, de ez segít érdekében a lépéseket a azáltal, hogy azokat a hibaelhárítása során az alábbi API-hívások.

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
                                                      execution_script="score.py",
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
Ha a rendszer nem lehet a Docker-rendszerkép létrehozásához a `image.wait_for_creation()` bizonyos hibaüzenetek által nyújtott néhány a keresőmotorok a hívás sikertelen. A kép build naplóból hibákkal kapcsolatos további részleteket is talál. Az alábbiakban látható néhány mintakódját felderítése a rendszerkép build napló uri.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images()['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images().items():
    print (img.name, img.version, img.image_build_log_uri)
```
A kép napló uri-ja egy SAS URL-t, az Azure blob storage szolgáltatásban tárolt naplófájl. Egyszerűen másolja be az URI-t egy böngészőablakban, és töltse le és a napló megtekintése.


## <a name="service-launch-fails"></a>Szolgáltatás indítása sikertelen lesz.
Miután a rendszerkép sikeresen létrejött, a rendszer megkísérli tároló indítása az aci Szolgáltatásban vagy az AKS üzembe helyezési konfigurációtól függően. Az ACI központi telepítésének egy egyszerűbb egy tároló-telepítést, mert először, próbálja meg. általában ajánlott. Ezzel a módszerrel is majd kizárja azt az AKS-specifikus problémát.

Tároló indítása folyamat részeként a `init()` függvényt a pontozó szkript hív a rendszer. Ha a nem kezelt kivételek a `init()` működni, előfordulhat, hogy látható **CrashLoopBackOff** hiba a hibaüzenetben. Az alábbiakban néhány tipp a probléma elhárításához nyújt segítséget.

### <a name="inspect-the-docker-log"></a>Vizsgálja meg a Docker-naplót
Kinyomtathatja a részletes Docker engine naplóüzenetek a szolgáltatás-objektumból.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices()['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>A Docker-rendszerkép helyi hibakeresése
Néhány eset a Docker-naplót nem gridre bocsáthatja ki az elegendő információ a nem megfelelő eseményeit. Egy lépéssel tovább, és kérje le a beépített Docker-rendszerképet, helyi tároló indítása és interaktív módon javítása közvetlenül a élő tárolóban. Helyi tároló indítása rendelkeznie kell egy Docker-motor helyben fut, és lenne sokkal egyszerűbb, ha akkor is [azure-cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) telepítve.

Először azt kell ismerje meg, hogy a lemezkép helyét:

```python
# print image location
print(image.image_location)
```

A lemezkép helyét a formátuma: `<acr-name>.azurecr.io/<image-name>:<version-number>`, mint például `myworkpaceacr.azurecr.io/myimage:3`. 

Lépjen a parancssori ablakot. Ha az azure cli telepítve van, írja be a következő parancsok futtatásával jelentkezzen be az ACR-REL (Azure Container Registry) társítva a munkaterülethez, a lemezkép tárolására. 

```sh
# log on to Azure first if you haven't done so before
$ az login

# make sure you set the right subscription in case you have access to multiple subscriptions
$ az account set -s <subscription_name_or_id>

# now let's log in to the workspace ACR
# note the acr-name is the domain name WITHOUT the ".azurecr.io" postfix
# e.g.: az acr login -n myworkpaceacr
$ az acr login -n <acr-name>
```
Ha nem rendelkezik azure-cli telepítve van, akkor használhatja `docker login` paranccsal jelentkezzen be az ACR-REL. De a felhasználó nevét és jelszavát, az ACR-REL először lekérése az Azure Portalon kell.

Miután bejelentkezett, az ACR-REL, kérje le a Docker-rendszerképet és a helyi tároló indítása, és beállíthatja, majd indítsa el a bash használatával hibakeresési munkamenet a `docker run` parancsot:

```sh
# note the image_id is <acr-name>.azurecr.io/<image-name>:<version-number>
# for example: myworkpaceacr.azurecr.io/myimage:3
$ docker run -it <image_id> /bin/bash
```

Miután egy bash-munkamenet indítása a futó tárolót, annak a pontozó szkripteket a a `/var/azureml-app` mappát. Egy Python-munkamenetet a pontozási parancsfájlokban való hibakeresés majd el is indíthatja. 

```sh
# enter the directory where scoring scripts live
cd /var/azureml-app

# find what Python packages are installed in the python environment
pip freeze

# sanity-check on score.py
# you might want to edit the score.py to trigger init().
# as most of the errors happen in init() when you are trying to load the model.
python score.py
```
Abban az esetben kell módosítani a parancsfájlokat egy szövegszerkesztőben, telepíthet vim, nano, Emacs vagy más kedvenc szerkesztőjében.

```sh
# update package index
apt-get update

# install a text editor of your choice
apt-get install vim
apt-get install nano
apt-get install emacs

# launch emacs (for example) to edit score.py
emacs score.py

# exit the container bash shell
exit
```

Is helyileg a web service indítása és HTTP-forgalom küldéséhez. A Docker-tárolót a Flask-kiszolgáló port 5001 fut. A gazdagépen rendelkezésre álló egyéb portok is leképezheti.
```sh
# you can find the scoring API at: http://localhost:8000/score
$ docker run -p 8000:5001 <image_id>
```

## <a name="function-fails-getmodelpath"></a>Függvény futása: get_model_path()
Gyakran előfordul, a a `init()` függvényt a pontozó szkript `Model.get_model_path()` függvény egy modell fájlt vagy mappát a modell fájlok található a tároló neve. Ha ez a gyakran egy hiba forrását a modell fájl vagy mappa nem található. A legegyszerűbben úgy, hogy ez a hiba hibakeresési, hogy futtassa az alábbi a tároló shellben a Python-kód:

```python
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

Ez lenne nyomtassa ki a helyi elérési út (viszonyítva `/var/azureml-app`) a tárolóban, ahol a modell fájl vagy mappa megkereséséhez a pontozó szkript feltételez. Ezután már ellenőrizhető, ha a fájl vagy mappa valóban ahol azt kellene lennie.


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
**Megjegyzés:**: visszaadó hibaüzenetek a `run(input_data)` hibakeresési célú csak hívást kell elvégezni. Nem lehet célszerű, hogy ehhez a biztonsági okokból éles környezetben.


## <a name="next-steps"></a>További lépések

További információk az üzembe helyezésről: 
* [Az aci Szolgáltatásban üzembe helyezése](how-to-deploy-to-aci.md)

* [Az aks üzembe helyezése](how-to-deploy-to-aks.md)

* [Az oktatóanyag 1. rész: modell betanítása](tutorial-train-models-with-aml.md)

* [Az oktatóanyag 2. rész: modell üzembe helyezése](tutorial-deploy-models-with-aml.md)

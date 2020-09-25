---
title: Telepítés & Azure Machine Learning parancssori felület használata
description: Ismerje meg, hogyan hozhatja létre és kezelheti a Azure Machine Learning CLI bővítményt olyan erőforrások létrehozásához és kezeléséhez, mint például a munkaterület, az adattárolók, az adatkészletek, a folyamatok, a modellek és a központi telepítések.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 06/22/2020
ms.custom: seodec18
ms.openlocfilehash: 490ee6e0dea5f5106d8027c2a4c9c7b09bb81fd0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91307959"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>Telepítse & a CLI-bővítményt használja Azure Machine Learning


A Azure Machine Learning CLI az Azure [CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true)egy platformfüggetlen parancssori felülete az Azure platformhoz. Ez a bővítmény parancsokat biztosít a Azure Machine Learning használatához. Lehetővé teszi a gépi tanulási tevékenységek automatizálását. Az alábbi lista néhány példát ismertet a CLI bővítménnyel:

+ Kísérletek futtatása gépi tanulási modellek létrehozásához

+ Gépi tanulási modellek regisztrálása az ügyfelek általi használatra

+ Gépi tanulási modelljeinek csomagja, üzembe helyezése és nyomon követése

A CLI nem helyettesíti a Azure Machine Learning SDK-t. Ez egy kiegészítő eszköz, amely az automatizáláshoz jól illeszkedő, nagymértékben paraméterekkel rendelkező feladatok kezelésére van optimalizálva.

## <a name="prerequisites"></a>Előfeltételek

* A CLI használatához Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot, mielőtt hozzákezd. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Ha a jelen dokumentumban a CLI-parancsokat a **helyi környezetből**szeretné használni, szüksége lesz az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)-re.

    Ha a [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)használja, a CLI a böngészőn keresztül érhető el, és a felhőben él.

## <a name="full-reference-docs"></a>Teljes dokumentációs dokumentumok

Megtalálhatja az Azure [parancssori felület Azure-CLI-ml-bővítményének teljes dokumentációját](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest&preserve-view=true).

## <a name="connect-the-cli-to-your-azure-subscription"></a>A CLI összekötése az Azure-előfizetéssel

> [!IMPORTANT]
> Ha a Azure Cloud Shell használja, akkor kihagyhatja ezt a szakaszt. A Cloud Shell automatikusan hitelesíti Önt az Azure-előfizetésében bejelentkezett fiók használatával.

Az Azure-előfizetések több módon is hitelesíthetők a parancssori felületről. A legalapvetőbb az interaktív hitelesítés böngésző használatával. Az interaktív hitelesítéshez nyisson meg egy parancssort vagy terminált, és használja a következő parancsot:

```azurecli-interactive
az login
```

Ha a CLI megnyithatja az alapértelmezett böngészőt, akkor megnyitja, és betölti a bejelentkezési oldalt. Ellenkező esetben meg kell nyitnia egy böngészőt, és követnie kell a parancssor utasításait. Az utasítások [https://aka.ms/devicelogin](https://aka.ms/devicelogin) egy engedélyezési kód böngészését és beírását foglalják magukban.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

A hitelesítés egyéb módszereivel kapcsolatban lásd: [Bejelentkezés az Azure CLI-vel](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true).

## <a name="install-the-extension"></a>A bővítmény telepítése

A Machine Learning CLI bővítmény telepítéséhez használja a következő parancsot:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Az alábbi parancsokkal használható fájlok [itt](https://aka.ms/azml-deploy-cloud)találhatók.

Ha a rendszer kéri, válassza `y` a bővítmény telepítéséhez.

A bővítmény telepítésének ellenőrzéséhez használja a következő parancsot az ML-specifikus alparancsok listájának megjelenítéséhez:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>A bővítmény frissítése

A Machine Learning CLI bővítmény frissítéséhez használja a következő parancsot:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>A bővítmény eltávolítása

A CLI-bővítmény eltávolításához használja a következő parancsot:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Erőforrás-kezelés

A következő parancsok bemutatják, hogyan használható a CLI a Azure Machine Learning által használt erőforrások kezeléséhez.

+ Ha még nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Azure Machine Learning munkaterület létrehozása:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    További információ: [az ml Workspace Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-workspace-create).

+ Munkaterület-konfiguráció csatolása egy mappához a CLI környezetfüggő ismeretének engedélyezéséhez.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Ez a parancs létrehoz egy `.azureml` alkönyvtárat, amely tartalmazza például a runconfig és a Conda környezeti fájlokat. Emellett tartalmaz egy fájlt is, `config.json` amely a Azure Machine learning munkaterülettel folytatott kommunikációhoz használható.

    További információ: [az ml mappa csatolása](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-folder-attach).

+ Azure Blob-tároló csatolása adattárként.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    További információ: [az ml adattár csatolása-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Fájlok feltöltése egy adattárba.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    További információ: [az ml adattár feltöltése](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-datastore-upload).

+ Egy AK-fürt csatlakoztatása számítási célként.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    További információ: [az ml computetarget Attach AK](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

### <a name="compute-clusters"></a>Számítási fürtök

+ Hozzon létre egy új felügyelt számítási fürtöt.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ Új felügyelt számítási fürt létrehozása felügyelt identitással

  + Felhasználó által hozzárendelt felügyelt identitás

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  + Rendszer által hozzárendelt felügyelt identitás

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
+ Felügyelt identitás hozzáadása meglévő fürthöz:

    + Felhasználó által hozzárendelt felügyelt identitás
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    + Rendszer által hozzárendelt felügyelt identitás

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

További információ: [az ml computetarget Create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### <a name="compute-instance"></a>Számítási példány
Számítási példányok kezelése.  Az alábbi példákban a számítási példány neve **CPU**

+ Hozzon létre egy új computeinstance.

    ```azurecli-interactive
    az ml computetarget create computeinstance  -n cpu -s "STANDARD_D3_V2" -v
    ```
    
    További információ: [az ml computetarget Create computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-create-computeinstance).

+ Computeinstance leállítása.
    
    ```azurecli-interactive
    az ml computetarget stop computeinstance -n cpu -v
    ```
    
    További információ: [az ml computetarget stop computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop).
    
+ Computeinstance elindítása.
    
    ```azurecli-interactive
    az ml computetarget start computeinstance -n cpu -v
    ```
    
    További információ: [az ml computetarget Start computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start).
    
+ Indítsa újra a computeinstance.
    
    ```azurecli-interactive
    az ml computetarget restart computeinstance -n cpu -v
    ```
    
    További információ: [az ml computetarget restart computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart).
    
+ Computeinstance törlése.
    
    ```azurecli-interactive
    az ml computetarget delete -n cpu -v
    ```
    
    További információ: [az ml computetarget delete computeinstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-computetarget-delete).


## <a name="run-experiments"></a><a id="experiments"></a>Kísérletek futtatása

* Indítsa el a kísérlet futtatását. Ha ezt a parancsot használja, adja meg a runconfig-fájl nevét (a. runconfig karakterláncot, \* Ha a fájlrendszert keresi) a-c paraméterrel.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > A `az ml folder attach` parancs létrehoz egy `.azureml` alkönyvtárat, amely két példa runconfig-fájlt tartalmaz. 
    >
    > Ha olyan Python-szkripttel rendelkezik, amely programozott módon hozza létre a futtatási konfigurációs objektumot, a [RunConfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py&preserve-view=true#&preserve-view=truesave-path-none--name-none--separate-environment-yaml-false-) paranccsal mentheti RunConfig-fájlként.
    >
    > A teljes runconfig séma ebben a [JSON-fájlban](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)található. A séma az `description` egyes objektumok kulcsán keresztüli öndokumentálás. Emellett a lehetséges értékek enumerálásai is megtalálhatók, a végén pedig egy sablon-kódrészlet.

    További információ: [az ml Run Submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-run-submit-script).

* A kísérletek listájának megtekintése:

    ```azurecli-interactive
    az ml experiment list
    ```

    További információ: [az ml-kísérletek listája](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-experiment-list).

### <a name="hyperdrive-run"></a>HyperDrive futtatása

A HyperDrive és az Azure CLI használatával is végrehajthatja a paraméterek hangolási futtatását. Először hozzon létre egy HyperDrive-konfigurációs fájlt a következő formátumban. A hiperparaméter hangolási paramétereinek részletes ismertetését lásd: [a modell Hiperparaméterek beállítása finomhangolása](how-to-tune-hyperparameters.md) .

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Adja hozzá ezt a fájlt a konfigurációs fájlok futtatása mellett. Ezután küldje el a HyperDrive futtatását a paranccsal:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Jegyezze fel az *argumentumok* szakaszt a runconfig és a *paraméter* területen a HyperDrive konfigurációban. Ezek tartalmazzák a betanítási parancsfájlnak átadandó parancssori argumentumokat. A runconfig értéke minden iteráció esetében azonos marad, míg a HyperDrive config tartománya megismétli a tartományt. Ne ugyanazt az argumentumot válassza mindkét fájlban.

## <a name="dataset-management"></a>Adatkészlet-kezelés

A következő parancsok bemutatják, hogyan használhatók az adatkészletek a Azure Machine Learningban:

+ Adatkészlet regisztrálása:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Az adatkészlet definiálásához használt JSON-fájl formátumával kapcsolatos információkért használja a következőt: `az ml dataset register --show-template` .

    További információ: [az ml adatkészlet regisztrálása](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-dataset-register).

+ Munkaterület összes adatkészletének listázása:

    ```azurecli-interactive
    az ml dataset list
    ```

    További információ: [az ml adatkészlet listája](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-dataset-list).

+ Adatkészlet részleteinek beolvasása:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    További információ: [az ml adatkészlet show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-dataset-show).

+ Adatkészlet regisztrációjának törlése:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    További információ: [az ml adatkészlet regisztrációjának törlése](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>A környezet kezelése

A következő parancsok bemutatják, hogyan hozhat létre, regisztrálhat és listázhat Azure Machine Learning [környezeteket](how-to-configure-environment.md) a munkaterülethez:

+ Állványzat-fájlok létrehozása környezetekhez:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    További információ: [az ml Environment állvány](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Környezet regisztrálása:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    További információ: [az ml Environment Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-environment-register).

+ Regisztrált környezetek listázása:

    ```azurecli-interactive
    az ml environment list
    ```

    További információ: [az ml Environment List](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-environment-list).

+ Regisztrált környezet letöltése:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    További információ: [az ml környezet letöltése](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Környezeti konfigurációs séma

Ha a parancsot használta `az ml environment scaffold` , egy olyan sablonfájlt hoz `azureml_environment.json` létre, amely módosítható és használható egyéni környezeti konfigurációk létrehozásához a CLI használatával. A legfelső szintű objektum lazán leképezi a [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true) PYTHON SDK osztályát. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

A következő táblázat részletezi a JSON-fájl legfelső szintű mezőjét, típusát és leírását. Ha egy objektumtípus egy osztályhoz van társítva a Python SDK-val, az egyes JSON-mezők és a Python-osztály nyilvános változójának neve minden esetben meg1:1 lazult. Bizonyos esetekben előfordulhat, hogy a mező egy konstruktor argumentumhoz rendelhető, nem pedig egy osztály változó. Például a mező a `environmentVariables` `environment_variables` osztályban lévő változóra mutat [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py&preserve-view=true) .

| JSON-mező | Típus | Description |
|---|---|---|
| `name` | `string` | A környezet neve. A név nem kezdődhet a **Microsofttal** vagy a **AzureML**. |
| `version` | `string` | A környezet verziója. |
| `environmentVariables` | `{string: string}` | A környezeti változók neveinek és értékeinek kivonata. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py&preserve-view=true)a kalap meghatározza a Python-környezetet és a tolmácsot a cél számítási erőforráson való használatra. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py&preserve-view=true) | Meghatározza a környezet specifikációi alapján létrehozott Docker-rendszerkép testreszabásához szükséges beállításokat. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py&preserve-view=true) | Ez a szakasz a Spark beállításait konfigurálja. A rendszer csak akkor használja, ha a keretrendszer PySpark értékre van állítva. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py&preserve-view=true) | A Databricks-függvénytár függőségeinek konfigurálása. |
| `inferencingStackVersion` | `string` | Megadja a rendszerképhez hozzáadott következtetési verem verzióját. Ha nem szeretne hozzáadni egy következtetést, hagyja üresen ezt a mezőt `null` . Érvényes érték: "Latest". |

## <a name="ml-pipeline-management"></a>ML-folyamat kezelése

A következő parancsok bemutatják, hogyan dolgozhat a Machine learning-folyamatokkal:

+ Machine learning-folyamat létrehozása:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    További információ: [az ml-folyamat létrehozása](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-pipeline-create).

    További információ a folyamat YAML fájlról: [Machine learning-folyamatok definiálása a YAML-ben](reference-pipeline-yaml.md).

+ Folyamat futtatása:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    További információ: [az ml Run Submit-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    További információ a folyamat YAML fájlról: [Machine learning-folyamatok definiálása a YAML-ben](reference-pipeline-yaml.md).

+ Folyamat beosztása:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    További információ: [az ml-folyamat létrehozása-Schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    További információ a folyamat ütemezett YAML fájlról: [Machine learning-folyamatok definiálása a YAML-ben](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Modell regisztrálása, profilkészítés, üzembe helyezés

A következő parancsok bemutatják, hogyan regisztrálhat egy betanított modellt, majd hogyan telepítheti éles szolgáltatásként:

+ Modell regisztrálása a Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    További információ: [az ml Model Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-register).

+ Nem **kötelező** A modell megszerzésével optimális CPU-és memória-értékeket érhet el az üzembe helyezéshez.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    További információ: [az ml Model Profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-profile).

+ A modell üzembe helyezése AK-ra
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    A viszonyítási konfigurációs fájl sémával kapcsolatos további információkért lásd: a [konfigurációs séma következtetése](#inferenceconfig).
    
    További információ a központi telepítési konfigurációs fájl sémával kapcsolatban: a [központi telepítés konfigurációs sémája](#deploymentconfig).

    További információ: [az ml Model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>A konfigurációs séma következtetése

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Központi telepítési konfigurációs séma

### <a name="local-deployment-configuration-schema"></a>Helyi telepítési konfigurációs séma

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Az Azure Container instance telepítési konfigurációs sémája 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Az Azure Kubernetes szolgáltatás telepítési konfigurációs sémája

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Következő lépések

* [A Machine learning CLI bővítményre vonatkozó parancssori útmutató](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest&preserve-view=true).

* [Gépi tanulási modellek betanítása és üzembe helyezése az Azure-folyamatokkal](/azure/devops/pipelines/targets/azure-machine-learning)

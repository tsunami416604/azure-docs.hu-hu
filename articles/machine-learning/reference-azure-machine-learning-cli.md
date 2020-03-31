---
title: CLI kiterjesztés
titleSuffix: Azure Machine Learning
description: Ismerje meg az Azure Machine Learning CLI bővítményt az Azure CLI-hez. Az Azure CLI egy többplatformos parancssori segédprogram, amely lehetővé teszi, hogy az Azure-felhőben erőforrásokkal dolgozzon. A Machine Learning-bővítmény lehetővé teszi az Azure Machine Learning használatával való munkát. A ml CLI olyan erőforrásokat hoz létre és kezel, mint a munkaterület, az adattárak, az adatkészletek, a folyamatok, a modellek és a központi telepítések.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 471b26ebc4bd4aecb814ec43c7eba56e3d764fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402492"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>A CLI-bővítmény használata az Azure Machine Learninghez
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Az Azure Machine Learning CLI az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)kiterjesztése, amely az Azure platform platformfüggetlen parancssori felülete. Ez a bővítmény parancsokat biztosít az Azure Machine Learning használatával való munkához. Lehetővé teszi a gépi tanulási tevékenységek automatizálását. Az alábbi lista néhány példaműveletet tartalmaz, amelyeket a CLI-bővítménysel ellehet végezni:

+ Kísérletek futtatása gépi tanulási modellek létrehozásához

+ Gépi tanulási modellek regisztrálása az ügyfelek számára

+ A gépi tanulási modellek csomagolása, üzembe helyezése és nyomon követése

A CLI nem helyettesíti az Azure Machine Learning SDK-t. Ez egy kiegészítő eszköz, amely optimalizált kezelésére erősen paraméteres feladatokat, amelyek jól illeszkednek az automatizálás.

## <a name="prerequisites"></a>Előfeltételek

* A CLI használatához Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy ingyenes fiókot. Próbálja ki még ma [az Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

* Az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="full-reference-docs"></a>Teljes referencia-dokumentumok

Keresse meg az [Azure CLI azure-cli-ml kiterjesztésének teljes referenciadokumentumait.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest)

## <a name="install-the-extension"></a>A bővítmény telepítése

A Machine Learning CLI bővítmény telepítéséhez használja a következő parancsot:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Példa fájlokat lehet használni az alábbi parancsokat itt található [.](https://aka.ms/azml-deploy-cloud)

Amikor a rendszer `y` kéri, válassza a bővítmény telepítését.

Annak ellenőrzéséhez, hogy a bővítmény telepítve van-e, a következő paranccsal jelenítse meg a pénzmosással kapcsolatos alparancsok listáját:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>A bővítmény frissítése

A Machine Learning CLI-bővítmény frissítéséhez használja a következő parancsot:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>A bővítmény eltávolítása

A CLI-bővítmény eltávolításához használja a következő parancsot:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Erőforrás-kezelés

Az alábbi parancsok bemutatják, hogyan használhatja a CLI-t az Azure Machine Learning által használt erőforrások kezeléséhez.

+ Ha még nem rendelkezik ilyenel, hozzon létre egy erőforráscsoportot:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Hozzon létre egy Azure Machine Learning-munkaterületet:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > Ez a parancs létrehoz egy alapkiadás-munkaterületet. Vállalati munkaterület létrehozásához használja `--sku enterprise` a kapcsolót a `az ml workspace create` paranccsal. Az Azure Machine Learning-kiadásokról a [Mi az Azure Machine Learning](overview-what-is-azure-ml.md#sku)című témakörben talál további információt.

    További információ: [az ML workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ A CLI környezettudatosság engedélyezéséhez csatlakoztasson munkaterület-konfigurációt egy mappához.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Ez a `.azureml` parancs létrehoz egy alkönyvtárat, amely példa runconfig és conda környezeti fájlokat tartalmaz. Egy `config.json` olyan fájlt is tartalmaz, amely az Azure Machine Learning-munkaterülettel való kommunikációhoz használható.

    További információ: [az ml mappa csatolása](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Egy Azure blob-tároló csatolása adattárként.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    További információ: [az ml datastore attach-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Fájlok feltöltése adattárba.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    További információ: [az ML datastore upload](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload).

+ AKS-fürt csatolása számítási célként.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    További információ: [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Hozzon létre egy új AMLcompute célt.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    További információ: [az ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a name="run-experiments"></a><a id="experiments"></a>Kísérletek futtatása

* Indítsa el a kísérlet futtatását. Ha ezt a parancsot használja, adja meg a \*runconfig fájl nevét (a .runconfig előtti szöveget, ha a fájlrendszert nézi) a -c paraméterhez.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > A `az ml folder attach` parancs `.azureml` létrehoz egy alkönyvtárat, amely két példa runconfig fájlt tartalmaz. 
    >
    > Ha olyan Python-parancsfájllal rendelkezik, amely programozott módon hoz létre egy futtatási konfigurációs objektumot, a [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) segítségével runconfig fájlként mentheti azt.
    >
    > A teljes runconfig séma ebben a [JSON-fájlban](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)található. A séma öndokumentálás az `description` egyes objektumok kulcsán keresztül. Emellett vannak felsorak a lehetséges értékeket, és egy sablonkódrészlet a végén.

    További információ: [az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Kísérletek listájának megtekintése:

    ```azurecli-interactive
    az ml experiment list
    ```

    További információ: [az ml kísérlet lista](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="dataset-management"></a>Adatkészletek kezelése

Az alábbi parancsok bemutatják, hogyan dolgozhat adatkészletekkel az Azure Machine Learningben:

+ Adatkészlet regisztrálása:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Az adatkészlet definiálására használt JSON-fájl formátumával kapcsolatos `az ml dataset register --show-template`információkért használja a használatát.

    További információ: [az ml adatkészlet-regiszter](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Aktív vagy elavult adatkészlet archiválása:

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    További információ: [az ml adatkészlet-archívum.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)

+ Adatkészlet elavultlá sújtása:

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    További információ: [az ml adatkészlet elavult.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)

+ A munkaterület összes adatkészlete listázása:

    ```azurecli-interactive
    az ml dataset list
    ```

    További információ: [az ml adatkészletlista](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Adatkészlet részletei:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    További információ: [az ml dataset show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Archivált vagy elavult adatkészlet újraaktiválása:

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    További információ: [az ml adatkészlet újraaktiválása](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Adatkészlet regisztrációjának megszüntetése:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    További információ: [az ml dataset unregister](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>Környezetgazdálkodás

Az alábbi parancsok bemutatják, hogyan hozhat létre, regisztrálhat és sorolhat fel Azure Machine [Learning-környezeteket](how-to-configure-environment.md) a munkaterülethez:

+ Állványzatfájlok létrehozása környezethez:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    További információ: [az ml környezet állványzata](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Környezet regisztrálása:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    További információ: [az ml környezeti nyilvántartás](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register).

+ Regisztrált környezetek listázása:

    ```azurecli-interactive
    az ml environment list
    ```

    További információ: [az ml környezetlista](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list).

+ Regisztrált környezet letöltése:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    További információ: [az ml környezet letöltése](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Környezeti konfigurációs séma

Ha a `az ml environment scaffold` parancsot használta, létrehoz `azureml_environment.json` egy sablonfájlt, amely módosítható, és egyéni környezeti konfigurációk létrehozására használható a CLI-vel. A legfelső szintű objektum lazán [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) leképezi az osztályt a Python SDK-ban. 

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

Az alábbi táblázat részletezi a JSON-fájl minden legfelső szintű mezőjét, a típusát és a leírását. Ha egy objektumtípus a Python SDK-ból származó osztályhoz kapcsolódik, laza 1:1 egyezés van az egyes JSON-mezőés a Python-osztály nyilvános változóneve között. Bizonyos esetekben a mező osztályváltozó helyett konstruktor argumentumhoz képezhető le. A mező `environmentVariables` például az `environment_variables` [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) osztály változójára van leképezve.

| JSON mező | Típus | Leírás |
|---|---|---|
| `name` | `string` | A környezet neve. Ne kezdje el a nevet a **Microsoft** vagy az **AzureML**segítségével. |
| `version` | `string` | A környezet verziója. |
| `environmentVariables` | `{string: string}` | Környezeti változók nevének és értékeinek kivonatleképezése. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | Objektum, amely meghatározza a Python-környezet és értelmező a cél számítási erőforrás. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | A beállítások at határozza meg a környezet specifikációinak megfelelően készített Docker-rendszerkép testreszabásához. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | A szakasz konfigurálja a Spark beállításait. Csak akkor használatos, ha a keretrendszer PySpark értékre van állítva. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Konfigurálja a Databricks könyvtárfüggőségeket. |
| `inferencingStackVersion` | `string` | Megadja a lemezképhez adott viszonyítási verem verziót. Ha el szeretné kerülni a viszonyítási `null`verem hozzáadását, hagyja el ezt a mezőt. Érvényes érték: "legújabb". |

## <a name="ml-pipeline-management"></a>Ml-es folyamatkezelés

Az alábbi parancsok bemutatják, hogyan dolgozhat a gépi tanulási folyamatokkal:

+ Hozzon létre egy gépi tanulási folyamatot:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    További információ: [az ml pipeline create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create).

    A folyamat YAML-fájljával kapcsolatos további információkért [lásd: Gépi tanulási folyamatok definiálása a YAML-ben című témakörben.](reference-pipeline-yaml.md)

+ Folyamat futtatása:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    További információ: [az ml run submit-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    A folyamat YAML-fájljával kapcsolatos további információkért [lásd: Gépi tanulási folyamatok definiálása a YAML-ben című témakörben.](reference-pipeline-yaml.md)

+ Csővezeték ütemezése:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    További információ: [az ml-es folyamat létrehozási ütemezése.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule)

    A folyamatütemezésYAML-fájlról a [Gépi tanulási folyamatok definiálása a YAML-ben című témakörben](reference-pipeline-yaml.md#schedules)talál további információt.

## <a name="model-registration-profiling-deployment"></a>Modellregisztráció, profilalkotás, telepítés

A következő parancsok bemutatják, hogyan regisztrálhat egy betanított modellt, majd üzembe helyezheti éles szolgáltatásként:

+ Modell regisztrálása az Azure Machine Learning szolgáltatással:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    További információ: [az ml modellregiszter](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **NEM KÖTELEZŐ** Profil a modell, hogy optimális CPU és a memória értékek üzembe helyezéshez.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    További információ: [az ml modell profil](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ A modell üzembe helyezése az AKS-ben
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    A következtetés konfigurációs fájlsémáról további információt a [Konfigurációs séma következtetése](#inferenceconfig)című témakörben talál.
    
    A központi telepítési konfigurációs fájlsémáról további információt a [Központi telepítési konfigurációs séma című témakörben talál.](#deploymentconfig)

    További információ: [az ml modell üzembe helyezése](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Következtetéskonfigurációs séma

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Központi telepítési konfigurációs séma

### <a name="local-deployment-configuration-schema"></a>Helyi telepítési konfigurációs séma

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Az Azure Container Instance telepítési konfigurációs sémája 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Az Azure Kubernetes szolgáltatás telepítési konfigurációs sémája

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>További lépések

* [A Machine Learning CLI bővítmény parancshivatkozása.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)

* [Gépi tanulási modellek betanítása és üzembe helyezése az Azure-folyamatok használatával](/azure/devops/pipelines/targets/azure-machine-learning)

---
title: A Machine learning CLI-bővítmény
titleSuffix: Azure Machine Learning service
description: További információ az Azure Machine Learning CLI-bővítmény az Azure CLI-hez. Az Azure CLI-vel egy platformfüggetlen parancssori segédprogram, amely lehetővé teszi, hogy az erőforrások az Azure-felhőben. A Machine Learning-bővítmény lehetővé teszi, hogy az Azure Machine Learning szolgáltatás.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 601a6139b81e45fa5005b7510189eac594c29fb0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67475993"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>A CLI-bővítmény használata az Azure Machine Learning szolgáltatáshoz

Az Azure Machine Learning parancssori az való kiterjesztése a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), többplatformos parancssori felület az Azure platformon. Ez a bővítmény használata az Azure Machine Learning szolgáltatással parancsokat tartalmaz. Lehetővé teszi, hogy automatizálja a machine learning-tevékenységek. Az alábbi listában néhány példa műveleteket is végezhet a CLI-bővítmény nyújt:

+ Futtasson kísérleteket a machine learning-modellek létrehozása

+ Regisztráljon machine learning-modellek a vásárlók általi használatra

+ Csomagolását, üzembe helyezése és a gépi tanulási modellek életciklusának nyomon követése

A CLI nem helyettesíti a az Azure Machine Learning SDK-t. Egy kiegészítő eszköz, amely igény szerint maguk is a automation magas paraméteres feladatok kezelésére van optimalizálva.

## <a name="prerequisites"></a>Előfeltételek

* A CLI használatához Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* A [az Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="full-reference-docs"></a>Teljes útmutató dokumentumok

Keresse meg a [referenciadokumentumok az azure-cli-ml-bővítmény az Azure CLI teljes](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="install-the-extension"></a>A bővítmény telepítése

A Machine Learning CLI-bővítmény telepítéséhez használja a következő parancsot:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Példa fájlt is használhatja az alábbi parancsok található [Itt](https://aka.ms/azml-deploy-cloud).

Amikor a rendszer kéri, válassza ki a `y` a bővítmény telepítésére.

Győződjön meg arról, hogy a bővítmény telepítve van-e, használja a következő parancsot egy Machine Learning-specifikus almenüpontok listájának megjelenítéséhez:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Frissítse a bővítményt

A Machine Learning CLI-bővítmény frissítéséhez használja a következő parancsot:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Távolítsa el a bővítményt

A CLI-bővítmény eltávolításához használja a következő parancsot:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Erőforrás-kezelés

A következő parancsok bemutatják, hogyan használják az Azure Machine Learning-erőforrások kezelése a parancssori felület használatával.

+ Ha Ön még nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Hozzon létre egy Azure Machine Learning szolgáltatás munkaterület:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    További információkért lásd: [az ml-munkaterület létrehozása](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ A munkaterület-konfiguráció csatolása engedélyezése a parancssori felület környezetfüggő tudatosság mappába.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Ez a parancs létrehoz egy `.azureml` példa runconfig és conda-környezet fájljait tartalmazó alkönyvtárat. Emellett tartalmaz egy `config.json` fájl, amellyel kommunikálni az Azure Machine Learning-munkaterületet.

    További információkért lásd: [az ml mappát csatolni](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Egy Azure blob-tároló csatlakoztatása egy adattár.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    További információkért lásd: [az ml adattárolója attach-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

    
+ AKS-fürt csatolása számítási célként.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    További információkért lásd: [az ml computetarget aks csatolása](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Hozzon létre egy új AMLcompute célt.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    További információkért lásd: [az ml computetarget létrehozása amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a id="experiments"></a>Futtasson kísérleteket

* A kísérlet egy Futtatás elindításához. Ez a parancs használata esetén adja meg a runconfig fájl neve (a szöveg előtt \*.runconfig, ha a fájlrendszer egyszerre) - c paraméterének ellen.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > A `az ml folder attach` parancs létrehoz egy `.azureml` alkönyvtárat két példa runconfig fájlokat tartalmazza. 
    >
    > Ha rendelkezik egy Python-szkriptet, amely programozott módon futtatási konfigurációs objektumot hoz létre, akkor használhatja [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) runconfig fájlba mentéséhez.
    >
    > További példa runconfig fájlok, lásd: [ https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml ](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml).

    További információkért lásd: [az ml elküldése-parancsfájl futtatása](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Kísérletek listájának megtekintéséhez:

    ```azurecli-interactive
    az ml experiment list
    ```

    További információkért lásd: [az ml-kísérletezés lista](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="model-registration-profiling-deployment"></a>Modell regisztrálását, profilkészítésre, üzembe helyezés

A következő parancsok bemutatják, hogyan lehet regisztrálni egy betanított modellt, és majd éles szolgáltatásként üzembe:

+ Regisztrálja a modellt az Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    További információkért lásd: [az gépi tanulási modell register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **Nem kötelező** profilt a modell üzembe helyezési optimális CPU és memória értékek beolvasása.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    További információkért lásd: [az gépi tanulási modell profil](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Az aks-ben a modell üzembe helyezése
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Az alábbiakban egy példa a `inferenceconfig.json` dokumentum:
    ```json
    {
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
    }
    ```
    A következő példázza dokumentum "deploymentconfig.json":
    ```json
    {
    "computeType": "aks",
    "ComputeTarget": "akscomputetarget"
    }
    ```

    További információkért lásd: [az gépi tanulási modell-üzembehelyezés](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).


## <a name="next-steps"></a>További lépések

* [A Machine Learning CLI-bővítmény hivatkozási parancs](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Betanítása és használó Folyamatokat az Azure machine learning-modellek üzembe helyezése](/azure/devops/pipelines/targets/azure-machine-learning)

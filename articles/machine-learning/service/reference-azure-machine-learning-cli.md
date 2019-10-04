---
title: Machine learning CLI-bővítmény
titleSuffix: Azure Machine Learning
description: További információ az Azure Machine Learning CLI-bővítmény az Azure CLI-hez. Az Azure CLI-vel egy platformfüggetlen parancssori segédprogram, amely lehetővé teszi, hogy az erőforrások az Azure-felhőben. A Machine Learning-bővítmény lehetővé teszi, hogy együttműködjön a Azure Machine Learningokkal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 85ebcc36d32b86ec2640ce7ce02190deaab19d6b
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70997084"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>A CLI-bővítmény használata Azure Machine Learning

Az Azure Machine Learning parancssori az való kiterjesztése a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), többplatformos parancssori felület az Azure platformon. Ez a bővítmény parancsokat biztosít a Azure Machine Learning használatához. Lehetővé teszi a gépi tanulási tevékenységek automatizálását. Az alábbi lista néhány példát ismertet a CLI bővítménnyel:

+ Futtasson kísérleteket a machine learning-modellek létrehozása

+ Regisztráljon machine learning-modellek a vásárlók általi használatra

+ Csomagolását, üzembe helyezése és a gépi tanulási modellek életciklusának nyomon követése

A CLI nem helyettesíti a az Azure Machine Learning SDK-t. Ez egy kiegészítő eszköz, amely az automatizáláshoz jól illeszkedő, nagymértékben paraméterekkel rendelkező feladatok kezelésére van optimalizálva.

## <a name="prerequisites"></a>Előfeltételek

* A CLI használatához Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* A [az Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="full-reference-docs"></a>Teljes dokumentációs dokumentumok

Megtalálhatja az Azure [parancssori felület Azure-CLI-ml-bővítményének teljes dokumentációját](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="install-the-extension"></a>A bővítmény telepítése

A Machine Learning CLI-bővítmény telepítéséhez használja a következő parancsot:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Az alábbi parancsokkal használható fájlok [itt](https://aka.ms/azml-deploy-cloud)találhatók.

Amikor a rendszer kéri, válassza ki a `y` a bővítmény telepítésére.

Győződjön meg arról, hogy a bővítmény telepítve van-e, használja a következő parancsot egy Machine Learning-specifikus almenüpontok listájának megjelenítéséhez:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>A bővítmény frissítése

A Machine Learning CLI bővítmény frissítéséhez használja a következő parancsot:

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

+ Ha még nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Azure Machine Learning munkaterület létrehozása:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    További információ: [az ml Workspace Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Munkaterület-konfiguráció csatolása egy mappához a CLI környezetfüggő ismeretének engedélyezéséhez.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Ez a parancs létrehoz `.azureml` egy alkönyvtárat, amely tartalmazza például a runconfig és a Conda környezeti fájlokat. Emellett tartalmaz egy `config.json` fájlt is, amely a Azure Machine learning munkaterülettel folytatott kommunikációhoz használható.

    További információ: [az ml mappa csatolása](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Azure Blob-tároló csatolása adattárként.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    További információ: [az ml adattár csatolása-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Fájlok feltöltése egy adattárba.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    További információ: [az ml adattár feltöltése](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload).

+ Egy AK-fürt csatlakoztatása számítási célként.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    További információ: [az ml computetarget Attach AK](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Hozzon létre egy új AMLcompute-célt.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    További információ: [az ml computetarget Create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a id="experiments"></a>Kísérletek futtatása

* A kísérlet egy Futtatás elindításához. Ha ezt a parancsot használja, adja meg a runconfig-fájl nevét (a \*. runconfig karakterláncot, ha a fájlrendszert keresi) a-c paraméterrel.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > A `az ml folder attach` parancs létrehoz egy `.azureml` alkönyvtárat, amely két példa runconfig-fájlt tartalmaz. 
    >
    > Ha olyan Python-szkripttel rendelkezik, amely programozott módon hozza létre a futtatási konfigurációs objektumot, a [RunConfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) paranccsal mentheti RunConfig-fájlként.
    >
    > További példák a runconfig-fájlokra [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml):.

    További információ: [az ml Run Submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* A kísérletek listájának megtekintése:

    ```azurecli-interactive
    az ml experiment list
    ```

    További információ: [az ml-kísérletek listája](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="environment-management"></a>Környezetek kezelése

A következő parancsok bemutatják, hogyan hozhat létre, regisztrálhat és listázhat Azure Machine Learning [környezeteket](how-to-configure-environment.md) a munkaterülethez:

+ Állványzat-fájlok létrehozása környezetekhez:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    További információ: [az ml Environment állvány](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Környezet regisztrálása:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    További információ: [az ml Environment Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register).

+ Regisztrált környezetek listázása:

    ```azurecli-interactive
    az ml environment list
    ```

    További információ: [az ml Environment List](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list).

+ Regisztrált környezet letöltése:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    További információ: [az ml környezet letöltése](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download).

## <a name="model-registration-profiling-deployment"></a>Modell regisztrálása, profilkészítés, üzembe helyezés

A következő parancsok bemutatják, hogyan lehet regisztrálni egy betanított modellt, és majd éles szolgáltatásként üzembe:

+ Regisztrálja a modellt az Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    További információ: [az ml Model Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ Nem **kötelező** A modell megszerzésével optimális CPU-és memória-értékeket érhet el az üzembe helyezéshez.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    További információ: [az ml Model Profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ A modell üzembe helyezése AK-ra
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    A viszonyítási konfigurációs fájl sémával kapcsolatos további információkért lásd: a [konfigurációs séma következtetése](#inferenceconfig).
    
    További információ a központi telepítési konfigurációs fájl sémával kapcsolatban: a [központi telepítés konfigurációs sémája](#deploymentconfig).

    További információ: [az ml Model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>A konfigurációs séma következtetése

[!INCLUDE [inferenceconfig](../../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Központi telepítési konfigurációs séma

### <a name="local-deployment-configuration-schema"></a>Helyi telepítési konfigurációs séma

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Az Azure Container instance telepítési konfigurációs sémája 

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Az Azure Kubernetes szolgáltatás telepítési konfigurációs sémája

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>További lépések

* [A Machine learning CLI bővítményre vonatkozó parancssori útmutató](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Gépi tanulási modellek betanítása és üzembe helyezése az Azure-folyamatokkal](/azure/devops/pipelines/targets/azure-machine-learning)

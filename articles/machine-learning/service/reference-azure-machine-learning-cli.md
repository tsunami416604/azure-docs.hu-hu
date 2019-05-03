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
ms.openlocfilehash: 9cc6ad4f7b33de4d132efe63ff11c34f10b614af
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023385"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>A CLI-bővítmény használata az Azure Machine Learning szolgáltatáshoz

Az Azure Machine Learning parancssori az való kiterjesztése a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), többplatformos parancssori felület az Azure platformon. Ez a bővítmény használata az Azure Machine Learning szolgáltatással, a parancssorból parancsokat tartalmaz. Lehetővé teszi a teljes gépi tanulási munkafolyamatok automatizálása. Ha például a következő műveleteket hajthatja végre:

+ Futtasson kísérleteket a machine learning-modellek létrehozása

+ Regisztráljon machine learning-modellek a vásárlók általi használatra

+ Csomagolását, üzembe helyezése és a gépi tanulási modellek életciklusának nyomon követése

A CLI nem helyettesíti a az Azure Machine Learning SDK-t. Egy kiegészítő eszköz, amely igény szerint maguk is a automation magas paraméteres feladatok kezelésére van optimalizálva.

## <a name="prerequisites"></a>Előfeltételek

* A CLI használatához Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* A [az Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="install-the-extension"></a>A bővítmény telepítése

A Machine Learning CLI-bővítmény telepítéséhez használja a következő parancsot:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Példa fájlt is használhatja az alábbi parancsok található [Itt](http://aka.ms/azml-deploy-cloud).

Amikor a rendszer kéri, válassza ki a `y` a bővítmény telepítésére.

Győződjön meg arról, hogy a bővítmény telepítve van-e, használja a következő parancsot egy Machine Learning-specifikus almenüpontok listájának megjelenítéséhez:

```azurecli-interactive
az ml -h
```

## <a name="remove-the-extension"></a>A bővítmény eltávolítása

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

+ A munkaterület-konfiguráció csatolása engedélyezése a parancssori felület környezetfüggő tudatosság mappába.
    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

+ Egy Azure blob-tároló csatlakoztatása egy adattár.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```
    
+ AKS-fürt csatolása számítási célként.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a id="experiments"></a>Futtasson kísérleteket

* A kísérlet egy Futtatás elindításához. Ez a parancs használata esetén adja meg a runconfig fájl neve (a szöveg előtt \*.runconfig, ha a fájlrendszer egyszerre) - c paraméterének ellen.

    ```azurecli-interactive
    az ml run submit-script -c local -e testexperiment train.py
    ```

* Kísérletek listájának megtekintéséhez:

    ```azurecli-interactive
    az ml experiment list
    ```

## <a name="model-registration-profiling--deployment"></a>Modell regisztráció, a profilkészítés és az üzembe helyezés

A következő parancsok bemutatják, hogyan lehet regisztrálni egy betanított modellt, és majd éles szolgáltatásként üzembe:

+ Regisztrálja a modellt az Azure Machine Learning:

  ```azurecli-interactive
  az ml model register -n mymodel -p sklearn_regression_model.pkl
  ```

+ Az aks-ben a modell üzembe helyezése

  ```azurecli-interactive
  az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
  ```

---
title: A Machine learning CLI-bővítmény
titleSuffix: Azure Machine Learning service
description: További információ az Azure Machine Learning CLI-bővítmény az Azure CLI-hez. Az Azure CLI-vel egy platformfüggetlen parancssori segédprogram, amely lehetővé teszi, hogy az erőforrások az Azure-felhőben. A Machine Learning-bővítmény lehetővé teszi, hogy az Azure Machine Learning szolgáltatás.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: f85934b0c800ca354cc9cff02132a40c8eccea57
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014845"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>A CLI-bővítmény használata az Azure Machine Learning szolgáltatáshoz

Az Azure Machine Learning parancssori az való kiterjesztése a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest), többplatformos parancssori felület az Azure platformon. Ez a bővítmény használata az Azure Machine Learning szolgáltatással, a parancssorból parancsokat tartalmaz. Lehetővé teszi a teljes gépi tanulási munkafolyamatokat automatizáló szkriptek létrehozására. Ha például hozhat létre parancsprogramokat, amelyek a következő műveletek végrehajtásához:

+ Futtasson kísérleteket a machine learning-modellek létrehozása

+ Regisztráljon machine learning-modellek a vásárlók általi használatra

+ Csomagolását, üzembe helyezése és a gépi tanulási modellek életciklusának nyomon követése

A CLI nem helyettesíti a az Azure Machine Learning SDK-t. Egy kiegészítő eszköz, például a magas paraméteres feladatok kezelésére van optimalizálva:

* A számítási erőforrások létrehozása

* a paraméteres kísérlet beküldése

* Modell-regisztráció

* Lemezkép létrehozása

* Szolgáltatástelepítés

## <a name="prerequisites"></a>Előfeltételek


* A CLI használatához Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](http://aka.ms/AMLFree) még ma.

* A [az Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="install-the-extension"></a>A bővítmény telepítése

A Machine Learning CLI-bővítmény telepítéséhez használja a következő parancsot:

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-1.0.6-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

Amikor a rendszer kéri, válassza ki a `y` a bővítmény telepítésére.

Győződjön meg arról, hogy a bővítmény telepítve van-e, használja a következő parancsot egy Machine Learning-specifikus almenüpontok listájának megjelenítéséhez:

```azurecli-interactive
az ml -h
```

> [!TIP]
> Frissíteni kell a bővítményt __eltávolítása__ , majd __telepítése__ azt. Ez telepíti a legújabb verzióra.

## <a name="remove-the-extension"></a>Távolítsa el a bővítményt

A CLI-bővítmény eltávolításához használja a következő parancsot:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Erőforrás-kezelés

A következő parancsok bemutatják, hogyan használják az Azure Machine Learning-erőforrások kezelése a parancssori felület használatával.


+ Hozzon létre egy Azure Machine Learning szolgáltatás munkaterület:

    ```azurecli-interactive
    az ml workspace create -n myworkspace -g myresourcegroup
    ```

+ Állítsa be egy alapértelmezett munkaterületet:

    ```azurecli-interactive
    az configure --defaults aml_workspace=myworkspace group=myresourcegroup
    ```

+ Hozzon létre egy felügyelt számítási célnak, az elosztott betanítása:

    ```azurecli-interactive
    az ml computetarget create amlcompute -n mycompute --max_nodes 4 --size Standard_NC6
    ```

* Frissítse a felügyelt számítási célt:

    ```azurecli-interactive
    az ml computetarget update --name mycompute --workspace –-group --max_nodes 4 --min_nodes 2 --idle_time 300
    ```

* Csatolhat egy nem felügyelt számítási célnak képzési vagy a központi telepítés:

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a name="experiments"></a>Kísérletek

A következő parancsok bemutatják, hogyan használhatja a parancssori felület kísérletek:

* A projekt (konfiguráció futtatása) csatolása a kísérlet elküldése előtt:

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* A kísérlet egy Futtatás elindításához. Ezzel a paranccsal egy számítási célnak adja meg. Ebben a példában `local` használja a helyi számítógép használata a modell betanításához az `train.py` parancsfájlt:

    ```azurecli-interactive
    az ml run submit -c local train.py
    ```

* Az elküldött kísérletek listájának megtekintéséhez:

    ```azurecli-interactive
    az ml history list
    ```

## <a name="model-registration-image-creation--deployment"></a>Modell regisztrációs, rendszerképek létrehozását és üzembe helyezés

A következő parancsok bemutatják, hogyan lehet regisztrálni egy betanított modellt, és majd éles szolgáltatásként üzembe:

+ Regisztrálja a modellt az Azure Machine Learning:

  ```azurecli-interactive
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ A gépi tanulási modell és a függőségeit tartalmazó lemezképek létrehozásához: 

  ```azurecli-interactive
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ Egy rendszerkép üzembe helyezése az számítási célt:

  ```azurecli-interactive
  az ml service create aci -n myaciservice --image-id myimage:1
  ```

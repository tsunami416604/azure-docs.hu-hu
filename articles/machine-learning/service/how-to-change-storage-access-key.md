---
title: A Storage-fiók hozzáférési kulcsainak módosítása
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan módosíthatja a munkaterület által használt Azure Storage-fiók elérési kulcsait. Azure Machine Learning szolgáltatás egy Azure Storage-fiókot használ az adattároláshoz és a modellekhez. A Storage-fiók elérési kulcsának újragenerálása után frissítenie kell a Azure Machine Learning szolgáltatást az új kulcsok használatára.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/08/2019
ms.openlocfilehash: bc9b6053e6e2f920b826b3c14c6820b71e129aef
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882820"
---
# <a name="regenerate-storage-account-access-keys"></a>A Storage-fiók elérési kulcsainak újragenerálása

Megtudhatja, hogyan módosíthatja a Azure Machine Learning szolgáltatás által használt Azure Storage-fiókok hozzáférési kulcsait. Azure Machine Learning használhatja a Storage-fiókokat az adattároláshoz vagy a betanított modellekhez.

Biztonsági okokból előfordulhat, hogy módosítania kell egy Azure Storage-fiók hozzáférési kulcsait. A hozzáférési kulcs újralétrehozásakor a Azure Machine Learning frissíteni kell az új kulcs használatához. A Azure Machine Learning a Storage-fiókot is használhatja mind a Model Storage, mind pedig adattárként.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Machine Learning szolgáltatás munkaterületén. További információt a [Munkaterület létrehozása](how-to-manage-workspace.md) című cikkben talál.

* A [Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* A [Azure Machine learning CLI-bővítmény](reference-azure-machine-learning-cli.md).

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Mit kell frissíteni

A Storage-fiókokat a Azure Machine Learning szolgáltatás munkaterülete (naplók, modellek, Pillanatképek stb.) és adattárként használhatja. A munkaterület frissítésének folyamata egyetlen Azure CLI-parancs, amely a Storage-kulcs frissítése után futtatható. Az adattárolók frissítésének folyamata nagyobb szerepet játszik, és azt igényli, hogy mely adattárakat használja jelenleg a Storage-fiók, majd regisztrálja újra azokat.

> [!IMPORTANT]
> Frissítse a munkaterületet az Azure CLI-vel és az adattárolókkal egy időben a Python használatával. Csak az egyik vagy a másik frissítése nem elegendő, és hibákhoz vezethet, amíg mindkettő nem frissül.

Az adattárolók által használt Storage-fiókok felderítéséhez használja a következő kódot:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob" or ds.datastore_type == "AzureFile":
        print("datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Ez a kód az Azure Storage-t használó regisztrált adattárakat keres, és felsorolja a következő információkat:

* Adattár neve: Annak az adattárnak a neve, amelyhez a Storage-fiók regisztrálva van.
* Tárfiók neve: Az Azure Storage-fiók neve.
* Tároló A regisztráció során használt Storage-fiókban található tároló.

Ha létezik egy bejegyzés ahhoz a Storage-fiókhoz, amelyet a hozzáférési kulcsok újragenerálásához tervez, mentse az adattár nevét, a Storage-fiók nevét és a tároló nevét.

## <a name="update-the-access-key"></a>Az elérési kulcs frissítése

Ha Azure Machine Learning szolgáltatást szeretné frissíteni az új kulcs használatára, kövesse az alábbi lépéseket:

> [!IMPORTANT]
> Hajtsa végre az összes lépést, és frissítse a munkaterületet a parancssori felület és az adattárolók használatával a Python használatával. A csak az egyik vagy a másik frissítése hibákhoz vezethet, amíg mindkettő nem frissül.

1. A kulcs újbóli előállítása. A hozzáférési kulcsok újragenerálásával kapcsolatos információkért lásd a [Storage-fiók kezelése](/azure/storage/common/storage-account-manage#access-keys) című cikket. Mentse az új kulcsot.

1. Ha frissíteni szeretné a munkaterületet az új kulcs használatára, kövesse az alábbi lépéseket:

    1. Ha be szeretné jelentkezni a munkaterületet tartalmazó Azure-előfizetésbe az alábbi Azure CLI-paranccsal:

        ```azurecli-interactive
        az login
        ```

    1. A Azure Machine Learning bővítmény telepítéséhez használja a következő parancsot:

        ```azurecli-interactive
        az extension add -n azure-cli-ml 
        ```

    1. Ha a munkaterületet az új kulcs használatára szeretné frissíteni, használja a következő parancsot. Cserélje `myworkspace` le a nevet a Azure Machine learning-munkaterület nevére `myresourcegroup` , és a helyére írja be a munkaterületet tartalmazó Azure-erőforráscsoport nevét.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        Ez a parancs automatikusan szinkronizálja a munkaterület által használt Azure Storage-fiók új kulcsait.

1. A Storage-fiókot használó adattár (ok) újbóli regisztrálásához használja a [mit kell frissíteni](#whattoupdate) és az 1. lépésben szereplő kulcsot a következő kóddal:

    ```python
    ds = Datastore.register_azure_blob_container(workspace=ws, 
                                              datastore_name='your datastore name', 
                                              container_name='your container name',
                                              account_name='your storage account name', 
                                              account_key='new storage account key',
                                              overwrite=True)
    ```

    Mivel `overwrite=True` a meg van adva, ez a kód felülírja a meglévő regisztrációt, és frissíti azt az új kulcs használatára.

## <a name="next-steps"></a>További lépések

Az adattárolók regisztrálásával kapcsolatos további információkért tekintse [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) meg az osztály referenciáját.

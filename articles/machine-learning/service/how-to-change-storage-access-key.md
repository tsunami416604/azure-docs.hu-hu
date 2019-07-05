---
title: Tárfiók hozzáférési kulcsainak módosítása
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan módosíthatja a munkaterület által használt Azure Storage-fiók hozzáférési kulcsait. Az Azure Machine Learning szolgáltatás egy Azure Storage-fiókot használ, adatok és modellek. A tárfiók elérési kulcsának újragenerálásakor, frissítenie kell az Azure Machine Learning szolgáltatás új kulcsok használatához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 05/17/2019
ms.openlocfilehash: 488a032e177897caf2897ba6335f4e7f64dc0e4d
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543833"
---
# <a name="regenerate-storage-account-access-keys"></a>Tárfiók hozzáférési kulcsainak újragenerálása

Megtudhatja, hogyan módosíthatja az Azure Machine Learning szolgáltatás által használt Azure Storage-tárfiókok hozzáférési kulcsait. Az Azure Machine Learning a storage-fiókok segítségével tárolja az adatokat vagy a betanított modellek.

Biztonsági okokból szükség lehet módosítani az Azure Storage-fiók hozzáférési kulcsait. Amikor újragenerálja a hozzáférési kulcsot, az Azure Machine Learning frissíteni kell az új kulcs használatára. Az Azure Machine Learning használja a tárfiók mindkét modellt, és mint adattárolót.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Machine Learning szolgáltatás munkaterületén. További információkért lásd: a [hozzon létre egy munkaterületet](setup-create-workspace.md) cikk.

* A [az Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* A [Azure Machine Learning parancssori bővítmény](reference-azure-machine-learning-cli.md).

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Frissíteni kell

Storage-fiókok használható az Azure Machine Learning szolgáltatás a munkaterület (tárolja a naplókat, a modellek, a pillanatképek, stb.) és a egy adattár. A munkaterület frissítése a folyamat egyetlen Azure CLI-parancsot, és a tárfiók kulcsát frissítése után tudja futtatni. Adattárolók frissítési folyamatának bonyolultabb, és milyen adattárainak jelenleg használja a tárfiók felderítése, és újból regisztrálja a őket igényel.

> [!IMPORTANT]
> Frissítse a munkaterületet, az Azure CLI és a pythonnal egyszerre adattárainak használatával. Csak az egyik vagy másik frissítése nem elegendő, és mindkettő frissítéséig hibát okozhat.

Az adattárolók által használt tárfiókok felderítése, a következő kód használatával:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " + default_ds.account_name + ", container name: " + ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob" or ds.datastore_type == "AzureFile":
        print("datastore name: " + name + ", storage account name: " + ds.account_name + ", container name: " + ds.container_name)
```

Ez a kód bármilyen regisztrált adattárolók, amelyek használják az Azure Storage keres, és felsorolja a következő információkat:

* Adattár neve: A storage-fiókot regisztrálták az adattárolót neve.
* Tárfiók neve: Az Azure Storage-fiók neve.
* Tároló: A tároló a tárfiókban, amelyet a regisztrációt.

Ha a bejegyzés létezik a tárfiók elérési kulcsainak újragenerálása tervezi, mentse a adattárnevet, a tárfiók nevét és a tároló neve.

## <a name="update-the-access-key"></a>Az elérési kulcs frissítése

Frissítése az Azure Machine Learning szolgáltatás az új kulcsot használja, kövesse az alábbi lépéseket:

> [!IMPORTANT]
> Hajtsa végre a lépéseket, a parancssori felület és a Python használatával adattárainak a munkaterület frissítése is. Csak az egyik vagy másik frissítése hibákat okozhat, amíg mindkettő frissülnek.

1. A kulcs újragenerálása. A hozzáférési kulcs újragenerálása információkért lásd: a [kezelése a storage-fiók](/azure/storage/common/storage-account-manage#access-keys) cikk. Az új kulcs mentéséhez.

1. A munkaterület használata az új kulcs frissítéséhez használja az alábbi lépéseket:

    1. Való bejelentkezéshez az Azure-előfizetést, amely tartalmazza a munkaterület a következő Azure CLI-parancs használatával:

        ```azurecli-interactive
        az login
        ```

    1. Az Azure Machine Learning-bővítményének telepítése használja a következő parancsot:

        ```azurecli-interactive
        az extension add -n azure-cli-ml 
        ```

    1. A munkaterület használata az új kulccsal frissíti, használja a következő parancsot. Cserélje le `myworkspace` az Azure Machine Learning-munkaterület neve, és cserélje le a `myresourcegroup` az Azure-erőforráscsoportot, amely tartalmazza a munkaterület nevét.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        Ez a parancs automatikusan szinkronizálja az új kulcsokat a munkaterület által használt Azure storage-fiókban.

1. Regisztrálja újra a tárfiókot használó datastore(s), használja az értékeket a [frissíteni kell](#whattoupdate) szakaszt, és a kulcs 1. lépésben a következő kóddal:

    ```python
    ds = Datastore.register_azure_blob_container(workspace=ws, 
                                              datastore_name='your datastore name', 
                                              container_name='your container name',
                                              account_name='your storage account name', 
                                              account_key='new storage account key',
                                              overwrite=True)
    ```

    Mivel `overwrite=True` van megadva, ez a kód felülírja a meglévő regisztrációt, és úgy, hogy használja az új kulccsal frissíti.

## <a name="next-steps"></a>További lépések

A regisztrálás adattárainak további információkért lásd: a [ `Datastore` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) referencia osztály.

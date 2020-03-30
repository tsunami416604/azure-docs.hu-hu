---
title: Tárfiók hozzáférési kulcsainak módosítása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan módosíthatja a hozzáférési kulcsokat a munkaterület által használt Azure Storage-fiók. Az Azure Machine Learning egy Azure Storage-fiók használatával tárolja az adatokat és modelleket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: f1541c177cea2d223a5e7df576d95fab7eafb310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296949"
---
# <a name="regenerate-storage-account-access-keys"></a>Tárfiók hozzáférési kulcsainak újragenerálása
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ismerje meg, hogyan módosíthatja az Azure Machine Learning által használt Azure Storage-fiókok hozzáférési kulcsait. Az Azure Machine Learning a tárfiókok segítségével adatok at tárolhat, vagy a betanított modellek.

Biztonsági okokból előfordulhat, hogy módosítania kell egy Azure Storage-fiók hozzáférési kulcsait. A hozzáférési kulcs újragenerálásakor az Azure Machine Learninget frissíteni kell az új kulcs használatához. Előfordulhat, hogy az Azure Machine Learning a tárfiókot használja modelltárként és adattárként is.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. További információt a [Munkaterület létrehozása](how-to-manage-workspace.md) című cikkben talál.

* Az [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Az [Azure Machine Learning CLI bővítmény.](reference-azure-machine-learning-cli.md)

> [!NOTE]
> A jelen dokumentumban szereplő kódrészleteket a Python SDK 1.0.83-as verziójával tesztelték.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Mit kell frissíteni?

A tárfiókokat az Azure Machine Learning-munkaterület (naplók, modellek, pillanatképek stb.) és adattárként használhatja. A munkaterület frissítésének folyamata egyetlen Azure CLI-parancs, és a tárolókulcs frissítése után futtatható. Az adattárak frissítésének folyamata nagyobb szerepet vesz igénybe, és fel kell tárolni, hogy jelenleg milyen adattárak használják a tárfiókot, majd újra regisztrálni kell őket.

> [!IMPORTANT]
> Frissítse a munkaterületet az Azure CLI és az adattárak segítségével Python, ugyanabban az időben. Csak az egyik vagy a másik frissítése nem elegendő, és hibákat okozhat, amíg mindkettő frissítésre nem kerül.

Az adattárak által használt tárfiókok felderítéséhez használja a következő kódot:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Ez a kód az Azure Storage-t használó regisztrált adattárakat keresi, és a következő információkat sorolja fel:

* Adattár neve: Annak az adattárnak a neve, amely alatt a tárfiók regisztrálva van.
* Tárfiók neve: Az Azure Storage-fiók neve.
* Tároló: A tároló a tárfiókban, amely a regisztráció által használt.

Azt is jelzi, hogy az adattár egy Azure Blob vagy egy Azure-fájlmegosztáshoz, mivel különböző módszerek vannak az egyes adattártípusok újbóli regisztrálására.

Ha létezik bejegyzés ahhoz a tárfiókhoz, amelyhez hozzáférési kulcsokat kíván újragenerálni, mentse az adattár nevét, a tárfiók nevét és a tároló nevét.

## <a name="update-the-access-key"></a>A hozzáférési kulcs frissítése

Ha frissíteni szeretné az Azure Machine Learninget az új kulcs használatához, kövesse az alábbi lépéseket:

> [!IMPORTANT]
> Hajtsa végre az összes lépést, frissítse mind a munkaterületet a CLI használatával, mind az adattárak python használatával. Csak az egyik vagy a másik frissítése hibákat okozhat, amíg mindkettő frissítésre nem kerül.

1. Hozza létre újra a kulcsot. A hozzáférési kulcsok újragenerálásáról a [Tárfiók hozzáférési kulcsainak kezelése](../storage/common/storage-account-keys-manage.md)című témakörben talál további információt. Mentse az új kulcsot.

1. Ha frissíteni szeretné a munkaterületet az új kulcs használatához, kövesse az alábbi lépéseket:

    1. Jelentkezzen be a munkaterületet tartalmazó Azure-előfizetésbe a következő Azure CLI-paranccsal:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Ha frissíteni szeretné a munkaterületet az új kulcs használatához, használja a következő parancsot. Cserélje `myworkspace` le az Azure Machine Learning `myresourcegroup` munkaterület nevét, és cserélje le a munkaterületet tartalmazó Azure-erőforráscsoport nevére.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        Ez a parancs automatikusan szinkronizálja a munkaterület által használt Azure storage-fiók új kulcsait.

1. A tárfiókot használó adattár(ok) újbóli regisztrálásához használja a [Frissítendő](#whattoupdate) szakasz és az 1.

    ```python
    # Re-register the blob container
    ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                              datastore_name='your datastore name',
                                              container_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
    # Re-register file shares
    ds_file = Datastore.register_azure_file_share(workspace=ws,
                                          datastore_name='your datastore name',
                                          file_share_name='your container name',
                                          account_name='your storage account name',
                                          account_key='new storage account key',
                                          overwrite=True)
    
    ```

    Mivel `overwrite=True` meg van adva, ez a kód felülírja a meglévő regisztrációt, és frissíti az új kulcs használatához.

## <a name="next-steps"></a>További lépések

Az adattárak regisztrálásával kapcsolatos [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) további információkért tekintse meg az osztályhivatkozást.

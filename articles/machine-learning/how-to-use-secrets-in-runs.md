---
title: Titkok használata a betanítási futtatások során
titleSuffix: Azure Machine Learning
description: Titkos titkok átadása a betanítási futtatásokbiztonságos módon a Workspace Key Vault használatával
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: d877794abf12b8b412cd1ecf4efd72fd1179d768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942264"
---
# <a name="use-secrets-in-training-runs"></a>Titkok használata a betanítási futtatások során
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan használhatja a titkos kulcsokat a betanítási futtatások biztonságosan. A hitelesítési adatok, például a felhasználónév és a jelszó titkos akta. Ha például egy külső adatbázishoz csatlakozik a betanítási adatok lekérdezéséhez, akkor meg kell adnia a felhasználónevét és jelszavát a távoli futtatási környezetben. Az ilyen értékek titkosítása a betanítási parancsfájlok cleartext nem biztonságos, mivel ez felfedné a titkos kulcsot. 

Ehelyett az Azure Machine Learning-munkaterület rendelkezik egy Azure Key Vault nevű társított [erőforrással.](https://docs.microsoft.com/azure/key-vault/key-vault-overview) Ezzel a Key Vault használatával titkos kulcsokat osztolhatsz át a távoli futtatások biztonságosan az Azure Machine Learning Python SDK API-k on keresztül.

A titkos kulcsok használatának alapvető folyamata a következő:
 1. A helyi számítógépen jelentkezzen be az Azure-ba, és csatlakozzon a munkaterülethez.
 2. Helyi számítógépen állítson be titkos kulcsot a Munkaterület i key vaultjában.
 3. Távoli futtatás beküldése.
 4. A távoli futtatás, a kulcstartó, és használja azt.

## <a name="set-secrets"></a>Titkok beállítása

Az Azure Machine Learning ben a [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) osztály titkos kulcsok beállítására szolgáló módszereket tartalmaz. A helyi Python-munkamenetben először szerezzen be egy hivatkozást [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) a munkaterületi Key Vault, majd a módszer segítségével hozzon létre egy titkos név és érték. A __set_secret__ metódus frissíti a titkos értéket, ha a név már létezik.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Ne helyezze a titkos értéket a Python-kódba, mert nem biztonságos a fájlban cleartext ként tárolni. Ehelyett szerezze be a titkos értéket egy környezeti változóból, például az Azure DevOps-készítési titkos kapcsolatából vagy interaktív felhasználói bevitelből.

A titkos neveket [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) a módszerrel is felsorolhatja, és van egy kötegelt verzió is,[set_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) amely lehetővé teszi, hogy egyszerre több titkos kulcsot állítson be.

## <a name="get-secrets"></a>Titkok beszerezése

A helyi kódban a[`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) módszer segítségével lekaphatja a titkos értéket név szerint.

A beküldött [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-) futtatások [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) esetén használja [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) az osztály metódusát. Mivel egy elküldött futtatás tisztában van a munkaterületével, ez a módszer a munkaterület-példányfuttatást és a titkos értéket közvetlenül adja vissza.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Ügyeljen arra, hogy a titkos értéket ne tegye ki írással vagy kinyomtatással.

Van is egy kötegelt verzió, [get_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) eléréséhez több titkos kulcsok egyszerre.

## <a name="next-steps"></a>További lépések

 * [Példa jegyzetfüzet megtekintése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Ismerje meg a vállalati biztonságot az Azure Machine Learning segítségével](concept-enterprise-security.md)

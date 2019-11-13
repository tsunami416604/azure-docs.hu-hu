---
title: Titkok használata a betanítási futtatásokban
titleSuffix: Azure Machine Learning
description: A titkok átadása biztonságos módon, a munkaterület Key Vault használatával
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: f4420824192ff3fd967cb6676cbe1de81ce7ad4c
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953919"
---
# <a name="use-secrets-in-training-runs"></a>Titkok használata a betanítási futtatásokban
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan használhatók biztonságosan a Titkok a betanításban. Ha például egy külső adatbázishoz szeretne csatlakozni a betanítási adatai lekérdezéséhez, a felhasználónevet és a jelszót át kell adnia a távoli futtatási környezetnek. Ha ezeket az értékeket a titkosítatlan szövegben lévő betanítási parancsfájlokba szeretné írni, nem biztonságos, mert a titkos kulcsot kiteszi. 

Ehelyett a Azure Machine Learning-munkaterület társított erőforrásként [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) . Ez a Key Vault a titkos kulcsok távoli futtatására használható a Azure Machine Learning Python SDK API-k készletén keresztül.

A titkok használatának alapszintű folyamata a következő:
 1. A helyi számítógépen jelentkezzen be az Azure-ba, és kapcsolódjon a munkaterületéhez.
 2. Helyi számítógépen állítson be egy titkos kulcsot a munkaterületen Key Vault.
 3. Távoli Futtatás küldése.
 4. A távoli Futtatás alatt szerezze be a titkos kulcsot a Key értékből, és használja azt.

## <a name="set-secrets"></a>Titkos kódok beállítása

A Azure Machine Learning Python SDK-ban a [kulcstartó osztály a](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) titkok beállítására szolgáló metódusokat tartalmaz. A helyi Python-munkamenetben először szerezzen be egy hivatkozást a munkaterületre Key Vault, majd a [set_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) metódus használatával állítsa be a titkos kulcsot név és érték alapján.

```python
from azureml.core import Workspace
import os

ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Ne helyezze el a titkos értéket a Python-kódban, mert nem biztonságos a fájlként való tárolása titkosítatlan fájlként. Ehelyett szerezze be a titkos értéket egy környezeti változóból, például az Azure DevOps Build Secret vagy az interaktív felhasználói bemenet alapján.

A titkos neveket a [list_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) metódus használatával listázhatja. Ha a név már létezik, a __set_secret__ metódus frissíti a titkos értéket.

## <a name="get-secrets"></a>Titkok beolvasása

A helyi kódban használhatja a kulcstartót[. get_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) metódust a titkos érték név szerinti lekéréséhez.

A [kísérlet. Submit](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-)használatával elküldött futtatások esetén használja a [Run. get_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) metódust. Mivel az elküldött futtatások tisztában vannak a munkaterületével, ez a módszer a munkaterület-példányát, és közvetlenül a titkos értéket adja vissza.

```python
# Code in submitted run
from azureml.core import Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Ügyeljen arra, hogy a titkos értéket ne tegye elérhetővé vagy nyomtassa ki.

A set és a Get metódus a Batch-verziókkal [set_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) és [get_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) , hogy egyszerre több titkot is hozzáférjen.

## <a name="next-steps"></a>Következő lépések

 * [Példa jegyzetfüzetek megtekintése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Ismerkedjen meg a nagyvállalati biztonsággal Azure Machine Learning](concept-enterprise-security.md)

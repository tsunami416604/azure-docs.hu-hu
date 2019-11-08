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
ms.date: 08/23/2019
ms.custom: seodec18
ms.openlocfilehash: e2074cec65ea4c1df803999c6a995f73ea4227ee
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796677"
---
# <a name="use-secrets-in-training-runs"></a>Titkok használata a betanítási futtatásokban
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan használhatók biztonságosan a Titkok a betanításban. Ha például egy külső adatbázishoz szeretne csatlakozni a betanítási adatlekérdezéshez, át kell adnia a felhasználónevet és a jelszót a távoli futtatási környezethez. Ha ezeket az értékeket a titkosítatlan szövegben lévő betanítási parancsfájlokba szeretné írni, nem biztonságos, mert a titkos kulcsot kiteszi. 

Ehelyett a Azure Machine Learning-munkaterület társított erőforrásként [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) . Ez a Key Vault a titkos kulcsok távoli futtatására használható a Azure Machine Learning Python SDK API-k készletén keresztül.

A titkok használatának alapszintű folyamata a következő:
 1. A helyi számítógépen jelentkezzen be az Azure-ba, és kapcsolódjon a munkaterületéhez.
 2. Helyi számítógépen állítson be egy titkos kulcsot a munkaterületen Key Vault.
 3. Távoli Futtatás küldése.
 4. A távoli Futtatás területen szerezze be a titkos kulcsot a kulcs értékéből, és használja azt.

## <a name="set-secrets"></a>Titkos kódok beállítása

Azure Machine Learning Python SDK-ban a [kulcstartó osztály a](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) titkok beállítására szolgáló metódusokat tartalmaz. A helyi Python-munkamenetben először szerezzen be egy hivatkozást a munkaterületre Key Vault, majd a [set_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) metódus használatával állítsa be a titkos kulcsot név és érték alapján.

```python
from azureml.core import Workspace
import os

ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Ne helyezze el a titkos értéket a Python-kódban, mert nem biztonságos a fájlban tárolt fájlok titkosítatlan fájlként való tárolásához. Ehelyett szerezze be a titkos értéket a környezeti változóból, például az Azure DevOps Build Secret vagy az interaktív felhasználói bemenet alapján.

A titkos neveket a [list_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) metódussal lehet listázni. Ha a név már létezik, a __set_secret__ metódus frissíti a titkos értéket.

## <a name="get-secrets"></a>Titkok beolvasása

A helyi kódban használhatja a kulcstartót [. a _secret metódus lekérésével](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) beolvashatja a titkos értéket név alapján.

A [kísérlet. Submit](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-)használatával elküldött futtatások esetében használja a [Run. Get _secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) metódust. Mivel az elküldött futtatások tisztában vannak a munkaterületével, ez a módszer a munkaterület-példányát, és közvetlenül a titkos értéket adja vissza.

```python
# Code in submitted run
from azureml.core import Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Ügyeljen arra, hogy a titkos értéket ne tegye elérhetővé vagy nyomtassa ki.

A set és a Get metódushoz a Batch-verziók [set_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) és a [get_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) egyszerre több titkot is elérheti.

## <a name="next-steps"></a>További lépések

 * [Példa jegyzetfüzetek megtekintése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Ismerkedjen meg a nagyvállalati biztonsággal Azure Machine Learning](concept-enterprise-security.md)

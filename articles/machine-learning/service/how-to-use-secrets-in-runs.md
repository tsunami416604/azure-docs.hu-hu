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
ms.openlocfilehash: 4872ba8a707192cd61ec371fa982a076d410e918
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996575"
---
# <a name="use-secrets-in-training-runs"></a>Titkok használata a betanítási futtatásokban

Ebből a cikkből megtudhatja, hogyan használhatók biztonságosan a Titkok a betanításban. Ha például egy külső adatbázishoz szeretne csatlakozni a betanítási adatlekérdezéshez, át kell adnia a felhasználónevet és a jelszót a távoli futtatási környezethez. Az ilyen értékek kódolása nem biztonságos, mert az ilyen értékeket nem biztonságos módon kell kialakítani, mert ez a titkos kulcs. 

Ehelyett a Azure Machine Learning-munkaterület társított erőforrásként [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) . Ez a Key Vault a titkos kulcsoknak a távoli futtatáshoz való átadására használható a Azure Machine Learning Python SDK API-jai segítségével

A titkok használatának alapszintű folyamata a következő:
 1. Helyi számítógépen jelentkezzen be az Azure-ba, és kapcsolódjon a munkaterülethez
 2. A helyi számítógépen állítsa be a titkot a munkaterületen Key Vault
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

A titkos neveket a [list_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) metódussal lehet listázni. Ha a név már létezik, a __set_secret__ metódus frissíti a titkos értéket.

## <a name="get-secrets"></a>Titkok beolvasása

A helyi kódban használhatja a kulcstartót [. a _secret metódus lekérésével](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) beolvashatja a titkos értéket név alapján.

A [kísérlet. Submit](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-)használatával elküldött futtatások esetében használja a [Run. Get _secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) metódust. Mivel a beküldött futtatási funkció ismeri a munkaterületét, ez a módszer a munkaterület-példányát, és közvetlenül a titkos értéket adja vissza.

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

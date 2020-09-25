---
title: Hitelesítési Titkok a betanításban
titleSuffix: Azure Machine Learning
description: A titkok átadása biztonságos módon, a munkaterület Key Vault használatával
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 89934470dc3bf86bb2843137a2129bff13323ca0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91302077"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Hitelesítő adatokkal kapcsolatos titkos kódok használata Azure Machine Learning betanítási futtatásokban


Ebből a cikkből megtudhatja, hogyan használhatók biztonságosan a Titkok a betanításban. A hitelesítési adatok, például a Felhasználónév és a jelszó titkos kódok. Ha például egy külső adatbázishoz csatlakozik a betanítási adatai lekérdezéséhez, a felhasználónevet és a jelszót át kell adnia a távoli futtatási környezetnek. Ha ezeket az értékeket a titkosítatlan szövegben lévő betanítási parancsfájlokba szeretné írni, nem biztonságos, mert a titkos kulcsot kiteszi. 

Ehelyett a Azure Machine Learning munkaterület egy [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)nevű társított erőforrással rendelkezik. Ennek a Key Vaultnak a használatával biztonságosan továbbíthatja a titkos kulcsokat a távoli futtatáshoz a Azure Machine Learning Python SDK API-jai segítségével.

A titkok használatára szolgáló standard folyamat a következő:
 1. A helyi számítógépen jelentkezzen be az Azure-ba, és kapcsolódjon a munkaterületéhez.
 2. Helyi számítógépen állítson be egy titkos kulcsot a munkaterületen Key Vault.
 3. Távoli Futtatás küldése.
 4. A távoli Futtatás alatt szerezze be a titkot Key Vault és használja.

## <a name="set-secrets"></a>Titkos kódok beállítása

A [Azure Machine learning a kulcstartó osztály a](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true) titkok beállítására szolgáló metódusokat tartalmazza. A helyi Python-munkamenetben először szerezzen be egy hivatkozást a munkaterületre Key Vault, majd a [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=trueset-secret-name--value-) metódus használatával állítsa be a titkos kulcsot név és érték alapján. Ha a név már létezik, a __set_secret__ metódus frissíti a titkos értéket.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Ne helyezze el a titkos értéket a Python-kódban, mert nem biztonságos a fájlként való tárolása titkosítatlan fájlként. Ehelyett szerezze be a titkos értéket egy környezeti változóból, például az Azure DevOps Build Secret vagy az interaktív felhasználói bemenet alapján.

A titkos neveket a [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=truelist-secrets--) metódussal listázhatja, és egy batch-verzió is,[set_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=trueset-secrets-secrets-batch-) segítségével egyszerre több titkot is beállíthat.

## <a name="get-secrets"></a>Titkok beolvasása

A helyi kódban a [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-secret-name-) metódus használatával kérheti le a titkos értéket név alapján.

A futtatásához a [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py&preserve-view=true#&preserve-view=truesubmit-config--tags-none----kwargs-)  metódust a következő osztály használatával küldi [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-secret-name-) el: [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true) . Mivel az elküldött futtatások tisztában vannak a munkaterületével, ez a módszer a munkaterület-példányát, és közvetlenül a titkos értéket adja vissza.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Ügyeljen arra, hogy a titkos értéket ne tegye elérhetővé vagy nyomtassa ki.

Létezik egy batch-verzió is, [get_secrets ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-secrets-secrets-) , amely egyszerre több titkot is elér.

## <a name="next-steps"></a>Következő lépések

 * [Példa jegyzetfüzetek megtekintése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Ismerkedjen meg a nagyvállalati biztonsággal Azure Machine Learning](concept-enterprise-security.md)

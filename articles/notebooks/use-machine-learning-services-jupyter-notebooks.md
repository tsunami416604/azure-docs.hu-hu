---
title: Azure Machine Learning használata Azure Notebooks előzetes verzióban
description: A Azure Notebooks Preview-val használható Azure Machine Learning minta notebookok áttekintése.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 435abca83255221d438d530b63c237c08bb0b672
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860571"
---
# <a name="use-azure-machine-learning-in-azure-notebooks-preview"></a>Azure Machine Learning használata Azure Notebooks előzetes verzióban

Azure Notebooks előre konfigurálva van a szükséges környezettel, hogy működjön a [Azure Machine Learningval](/azure/machine-learning/). A különböző Machine Learning forgatókönyvek megismeréséhez könnyedén klónozott minta-projekteket telepíthet notebook-fiókjába.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="clone-the-sample-into-your-account"></a>A minta klónozása a fiókba

1. Jelentkezzen be [Azure Notebooksba](https://notebooks.azure.com/).
1. Válassza a **saját projektek** elemet a projektek irányítópultra való ugráshoz.
1. Válassza a **GitHub** -tárház feltöltése (a felfelé mutató nyíl) gombot a **GitHub-adattár** felugró ablakának megnyitásához.
1. A felugró ablakban adja meg `Azure/MachineLearningNotebooks` **a GitHub-tárházban**, adjon meg egy nevet a projektnek a projekt **neve** mezőben (például "Azure Machine learning"), adjon meg egy azonosítót a **projekt azonosítójában**, törölje a **nyilvános** elemet, ha kívánja, majd válassza az **Importálás**lehetőséget.

    ![Azure Machine Learning notebook-minta importálása notebook-fiókjába](media/azureml-import-project.png)

1. Egy-két perc után Azure Notebooks automatikusan elvégzi az új projekt irányítópultját.

## <a name="run-a-sample-notebook"></a>Minta jegyzetfüzet futtatása

1. Válassza a **00-Configuration. ipynb** elemet a jegyzetfüzet konfigurációs szakaszának elindításához, és kövesse az utasításait Azure Machine learning-munkaterület létrehozásához.

    - Mivel Azure Notebooks már tartalmazza a szükséges Python-csomagokat, egyszerűen futtathatja az előfeltételek 2. lépésében található kódrészletet az Azure ML SDK verziójának ellenőrzéséhez.

1. A konfiguráció befejezése után válassza az **01. első lépések** lehetőséget a tizenhárom különböző minta jegyzetfüzetet tartalmazó mappa megnyitásához, amelyek mindegyike magától értetődő.

## <a name="next-steps"></a>Következő lépések

Az Azure Machine Learning dokumentációja számos más erőforrást tartalmaz, amelyek végigvezetik a Machine Learning jegyzetfüzeteken belüli használatának lépésein:

- [Rövid útmutató: a Azure Machine Learning használatának első lépései a Python használatával](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#local)
- [Oktatóanyag #1: képosztályozási modell betanítása Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/tutorial-train-models-with-aml)
- [Oktatóanyag #2: lemezkép-besorolási modell üzembe helyezése az Azure Container Instanceban (ACI)](https://docs.microsoft.com/azure/machine-learning/tutorial-deploy-models-with-aml)
- [Oktatóanyag: besorolási modell betanítása automatizált gépi tanulással Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/tutorial-auto-train-models)

Tekintse meg a [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)dokumentációját is.

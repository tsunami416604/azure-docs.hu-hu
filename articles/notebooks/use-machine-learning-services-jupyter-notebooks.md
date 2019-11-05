---
title: Azure Machine Learning használata Azure Notebooks
description: A Azure Notebooks használatával használható Azure Machine Learning minta notebookok áttekintése.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 6eac5d77404c85d5481ded7e58b0cd9fab0de083
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496635"
---
# <a name="use-azure-machine-learning-in-a-notebook"></a>Azure Machine Learning használata jegyzetfüzetben

Azure Notebooks előre konfigurálva van a szükséges környezettel, hogy működjön a [Azure Machine Learningval](/azure/machine-learning/service/). A különböző Machine Learning forgatókönyvek megismeréséhez könnyedén klónozott minta-projekteket telepíthet notebook-fiókjába.

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

## <a name="next-steps"></a>További lépések

Az Azure Machine Learning dokumentációja számos más erőforrást tartalmaz, amelyek végigvezetik a Machine Learning jegyzetfüzeteken belüli használatának lépésein:

- [Rövid útmutató: a Azure Machine Learning használatának első lépései a Python használatával](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Oktatóanyag #1: képosztályozási modell betanítása Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Oktatóanyag #2: lemezkép-besorolási modell üzembe helyezése az Azure Container Instanceban (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Oktatóanyag: besorolási modell betanítása automatizált gépi tanulással Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Tekintse meg a [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)dokumentációját is.

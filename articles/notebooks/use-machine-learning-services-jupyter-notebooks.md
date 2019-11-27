---
title: Azure Machine Learning használata Azure Notebooks
description: A Azure Notebooks használatával használható Azure Machine Learning minta notebookok áttekintése.
ms.topic: article
ms.date: 12/04/2018
ms.openlocfilehash: e3c4fbdf35bf7ea1f4dddbceb9d7235c67ed13a4
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277458"
---
# <a name="use-azure-machine-learning-in-a-notebook"></a>Azure Machine Learning használata jegyzetfüzetben

Azure Notebooks előre konfigurálva van a szükséges környezettel, hogy működjön a [Azure Machine Learningval](/azure/machine-learning/service/). A mintaprojekt az jegyzetfüzet-fiókba való ismerkedés a Machine Learning forgatókönyvek széles könnyedén klónozhat.

## <a name="clone-the-sample-into-your-account"></a>A fiók a minta klónozása

1. Jelentkezzen be [Azure Notebooksba](https://notebooks.azure.com/).
1. Válassza a **saját projektek** elemet a projektek irányítópultra való ugráshoz.
1. Válassza a **GitHub** -tárház feltöltése (a felfelé mutató nyíl) gombot a **GitHub-adattár** felugró ablakának megnyitásához.
1. A felugró ablakban adja meg `Azure/MachineLearningNotebooks` **a GitHub-tárházban**, adjon meg egy nevet a projektnek a projekt **neve** mezőben (például "Azure Machine learning"), adjon meg egy azonosítót a **projekt azonosítójában**, törölje a **nyilvános** elemet, ha kívánja, majd válassza az **Importálás**lehetőséget.

    ![A jegyzetfüzet-fiókba az Azure Machine Learning Notebook minta importálása](media/azureml-import-project.png)

1. Miután egy-két percen belül Azure notebookok automatikusan megnyílik az új projekt-irányítópult.

## <a name="run-a-sample-notebook"></a>Egy minta-jegyzetfüzet futtatása

1. Válassza a **00-Configuration. ipynb** elemet a jegyzetfüzet konfigurációs szakaszának elindításához, és kövesse az utasításait Azure Machine learning-munkaterület létrehozásához.

    - Azure-jegyzetfüzetek már tartalmazza a szükséges Python-csomagokat, mert csak futtathatja a kódrészletet az Előfeltételek ellenőrzése az Azure Machine Learning SDK-verziójának 2. lépésben.

1. A konfiguráció befejezése után válassza az **01. első lépések** lehetőséget a tizenhárom különböző minta jegyzetfüzetet tartalmazó mappa megnyitásához, amelyek mindegyike magától értetődő.

## <a name="next-steps"></a>Következő lépések

Az Azure Machine Learning dokumentációja számos más erőforrást tartalmaz, amelyek végigvezetik a Machine Learning jegyzetfüzeteken belüli használatának lépésein:

- [Rövid útmutató: a Azure Machine Learning használatának első lépései a Python használatával](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Oktatóanyag #1: képosztályozási modell betanítása Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Oktatóanyag #2: lemezkép-besorolási modell üzembe helyezése az Azure Container Instanceban (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Oktatóanyag: besorolási modell betanítása automatizált gépi tanulással Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Tekintse meg a [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)dokumentációját is.

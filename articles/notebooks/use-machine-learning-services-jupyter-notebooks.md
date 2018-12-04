---
title: Az Azure Machine Learning-szolgáltatások az Azure notebookok használata |} A Microsoft Docs
description: Áttekintés az Azure Machine Learning-szolgáltatások, amelyek az Azure-jegyzetfüzeteket is használhatja a minta-jegyzetfüzeteket.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: fabd89f7591abafd68b318921fb7723f3eb7373d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856454"
---
# <a name="use-azure-machine-learning-services-in-a-notebook"></a>Az Azure Machine Learning-szolgáltatások használata a notebook

Az Azure notebookok érhető el a szükséges környezetet az előre konfigurált [Azure Machine Learning-szolgáltatások](/azure/machine-learning/service/). A mintaprojekt az jegyzetfüzet-fiókba való ismerkedés a Machine Learning forgatókönyvek széles könnyedén klónozhat.

## <a name="clone-the-sample-into-your-account"></a>A fiók a minta klónozása

1. Jelentkezzen be a [Azure notebookok](https://notebooks.azure.com/).
1. Válassza ki **saját projektek** , keresse meg a projekt irányítópultján.
1. Válassza ki a **GitHub-adattár feltöltése** (a felfelé mutató nyíl) gombra, nyissa meg a **Github-adattár feltöltése** előugró ablak.
1. Az előugró ablakban adja meg a `Azure/MachineLearningNotebooks` a **GitHub-adattár**, adjon meg egy nevet a projekt **projektnév** "Azure Machine Learning-szolgáltatásokhoz hasonlóan", adja meg az azonosítót **Projektazonosító**, törölje **nyilvános** Ha azt szeretné, majd válassza ki **importálás**.

    ![A jegyzetfüzet-fiókba az Azure Machine Learning Notebook minta importálása](media/azureml-import-project.png)

1. Miután egy-két percen belül Azure notebookok automatikusan megnyílik az új projekt-irányítópult.

## <a name="run-a-sample-notebook"></a>Egy minta-jegyzetfüzet futtatása

1. Válassza ki **00 - configuration.ipynb** indítsa el a konfigurációs szakasz a jegyzetfüzetet, és kövesse az utasításokat egy Azure Machine Learning-munkaterület létrehozása.

    - Azure-jegyzetfüzetek már tartalmazza a szükséges Python-csomagokat, mert csak futtathatja a kódrészletet az Előfeltételek ellenőrzése az Azure Machine Learning SDK-verziójának 2. lépésben.

1. Konfigurálás elvégzését követően válassza ki a **01.getting indított** navigálni a tizenhárom mintafüzetek tartalmazó mappa, amelyek mindegyike értetődő.

## <a name="next-steps"></a>További lépések

Az Azure Machine Learning-szolgáltatások dokumentációja tartalmaz számos más erőforráshoz, amely végigvezeti Önt a Machine Learning szolgáltatás belül notebookok használata:

- [Gyors útmutató: A Python használatával Azure Machine Learning használatának első lépései](/azure/machine-learning/service/quickstart-create-workspace-with-python.md)
- [#1. oktatóanyag: Betanításához egy kép osztályozási modell Azure Machine Learning szolgáltatással](/azure/machine-learning/service/tutorial-train-models-with-aml.md)
- [#2. oktatóanyag: Egy kép osztályozási modell az Azure Container Instance (ACI) üzembe helyezése](/azure/machine-learning/service/tutorial-deploy-models-with-aml.md)
- [Oktatóanyag: Az automatikus machine learning az Azure Machine Learning szolgáltatásban egy osztályozási modell betanítása](/azure/machine-learning/service/tutorial-auto-train-models.md)

Emellett a dokumentációjában talál a [Azure Machine Learning SDK Pythonhoz készült](/python/api/overview/azure/ml/intro.md?view=azure-ml-py).

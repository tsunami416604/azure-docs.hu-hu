---
title: Az Azure Machine Learning-szolgáltatások az Azure notebookok használata
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
ms.openlocfilehash: bdfc9b3e981a1b88fbae7805785d9bb4c823bccb
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55074462"
---
# <a name="use-azure-machine-learning-service-in-a-notebook"></a>Egy jegyzetfüzetet az Azure Machine Learning szolgáltatás használata

Az Azure notebookok érhető el a szükséges környezetet az előre konfigurált [Azure Machine Learning szolgáltatás](/azure/machine-learning/service/). A mintaprojekt az jegyzetfüzet-fiókba való ismerkedés a Machine Learning forgatókönyvek széles könnyedén klónozhat.

## <a name="clone-the-sample-into-your-account"></a>A fiók a minta klónozása

1. Jelentkezzen be a [Azure notebookok](https://notebooks.azure.com/).
1. Válassza ki **saját projektek** , keresse meg a projekt irányítópultján.
1. Válassza ki a **GitHub-adattár feltöltése** (a felfelé mutató nyíl) gombra, nyissa meg a **GitHub-adattár feltöltése** előugró ablak.
1. Az előugró ablakban adja meg a `Azure/MachineLearningNotebooks` a **GitHub-adattár**, adjon meg egy nevet a projekt **projektnév** például "Az Azure Machine Learning szolgáltatás", adja meg az azonosítót **Projektazonosító** , törölje **nyilvános** Ha azt szeretné, majd válassza ki **importálás**.

    ![A jegyzetfüzet-fiókba az Azure Machine Learning Notebook minta importálása](media/azureml-import-project.png)

1. Miután egy-két percen belül Azure notebookok automatikusan megnyílik az új projekt-irányítópult.

## <a name="run-a-sample-notebook"></a>Egy minta-jegyzetfüzet futtatása

1. Válassza ki **00 - configuration.ipynb** indítsa el a konfigurációs szakasz a jegyzetfüzetet, és kövesse az utasításokat egy Azure Machine Learning-munkaterület létrehozása.

    - Azure-jegyzetfüzetek már tartalmazza a szükséges Python-csomagokat, mert csak futtathatja a kódrészletet az Előfeltételek ellenőrzése az Azure Machine Learning SDK-verziójának 2. lépésben.

1. Konfigurálás elvégzését követően válassza ki a **01.getting indított** navigálni a tizenhárom mintafüzetek tartalmazó mappa, amelyek mindegyike értetődő.

## <a name="next-steps"></a>További lépések

Az Azure Machine Learning-szolgáltatások dokumentációja tartalmaz számos más erőforráshoz, amely végigvezeti Önt a Machine Learning szolgáltatás belül notebookok használata:

- [Rövid útmutató: Az Azure Machine Learning használatának első lépései a Python használatával](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [#1. oktatóanyag: Betanításához egy kép osztályozási modell Azure Machine Learning szolgáltatással](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [#2. oktatóanyag: Egy rendszerkép besorolási modell az Azure Container Instance (ACI) üzembe helyezése](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Oktatóanyag: Az automatikus machine learning az Azure Machine Learning szolgáltatásban egy osztályozási modell betanítása](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Emellett a dokumentációjában talál a [Azure Machine Learning SDK Pythonhoz készült](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

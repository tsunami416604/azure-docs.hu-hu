---
title: Az Azure Machine Learning-szolgáltatások az Azure notebookok használata
description: Áttekintés az Azure Machine Learning-szolgáltatások, amelyek az Azure-jegyzetfüzeteket is használhatja a minta-jegyzetfüzeteket.
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
ms.openlocfilehash: f591758fa6e51c420a090aa62d5160320fe15fe8
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973035"
---
# <a name="use-azure-machine-learning-service-in-a-notebook"></a>Azure Machine Learning szolgáltatás használata jegyzetfüzetben

Azure Notebooks előre konfigurálva van a [Azure Machine learning szolgáltatással](/azure/machine-learning/service/)való együttműködéshez szükséges környezettel. A mintaprojekt az jegyzetfüzet-fiókba való ismerkedés a Machine Learning forgatókönyvek széles könnyedén klónozhat.

## <a name="clone-the-sample-into-your-account"></a>A fiók a minta klónozása

1. Jelentkezzen be a [Azure notebookok](https://notebooks.azure.com/).
1. Válassza a **saját projektek** elemet a projektek irányítópultra való ugráshoz.
1. Válassza a **GitHub** -tárház feltöltése (a felfelé mutató nyíl) gombot a **GitHub-adattár** felugró ablakának megnyitásához.
1. A felugró ablakban írja be a `Azure/MachineLearningNotebooks` értéket a **GitHub-tárházba**, adja meg a projekt nevét a projekt **neve** mezőben (például "Azure Machine learning szolgáltatás"), adjon meg egy azonosítót a **projekt azonosítójában**, törölje a **nyilvános** elemet, ha kívánja, majd válassza az importálás lehetőséget..

    ![A jegyzetfüzet-fiókba az Azure Machine Learning Notebook minta importálása](media/azureml-import-project.png)

1. Miután egy-két percen belül Azure notebookok automatikusan megnyílik az új projekt-irányítópult.

## <a name="run-a-sample-notebook"></a>Egy minta-jegyzetfüzet futtatása

1. Válassza ki **00 - configuration.ipynb** indítsa el a konfigurációs szakasz a jegyzetfüzetet, és kövesse az utasításokat egy Azure Machine Learning-munkaterület létrehozása.

    - Azure-jegyzetfüzetek már tartalmazza a szükséges Python-csomagokat, mert csak futtathatja a kódrészletet az Előfeltételek ellenőrzése az Azure Machine Learning SDK-verziójának 2. lépésben.

1. A konfiguráció befejezése után válassza az **01. első lépések** lehetőséget a tizenhárom különböző minta jegyzetfüzetet tartalmazó mappa megnyitásához, amelyek mindegyike magától értetődő.

## <a name="next-steps"></a>További lépések

Az Azure Machine Learning-szolgáltatások dokumentációja tartalmaz számos más erőforráshoz, amely végigvezeti Önt a Machine Learning szolgáltatás belül notebookok használata:

- [Rövid útmutató: A Python használata a Azure Machine Learning @ no__t-0 használatának megkezdéséhez
- @no__t – 0Tutorial #1: Képbesorolási modell betanítása a Azure Machine Learning Service @ no__t-0
- @no__t – 0Tutorial #2: Rendszerkép-besorolási modell üzembe helyezése az Azure Container Instanceban (ACI) ](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Oktatóanyag: Besorolási modell betanítása automatizált gépi tanulással Azure Machine Learning Service @ no__t-0

Emellett a dokumentációjában talál a [Azure Machine Learning SDK Pythonhoz készült](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

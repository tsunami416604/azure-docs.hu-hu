---
title: Rövid útmutató-jegyzetfüzet futtatása a saját Jegyzetfüzet-kiszolgálón
titleSuffix: Azure Machine Learning service
description: Ismerkedés az Azure Machine Learning szolgáltatásban. A saját helyi notebook server használatával próbálja ki a munkaterülethez.  A munkaterület az eligazodást blokk, amellyel kísérletezhet, betanítását és gépi tanulási modellek üzembe helyezése a felhőben.
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
ms.reviewer: sgilley
author: sdgilley
ms.author: sgilley
ms.date: 03/21/2019
ms.custom: seodec18
ms.openlocfilehash: 3afea20fe02eafbf14b5162eef3a198d27140b9e
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549135"
---
# <a name="quickstart-use-your-own-notebook-server-to-get-started-with-azure-machine-learning"></a>Gyors útmutató: A saját notebook server használata az Azure Machine Learning használatának első lépései

Futtassa a kódot, amely bejelentkezik az értékeket a saját notebook server segítségével a [Azure Machine Learning szolgáltatás munkaterület](concept-azure-machine-learning-architecture.md). A munkaterület az eligazodást blokk, amellyel kísérletezhet, betanítását és a Machine Learning gépi tanulási modellek üzembe helyezése a felhőben.

Ez a rövid útmutató a saját Python-környezetet és a Jupyter Notebook Server használ. Ehhez a rövid útmutatóhoz az SDK-telepítés nélkül, tekintse meg a [a rövid útmutató: Ismerkedés az Azure Machine Learning felhőalapú notebook server használatával](quickstart-run-cloud-notebook.md) 

Ebben a rövid videó verziójának megtekintése:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="prerequisites"></a>Előfeltételek

* A Python 3.6-os notebook server, az Azure Machine Learning SDK
* Az Azure Machine Learning szolgáltatás munkaterület
* A munkaterület konfigurációs fájl (**.azureml/config.json** ).

Ezek az összes előfeltétel beolvasása [hozzon létre egy Azure Machine Learning szolgáltatás munkaterület](setup-create-workspace.md#portal).


## <a name="use-the-workspace"></a>A munkaterület használata

Hozzon létre egy parancsfájlt, vagy indítsa el a jegyzetfüzet a munkaterület konfigurációs fájl ugyanabban a címtárban. Futtassa ezt a kódot, amely az alapszintű SDK API-k segítségével nyomon követheti a Kísérletezési futtatások.

1. Egy kísérlet létrehozása a munkaterületen.
1. Egyetlen érték jelentkezzen be a kísérletet.
1. Értékek listáját jelentkezzen be a kísérletet.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=useWs)]

## <a name="view-logged-results"></a>A naplózott eredmények megtekintése

A futtatás végeztével áttekintheti a próbafuttatást az Azure Portalon. Egy URL-címet, amely az eredményeket a legutóbbi futtatás navigál, használja a következő kódot:

```python
print(run.get_portal_url())
```

Ezt a kódot adja vissza egy hivatkozást a naplózott értékeinek megtekintése a böngészőben az Azure Portal használatával.

![Az Azure Portalon naplózott értékek](./media/quickstart-run-local-notebook/logged-values.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

>[!IMPORTANT]
>Használhatja a többi Machine Learning-oktatóanyag előfeltételei itt és útmutató létrehozott erőforrások cikkeket.

Ha nem szeretné használni az ebben a cikkben létrehozott erőforrásokat, törölje azokat az díjak elkerüléséhez.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=delete)]

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott kísérletezhet, és a modellek üzembe helyezése a szükséges erőforrásokat. Jegyzetfüzet egy kódot futtatta, és a kód futtatási előzményeinek megismerte a munkaterületen, a felhőben.

> [!div class="nextstepaction"]
> [Oktatóanyag: Egy rendszerkép osztályozási modell betanítása](tutorial-train-models-with-aml.md)

Is feltárhatja [speciális példák a Githubon](https://aka.ms/aml-notebooks) vagy megtekintheti a [SDK felhasználói útmutató](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

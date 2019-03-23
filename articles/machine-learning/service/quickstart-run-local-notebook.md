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
ms.openlocfilehash: 8bb601ac5c1ed8e9ca6d2027fe1b774a938e1f0a
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58362410"
---
# <a name="quickstart-use-your-own-notebook-server-to-get-started-with-azure-machine-learning"></a>Gyors útmutató: A saját notebook server használata az Azure Machine Learning használatának első lépései

Ez a cikk a saját notebook server futtatásához használt kódot, amely be van jelentkezve a [Azure Machine Learning szolgáltatás munkaterület](concept-azure-machine-learning-architecture.md). A munkaterület az eligazodást blokk, amellyel kísérletezhet, betanítását és a Machine Learning gépi tanulási modellek üzembe helyezése a felhőben.

Ez a rövid útmutató a saját Python-környezetet és a Jupyter Notebook Server használ. Ehhez a rövid útmutatóhoz a telepítés nélkül, tekintse meg a [a rövid útmutató: Ismerkedés az Azure Machine Learning felhőalapú notebook server használatával](quickstart-run-cloud-notebook.md) 

Ebben a rövid videó verziójának megtekintése:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2G9N6]

Ebben a rövid útmutatóban a következőket hajtja végre:

* Kódot ír a munkaterületen belüli értékek naplózásához.
* A naplózott értékek megtekintése a munkaterületen.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy ingyenes fiókot megkezdése előtt. Próbálja ki a [Azure Machine Learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

## <a name="prerequisites"></a>Előfeltételek

* A Python 3.6-os notebook server, az Azure Machine Learning SDK
* Az Azure Machine Learning szolgáltatás munkaterület
* A munkaterület konfigurációs fájl (**aml_config/config.json** ).

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

A hivatkozás segítségével a böngészőjében megtekintheti a naplózott értékeket a Microsoft Azure Portalon.

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

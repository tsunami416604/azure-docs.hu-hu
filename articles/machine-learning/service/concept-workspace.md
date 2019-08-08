---
title: Mi az a munkaterület?
titleSuffix: Azure Machine Learning service
description: A munkaterület a Azure Machine Learning szolgáltatás legfelső szintű erőforrása. Megőrzi az összes képzési folyamat előzményeit, beleértve a naplókat, a metrikákat, a kimenetet és a parancsfájlok pillanatképét. Ezekkel az információkkal meghatározhatja, hogy melyik betanítási Futtatás a legjobb modellt állítja elő
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: fc3f88e627e0ce19195f6df947d4f11f5f8a73ae
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68772756"
---
# <a name="what-is-an-azure-machine-learning-service-workspace"></a>Mi az Azure Machine Learning szolgáltatás munkaterülete?

A munkaterület a Azure Machine Learning szolgáltatás legfelső szintű erőforrása, amely központosított helyet biztosít a Azure Machine Learning szolgáltatás használatakor létrehozott összes összetevővel való együttműködéshez.  A munkaterület megőrzi az összes betanítási Futtatás előzményeit, beleértve a naplókat, a metrikákat, a kimenetet és a parancsfájlok pillanatképét. Ezekkel az információkkal meghatározhatja, hogy melyik betanítási Futtatás a legjobb modellt állítja elő.  

Ha egy modellt szeretne, regisztrálja azt a munkaterületen. Ezután a regisztrált modell-és pontozási szkriptekkel üzembe helyezheti az Azure Container Instances, az Azure Kubernetes Service-t vagy egy mezőre programozható Gate array (FPGA)-t REST-alapú HTTP-végpontként. A modellt egy Azure IoT Edge eszközre is üzembe helyezheti modulként.

## <a name="taxonomy"></a>Elnevezési rendszert 

A munkaterület taxonómiaét az alábbi ábra szemlélteti:

[![Munkaterület besorolás](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

A diagram a munkaterület következő összetevőit jeleníti meg:

+ A munkaterületek tartalmazhatnak [notebookos virtuális gépeket](tutorial-1st-experiment-sdk-setup.md), valamint a Azure Machine learning futtatásához szükséges Python-környezettel konfigurált Felhőbeli erőforrásokat.
+ A [felhasználói szerepkörök](how-to-assign-roles.md) lehetővé teszik a munkaterület más felhasználókkal, csapatokkal vagy projektekkel való megosztását.
+ A kísérletek futtatásához [számítási célokat](concept-azure-machine-learning-architecture.md#compute-targets) kell használni.
+ A munkaterület létrehozásakor a [társított erőforrások](#resources) is létrejönnek Önnek.
+ A [kísérletek](concept-azure-machine-learning-architecture.md#experiments) olyan képzések, amelyeket a modellek létrehozásához használhat.  Létrehozhat és futtathat kísérleteket
    + A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + A Azure Portal [automatikus Machine learning-kísérletek (előzetes verzió)](how-to-create-portal-experiments.md) szakasza.
    + A [vizuális felület (előzetes verzió)](ui-concept-visual-interface.md).
+ A [folyamatok](concept-azure-machine-learning-architecture.md#ml-pipelines) újrafelhasználható munkafolyamatok a modell betanításához és átképzéséhez.
+ [](concept-azure-machine-learning-architecture.md#datasets-and-datastores) Az adatkészletek segítséget nyújtanak a modell betanításához és a folyamat létrehozásához használt adat kezeléséhez.
+ Ha már rendelkezik egy olyan modellel, amelyet telepíteni szeretne, hozzon létre egy regisztrált modellt.
+ Hozzon létre egy [központi telepítést](concept-azure-machine-learning-architecture.md#deployment)a regisztrált modell és egy pontozási parancsfájl használatával.

## <a name="tools-for-workspace-interaction"></a>A munkaterület-interakció eszközei

A munkaterületet a következő módokon használhatja:

+ A weben:
    + Az [Azure Portal](https://portal.azure.com)
    + A [vizuális felület (előzetes verzió)](ui-concept-visual-interface.md)
+ Python Azure Machine Learning [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) használatával
+ A parancssorban a Azure Machine Learning [CLI bővítmény](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli) használatával

## <a name="machine-learning-with-a-workspace"></a>Gépi tanulás munkaterülettel

A gépi tanulási feladatok beolvassák és/vagy leírják a munkaterületet. 

+ Futtasson egy kísérletet a modell betanításához – a kísérlet futtatása az eredményeket a munkaterületre.
+ Az automatizált ML használatával betaníthatja a modelleket – a betanítási eredményeket a munkaterületre írja.
+ Modell regisztrálása a munkaterületen.
+ Modell üzembe helyezése – a regisztrált modell használatával hozza létre a központi telepítést.
+ Újrafelhasználható munkafolyamatok létrehozása és futtatása.
+ Megtekintheti a gépi tanulási összetevőket, például a kísérleteket, a folyamatokat, a modelleket, a központi telepítéseket.
+ Modellek nyomon követése és figyelése.

## <a name="workspace-management"></a>Munkaterület-kezelés

A következő munkaterület-felügyeleti feladatokat is végrehajthatja:

| Munkaterület-felügyeleti feladat   | Portál              | SDK        | parancssori felület        |
|---------------------------|------------------|------------|------------|
| Munkaterület létrehozása        | **&check;**     | **&check;** | **&check;** |
| Számítási erőforrások létrehozása és kezelése    | **&check;**   | **&check;** |  **&check;**   |
| Munkaterület-hozzáférés kezelése    | **&check;**   | |  **&check;**    |
| Jegyzetfüzet-alapú virtuális gép létrehozása | **&check;**   | |     |

Ismerkedjen meg a szolgáltatással [egy munkaterület létrehozásával](setup-create-workspace.md).

## <a name="resources"></a>Társított erőforrások

Amikor létrehoz egy új munkaterületet, automatikusan létrehoz több Azure-a munkaterület által használt erőforrások:

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Regisztrálja a betanítás során használt Docker-tárolókat és a modellek üzembe helyezését. A költségek csökkentése érdekében az ACR a telepítési lemezképek létrehozása előtt betöltődik.
+ [Azure Storage-fiók](https://azure.microsoft.com/services/storage/): A munkaterülethez tartozó alapértelmezett adattárként van használatban.  A notebook virtuális gépekhez használt Jupyter-jegyzetfüzeteket is itt tárolja.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): A modellek figyelési információit tárolja.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): A számítási célok és a munkaterület által igényelt egyéb bizalmas információk által használt titkokat tárolja.

> [!NOTE]
> Az új verziók létrehozása mellett használhatja a meglévő Azure-szolgáltatásokat is.

## <a name="next-steps"></a>További lépések

A Azure Machine Learning szolgáltatás megkezdéséhez lásd:

+ [Azure Machine Learning szolgáltatás áttekintése](overview-what-is-azure-ml.md)
+ [Munkaterület létrehozása](setup-create-workspace.md)
+ [Munkaterületek kezelése](how-to-manage-workspace.md)
+ [Oktatóanyag: Modell betanítása](tutorial-train-models-with-aml.md)

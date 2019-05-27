---
title: Mi az, hogy egy munkaterületet
titleSuffix: Azure Machine Learning service
description: Ismerje meg, milyen egy munkaterületet, és miért van szükség, egy az Azure Machine Learning szolgáltatáshoz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/21/2019
ms.openlocfilehash: 5785bf8f6538f1d91e7a23178e29487ebee14f29
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989832"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Mi az Azure Machine Learning-munkaterület?

A munkaterületet, hogy az Azure Machine Learning szolgáltatás legfelső szintű erőforrás. Biztosít egy központi helyen hoz létre az Azure Machine Learning szolgáltatás használatakor minden összetevő dolgozhat.

A munkaterület előzményeket a betanítási futtatás, beleértve a naplók, metrikák, kimeneti és a parancsfájlok pillanatképét. Ez az információ segítségével meghatározhatja, melyik betanítási Futtatás a legjobb modellt hoz létre.  

Miután egy modellt szeretné, hogy regisztrálja az a munkaterület. Az Azure Container Instances Azure Kubernetes Service-ben vagy egy mező-programmable gate array (FPGA) REST-alapú HTTP-végpontként történő üzembe helyezéséhez használhatja a regisztrált modell és a pontozó szkripteket. A modell az Azure IoT Edge-eszköz modulként is telepítheti.

## <a name="taxonomy"></a>Besorolás 

A besorolás, a munkaterület az alábbi ábra mutatja be:

[![Munkaterület besorolás](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Az ábrán egy munkaterület a következő összetevők:

+ A munkaterület tartalmazhat [Notebook virtuális gépek](quickstart-run-cloud-notebook.md), felhőbeli erőforrások konfigurálása az Azure Machine Learning futtatásához szükséges Python-környezetet.
+ [Felhasználói szerepkörök](how-to-assign-roles.md) lehetővé teszi a munkaterület megosztása más felhasználók, csoportok vagy projektekhez.
+ [Számítási céljainak](concept-azure-machine-learning-architecture.md#compute-target) a kísérletek futtatásához használt.
+ A munkaterület létrehozásakor [kapcsolódó erőforrások](#resources) is jönnek létre az Ön számára.
+ [Kísérletek](concept-azure-machine-learning-architecture.md#experiment) a modellek létrehozásához használja a betanítási Futtatás vannak.  Hozhat létre és futtathat kísérleteket az
    + A [az Azure Machine Learning SDK Pythonhoz készült](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
    + A [machine learning-kísérletek (előzetes verzió) automatikus](how-to-create-portal-experiments.md) szakaszban az Azure Portalon.
    + A [vizuális felhasználói felületet (előzetes verzió)](ui-concept-visual-interface.md).
+ [A folyamatok](concept-azure-machine-learning-architecture.md#pipeline) képzés és a modell átképezési újrafelhasználható munkafolyamatok.
+ [Az adatkészletek](concept-azure-machine-learning-architecture.md#dataset) támogatás a felügyeleti modell betanítási és a folyamat létrehozásához használja az adatok.
+ Ha már rendelkezik egy modell számára telepíteni kívánja, létrehozhat egy [regisztrált modell](concept-azure-machine-learning-architecture.md#model-registry).
+ A regisztrált modell és a egy pontozó szkript használatával létrehozhat egy [üzembe helyezési](concept-azure-machine-learning-architecture.md#image-registry).

## <a name="tools-for-workspace-interaction"></a>Nástroje Pro munkaterület interakció

A munkaterület a következő módokon kezelheti:

+ A webes:
    + Az [Azure Portal](https://azure.portal.com)
    + A [vizuális felhasználói felületet (előzetes verzió)](ui-concept-visual-interface.md)
+ A Python használatával Azure Machine Learning [SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ A parancssorban, az Azure Machine Learning segítségével [CLI-bővítmény](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)

## <a name="machine-learning-with-a-workspace"></a>A Machine learning-munkaterület

Gépi tanulási feladatokat az olvasási és/vagy összetevők írni a munkaterülethez. 

+ -Modell betanításához kísérlet futtatása írások kísérletezhet a munkaterülethez futtatási eredmények.
+ Használja a Machine Learning - modell betanításához automatikus képzési eredmények ír a munkaterületen.
+ Regisztrálja a modellt a munkaterületen.
+ Modell üzembe helyezése – a regisztrált modell használatával hozzon létre egy központi telepítést.
+ Hozzon létre, és újra felhasználható a munkafolyamatok futtatásához.
+ Tovább a gépi tanulási összetevőket, például a kísérleteket, a folyamatok, a modellek, a központi telepítések.
+ Nyomon követheti és figyelheti a modellek.

## <a name="workspace-management"></a>Munkaterület-felügyelet

A következő munkaterület felügyeleti feladatokat is elvégezheti:

| Munkaterület felügyeleti feladat   | Portál              | SDK        | CLI        |
|---------------------------|------------------|------------|------------|
| Munkaterület létrehozása        | **&check;**     | **&check;** | **&check;** |
| Számítási erőforrások létrehozása és felügyelete    | **&check;**   | **&check;** |  **&check;**   |
| Munkaterület-hozzáférés kezelése    | **&check;**   | |  **&check;**    |
| Hozzon létre egy jegyzetfüzetet VM | **&check;**   | |     |

A szolgáltatás használatának első lépései [létre munkaterületet](setup-create-workspace.md).

## <a name="resources"></a> Társított erőforrások

Amikor létrehoz egy új munkaterületet, automatikusan létrehoz több Azure-a munkaterület által használt erőforrások:

+ [Az Azure Container Registry](https://azure.microsoft.com/services/container-registry/): Regisztrálja a docker-tárolókat, hogy a betanítás során, és a egy modell központi telepítésekor használ. A költségek csökkentésére, az ACR **Lusta betöltött** központi telepítési lemezképei létrehozásáig.
+ [Az Azure Storage-fiók](https://azure.microsoft.com/services/storage/): Az alapértelmezett adattároló mint szolgál a munkaterületen.
+ [Az Azure Application Insights](https://azure.microsoft.com/services/application-insights/): A tároló figyelése a modellek kapcsolatos információkat.
+ [Az Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Tárolók titkos kulcsok által használt számítási célozza meg, és egyéb bizalmas információkat, amelyeket a munkaterületen.

> [!NOTE]
> Mellett hoz létre új verziókat, meglévő Azure-szolgáltatásokat is használhatja.

## <a name="next-steps"></a>További lépések

Ismerkedés az Azure Machine Learning szolgáltatással, tekintse meg:

+ [Az Azure Machine Learning szolgáltatás áttekintése](overview-what-is-azure-ml.md)
+ [Munkaterület létrehozása](setup-create-workspace.md)
+ [Munkaterületek kezelése](how-to-manage-workspace.md)
+ [Oktatóanyag: A modell tanítása](tutorial-train-models-with-aml.md)

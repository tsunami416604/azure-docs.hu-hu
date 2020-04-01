---
title: Mik azok a számítási célok?
titleSuffix: Azure Machine Learning
description: Az Azure Machine Learning segítségével meghatározhatja, hogy hol szeretné betanítani vagy üzembe helyezni a modellt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 03/30/2020
ms.openlocfilehash: f9ca75943eaec2ae018b54145d872fc09294035e
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398184"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Mik azok a számítási célok az Azure Machine Learningben? 

A **számítási cél** egy kijelölt számítási erőforrás/környezet, ahol futtatja a betanítási parancsfájlt, vagy üzemelteti a szolgáltatás központi telepítését. Ez a hely lehet a helyi gép vagy egy felhőalapú számítási erőforrás. A számítási célok használatával később anélkül módosíthatja a számítási környezetet, hogy módosítania kellene a kódot.  

Egy tipikus modellfejlesztési életciklussorán a következőket teheti:
1. Kezdje azzal, hogy kis mennyiségű adatot fejleszt ki és kísérletezik. Ebben a szakaszban azt javasoljuk, hogy a helyi környezet (helyi számítógép vagy felhőalapú virtuális gép) a számítási cél. 
2. Skálázható nagyobb adatokra, vagy elosztott betanítást végez az alábbi [betanítási számítási célok](#train)egyikével.  
3. Ha a modell készen áll, telepítse egy webüzemeltetési környezetben vagy IoT-eszközön az egyik ilyen [központi telepítési számítási célokkal.](#deploy)

A számítási célokhoz használt számítási erőforrások egy [munkaterülethez](concept-workspace.md)vannak csatolva. A helyi géptől eltérő számítási erőforrásokat a munkaterület felhasználói osztják meg.

## <a name="training-compute-targets"></a><a name="train"></a>Számítási célok betanítása

Az Azure Machine Learning különböző támogatással rendelkezik a különböző számítási erőforrások között.  Saját számítási erőforrást is csatolhat, bár a különböző forgatókönyvek támogatása eltérő lehet.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

További információ a [számítási cél beállításáról és használatáról a modellbetanításhoz.](how-to-set-up-training-targets.md)

## <a name="deployment-targets"></a><a name="deploy"></a>Telepítési célok

A következő számítási erőforrások at lehet használni a modell központi telepítésének üzemeltetéséhez.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Ismerje [meg, hogy hol és hogyan telepítheti a modellt egy számítási célra.](how-to-deploy-and-where.md)

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning számítás (felügyelt)

A felügyelt számítási erőforrást az Azure Machine Learning hoz létre és kezeli. Ez a számítási gépi tanulási számítási feladatokra van optimalizálva. Az Azure Machine Learning számítási fürtjei és [számítási példányai](concept-compute-instance.md) az egyetlen felügyelt számítási feladatok. A jövőben további felügyelt számítási erőforrások is hozzáadhatók.

Azure Machine Learning-számítási példányokat (előzetes verzió) vagy a következő ből hozhat létre fürtöket:
* Azure Machine Learning Studio
* Azure Portal
* Python SDK [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py) és [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py) osztályok
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets)
* Resource Manager-sablon

Számítási fürtöket is létrehozhat az [Azure CLI gépi tanulási bővítményével.](tutorial-train-deploy-model-cli.md#create-the-compute-target-for-training)

Amikor ezeket a számítási erőforrásokat automatikusan része a munkaterület ellentétben más típusú számítási célokat.

### <a name="compute-clusters"></a>Számítási fürtök

Használhatja az Azure Machine Learning számítási fürtök betanításához és kötegelt következtetés (előzetes verzió).  Ezzel a számítási erőforrással a következőkkel rendelkezik:

* Egy- vagy többcsomópontos fürt
* Automatikus skálázás minden futtatáskor 
* Automatikus fürtkezelés és feladatütemezés 
* A CPU- és A GPU-erőforrások támogatása



## <a name="unmanaged-compute"></a>Nem felügyelt számítás

A nem felügyelt számítási cél *nem* kezeli az Azure Machine Learning. Az Azure Machine Learningen kívül hozza létre ezt a számítási célt, majd csatolja a munkaterülethez. A nem felügyelt számítási erőforrások további lépéseket igényelhetnek a gépi tanulási számítási feladatok teljesítményének fenntartásához vagy javításához.

## <a name="next-steps"></a>További lépések

Az alábbiak végrehajtásának módját ismerheti meg:
* [Számítási cél beállítása a modell betanításához](how-to-set-up-training-targets.md)
* [A modell üzembe helyezése számítási célra](how-to-deploy-and-where.md)
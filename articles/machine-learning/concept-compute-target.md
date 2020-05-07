---
title: Mik a számítási célok
titleSuffix: Azure Machine Learning
description: Itt adhatja meg, hogy hol szeretné betanítani vagy üzembe helyezni a modellt Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 03/30/2020
ms.openlocfilehash: ed65d69c18f2dbcd53324fe3cc18af8c51c546b2
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780113"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Mik azok a számítási célok Azure Machine Learning? 

A **számítási cél** egy kijelölt számítási erőforrás/környezet, amelyben futtathatja a betanítási szkriptet, vagy üzemeltetheti a szolgáltatás központi telepítését. Ez a hely lehet a helyi számítógép vagy egy felhőalapú számítási erőforrás. A számítási célok használatával könnyedén módosíthatja a számítási környezetet a kód módosítása nélkül.  

Egy tipikus modell-fejlesztési életciklus esetén a következőket teheti:
1. Első lépésként fejlesztheti és kísérletezheti kis mennyiségű adaton. Ezen a ponton javasolt a helyi környezet (helyi számítógép vagy felhőalapú virtuális gép) számítási célként való használatát. 
2. Vertikális felskálázás nagyobb mennyiségű adatokra, vagy az elosztott képzések ezen [képzési számítási célok](#train)egyikével is felhasználható.  
3. Miután a modell elkészült, üzembe helyezheti egy webüzemeltetési környezetben vagy IoT-eszközön a fenti [telepítési számítási célok](#deploy)egyikével.

A számítási célokhoz használt számítási erőforrások egy [munkaterülethez](concept-workspace.md)vannak csatolva. A helyi gépen kívüli számítási erőforrásokat a munkaterület felhasználói osztják meg.

## <a name="training-compute-targets"></a><a name="train"></a>Számítási célok betanítása

A Azure Machine Learning különböző számítási erőforrásokon keresztül eltérő támogatást nyújt.  Saját számítási erőforrást is csatolhat, bár a különböző forgatókönyvek támogatása eltérő lehet.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

További információ [a számítási cél beállításáról és használatáról a modell betanításához](how-to-set-up-training-targets.md).

## <a name="deployment-targets"></a><a name="deploy"></a>Telepítési célok

A modell központi telepítésének üzemeltetéséhez a következő számítási erőforrások használhatók.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Megtudhatja, [hol és hogyan helyezheti üzembe a modellt egy számítási célra](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning számítás (felügyelt)

A felügyelt számítási erőforrásokat Azure Machine Learning hozza létre és kezeli. Ez a számítás a gépi tanulási munkaterhelésekre van optimalizálva. Azure Machine Learning számítási fürtök és [számítási példányok](concept-compute-instance.md) az egyetlen felügyelt számítások. A későbbiekben további felügyelt számítási erőforrások is hozzáadhatók.

Létrehozhat Azure Machine Learning számítási példányokat (előzetes verzió) vagy számítási fürtöket a következő helyekről:
* Azure Machine Learning Studio
* Azure Portal
* Python SDK [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py) és [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py) osztályok
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets)
* Resource Manager-sablon

Számítási fürtöket az [Azure CLI Machine learning bővítménnyel](tutorial-train-deploy-model-cli.md#create-the-compute-target-for-training)is létrehozhat.

Ha létrehozta ezeket a számítási erőforrásokat, az automatikusan a munkaterület részét képezi, más típusú számítási céloktól eltérően.

### <a name="compute-clusters"></a>Számítási fürtök

Azure Machine Learning számítási fürtöket a betanításhoz és a Batch-következtetésekhez (előzetes verzió) is használhatja.  Ezzel a számítási erőforrással a következőket teheti:

* Egy vagy több csomópontos fürt
* Automatikus skálázás minden egyes futtatásakor 
* Fürt automatikus kezelése és feladatütemezés 
* A processzor-és a GPU-erőforrások támogatása

### <a name="supported-vm-series-and-sizes"></a>Támogatott VM-sorozatok és-méretek

Ha Azure Machine Learning felügyelt számítási erőforráshoz kiválasztja a csomópont méretét, az Azure-ban elérhető virtuálisgép-méretek közül választhat. Az Azure számos méretet kínál a különböző számítási feladatokhoz használható Linux és Windows rendszerekhez. A különböző virtuálisgép [-típusokkal és-méretekkel](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)kapcsolatos további információkért tekintse meg a következőt:.

A virtuális gépek méretének kiválasztására néhány kivétel és korlátozás vonatkozik:
* Azure Machine Learningban nem támogatottak a virtuálisgép-adatsorozatok.
* Néhány virtuálisgép-sorozat korlátozott. Ha korlátozott adatsorozatot szeretne használni, forduljon az ügyfélszolgálathoz, és igényeljen kvóta-növekedést az adatsorozathoz. Az ügyfélszolgálattal való kapcsolatfelvételsel kapcsolatos információkért lásd: [Azure-támogatási lehetőségek](https://azure.microsoft.com/support/options/)

A támogatott adatsorozatokkal és korlátozásokkal kapcsolatos további információkért tekintse meg a következő táblázatot. 

| **Támogatott VM-sorozat**  | **Korlátozások** |
|------------|------------|
| D | Nincs |
| Dv2 | Nincs |  
| DSv2 | Nincs |  
| FSv2 | Nincs |  
| M | Jóváhagyást igényel |
| NC | Nincs |    
| NCsv2 | Jóváhagyást igényel |
| NCsv3 | Jóváhagyást igényel |  
| NDs | Jóváhagyást igényel |
| NDv2 | Jóváhagyást igényel |
| NV | Nincs |
| NVv3 | Jóváhagyást igényel | 


Habár a Azure Machine Learning támogatja ezeket a virtuálisgép-sorozatokat, előfordulhat, hogy az összes Azure-régióban nem érhetők el. A virtuálisgép-sorozatokat itt tekintheti meg: [régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

## <a name="unmanaged-compute"></a>Nem felügyelt számítás

A nem felügyelt számítási célt *nem* a Azure Machine learning felügyeli. Ezt a számítási célt a Azure Machine Learningon kívül hozza létre, majd csatolja a munkaterülethez. A nem felügyelt számítási erőforrások további lépéseket igényelhetnek a gépi tanulási feladatok teljesítményének fenntartása vagy javítása érdekében.

## <a name="next-steps"></a>További lépések

Az alábbiak végrehajtásának módját ismerheti meg:
* [Számítási cél beállítása a modell betanításához](how-to-set-up-training-targets.md)
* [Modell üzembe helyezése számítási célra](how-to-deploy-and-where.md)

---
title: 'Számítási célok: a modellek betanítása és üzembe helyezése'
titleSuffix: Azure Machine Learning
description: Itt adhatja meg, hogy hol szeretné betanítani vagy üzembe helyezni a modellt Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: 75e789ac1f9c161bc67ba90062be39f986f5b140
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580585"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>Mik azok a számítási célok Azure Machine Learning? 

A **számítási cél** egy kijelölt számítási erőforrás/környezet, amelyben futtathatja a betanítási szkriptet, vagy üzemeltetheti a szolgáltatás központi telepítését. Ez a hely lehet a helyi számítógép vagy egy felhőalapú számítási erőforrás. A számítási célok használatával könnyedén módosíthatja a számítási környezetet a kód módosítása nélkül.  

Egy tipikus modell-fejlesztési életciklus esetén a következőket teheti:
1. Első lépésként fejlesztheti és kísérletezheti kis mennyiségű adaton. Ezen a ponton javasolt a helyi környezet (helyi számítógép vagy felhőalapú virtuális gép) számítási célként való használatát. 
2. Vertikális felskálázás nagyobb mennyiségű adatokra, vagy az elosztott képzések ezen [képzési számítási célok](#train)egyikével is felhasználható.  
3. Miután a modell elkészült, üzembe helyezheti egy webüzemeltetési környezetben vagy IoT-eszközön a fenti [telepítési számítási célok](#deploy)egyikével.

A számítási célokhoz használt számítási erőforrások egy [munkaterülethez](concept-workspace.md)vannak csatolva. A helyi gépen kívüli számítási erőforrásokat a munkaterület felhasználói osztják meg.

## <a name="train"></a>Számítási célok betanítása

A Azure Machine Learning különböző számítási erőforrásokon keresztül eltérő támogatást nyújt.  Saját számítási erőforrást is csatolhat, bár a különböző forgatókönyvek támogatása eltérő lehet.

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

További információ [a számítási cél beállításáról és használatáról a modell betanításához](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Telepítési célok

A modell központi telepítésének üzemeltetéséhez a következő számítási erőforrások használhatók.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

Megtudhatja, [hol és hogyan helyezheti üzembe a modellt egy számítási célra](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning számítás (felügyelt)

A felügyelt számítási erőforrásokat Azure Machine Learning hozza létre és kezeli. Ez a számítás a gépi tanulási munkaterhelésekre van optimalizálva. Azure Machine Learning számítási fürtök. A későbbiekben további felügyelt számítási erőforrások is hozzáadhatók.

Létrehozhat Azure Machine Learning számítási fürtöket Azure Machine Learning Studióban, Azure Portalban, az SDK-ban, egy Resource Manager-sablonban vagy a parancssori felületen.

Ha létrehozta ezeket a számítási erőforrásokat, az automatikusan a munkaterület részét képezi, a más típusú számítási céloktól eltérően.

Azure Machine Learning számítási fürtöket a betanításhoz és a Batch-következtetésekhez (előzetes verzió) is használhatja.  Ezzel a számítási erőforrással a következőket teheti:

* Egy vagy több csomópontos fürt
* Minden alkalommal, amikor elküld egy futtatást 
* Fürt automatikus kezelése és feladatütemezés 
* A processzor-és a GPU-erőforrások támogatása



## <a name="unmanaged-compute"></a>Nem felügyelt számítás

A nem felügyelt számítási célt *nem* a Azure Machine learning felügyeli. Ezt a számítási célt a Azure Machine Learningon kívül hozza létre, majd csatolja a munkaterülethez. A nem felügyelt számítási erőforrások további lépéseket igényelhetnek a gépi tanulási feladatok teljesítményének fenntartása vagy javítása érdekében.

## <a name="next-steps"></a>További lépések

Az alábbiak végrehajtásának módját ismerheti meg:
* [Számítási cél beállítása a modell betanításához](how-to-set-up-training-targets.md)
* [Modell üzembe helyezése számítási célra](how-to-deploy-and-where.md)
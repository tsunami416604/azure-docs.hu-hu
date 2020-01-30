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
ms.date: 11/04/2019
ms.openlocfilehash: 15bf6533106fe826c56b38cfb345c509d6d67966
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844092"
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

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

További információ [a számítási cél beállításáról és használatáról a modell betanításához](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Telepítési célok

A modell központi telepítésének üzemeltetéséhez a következő számítási erőforrások használhatók.

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

Megtudhatja, [hol és hogyan helyezheti üzembe a modellt egy számítási célra](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure Machine Learning számítás (felügyelt)

A felügyelt számítási erőforrásokat Azure Machine Learning hozza létre és kezeli. Ez a számítás a gépi tanulási munkaterhelésekre van optimalizálva. Azure Machine Learning számítási fürtök és [számítási példányok](concept-compute-instance.md) az egyetlen felügyelt számítások. A későbbiekben további felügyelt számítási erőforrások is hozzáadhatók.

A következő esetekben hozhat létre Azure Machine Learning számítási példányokat (előzetes verzió) vagy számítási fürtöket:

| | Azure Machine Learning Studio | Azure portál | SDK | Resource Manager-sablon | CLI |
|---| ----- | ----- | ----- | ----- | ----- |
| Számítási példány | igen | igen | igen | igen |  |
| Számítási fürt | igen | igen | igen | igen | igen |

Ha létrehozta ezeket a számítási erőforrásokat, az automatikusan a munkaterület részét képezi, a más típusú számítási céloktól eltérően.

> [!NOTE]
> A számítási példányok csak az **USA északi középső**régiójában, az USA 2. keleti régiójában, **Észak-Európában** vagy **Egyesült Királyság déli régiója**található munkaterületek esetében érhetők el.
>Ha a munkaterület bármely más régióban található, akkor továbbra is létrehozhat és használhat [notebookos virtuális gépet](concept-compute-instance.md#notebookvm) . 

### <a name="compute-clusters"></a>Számítási fürtök

Azure Machine Learning számítási fürtöket a betanításhoz és a Batch-következtetésekhez (előzetes verzió) is használhatja.  Ezzel a számítási erőforrással a következőket teheti:

* Egy vagy több csomópontos fürt
* Minden alkalommal, amikor elküld egy futtatást 
* Fürt automatikus kezelése és feladatütemezés 
* A processzor-és a GPU-erőforrások támogatása



## <a name="unmanaged-compute"></a>Nem felügyelt számítás

A nem felügyelt számítási célt *nem* a Azure Machine learning felügyeli. Ezt a számítási célt a Azure Machine Learningon kívül hozza létre, majd csatolja a munkaterülethez. A nem felügyelt számítási erőforrások további lépéseket igényelhetnek a gépi tanulási feladatok teljesítményének fenntartása vagy javítása érdekében.

## <a name="next-steps"></a>Következő lépések

A webinárium témái:
* [Számítási cél beállítása a modell betanításához](how-to-set-up-training-targets.md)
* [Modell üzembe helyezése számítási célra](how-to-deploy-and-where.md)
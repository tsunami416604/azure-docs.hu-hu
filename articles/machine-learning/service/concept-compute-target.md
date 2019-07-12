---
title: 'Számítási céljainak: hol betanítása és a modellek üzembe helyezése'
titleSuffix: Azure Machine Learning service
description: Adja meg, ahol szeretne betanítani vagy üzembe helyezését az Azure Machine Learning szolgáltatással.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 07/10/2019
ms.openlocfilehash: a7944b284a9c1c0424af54874554d05d49ad4b20
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806042"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning-service"></a>Mik a számítási célokhoz, az Azure Machine Learning szolgáltatásban? 

A **számítási célt** kijelölt számítási erőforrás/környezetek van, amelyen futtatja a tanítási szkriptet vagy a gazdagép helyezheti üzembe. Ezen a helyen a helyi számítógépre vagy egy olyan felhőalapú számítási erőforrásra lehet. Segítségével számítási a tárolók megkönnyítik a később módosíthatja a számítási környezetben a kód módosítása nélkül.  

A tipikus modell fejlesztési életciklus során, előfordulhat, hogy:
1. Első lépésként a fejlesztés és kísérletezés az egy kisebb mennyiségű adatot. Ezen a ponton, javasoljuk, hogy a helyi környezetben (helyi számítógép vagy felhőalapú virtuális Gépen), a számítási célnak. 
2. Vertikális felskálázás nagyobb adatokhoz, vagy ezek egyikét képzést elosztott [képzési számítási céljainak](#train).  
3. Ha készen áll a modellt, helyezze üzembe azt egy webes üzemeltetési környezetben vagy ezek egyikét az IoT device [számítási céljainak](#deploy).

A számítási erőforrások után a számítási példányok is csatlakozik egy [munkaterület](concept-workspace.md). A számítási erőforrásokat a helyi gép nem a felhasználók a munkaterület megosztott.

## <a name="train"></a> Képzési számítási célnak

Az Azure Machine Learning szolgáltatás különböző támogatással rendelkezik a különböző számítási erőforrások között.  Bár a csoporthoz is hozzáadhat a saját számítási erőforrások eltérőek lehetnek a különböző forgatókönyvek támogatása.

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]

Tudjon meg többet [beállítása és használata egy számítási célnak modell betanítása](how-to-set-up-training-targets.md).

## <a name="deploy"></a>Telepítési célok

A következő számítási erőforrások a modell-üzembehelyezés üzemeltetéséhez használható.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

Ismerje meg, [hol és hogyan helyezheti üzembe a modellt egy számítási célnak](how-to-deploy-and-where.md).

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Az Azure Machine Learning compute (felügyelt)

Felügyelt számítási erőforrás létrejött, és az Azure Machine Learning szolgáltatás kezeli. A számítási machine learning számítási feladatokhoz van optimalizálva. A kizárólag felügyelt számítási 2019. május 30-tól az Azure Machine Learning Compute. A jövőben további felügyelt számítási erőforrások vehetők fel.

Használhatja az Azure Machine Learning Compute a betanításhoz és a batch következtetési (előzetes verzió).  A számítási erőforrással rendelkezik:

* Egy vagy több node fürt
* Elvégzi az automatikus skálázást egy Futtatás minden elküldésekor 
* Automatikus fürtfelügyelethez és -feladat ütemezése 
* Processzor-és GPU támogatása

Az Azure Machine Learning COMPUTE számítási példányok az Azure Portalon, az SDK-val vagy a CLI-vel is létrehozhat. Automatikusan a munkaterületet, ellentétben más típusú számítási célnak részét képezi, létrehozásakor.

## <a name="unmanaged-compute"></a>Nem felügyelt számítási

Van egy nem felügyelt számítási célnak *nem* Azure Machine Learning szolgáltatás kezeli. Hozzon létre ilyen típusú kívül az Azure Machine Learning számítási célnak, majd csatolja a munkaterületet. Nem felügyelt számítási erőforrások karbantartása, illetve a machine learning számítási feladatokhoz a teljesítmény javítása szükséges további lépéseket is szükséges.

## <a name="next-steps"></a>További lépések

Az alábbiak végrehajtásának módját ismerheti meg:
* [A modell betanításához egy számítási célnak beállítása](how-to-set-up-training-targets.md)
* [A modell üzembe helyezése egy számítási célnak](how-to-deploy-and-where.md)
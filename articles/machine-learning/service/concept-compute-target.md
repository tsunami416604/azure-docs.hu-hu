---
title: Számítási céljainak
titleSuffix: Azure Machine Learning service
description: Egy számítási célnak lehetővé teszi, hogy adja meg, amelyen futtatja a tanítási szkriptet vagy a gazdagép helyezheti üzembe számítási erőforrásokat. Ezen a helyen a helyi számítógépre vagy egy olyan felhőalapú számítási erőforrásra lehet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/30/2019
ms.openlocfilehash: 42c0f5460a63b781aafdd43410761e2d7b17944d
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755350"
---
#  <a name="what-is-a-compute-target-in-azure-machine-learning-service"></a>Mi az Azure Machine Learning szolgáltatásban egy számítási célnak? 

Egy számítási célnak lehetővé teszi, hogy adja meg, amelyen futtatja a tanítási szkriptet vagy a gazdagép helyezheti üzembe számítási erőforrásokat. Ezen a helyen a helyi számítógépre vagy egy olyan felhőalapú számítási erőforrásra lehet.

Számítási célnak megkönnyítik a számítási környezetben módosítsa a kód módosítása nélkül.  Egy tipikus modell fejlesztési életciklus során:

* Indítsa el a dev/Kísérletezési a kisebb mennyiségű adatot. Ezen a ponton használatát javasoljuk a helyi környezetben. Például a helyi számítógépen vagy egy felhőalapú virtuális Gépen.
* Vertikális felskálázás a tanítási a nagyobb adatkészletek, vagy az egyik képzési elosztott a [képzési célok](#train).  
* Számos webes üzemeltetési környezetek vagy IoT-eszközök használatával történő üzembe helyezéséhez a [telepítési céljainak](#deploy).

A számítási erőforrások után a számítási példányok is csatlakozik egy [munkaterület](concept-workspace.md). A számítási erőforrásokat a helyi gép nem a felhasználók a munkaterület megosztott.

## <a name="train"></a> Képzési célok

Az Azure Machine Learning szolgáltatás különböző támogatással rendelkezik a különböző számítási erőforrások között.  Bár a különböző forgatókönyvekben eltérőek lehetnek az alábbiakban ismertetett támogatási is hozzáadhat a saját számítási erőforrás:

[!INCLUDE [aml-compute-target-train](../../../includes/aml-compute-target-train.md)]


## <a name="deploy"></a>Telepítési célok

A következő számítási erőforrások a modell-üzembehelyezés üzemeltetéséhez használható.

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]


## <a name="managed-compute"></a>Felügyelt számítás

Felügyelt számítási erőforrás létrejött, és az Azure Machine Learning szolgáltatás kezeli. A számítási machine learning számítási feladatokhoz van optimalizálva. A kizárólag felügyelt számítási 2019. május 30-tól az Azure Machine Learning Compute. A jövőben további felügyelt számítási erőforrások vehetők fel.

### <a name="amlcompute"></a> Az Azure Machine Learning Compute

Használhatja az Azure Machine Learning Compute a betanításhoz és a batch következtetési (előzetes verzió).  A számítási erőforrással rendelkezik:

* Egy vagy több node fürt
* Elvégzi az automatikus skálázást egy Futtatás minden elküldésekor 
* Automatikus fürtfelügyelethez és -feladat ütemezése 
* Processzor-és GPU támogatása

Az alábbi az Azure Machine Learning COMPUTE számítási példányok hozhat létre:

* Az Azure Portal
* Az Azure Machine Learning SDK
* Az Azure CLI

Minden egyéb számítási erőforrások a munkaterület alkalmazáson kívül létrehozott legyen, és ezután csatlakozik.

## <a name="unmanaged-compute"></a>Nem felügyelt számítási

Egy nem felügyelt számítási erőforrás *nem* Azure Machine Learning szolgáltatás kezeli. Az ilyen típusú kívül az Azure Machine Learning compute létrehoz, majd csatolja a munkaterületet. Nem felügyelt számítási erőforrások karbantartása, illetve a machine learning számítási feladatokhoz a teljesítmény javítása szükséges további lépéseket is szükséges.

## <a name="next-steps"></a>További lépések

* [Állítsa be a modell betanítása és számítási célnak](how-to-set-up-training-targets.md)
* [Az Azure Machine Learning szolgáltatással modellek üzembe helyezése](how-to-deploy-and-where.md)
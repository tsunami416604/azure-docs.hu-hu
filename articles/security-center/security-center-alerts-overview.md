---
title: Biztonsági riasztások a Azure Security Centerban | Microsoft Docs
description: Ez a témakör ismerteti a biztonsági riasztásokat és a Azure Security Centerban elérhető különböző típusokat.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 8/20/2019
ms.author: v-mohabe
ms.openlocfilehash: 25fb94afdfe68510c66cff130a3abc8b74d7311d
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647312"
---
# <a name="security-alerts-in-azure-security-center"></a>Biztonsági riasztások Azure Security Center

Ez a cikk a Azure Security Centerban (ASC) elérhető biztonsági riasztások különböző típusait ismerteti. Számos különböző erőforrás-típussal kapcsolatos riasztás létezik. Az ASC riasztásokat hoz létre az Azure-ban üzembe helyezett összes erőforrásra, valamint a helyszíni és hibrid felhőalapú környezetekben üzembe helyezett erőforrásokra vonatkozóan. 

## <a name="what-are-security-alerts"></a>Mik azok a biztonsági riasztások?

A riasztások a Security Center által generált értesítések, amikor észlelik a fenyegetéseket az erőforrásokon. Rangsorolja és felsorolja a riasztásokat, valamint azokat az információkat, amelyek a probléma gyors kivizsgálásához szükségesek. A Security Center javaslatokat is tartalmaz a támadások megoldásához.

## <a name="how-does-security-center-detect-threats"></a>Hogyan ismeri fel Security Center a fenyegetéseket?

A valós fenyegetések észlelése és a téves pozitív állapotok csökkentése érdekében Security Center gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózat és a csatlakoztatott partneri megoldások, például a tűzfal és az Endpoint Protection-megoldások naplójának adatait. Security Center elemzi ezeket az információkat, és gyakran korrelál több forrásból származó információkat a fenyegetések azonosítása érdekében.

Az ASC figyeli az Azure-ban üzembe helyezett vagy más helyszíni és hibrid felhőalapú környezetekben üzembe helyezett erőforrásokat. A fenyegetések észlelésével és reagálásával kapcsolatos további információkért lásd: [How Security Center észleli és reagál a fenyegetésekre](security-center-detection-capabilities.md#asc-detects).

## <a name="security-alert-types"></a>Biztonsági riasztások típusai

Az alábbi témakörök a különböző ASC-riasztásokat ismertetik az erőforrástípusok szerint:

* [IaaS virtuális gépek &-kiszolgálók riasztásai](security-center-alerts-iaas.md)
* [Natív számítási riasztások](security-center-alerts-compute.md)
* [Adatszolgáltatási riasztások](security-center-alerts-data-services.md)

A következő témakörök azt ismertetik, hogy Security Center milyen módon használja fel az Azure-infrastruktúra integrálásával gyűjtött különböző telemetria az Azure-ban üzembe helyezett erőforrások további védelmi rétegeinek alkalmazása érdekében:

* [Szolgáltatási réteggel kapcsolatos riasztások](security-center-alerts-service-layer.md)
* [Integráció az Azure Security-termékekkel](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>Mik azok a riasztási incidensek?

A biztonsági incidensek a kapcsolódó riasztások gyűjteményei, az egyes riasztások egyenkénti listázása helyett. A Security Center a [Felhőbeli intelligens riasztások korrelációját](security-center-alerts-cloud-smart.md) használja a különböző riasztások és az alacsony megbízhatósági jelzések biztonsági incidensekre való összekapcsolásához.

Az incidensek használatával a Security Center egyetlen nézetet biztosít a támadási kampányokról és az összes kapcsolódó riasztásról. Ez a nézet lehetővé teszi, hogy gyorsan megértse, milyen műveleteket végeztek a támadó, és milyen erőforrásokra volt hatással. További információ: [felhőalapú intelligens riasztás korrelációja](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Ismerkedés a riasztásokkal

Tekintse meg az alábbi témaköröket, amelyekkel jobban megismerheti az ASC által figyelt erőforrásokat, valamint az ASC által bemutatott riasztásokra való reagálásra vonatkozó útmutatást.

* Az ASC által védett platformok és szolgáltatások megtekintéséhez lásd: [Azure Security Center által támogatott platformok és szolgáltatások](security-center-os-coverage.md).  
* A biztonsági incidensek és az ASC által reagált információk megismeréséhez tekintse meg a [biztonsági incidensek kezelése a Azure Security Center-ben](security-center-incident.md)című témakört. 
* A kapott riasztások kezelésével kapcsolatos további információkért lásd: a [biztonsági riasztások kezelése és válaszadás a Azure Security Center](security-center-managing-and-responding-alerts.md).
* További információ arról, hogy miként ellenőrizhető, hogy a Security Center megfelelően van-e konfigurálva, és hogyan ösztönözheti a riasztásokat a [riasztások érvényesítése Azure Security Center](security-center-alert-validation.md).  


## <a name="upgrade-to-standard-for-advanced-detections"></a>A standard verzióra való frissítés speciális észlelések esetén

A speciális észlelések beállításához frissítsen az Azure Security Center Standard verzióra. 

1. A Security Center menüben válassza a **biztonsági házirend**elemet.
2. A standard szintű csomagra áthelyezni kívánt előfizetések esetében kattintson a **beállítások szerkesztése**elemre. 
3. A beállítások lapon válassza ki az **árképzési szintet**. 
   Egy hónapig ingyenes próbaverzió érhető el. További információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/security-center/). 

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, milyen biztonsági riasztások és milyen típusú riasztások érhetők el a Security Centerban. További információkért tekintse át a következők témaköröket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Security Center gyakori kérdések](https://docs.microsoft.com/azure/security-center/security-center-faq): Gyakori kérdések a szolgáltatás használatával kapcsolatban.


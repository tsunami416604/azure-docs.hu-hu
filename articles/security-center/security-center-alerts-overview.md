---
title: Biztonsági riasztások a Azure Security Centerban | Microsoft Docs
description: Ez a témakör ismerteti a biztonsági riasztásokat, valamint a Azure Security Centerban elérhető különböző típusokat.
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
ms.openlocfilehash: d905096432c6e05e5386631480e5cc45b5c890c5
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013298"
---
# <a name="security-alerts-in-azure-security-center"></a>Biztonsági riasztások az Azure Security Centerben

Azure Security Center számos különböző típusú erőforráshoz tartozó riasztás létezik. A Security Center riasztásokat hoz létre az Azure-ban üzembe helyezett erőforrásokról, valamint a helyszíni és hibrid felhőalapú környezetekben üzembe helyezett erőforrásokról is. 

## <a name="what-are-security-alerts"></a>Mik azok a biztonsági riasztások?

A riasztások a Security Center által generált értesítések, amikor észlelik a fenyegetéseket az erőforrásokon. Rangsorolja és felsorolja a riasztásokat, valamint azokat az információkat, amelyek a probléma gyors kivizsgálásához szükségesek. A Security Center javaslatokat is tartalmaz a támadások megoldásához.

## <a name="how-does-security-center-detect-threats"></a>Hogyan ismeri fel Security Center a fenyegetéseket?

A valós fenyegetések észlelése és a téves pozitív állapotok csökkentése érdekében Security Center gyűjti, elemzi és integrálja az Azure-erőforrások és a hálózat naplófájljait. Együttműködik továbbá a csatlakoztatott partneri megoldásokkal, például a tűzfal-és Endpoint Protection-megoldásokkal. Security Center elemzi ezeket az információkat, és gyakran korrelál több forrásból származó információkat a fenyegetések azonosítása érdekében.

Security Center figyeli az erőforrásokat bármilyen környezetben. A fenyegetések észlelésével és reagálásával kapcsolatos további információkért lásd: [How Security Center észleli és reagál a fenyegetésekre](security-center-detection-capabilities.md#asc-detects).

## <a name="security-alert-types"></a>Biztonsági riasztások típusai

Az alábbi témakörök a különböző riasztásokat ismertetik az erőforrástípusok szerint:

* [IaaS virtuális gépek és kiszolgálók riasztásai](security-center-alerts-iaas.md)
* [Natív számítási riasztások](security-center-alerts-compute.md)
* [Adatszolgáltatási riasztások](security-center-alerts-data-services.md)

A következő témakörök azt ismertetik, hogyan használják a Security Center az Azure-infrastruktúrával való integrálás során gyűjtött különböző telemetria, hogy az Azure-ban üzembe helyezett erőforrások további védelmi rétegeit is alkalmazni lehessen:

* [Szolgáltatási réteggel kapcsolatos riasztások](security-center-alerts-service-layer.md)
* [Integráció az Azure Security-termékekkel](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>Mik azok a biztonsági incidensek?

A biztonsági incidensek a kapcsolódó riasztások gyűjteményei, az egyes riasztások egyenkénti listázása helyett. A Security Center a [Felhőbeli intelligens riasztások korrelációját](security-center-alerts-cloud-smart.md) használja a különböző riasztások és az alacsony megbízhatósági jelzések biztonsági incidensekre való összekapcsolásához.

Az incidensek használatával a Security Center egyetlen nézetet biztosít a támadási kampányokról és az összes kapcsolódó riasztásról. Ez a nézet lehetővé teszi, hogy gyorsan megértse, milyen műveleteket tartott a támadó, és milyen erőforrásokat érintettek. További információ: [felhőalapú intelligens riasztás korrelációja](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Ismerkedés a riasztásokkal

Az alábbiakban olyan cikkek érhetők el, amelyek segítenek a Security Center által figyelt erőforrásokkal kapcsolatban, valamint útmutatást nyújtanak a bemutatott riasztásokra való válaszadáshoz.

* [Az Azure Security Center által támogatott platformok és szolgáltatások](security-center-os-coverage.md)  
* [A biztonsági incidensek kezelése a Azure Security Centerban](security-center-incident.md) 
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md)
* [Riasztások érvényesítése az Azure Security Centerben](security-center-alert-validation.md)  


## <a name="upgrade-to-standard-for-advanced-detections"></a>A standard verzióra való frissítés speciális észlelések esetén

A speciális észlelések beállításához frissítsen az Azure Security Center Standard verzióra. 

1. A **Security Center** menüben válassza a **biztonsági házirend**elemet.
2. A standard szintű csomagra áthelyezni kívánt előfizetések esetében válassza a **beállítások szerkesztése**lehetőséget. 
3. A beállítások lapon válassza ki az **árképzési szintet**. 
   Egy hónapig ingyenes próbaverzió érhető el. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/). 

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, milyen típusú riasztások érhetők el a Security Centerban. További információkért lásd:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Security Center – gyakori kérdések](https://docs.microsoft.com/azure/security-center/security-center-faq)


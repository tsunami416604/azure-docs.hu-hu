---
title: Az Azure Security Center biztonsági riasztásainak |} A Microsoft Docs
description: Ez a témakör ismerteti, melyek a biztonsági riasztások és az Azure Security Centerben elérhető különböző típusait.
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
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 06f41bbfd97d5deb59e7bfd08615b2f28e256070
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571672"
---
# <a name="security-alerts-in-azure-security-center"></a>Az Azure Security Center biztonsági riasztások

Ez a cikk bemutatja az Azure Security Center (ASC) elérhető biztonsági riasztások különböző típusú. Nincsenek a számos különböző típusú riasztások különböző. ASC mind az Azure-ban üzembe helyezett erőforrásokhoz riasztásokat állít elő, és a telepített erőforrások a helyszíni és hibrid felhőkörnyezetekben. 

## <a name="what-are-security-alerts"></a>Mik azok a biztonsági riasztások?

Riasztások az értesítéseket, amelyek a Security Center állít elő, amikor a rendszer a fenyegetéseket az erőforrások. Rangsorolja és a szükséges, hogy a probléma gyors vizsgálatára vonatkozó információk a riasztások listája. A Security Center is a támadás elhárításával hogyan vonatkozó javaslatokkal szolgál.

## <a name="how-does-security-center-detect-threats"></a>A Security Center hogyan nem észleli a fenyegetéseket?

A valós fenyegetések észlelése és a vakriasztások számának csökkentése érdekében a Security Center gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózati, és a csatlakoztatott partneri megoldások, például tűzfal és a végpontvédelmi megoldások naplóadatait. A Security Center elemzi ezt az információt, gyakran naplókezelője azonosítani a fenyegetéseket, több forrásból származó adatokat.

ASC figyeli az erőforrásokat, hogy a üzembe helyezett Azure-ban vagy más helyszíni és hibrid környezetekben telepíthető. Észlelésével és a fenyegetésekkel szembeni további tudnivalókért lásd: [útmutató a Security Center észleli, és reagáljon a fenyegetések](security-center-detection-capabilities.md#asc-detects).

## <a name="security-alert-types"></a>Biztonsági riasztástípusok

A következő témakörök végigvezeti Önt a különböző ASC riasztások erőforrástípusok szerint:

* [IaaS virtuális gépek és kiszolgálók riasztások](security-center-alerts-iaas.md)
* [Natív számítási riasztások](security-center-alerts-compute.md)
* [Data services – riasztások](security-center-alerts-data-services.md)

Az alábbi témakörök ismertetik, hogyan használja a Security Center a a különféle telemetriai adatokból, hogy azt gyűjti össze az Azure-infrastruktúra integrálása annak érdekében, hogy további védelmi réteget üzembe helyezett erőforrásokra érvényesek az Azure-ban:

* [Szolgáltatási réteg riasztások](security-center-alerts-service-layer.md)
* [Integráció az Azure biztonsági termékek](security-center-alerts-integration.md)

## <a name="what-are-alert-incidents"></a>Mik a riasztás incidensek?

Biztonsági incidensek kapcsolódó riasztások helyett minden egyes riasztás külön-külön ajánlati gyűjteménye. A Security Center fusion használ, amelyek a biztonsági incidensek különböző riasztások és kis pontosságú jelek.

Használja az incidensek, a Security Center biztosít a támadássorozatot és az összes kapcsolódó riasztást egyetlen nézetben. Ez a nézet lehetővé teszi, hogy gyorsan megismerheti, milyen műveletek, a támadó időt vett igénybe, és az érintett erőforrások. További információkért lásd: [Felhőbeli intelligens riasztási korrelációs](security-center-alerts-cloud-smart.md).

## <a name="get-started-with-alerts"></a>Riasztások – első lépések

Többet szeretne tudni az erőforrásokat az ASC, és hogyan reagál a riasztásokat az ASC által bemutatott figyelni, a következő témakörökben talál.

* Mely platformokat és funkciókat az ASC által védett, olvassa el [platformok és az Azure Security Center által támogatott funkciók](security-center-os-coverage.md).  
* Mik azok a biztonsági incidensek és a hozzájuk ASC reakciója ismertetése: [az Azure Security Centerben a biztonsági incidensek kezelése](security-center-incident.md). 
* Megtudhatja, hogyan kezelheti a riasztásokat kap, tekintse meg a [kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md).
* Információ arról, hogy miként ellenőrizze, hogy megfelelően van-e konfigurálva a Security Center és a egy teszt a riasztás kelteni, lásd: [riasztások érvényesítése az Azure Security Center](security-center-alert-validation.md).  


## <a name="upgrade-to-standard-for-advanced-detections"></a>A speciális észlelések frissítsen a standard előfizetésre

A speciális észlelések beállításához frissítsen az Azure Security Center Standard verzióra. 

1. A Security Center menüből válassza ki a **biztonsági házirend**.
2. Az előfizetések Standard csomagra váltani szeretne, kattintson a **beállításainak szerkesztése**. 
3. A beállítások lapon válassza ki a **Tarifacsomag**. 
   Ingyenes próbaverzió egy hónapig érhető el. További tudnivalókért tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/security-center/). 

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, melyek a biztonsági riasztások és a Security Centerben elérhető riasztások különböző típusait. További információkért tekintse át a következők témaköröket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Az Azure Security Center – gyakori kérdések](https://docs.microsoft.com/azure/security-center/security-center-faq): Gyakori kérdések a szolgáltatás használatával kapcsolatban.


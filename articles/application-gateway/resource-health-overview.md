---
title: Az Azure Application Gateway Resource Health áttekintése
description: Ez a cikk az Azure Resource Health szolgáltatásának áttekintését Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "67659501"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Az Azure Application Gateway Resource Health áttekintése

[Az Azure Resource Health](../service-health/resource-health-overview.md) segítséget nyújt a diagnosztizálásban és a támogatás igénylésében, ha egy Azure-szolgáltatással kapcsolatos probléma hatással van az erőforrásaira. Tájékoztatja az erőforrások aktuális és korábbi állapotáról. Emellett technikai támogatást nyújt a problémák enyhítéséhez.

Application Gateway esetén a Resource Health az átjáró által kibocsátott jelekre támaszkodik annak felmérésére, hogy kifogástalan-e vagy sem. Ha az átjáró nem kifogástalan állapotú, Resource Health elemzi a probléma forrásának megállapításához szükséges további információkat. Emellett azonosítja a Microsoft által végzett műveleteket, vagy a probléma megoldásához szükséges lépéseket.

Az állapot értékelésével kapcsolatos további részletekért tekintse át a [Azure Resource Healthban](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways)található erőforrástípusok és állapot-ellenőrzések teljes listáját.


A Application Gateway állapota a következő állapotok egyike jelenik meg:

## <a name="available"></a>Elérhető

A **rendelkezésre álló** állapot azt jelenti, hogy a szolgáltatás nem észlelt olyan eseményt, amely hatással van az erőforrás állapotára. Ekkor megjelenik a **nemrég megoldott** értesítés azokban az esetekben, amikor az átjáró a nem tervezett állásidőből lett helyreállítva az elmúlt 24 órában.

![Rendelkezésre álló állapot](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Nem érhető el

A nem **elérhető** állapot azt jelenti, hogy a szolgáltatás egy folyamatos platformot észlelt, vagy nem platformos eseményt észlelt az átjáró állapotának hatására.

### <a name="platform-events"></a>Platformesemények

A platform eseményeire az Azure-infrastruktúra több összetevője is aktiválódik. Ezek közé tartoznak az ütemezett műveletek (például a tervezett karbantartás) és a váratlan incidensek (például egy nem tervezett gazdagép újraindítása).

Resource Health további részleteket tartalmaz az eseményről és a helyreállítási folyamatról. Azt is lehetővé teszi, hogy forduljon az ügyfélszolgálathoz, még akkor is, ha nincs aktív Microsoft-támogatási szerződése.

![Nem elérhető állapot](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Ismeretlen

Az **ismeretlen** állapot azt jelzi, Resource Health több mint 10 percen belül nem érkezett információ az átjáróról. Ez az állapot nem az átjáró állapotának végleges jelzése. A hibaelhárítási folyamat azonban fontos adatpont.

Ha az átjáró a várt módon fut, az állapot pár perc elteltével **elérhetővé** vált.

Ha problémákat tapasztal, az **ismeretlen** állapot arra utalhat, hogy a platformon egy esemény hatással van az átjáróra.

![Ismeretlen állapot](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Csökkentett teljesítményű

A **csökkentett** állapotú állapot azt jelzi, hogy az átjáró a teljesítmény csökkenését észlelte, bár még mindig elérhető a használathoz.

![Degratis állapot](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>További lépések

A webalkalmazási tűzfal (WAF) Application Gateway hibaelhárításával kapcsolatos további információkért lásd: [webalkalmazási tűzfal (WAF) hibaelhárítása Az Azure Application Gatewayhoz](web-application-firewall-troubleshoot.md).
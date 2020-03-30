---
title: Az Azure Application Gateway erőforrás állapotának áttekintése
description: Ez a cikk az Azure Application Gateway erőforrás-állapotszolgáltatásának áttekintése
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67659501"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Az Azure Application Gateway erőforrás állapotának áttekintése

[Az Azure Resource Health](../service-health/resource-health-overview.md) segítséget nyújt a diagnosztizálásban és a támogatás igénylésében, ha egy Azure-szolgáltatással kapcsolatos probléma hatással van az erőforrásaira. Ez tájékoztatja Önt a jelenlegi és a múltbeli egészségügyi erőforrásait. Technikai támogatást nyújt a problémák enyhítéséhez.

Az Application Gateway esetében a Resource Health az átjáró által kibocsátott jelekre támaszkodik, hogy felmérje, hogy kifogástalan állapotú-e vagy sem. Ha az átjáró nem megfelelő állapotú, a Resource Health további információkat elemez a probléma forrásának meghatározásához. Azt is azonosítja, hogy a Microsoft milyen műveleteket végez, illetve hogy mit tehet a probléma megoldásához.

Az állapot felmérésének módjáról az Azure Resource Health szolgáltatásban található erőforrástípusok és állapotellenőrzések teljes listáját további részletekért [tekintheti](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways)meg.


Az Application Gateway állapotállapota a következő állapotok egyikeként jelenik meg:

## <a name="available"></a>Elérhető

A **Rendelkezésre álló** állapot azt jelenti, hogy a szolgáltatás nem észlelt olyan eseményeket, amelyek befolyásolják az erőforrás állapotát. A **Nemrég feloldott** értesítés jelenik meg azokban az esetekben, amikor az átjáró az elmúlt 24 órában helyreállt a nem tervezett állásidőből.

![Elérhető állapot](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Nem érhető el

A **Nem érhető el** állapot azt jelenti, hogy a szolgáltatás olyan folyamatos platform- vagy nem platformeseményt észlelt, amely hatással van az átjáró állapotára.

### <a name="platform-events"></a>Platform események

A platformeseményeket az Azure-infrastruktúra több összetevője váltja ki. Ezek közé tartoznak az ütemezett műveletek (például a tervezett karbantartás) és a váratlan események (például egy nem tervezett állomás újraindítása).

A Resource Health további részleteket tartalmaz az eseményről és a helyreállítási folyamatról. Azt is lehetővé teszi, hogy lépjen kapcsolatba az ügyfélszolgálattal akkor is, ha nem rendelkezik aktív Microsoft támogatási szerződéssel.

![Nem érhető el állapot](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Ismeretlen

Az **Ismeretlen** állapot azt jelzi, hogy az erőforrás állapota több mint 10 percig nem kapott információt az átjáróról. Ez az állapot nem jelzi az átjáró állapotát. De ez egy fontos adatpont a hibaelhárítási folyamatban.

Ha az átjáró a várt módon fut, az állapot néhány perc múlva **Elérhető** re változik.

Ha problémákat tapasztal, az **Ismeretlen** állapot azt sugallhatja, hogy a platformon lévő esemény hatással van az átjáróra.

![Ismeretlen állapot](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Csökkentett teljesítményű

A **degradált** állapot azt jelzi, hogy az átjáró teljesítménycsökkenést észlelt, bár továbbra is használható.

![Degrated állapot](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>További lépések

Az Application Gateway webalkalmazás-tűzfal (WAF) hibaelhárításáról az [Azure Application Gateway webalkalmazás-tűzfalhiba -elhárítása](web-application-firewall-troubleshoot.md)című témakörben olvashat.
---
title: Az Azure Application Gateway a Resource Health áttekintése
description: Ez a cikk a resource health szolgáltatás az Azure Application Gateway áttekintése
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659501"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Az Azure Application Gateway a Resource Health áttekintése

[Az Azure Resource Health](../service-health/resource-health-overview.md) segít diagnosztizálni és a támogatás igénylésében, ha egy Azure-szolgáltatási probléma hatással van az erőforrások. Figyelmeztet az erőforrások jelenlegi és korábbi állapotát. És a problémák megoldásához nyújt segítséget a technikai támogatási biztosít.

Az Application Gateway a Resource Health annak ellenőrzéséhez, hogy kifogástalan-e az átjáró által kibocsátott jelek támaszkodik. Ha az átjáró állapota nem megfelelő, a Resource Health elemzi a további információkat a probléma okának meghatározása. Meghatározza azt is műveleteket, amelyeket a Microsoft most és mit tehet a probléma megoldása érdekében.

További részleteket az egészségügyi hogyan értékelik, tekintse át a teljes erőforrástípusok és állapot-ellenőrzések [az Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


Az állapot ellenőrzése az Application Gateway a következő állapotok egyik jelenik meg:

## <a name="available"></a>Elérhető

Egy **elérhető** állapot azt jelenti, hogy a szolgáltatás még nem észlelte az erőforrás állapotát befolyásoló eseményeket. Látni fogja a **a közelmúltban Ártalmatlanítva** értesítési azokban az esetekben, ahol az átjáró állt nem tervezett üzemkimaradások az elmúlt 24 órában.

![Rendelkezésre álló állapota](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Nem elérhető

Egy **nem érhető el** állapot azt jelenti, hogy a szolgáltatás azt észlelte, egy folyamatban lévő platform vagy platformok közötti esemény, amely hatással van az átjáró állapotát.

### <a name="platform-events"></a>Platform-események

Az Azure-infrastruktúra több összetevői által aktivált platform eseményeket. Ezek tartalmazzák az ütemezett műveletekhez (például a tervezett karbantartás) és a nem várt események (például egy gazdagép nem tervezett újraindítás).

A Resource Health további részleteket biztosít az esemény-és a helyreállítási folyamatot. Lehetővé teszi, hogy forduljon az ügyfélszolgálathoz, még akkor is, ha nem rendelkezik egy aktív Microsoft támogatási szerződés.

![Nem elérhető állapot](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Ismeretlen

A **ismeretlen** állapot azt jelzi, hogy a Resource Health nem kapott információ az átjáró több mint 10 perc. Ez az állapot nem egy végleges arra utalhat, hogy az átjáró állapotát. De ez fontos adatpontnak a hibaelhárítási folyamat során.

Ha az átjáró a várt módon fut, a állapota **elérhető** néhány perc múlva.

Ha problémákat tapasztal problémákat, a **ismeretlen** állapot mérete alapján feltételezhető, hogy a platform esemény van hatással az átjárót.

![Ismeretlen állapot](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Csökkentett teljesítmény

A **csökkentett teljesítményű** állapot azt jelzi, hogy az átjáró észlelt a teljesítményt, bár továbbra is használható a felhasználás.

![Degrated állapota](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>További lépések

Alkalmazás Gateway webalkalmazási tűzfala (WAF) hibaelhárítással kapcsolatos tudnivalókért lásd: [hibaelhárítása webalkalmazási tűzfala (WAF) az Azure Application Gateway](web-application-firewall-troubleshoot.md).
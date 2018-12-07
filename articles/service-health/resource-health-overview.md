---
title: Az Azure Resource Health áttekintése |} A Microsoft Docs
description: Az Azure Resource Health áttekintése
services: Resource health
documentationcenter: ''
author: stephbaron
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 11/16/2018
ms.author: stbaron
ms.openlocfilehash: 918ff674e57a14f685c7f9ef2aaaca5ca064976e
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012844"
---
# <a name="azure-resource-health-overview"></a>Az Azure Resource Health áttekintése
 
Az Azure Resource Health segítségével diagnosztizálhatja és a támogatás igénylésében, ha egy Azure-szolgáltatási probléma hatással van az erőforrások. Figyelmeztet az erőforrások jelenlegi és korábbi állapotát. És a problémák megoldásához nyújt segítséget a technikai támogatási biztosít.

Mivel a [Azure állapotlapján](https://status.azure.com) szolgáltatási problémák, amelyek hatással vannak az Azure-ügyfelek széles körét nyújt tájékoztatást, a Resource Health kínál az erőforrások állapotának személyre szabott irányítópult. A Resource Health az Azure-szolgáltatási problémák miatt nem érhető el, az elmúlt volt az erőforrások mindig látható. Ezután használata egyszerű érthető, ha az SLA-t megsértettek. 

## <a name="resource-definition-and-health-assessment"></a>Erőforrás-definíció- és egészségügyi értékelés
Egy erőforrás egy adott példányán, az Azure-szolgáltatások: például egy virtuális gép, egy webalkalmazás vagy egy SQL-adatbázist.

A Resource Health annak ellenőrzéséhez, hogy az erőforrás állapota kifogástalan-e a különböző Azure-szolgáltatások által kibocsátott jelek támaszkodik. Erőforrás állapota nem megfelelő, ha a Resource Health elemzi a további információkat a probléma okának meghatározása. Meghatározza azt is, műveletek, amelyek a probléma megoldása érdekében a Microsoft most vagy a műveleteket, amelyeket az okot a probléma megoldása érdekében. 

További részleteket az egészségügyi hogyan értékelik, tekintse át a teljes erőforrástípusok és állapot-ellenőrzések [az Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Állapotadatok
Az erőforrás állapotát, a következő állapotok egyike jelenik meg.

### <a name="available"></a>Elérhető
Az állapot **elérhető** azt jelenti, hogy a szolgáltatás még nem észleli más eseményeket, amelyek hatással vannak az erőforrás állapotát. Azokban az esetekben, ahol az erőforrás állt nem tervezett üzemkimaradások az elmúlt 24 órában, tekintse meg a **a közelmúltban Ártalmatlanítva** értesítést.

![A "Nemrégiben megoldódott" értesítés "Elérhető" virtuális gép állapotát](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Nem elérhető
Az állapot **nem érhető el** azt jelenti, hogy a szolgáltatás azt észlelte, egy folyamatban lévő platform vagy platformok közötti esemény, amely hatással van az erőforrás állapotát.

#### <a name="platform-events"></a>Platform-események
Az Azure-infrastruktúra több összetevői által aktivált platform eseményeket. Ezek tartalmazzák az ütemezett műveletekhez (például a tervezett karbantartás) és a nem várt események (például egy gazdagép nem tervezett újraindítás).

A Resource Health további részleteket biztosít az esemény-és a helyreállítási folyamatot. Lehetővé teszi, hogy forduljon az ügyfélszolgálathoz, még akkor is, ha nem rendelkezik egy aktív Microsoft támogatási szerződés.

![Platform esemény miatt a virtuális gép állapota "Nem érhető el"](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Nem-platform-események
Nem-platform eseményeket váltja ki a felhasználók műveleteit. Példa egy virtuális gép leállítása vagy elérése a redis az Azure Cache kapcsolatok maximális számát.

![Platformok közötti esemény miatt a virtuális gép állapota "Nem érhető el"](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Ismeretlen
Állapotát **ismeretlen** azt jelzi, hogy a Resource Health még nem kapott ehhez az erőforráshoz információ a több mint 10 percig. Bár ez az állapot nem végleges megjelölése erőforrás állapotát, a hibaelhárítási folyamat fontos adatpontnak.

Az erőforrás a vártnak megfelelően működik, ha az erőforrás állapota változik **elérhető** néhány perc múlva.

Ha az erőforrás-problémákat tapasztal a **ismeretlen** állapot mérete alapján feltételezhető, hogy a platform esemény van hatással az erőforrást.

![Virtuális gép "Ismeretlen" állapota](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Csökkentett teljesítmény
Állapotát **csökkentett teljesítményű** jelzi, hogy az erőforrás észlelt a teljesítményt, bár továbbra is használható a felhasználás.
Különböző erőforrások rendelkezik saját mikor kell adnia, hogy egy erőforrás csökkentett teljesítményű kritériumait.

![Virtuális gép "Csökkentett teljesítményű" állapota](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Egy nem megfelelő állapotát
Ha úgy véli, hogy az aktuális állapot-ellenőrzése nem megfelelő, akkor is ossza meg velünk kiválasztásával **helytelen állapot jelentése**. Azokban az esetekben, ahol egy Azure probléma van hatással, azt javasoljuk, hogy forduljon az ügyfélszolgálathoz az erőforrás állapota. 

![Egy helytelen állapotára vonatkozó információkat küldjön be](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Előzményadatok
Legfeljebb 14 napig az állapotelőzmények érheti el a **állapotelőzmények** Resource Health szakaszában. 

![A Resource Health-események az elmúlt két hétben](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Első lépések
A Resource Health megnyitása egy erőforráshoz:
1.  Jelentkezzen be az Azure portálra.
2.  Keresse meg az erőforrást.
3.  A bal oldali ablaktábláján erőforrás menüben válassza ki a **a Resource health**.

![A Resource Health megnyitása az erőforrás-nézet](./media/resource-health-overview/from-resource-blade.png)

A Resource Health kiválasztásával is elérhető **minden szolgáltatás** , és írja be **a resource health** a szűrő szövegmezőbe. Az a **súgó + támogatás** ablaktáblán válassza előbb [a Resource health](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![A Resource Health megnyitása a "Minden szolgáltatás"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>További lépések

Tekintse meg az alábbi Resource Health szolgáltatással kapcsolatos további források:
-  [Erőforrástípusok és állapot-ellenőrzések a az Azure Resource Health](resource-health-checks-resource-types.md)
-  [Az Azure Resource Health kapcsolatos gyakori kérdések](resource-health-faq.md)





---
title: Az Azure Resource Health áttekintése |} Microsoft Docs
description: Azure-erőforrás állapotának áttekintése
services: Resource health
documentationcenter: ''
author: shawntabrizi
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 03/27/2018
ms.author: shawn.tabrizi
ms.openlocfilehash: 99e996f182aac774f2e2565d87fd0debaba1b2d1
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
ms.locfileid: "30263122"
---
# <a name="azure-resource-health-overview"></a>Az Azure Resource Health áttekintése
 
Az Azure Resource Health segít diagnosztizálni és segítségre van szüksége, amikor egy Azure-szolgáltatás a hibát észleli az erőforrások. Figyelmeztet az erőforrások aktuális és korábbi állapotát. És biztosít a problémák mérséklése nyújt segítséget a technikai támogatási szolgálathoz.

Mivel [Azure állapot](https://status.azure.com) szolgáltatás problémák, amelyek hatással vannak az Azure-ügyfél széleskörű nyújt tájékoztatást, a Resource Health lehetővé teszi az erőforrások állapotának személyre szabott irányítópultot. Erőforrás állapota jeleníti meg az Azure-szolgáltatások problémák miatt nem érhető el, az elmúlt volt az erőforrások mindig. Majd használata egyszerű is meg tudjon érteni, ha a szolgáltatásiszint-szerződésben garantált megsértettek. 

## <a name="resource-definition-and-health-assessment"></a>Erőforrás-definíció- és állapot assessment
Egy erőforrás egy adott példányt az Azure-szolgáltatások: például egy virtuális gépet, a webes alkalmazás vagy egy SQL-adatbázis.

Erőforrás állapota annak ellenőrzéséhez, hogy az erőforrás állapota megfelelő-e a különböző Azure-szolgáltatások kibocsátott jelek támaszkodik. Erőforrás állapota nem kifogástalan, ha az erőforrás állapota elemzi további információt a probléma meghatározásához. A probléma megoldása érdekében tart a Microsoft vagy a műveletekről, amelyek a probléma okának elhárítása is azonosítja. 

Az állapotfigyelő értékelési módját, a további részletekért tekintse át a erőforrástípusok teljes listáját, és ellenőrzi [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Állapot ellenőrzése
Egy erőforrás állapotának jelenik meg a következő állapotok közül.

### <a name="available"></a>Elérhető
Állapot **elérhető** azt jelenti, hogy a szolgáltatás még nem észleli az erőforrás állapotát befolyásoló eseményeket. Azokban az esetekben, ahol az erőforrás helyreállt nem tervezett leállás az elmúlt 24 órában, tekintse meg a **a közelmúltban ártalmatlanított** értesítést.

!["Elérhető" virtuális gép állapotát a "Nemrég ártalmatlanított" értesítést](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Nem érhető el
Állapot **nem érhető el** azt jelenti, hogy a szolgáltatás észlelt egy platform folyamatban lévő vagy a nem-platformesemény, amely befolyásolja az erőforrás állapotát.

#### <a name="platform-events"></a>Platform-események
Platform eseményeket váltja ki az Azure infrastruktúra több összetevőt. Ütemezett műveletekhez (például tervezett karbantartás) és a nem várt események (például egy nem tervezett állomás újraindítás) tartalmaznak.

Erőforrás állapota további részleteket biztosít a esemény és a helyreállítási folyamatot. Lehetővé teszi, hogy forduljon az ügyfélszolgálathoz, még akkor is, ha nincs egy aktív Microsoft támogatja a szerződést.

!["Nem érhető el" platformesemény miatt a virtuális gép állapota](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Nem-platform események
Nem-platform eseményeket váltja ki a felhasználói műveletek. Példák egy virtuális gép leállítása, vagy a Redis gyorsítótár kapcsolatok maximális számának elérése.

![A virtuális gép nem platformesemény miatt "Nem érhető el" állapota](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Ismeretlen
Állapotának **ismeretlen** azt jelzi, hogy az erőforrás állapota több mint 10 percig bővebben még nem kapott. Bár ez az állapot nem egy végleges arra utal, hogy az erőforrás állapotának, egy fontos adatpont a hibaelhárítást is.

Ha az erőforrás a várt módon fut, az erőforrás állapotának módosul **elérhető** néhány perc múlva.

Ha az erőforrás problémákat tapasztal a **ismeretlen** állapot mérete alapján feltételezhető, hogy a platform esemény érinti az erőforrás.

![A virtuális gép "Ismeretlen" állapota](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Csökkentett teljesítmény
Állapotának **csökkentett teljesítményű** jelzi, hogy az erőforrás észlelt a teljesítményét, bár ez továbbra is elérhető a használati.
Különböző erőforrások rendelkezik, ha azok meg, hogy egy erőforrás csökken, mert a saját szempontjai.

![A virtuális gép "Csökkentett teljesítményű" állapota](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>A nem megfelelő állapotú Reporting
Ha úgy véli, hogy a jelenlegi állapot nem megfelelő, akkor is ossza meg velünk kiválasztásával **helytelen állapot jelentést**. Azokban az esetekben, ahol egy Azure probléma érinti Önt javasoljuk, hogy forduljon az ügyfélszolgálathoz erőforrás állapotát. 

![Egy helytelen állapotára vonatkozó adatok küldésére mezőbe](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Előzményadatok
14 nappal az előzmények végezheti el a **állapotelőzményeinek** Resource Health szakasza. 

![Erőforrás állapota események az elmúlt két hétben](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Első lépések
Erőforrás állapota megnyitása egy erőforrás:
1.  Jelentkezzen be az Azure portálra.
2.  Keresse meg az erőforrást.
3.  Válassza a bal oldali ablaktáblán erőforrás menü **erőforrás állapota**.

![Megnyitja az erőforrás-kihasználása nézetét erőforrás állapota](./media/resource-health-overview/from-resource-blade.png)

Emellett Resource Health kiválasztásával **minden szolgáltatás** , és írja be **erőforrás állapota** a Szűrő mezőbe. Az a **súgó + támogatás** ablaktáblán válassza előbb [erőforrás állapota](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Az "Összes szolgáltatások" Resource Health megnyitása](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>További lépések

Tekintse meg ezeket az erőforrásokat Resource Health tájékozódhat:
-  [Erőforrástípusok és állapotát ellenőrzi az Azure-erőforrás állapota](resource-health-checks-resource-types.md)
-  [Az Azure Resource Health kapcsolatos gyakori kérdések](resource-health-faq.md)





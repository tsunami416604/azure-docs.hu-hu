---
title: "Az Azure Resource health áttekintése |} Microsoft Docs"
description: "Azure-erőforrás állapotának áttekintése"
services: Resource health
documentationcenter: 
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/01/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 9912911d6ed43a70a7a0f9316079d8f66d063f64
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="azure-resource-health-overview"></a>Azure-erőforrás állapotának áttekintése
 
A Resource Health segítséget nyújt a diagnosztizálásban és a támogatás igénylésében, ha egy Azure-ral kapcsolatos probléma hatással van az erőforrásaira. Tájékoztatja az erőforrásai aktuális és korábbi állapotáról, és segít a problémák kezelésében. A Resource Health műszaki támogatást nyújt, ha segítségre van szüksége az Azure szolgáltatásait érintő problémákkal kapcsolatban.

Mivel [Azure állapot](https://status.azure.com) , amelyek hatással vannak az Azure-ügyfél széleskörű szolgáltatásokkal kapcsolatos problémákról nyújt tájékoztatást, erőforrás állapota tesz lehetővé az erőforrások állapotának személyre szabott irányítópultot. Erőforrás állapota nem érhető el az Azure szolgáltatás problémák, így érthető, ha a szolgáltatásiszint-szerződésben garantált megsértettek egyszerű miatt a múltban volt az erőforrások mindig látható. 

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-a-resource-is-healthy-or-not"></a>Egy erőforrás szempontjából, és hogyan működik az erőforrás állapota úgy dönt, hogy erőforrás kifogástalan-e vagy sem?
Egy erőforrás egy adott példányt az Azure-szolgáltatások, például: egy virtuális gépet, a webes alkalmazás vagy egy SQL-adatbázis.

Erőforrás állapota annak ellenőrzéséhez, hogy az erőforrás állapota megfelelő-e a különböző Azure-szolgáltatások kibocsátott jelek támaszkodik. Erőforrás állapota nem kifogástalan, ha az erőforrás állapota elemzi további információt a probléma meghatározásához. Microsoft tart a probléma elhárításához műveletek is azonosított vagy milyen műveleteket lehet végrehajtani a probléma okának elhárítása. 

Tekintse át a erőforrástípusok teljes listáját, és ellenőrzi [Azure-erőforrás állapotának](resource-health-checks-resource-types.md) további részleteket a health értékelési módját.

## <a name="health-status-provided-by-resource-health"></a>Erőforrás állapota által megadott állapotadatai
Az erőforrás állapotát a következő állapotok egyike:

### <a name="available"></a>Elérhető
A szolgáltatás nem talált az erőforrás állapotának érintő eseményeket. Azokban az esetekben, ahol az erőforrás helyreállt nem tervezett leállás az elmúlt 24 órában tekintse meg a **mostanában helyre** értesítést.

![Erőforrás állapotának rendelkezésre állású virtuális gép](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Nem érhető el
A szolgáltatás azt észlelte, egy platform folyamatban lévő vagy a nem platform eseményt érintő az erőforrás állapotát.

#### <a name="platform-events"></a>Platform-események
Ezeket az eseményeket váltja ki az Azure infrastruktúra több összetevőt. Ütemezett műveletekhez (pl. tervezett karbantartás) és a nem várt események (pl. egy nem tervezett állomás újraindítás) tartalmaznak.

Erőforrás állapota további részleteket biztosít a esemény és a helyreállítási folyamatot. Lehetővé teszi, hogy forduljon az ügyfélszolgálathoz, még akkor is, ha nincs egy aktív Microsoft támogatja a szerződést.

![Erőforrás állapota nem érhető el virtuális gép platformesemény miatt](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Nem-Platform események
Ezeket az eseményeket váltja ki a felhasználók által végrehajtott műveleteket. Példa: egy virtuális gép leállítása vagy egy Redis gyorsítótárra kapcsolatok maximális számának elérése.

![Erőforrás állapota nem érhető el virtuális gép nem platformesemény miatt](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Ismeretlen
Állapot azt jelzi, hogy az erőforrás állapota több mint 10 percig bővebben még nem kapott. Ez az állapot nem végleges feltüntetése az erőforrás állapotát, de napjainkban a hibaelhárítási folyamat fontos adatok pontként:
* Ha az erőforrás a várt módon fut, az erőforrás állapota frissül, hogy elérhető néhány perc múlva.
* Ha az erőforrás problémákat tapasztal, az ismeretlen állapot javasolhat az erőforrás kihatással van a platform esemény történt.

![Erőforrás állapota ismeretlen virtuális gép](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Csökkentett teljesítmény
Állapot azt jelzi, hogy, hogy az erőforrás észlelt a teljesítményét, bár ez továbbra is elérhető a használati.
Különböző erőforrások rendelkeznek a saját feltételei, ha azok adjon meg egy erőforrást jelenleg csökkentett.

![Erőforrás állapota állapotú virtuális gép](./media/resource-health-overview/degraded.png)

## <a name="report-an-incorrect-status"></a>A nem megfelelő állapotú jelentés
Ha bármikor véleménye szerint a jelenlegi állapot nem megfelelő, akkor is ossza meg velünk kattintva **helytelen állapot jelentést**. Azokban az esetekben, ahol van egy Azure probléma által érintett javasoljuk, hogy forduljon az ügyfélszolgálathoz erőforrás állapotát. 

![Erőforrás állapota nem megfelelő állapot jelentése](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Előzményadatok
Az előzmények 14 nappal eléréséhez kattintson **előzményeinek megtekintése** az erőforrás állapota. 

![A jelentés előzményei erőforrás állapota](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Első lépések
Egy erőforrás erőforrásállapot megnyitása
1.  Jelentkezzen be az Azure-portálon.
2.  Nyissa meg az erőforrás.
3.  Kattintson a bal oldalon található erőforrás menüben **erőforrás állapota**.

![Nyissa meg az erőforrás állapota az erőforrás-kihasználása nézetét](./media/resource-health-overview/from-resource-blade.png)

Erőforrás állapota kattintva is elérheti **minden szolgáltatás**, és írja be **erőforrás állapota** nyissa meg a szűrő szövegmezőbe a **súgó + támogatás** panelen. Végül [ **erőforrás állapota**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Nyissa meg az erőforrás állapota minden szolgáltatás](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>További lépések

Tekintse meg ezeket az erőforrásokat erőforrás állapota tájékozódhat:
-  [Erőforrástípusok és állapotát ellenőrzi az Azure-erőforrás állapota](resource-health-checks-resource-types.md)
-  [Azure-erőforrás állapotának kapcsolatos gyakori kérdések](resource-health-faq.md)





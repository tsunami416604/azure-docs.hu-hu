---
title: Az Azure Resource Health áttekintése
description: Az Azure-erőforrások állapotának áttekintése
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 7a1dfe5e93d0e19aeb343d113a24ed882a5b3f69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159251"
---
# <a name="resource-health-overview"></a>Erőforrás-állapot – áttekintés
 
Az Azure Resource Health segítségével diagnosztizálhatja és támogatást kaphat az Azure-erőforrásokat érintő szolgáltatási problémákhoz. Az erőforrások aktuális és korábbi állapotáról számol be.

[Az Azure-állapotjelentések](https://status.azure.com) az Azure-ügyfelek széles körét érintő szolgáltatási problémákról. A Resource Health személyre szabott irányítópultot biztosít az erőforrások állapotáról. A Resource Health azt mutatja, hogy az erőforrások az Azure-szolgáltatási problémák miatt nem érhetők el. Ezek az adatok megkönnyítik annak megtekintését, hogy egy SLA-t megsértették-e.

## <a name="resource-definition-and-health-assessment"></a>Az erőforrások meghatározása és az egészségügyi értékelés

Az *erőforrás* egy Azure-szolgáltatás, például egy virtuális gép, webalkalmazás vagy SQL-adatbázis adott példánya. A Resource Health a különböző Azure-szolgáltatásokból származó jelekre támaszkodik annak felméréséhez, hogy egy erőforrás kifogástalan állapotú-e. Ha egy erőforrás nem kifogástalan állapotú, a Resource Health további információkat elemez a probléma forrásának meghatározásához. Emellett beszámol a Microsoft által a probléma megoldásához végrehajtott műveletekről, és azonosítja a probléma megoldásához szükséges műveleteket.

Az állapot felméréséről az Azure Resource Health című témakörben talál további információt az erőforrástípusok és állapot-ellenőrzések [listájában.](resource-health-checks-resource-types.md)

## <a name="health-status"></a>Állapotadatok

Egy erőforrás állapota a következő állapotok egyikeként jelenik meg.

### <a name="available"></a>Elérhető

*A rendelkezésre álló* azt jelenti, hogy nincsenek olyan események, amelyek befolyásolják az erőforrás állapotát. Azokban az esetekben, amikor az erőforrás az elmúlt 24 órában nem tervezett állásidőből helyreállt, megjelenik egy "Nemrég feloldott" értesítés.

![*Elérhető* állapot "Nemrég feloldott" értesítéssel rendelkező virtuális gép esetén](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Nem érhető el

*Nem érhető el* azt jelenti, hogy a szolgáltatás észlelt egy folyamatban lévő platform vagy nem platform esemény, amely befolyásolja az erőforrás állapotát.

#### <a name="platform-events"></a>Platform események

A platformeseményeket az Azure-infrastruktúra több összetevője váltja ki. Ezek közé tartoznak az ütemezett műveletek (például a tervezett karbantartás) és a váratlan események (például egy nem tervezett állomás újraindítása vagy egy degradált gazdagép hardver, amely az előrejelzések szerint egy adott időablak után sikertelen lesz).

A Resource Health további részleteket tartalmaz az eseményről és a helyreállítási folyamatról. Azt is lehetővé teszi, hogy lépjen kapcsolatba a Microsoft támogatási szolgálatával, még akkor is, ha nem rendelkezik aktív támogatási szerződéssel.

![A *Nem érhető el* állapot egy virtuális gép esetében platformesemény miatt](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Nem platformjellegű események

A nem platformjellegű eseményeket felhasználói műveletek váltják ki. Ilyenpéldául egy virtuális gép leállítása vagy az Azure Cache for Redis maximális számú kapcsolatának elérése.

![Nem platformjellegű esemény miatt a "Nem érhető el" állapot egy virtuális gép esetében](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Ismeretlen

*Az ismeretlen* azt jelenti, hogy az erőforrás állapota több mint 10 percig nem kapott információt az erőforrásról. Bár ez az állapot nem az erőforrás állapotának végleges jelzése, fontos adatpont a hibaelhárításhoz.

Ha az erőforrás a várt módon fut, az erőforrás állapota néhány perc múlva *Elérhető* re változik.

Ha problémákat tapasztal az erőforrással, az *Ismeretlen* állapot azt jelentheti, hogy a platformon lévő esemény hatással van az erőforrásra.

![*Ismeretlen* állapota virtuális gép esetén](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Csökkentett teljesítményű

*A leromlott* azt jelenti, hogy az erőforrás teljesítményvesztést észlelt, bár továbbra is használható.

A különböző erőforrásoknak megvannak a saját feltételeik arra az időpontjára, amikor arról számolnak be, hogy leromlottak.

![*Degradálva* állapot egy virtuális gépnél](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Helytelen állapot jelentése

Ha úgy gondolja, hogy az aktuális állapot helytelen, akkor a **Helytelen állapot jelentése**lehetőséget választva közölheti velünk. Abban az esetben, ha egy Azure-probléma érinti Önt, javasoljuk, hogy forduljon a Resource Health támogatási szolgálatához.

![A helytelen állapotra vonatkozó információk elküldéséhez](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>Előzményekre vonatkozó információk

Legfeljebb 30 napos előzményeket érhet el az Erőforrás állapota szakasz **Állapotelőzményszakaszában.**

![Az elmúlt két hét erőforrás-egészségügyi eseményeinek listája](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>Bevezetés

Erőforrás állapota egy erőforráshoz való megnyitása:

1. Jelentkezzen be az Azure portálra.
2. Keresse meg az erőforrást.
3. A bal oldali ablaktábla erőforrás menüjében válassza az **Erőforrás állapota**lehetőséget.

![Erőforrás állapota az erőforrásnézetből](./media/resource-health-overview/from-resource-blade.png)

Az Erőforrás állapota a **Szűrő** szövegmezőjébe **resource health** az Összes szolgáltatás elemre kattintva is elérhető. A **Súgó + támogatás** ablaktáblán válassza az [Erőforrás állapota](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth)lehetőséget.

![Erőforrás-állapot megnyitása az "Összes szolgáltatás" szolgáltatásból](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>További lépések

Az erőforrás-állapotról az alábbi hivatkozásokból megtudhatja:
-  [Erőforrástípusok és állapotellenőrzések az Azure Resource Health szolgáltatásban](resource-health-checks-resource-types.md)
-  [Gyakori kérdések az Azure Resource Health szolgáltatásról](resource-health-faq.md)

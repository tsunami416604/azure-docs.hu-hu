---
title: Az Azure Resource Health használata az adatbázis állapotának figyeléséhez
description: Az Azure Resource Health használatával figyelheti az SQL Database állapotát, így diagnosztizálhatja és támogatást kaphat, ha egy Azure-probléma hatással van az SQL-erőforrásokra.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 9e19e904b47d69444b491dd88ffe49ff812aafc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208868"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Az Azure SQL Database kapcsolatainak hibaelhárítása a Resource Health használatával

## <a name="overview"></a>Áttekintés

[A Resource Health](../service-health/resource-health-overview.md#get-started) for SQL Database segít diagnosztizálni és támogatást kapni, ha egy Azure-probléma hatással van az SQL-erőforrásokra. Tájékoztatja az erőforrásai aktuális és korábbi állapotáról, és segít a problémák kezelésében. A Resource Health műszaki támogatást nyújt, ha segítségre van szüksége az Azure szolgáltatásait érintő problémákkal kapcsolatban.

![Áttekintés](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Egészségügyi ellenőrzések

A Resource Health az SQL-erőforrás állapotát határozza meg az erőforrásba való bejelentkezések sikeresse és sikertelensítésével. Jelenleg az SQL DB erőforrás erőforrás állapota csak a rendszerhiba miatti bejelentkezési hibákat vizsgálja, felhasználói hiba miatt nem. Az erőforrás állapota 1-2 percenként frissül.

## <a name="health-states"></a>Egészségügyi államok

### <a name="available"></a>Elérhető

**A Rendelkezésre álló** állapot azt jelenti, hogy az erőforrás állapota nem észlelt bejelentkezési hibákat az SQL-erőforrás rendszerhibái miatt.

![Elérhető](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Csökkentett teljesítményű

**A degradált** állapot azt jelenti, hogy a Resource Health a sikeres bejelentkezések többségét észlelte, de néhány hibát is. Ezek valószínűleg átmeneti bejelentkezési hibák. Az átmeneti bejelentkezési hibák által okozott csatlakozási problémák hatásának csökkentése érdekében valósítsa meg [az újrapróbálkozási logikát](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) a kódban.

![Csökkentett teljesítményű](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Nem érhető el

A **Nem érhető el** állapot azt jelenti, hogy az erőforrás-állapot konzisztens bejelentkezési hibákat észlelt az SQL-erőforrásban. Ha az erőforrás hosszabb ideig ebben az állapotban marad, forduljon az ügyfélszolgálathoz.

![Nem érhető el](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Ismeretlen

**Az Ismeretlen** állapot azt jelzi, hogy az erőforrás állapota több mint 10 percig nem kapott információt erről az erőforrásról. Bár ez az állapot nem az erőforrás állapotának végleges jelzése, fontos adatpont a hibaelhárítási folyamatban. Ha az erőforrás a várt módon fut, az erőforrás állapota néhány perc múlva Elérhető re változik. Ha problémákat tapasztal az erőforrással kapcsolatban, az Ismeretlen állapot azt sugallhatja, hogy a platformon lévő esemény hatással van az erőforrásra.

![Ismeretlen](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Történelmi információk

Akár 14 napos állapottörténetet is elérhet az Erőforrás-állapot állapot szakasz Ának előzmények szakaszában. A szakasz a Resource Health által jelentett állásidők (ha rendelkezésre áll) állásidőt is tartalmazza. Az Azure az SQL-adatbázis állásidejét jelenleg kétperces pontossággal jeleníti meg. A tényleges állásidő valószínűleg egy percnél kevesebb, átlagosan 8 másodperc.

### <a name="downtime-reasons"></a>Állásidő-okok

Amikor az SQL Database állásidőt tapasztal, elemzés történik az ok meghatározásához. Ha elérhető, az állásidő oka az Erőforrás állapota című szakaszban található. Az állásidő okai általában az eseményt követő 30 percen belül megjelennek.

#### <a name="planned-maintenance"></a>Tervezett karbantartás

Az Azure-infrastruktúra rendszeres időközönként elvégzi a tervezett karbantartást – hardver- vagy szoftverösszetevők frissítése az adatközpontban. Amíg az adatbázis karbantartáson megy keresztül, az SQL megszüntethet néhány meglévő kapcsolatot, és megtagadhatja az újakat. A tervezett karbantartás során tapasztalt bejelentkezési hibák általában átmenetiek, és az [újrapróbálkozási logika](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) segít csökkenteni a hatást. Ha továbbra is bejelentkezési hibákat tapasztal, forduljon az ügyfélszolgálathoz.

#### <a name="reconfiguration"></a>Reconfiguration

Az újrakonfigurációk átmeneti körülményeknek minősülnek, és időről időre várhatók. Ezeket az eseményeket terheléselosztás vagy szoftver/hardver hibák válthatják ki. Minden olyan ügyfél-éles alkalmazás, amely egy felhőalapú adatbázishoz csatlakozik, egy megbízható [kapcsolat-újrapróbálkozási logikát kell megvalósítania,](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)mivel ez segít enyhíteni ezeket a helyzeteket, és általában a hibákat a végfelhasználó számára átláthatóvá kell tennie.

## <a name="next-steps"></a>További lépések

- További információ [az átmeneti hibák újrapróbálkozási logikájáról](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [SQL-csatlakozási hibák elhárítása, diagnosztizálása és elkerülése](./sql-database-connectivity-issues.md)
- További információ [az erőforrás-állapot-riasztások konfigurálásáról](../service-health/resource-health-alert-arm-template-guide.md)
- Az [erőforrás-állapot](../service-health/resource-health-overview.md) áttekintése
- [Erőforrás állapota GYIK](../service-health/resource-health-faq.md)

---
title: Az adatbázis állapotának figyelése Azure Resource Health
description: Az Azure Resource Health segítségével megfigyelheti azok állapotát. az SQL Database és segít diagnosztizálni, és a támogatás igénylésében, ha egy Azure-beli probléma kihat az SQL-erőforrások.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79208868"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>A Resource Health használata az Azure SQL Database-kapcsolatának hibaelhárítása

## <a name="overview"></a>Áttekintés

A [Resource Health](../service-health/resource-health-overview.md#get-started) for SQL Database segítséget nyújt a diagnosztizálásban és a támogatásban, ha egy Azure-probléma hatással van az SQL-erőforrásokra. Tájékoztatja az erőforrásai aktuális és korábbi állapotáról, és segít a problémák kezelésében. A Resource Health műszaki támogatást nyújt, ha segítségre van szüksége az Azure szolgáltatásait érintő problémákkal kapcsolatban.

![Áttekintés](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Állapot-ellenőrzések

A Resource Health az SQL-erőforrás állapotát a sikeres és sikertelen bejelentkezések az erőforráshoz megvizsgálásával határozza meg. Jelenleg a Resource Health az SQL DB-erőforrás csak megvizsgálja a rendszer által visszaadott hiba, és nem a felhasználói hiba miatt sikertelen. Az erőforrás-állapot 1-2 percenként frissül.

## <a name="health-states"></a>Állapotokat

### <a name="available"></a>Elérhető

Az **elérhető** állapot azt jelenti, hogy a Resource Health nem észlelt bejelentkezési hibákat az SQL-erőforráson rendszerhibák miatt.

![Elérhető](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Csökkentett teljesítményű

A **Csökkentett teljesítmény** állapot azt jelenti, hogy a Resource Health főleg sikeres bejelentkezéseket észlelt, de néhány sikertelent is. Ezek a legnagyobb valószínűséggel átmeneti bejelentkezési hibák. Az átmeneti bejelentkezési hibák okozta kapcsolódási problémák csökkentése érdekében hajtsa végre az [újrapróbálkozási logikát](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) a kódban.

![Csökkentett teljesítményű](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Nem érhető el

A nem **elérhető** állapot azt jelenti, hogy a Resource Health konzisztens bejelentkezési hibákat észlelt az SQL-erőforráshoz. Ha az erőforrás egy hosszabb ideig ebben az állapotban marad, kérjük forduljon az ügyfélszolgálathoz.

![Nem érhető el](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Ismeretlen

Az **ismeretlen** állapot azt jelzi, hogy Resource Health több mint 10 percig nem kapott információt az erőforrásról. Bár ez az állapot nem végleges megjelölése erőforrás állapotát, a hibaelhárítási folyamat fontos adatpontnak. Ha az erőforrás a vártnak megfelelően működik, az erőforrás állapota változik elérhető néhány perc múlva. Az erőforrás-problémákat tapasztal, ha az ismeretlen állapot mérete alapján feltételezhető, hogy a platform esemény van hatással az erőforrást.

![Ismeretlen](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Előzményadatok

Akár 14 napig állapotelőzmények a Resource Health egészségügyi korábbi szakaszában is elérheti. A szakasz a Resource Health által jelentett állásidők üzemszünet okát (ha elérhető) is tartalmazzák. Az Azure az SQL-adatbázis állásidejét jelenleg kétperces pontossággal jeleníti meg. A tényleges állásidő valószínűleg egy percnél kevesebb, átlagosan 8 másodperc.

### <a name="downtime-reasons"></a>Állásidő okok

Amikor az SQL Database állásidő, elemzési történik egy okának megállapításához. Ha elérhető, a Resource Health állapotelőzmények szakaszában jelenti, az üzemszünet okát. Az állásidő okai általában az eseményt követő 30 percen belül megjelennek.

#### <a name="planned-maintenance"></a>Tervezett karbantartás

Az Azure-infrastruktúra rendszeres időközönként hajtja végre a tervezett karbantartás – frissítse az adatközpontban található hardveres vagy szoftveres összetevőket. Az adatbázis karbantartási megy keresztül, míg az SQL néhány meglévő megszakítja, és újakat utasítania. A tervezett karbantartás során tapasztalt bejelentkezési hibák általában átmenetiek, és az [újrapróbálkozási logika](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) segít csökkenteni a hatást. Ha továbbra is jelentkezik a bejelentkezési hibák, kérjük forduljon az ügyfélszolgálathoz.

#### <a name="reconfiguration"></a>Reconfiguration

Reconfigurations átmeneti feltételek minősülnek, és várhatóan időről időre. Ezeket az eseményeket is elindítható a terhelés terheléselosztási vagy a szoftver vagy hardver meghibásodása. A felhőalapú adatbázishoz csatlakozó összes ügyfél-előállító alkalmazásnak robusztus kapcsolati [újrapróbálkozási logikát](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)kell létrehoznia, mivel ez segít az ilyen helyzetek enyhítésében, és általánosságban a hibákat általában a végfelhasználók számára transzparensvé teszi.

## <a name="next-steps"></a>Következő lépések

- További információ az [átmeneti hibákra vonatkozó újrapróbálkozási logikáról](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [SQL-csatlakozási hibák elhárítása, diagnosztizálása és elkerülése](./sql-database-connectivity-issues.md)
- További információ a [Resource Health riasztások konfigurálásáról](../service-health/resource-health-alert-arm-template-guide.md)
- A [Resource Health](../service-health/resource-health-overview.md) áttekintése
- [Resource Health GYIK](../service-health/resource-health-faq.md)

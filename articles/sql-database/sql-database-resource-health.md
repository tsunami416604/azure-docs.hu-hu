---
title: Az Azure Resource Health használata az SQL Database állapotának figyeléséhez |} A Microsoft Docs
description: Az Azure Resource Health segítségével megfigyelheti azok állapotát. az SQL Database és segít diagnosztizálni, és a támogatás igénylésében, ha egy Azure-beli probléma kihat az SQL-erőforrások.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/06/2018
ms.openlocfilehash: 5b0543b28117d349ceaef173a1201574cf2b3682
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53604548"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>A Resource Health használata az Azure SQL Database-kapcsolatának hibaelhárítása

## <a name="overview"></a>Áttekintés

[A Resource Health](../service-health/resource-health-overview.md#getting-started) az SQL Database segítségével diagnosztizálhatja és támogatást kérhet, ha egy Azure-beli probléma kihat az SQL-erőforrások. Tájékoztatja az erőforrásai aktuális és korábbi állapotáról, és segít a problémák kezelésében. A Resource Health műszaki támogatást nyújt, ha segítségre van szüksége az Azure szolgáltatásait érintő problémákkal kapcsolatban.

![Áttekintés](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Állapot-ellenőrzések

A Resource Health az SQL-erőforrás állapotát a sikeres és sikertelen bejelentkezések az erőforráshoz megvizsgálásával határozza meg. Jelenleg a Resource Health az SQL DB-erőforrás csak megvizsgálja a rendszer által visszaadott hiba, és nem a felhasználói hiba miatt sikertelen. Az erőforrás-állapot 1-2 percenként frissül.

## <a name="health-states"></a>Állapotokat

### <a name="available"></a>Elérhető

Az állapot **elérhető** azt jelenti, hogy a Resource Health bejelentkezések hibák az SQL-erőforrás a rendszer hibák miatt nem talált.

![Elérhető](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Csökkentett teljesítmény

A **Csökkentett teljesítmény** állapot azt jelenti, hogy a Resource Health főleg sikeres bejelentkezéseket észlelt, de néhány sikertelent is. Ezek a legnagyobb valószínűséggel átmeneti bejelentkezési hibák. Kapcsolódási problémákat, amelyeket átmeneti bejelentkezési hibák hatásának csökkentése érdekében hajtsa [újrapróbálkozási logika](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) a kódban.

![Csökkentett teljesítmény](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Nem elérhető

Az állapot **nem érhető el** azt jelenti, hogy a Resource Health azt észlelte, az SQL-erőforrás konzisztens sikertelen. Ha az erőforrás egy hosszabb ideig ebben az állapotban marad, kérjük forduljon az ügyfélszolgálathoz.

![Nem elérhető](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Ismeretlen

Állapotát **ismeretlen** azt jelzi, hogy a Resource Health még nem kapott ehhez az erőforráshoz információ a több mint 10 percig. Bár ez az állapot nem végleges megjelölése erőforrás állapotát, a hibaelhárítási folyamat fontos adatpontnak.
Ha az erőforrás a vártnak megfelelően működik, az erőforrás állapota változik elérhető néhány perc múlva.
Az erőforrás-problémákat tapasztal, ha az ismeretlen állapot mérete alapján feltételezhető, hogy a platform esemény van hatással az erőforrást.

## <a name="historical-information"></a>Előzményadatok

Akár 14 napig állapotelőzmények a Resource Health egészségügyi korábbi szakaszában is elérheti. A szakasz a Resource Health által jelentett állásidők üzemszünet okát (ha elérhető) is tartalmazzák. Jelenleg az Azure az SQL adatbázis-erőforrás állásidejét mutatja a két perces pontossággal. A tényleges állásidő kevesebb egy percnél valószínűleg – átlagos 8s.

### <a name="downtime-reasons"></a>Állásidő okok

Amikor az SQL Database állásidő, elemzési történik egy okának megállapításához. Ha elérhető, a Resource Health állapotelőzmények szakaszában jelenti, az üzemszünet okát. Az állásidő okai általában az eseményt követő 30 percen belül megjelennek.

#### <a name="planned-maintenance"></a>Tervezett karbantartás

Az Azure-infrastruktúra rendszeres időközönként hajtja végre a tervezett karbantartás – frissítse az adatközpontban található hardveres vagy szoftveres összetevőket. Az adatbázis karbantartási megy keresztül, míg az SQL néhány meglévő megszakítja, és újakat utasítania. A tervezett karbantartás során észlelt sikertelen általában átmenetiek, és [újrapróbálkozási logika](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) segít csökkentésében. Ha továbbra is jelentkezik a bejelentkezési hibák, kérjük forduljon az ügyfélszolgálathoz.

#### <a name="reconfiguration"></a>Reconfiguration

Reconfigurations átmeneti feltételek minősülnek, és várhatóan időről időre. Ezeket az eseményeket is elindítható a terhelés terheléselosztási vagy a szoftver vagy hardver meghibásodása. Minden olyan éles ügyfélalkalmazás, amely csatlakozik egy felhőalapú adatbázis meg kell valósítania egy robusztus kapcsolat [újrapróbálkozási logika](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors), ahogy ez segít az ilyen helyzetekben csökkentése, és általában győződjön meg a hibákat átlátható a végfelhasználók számára.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [újrapróbálkozási logika átmeneti hibák esetén](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [SQL-csatlakozási hibák elhárítása, diagnosztizálása és elkerülése](./sql-database-connectivity-issues.md)
- Tudjon meg többet [Resource Health-riasztások konfigurálása](/articles/service-health/resource-health-alert-arm-template-guide.md)
- Áttekintheti a [Resource Health](/articles/service-health/resource-health-overview.md)
- [A Resource Health – gyakori kérdések](/articles/service-health/resource-health-faq.md)

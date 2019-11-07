---
title: A Azure Resource Health használata a SQL Database állapotának figyelésére
description: A Azure Resource Health segítségével figyelheti a SQL Database Health-t, segít a diagnosztizálásban és a támogatásban, ha egy Azure-probléma hatással van az SQL-erőforrásokra.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 10ce2da8ae8bcd8a12f0972d437a84a37a8390b9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687500"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Resource Health használata a Azure SQL Database kapcsolatának hibakereséséhez

## <a name="overview"></a>Áttekintés

A [Resource Health](../service-health/resource-health-overview.md#get-started) for SQL Database segítséget nyújt a diagnosztizálásban és a támogatásban, ha egy Azure-probléma hatással van az SQL-erőforrásokra. Tájékoztatja az erőforrásai aktuális és korábbi állapotáról, és segít a problémák kezelésében. A Resource Health műszaki támogatást nyújt, ha segítségre van szüksége az Azure szolgáltatásait érintő problémákkal kapcsolatban.

![Áttekintés](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Állapot-ellenőrzések

Resource Health meghatározza az SQL-erőforrás állapotát az erőforrásba való bejelentkezés sikerességének és hibájának vizsgálatával. Az SQL-ADATBÁZIShoz tartozó erőforrás-Resource Health jelenleg csak rendszerhiba miatt ellenőrzi a bejelentkezési hibákat, és nem felhasználói hiba történt. A Resource Health állapota 1-2 percenként frissül.

## <a name="health-states"></a>Állapotok

### <a name="available"></a>Elérhető

Az **elérhető** állapot azt jelenti, hogy a Resource Health nem észlelt bejelentkezési hibákat az SQL-erőforráson rendszerhibák miatt.

![Elérhető](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Csökkentett teljesítményű

A **Csökkentett teljesítmény** állapot azt jelenti, hogy a Resource Health főleg sikeres bejelentkezéseket észlelt, de néhány sikertelent is. Ezek valószínűleg átmeneti bejelentkezési hibák. Az átmeneti bejelentkezési hibák okozta kapcsolódási problémák csökkentése érdekében hajtsa végre az [újrapróbálkozási logikát](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) a kódban.

![Csökkentett teljesítményű](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Nem érhető el

A nem **elérhető** állapot azt jelenti, hogy a Resource Health konzisztens bejelentkezési hibákat észlelt az SQL-erőforráshoz. Ha az erőforrás hosszabb ideig ebben az állapotban marad, forduljon az ügyfélszolgálathoz.

![Nem érhető el](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Ismeretlen

Az **ismeretlen** állapot azt jelzi, hogy Resource Health több mint 10 percig nem kapott információt az erőforrásról. Bár ez az állapot nem az erőforrás állapotának végleges jele, a hibaelhárítási folyamat fontos adatpontja. Ha az erőforrás a várt módon fut, az erőforrás állapota néhány perc múlva elérhetővé válik. Ha problémákat tapasztal az erőforrással kapcsolatban, az ismeretlen állapot azt sugallhatja, hogy a platformon lévő esemény hatással van az erőforrásra.

![Ismeretlen](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Korábbi információk

A Resource Health Health History (állapot előzményei) szakaszában akár 14 napig is elérheti az állapotot. A szakasz a Resource Health által jelentett állásidőt is tartalmazni fogja (ha elérhető). Az Azure az SQL-adatbázis állásidejét jelenleg kétperces pontossággal jeleníti meg. A tényleges állásidő valószínűleg egy percnél kevesebb, átlagosan 8 másodperc.

### <a name="downtime-reasons"></a>Leállás okai

Ha a SQL Database leállást tapasztal, elemzést végez az ok megállapításához. Ha elérhető, az állásidő oka a Resource Health állapotának előzményei című szakaszban található. Az állásidő okai általában az eseményt követő 30 percen belül megjelennek.

#### <a name="planned-maintenance"></a>Tervezett karbantartás

Az Azure-infrastruktúra rendszeresen hajtja végre a tervezett karbantartást – hardver-vagy szoftver-összetevők frissítése az adatközpontban. Amíg az adatbázis karbantartás alatt áll, az SQL megszakíthatja a meglévő kapcsolatokat, és elutasítja az újakat. A tervezett karbantartás során tapasztalt bejelentkezési hibák általában átmenetiek, és az [újrapróbálkozási logika](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) segít csökkenteni a hatást. Ha továbbra is bejelentkezési hibákat tapasztal, forduljon az ügyfélszolgálathoz.

#### <a name="reconfiguration"></a>Reconfiguration

Az újrakonfigurálás átmeneti feltételeknek tekintendő, és időről időre várható. Ezeket az eseményeket terheléselosztás vagy szoftver/hardver meghibásodása esetén aktiválhatja. A felhőalapú adatbázishoz csatlakozó összes ügyfél-előállító alkalmazásnak robusztus kapcsolati [újrapróbálkozási logikát](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)kell létrehoznia, mivel ez segít az ilyen helyzetek enyhítésében, és általánosságban a hibákat általában a végfelhasználók számára transzparensvé teszi.

## <a name="next-steps"></a>További lépések

- További információ az [átmeneti hibákra vonatkozó újrapróbálkozási logikáról](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [SQL-csatlakozási hibák elhárítása, diagnosztizálása és elkerülése](./sql-database-connectivity-issues.md)
- További információ a [Resource Health riasztások konfigurálásáról](../service-health/resource-health-alert-arm-template-guide.md)
- A [Resource Health](../service-health/resource-health-overview.md) áttekintése
- [Resource Health GYIK](../service-health/resource-health-faq.md)

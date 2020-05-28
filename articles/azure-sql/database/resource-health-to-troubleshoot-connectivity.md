---
title: Az adatbázis állapotának figyelése Azure Resource Health
description: A Azure Resource Health segítségével figyelheti a Azure SQL Database és az Azure SQL felügyelt példányának állapotát, segít a diagnosztizálásban és a támogatásban, ha egy Azure-probléma hatással van az SQL-erőforrásokra.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: fd4804ccbd98bd3cab9f5b55c56274f8cbc34c65
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84051023"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database-and-azure-sql-managed-instance"></a>A Resource Health használata a Azure SQL Database és az Azure SQL felügyelt példányaihoz való kapcsolódással kapcsolatos problémák megoldásához
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

## <a name="overview"></a>Áttekintés

A Azure SQL Database és az Azure SQL felügyelt példányának [Resource Health](../../service-health/resource-health-overview.md#get-started) segítségével diagnosztizálhatja és kérheti a támogatást, ha egy Azure-probléma hatással van az SQL-erőforrásokra. Tájékoztatja az erőforrásai aktuális és korábbi állapotáról, és segít a problémák kezelésében. A Resource Health műszaki támogatást nyújt, ha segítségre van szüksége az Azure szolgáltatásait érintő problémákkal kapcsolatban.

![Áttekintés](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Állapot-ellenőrzések

Resource Health meghatározza az SQL-erőforrás állapotát az erőforrásba való bejelentkezés sikerességének és hibájának vizsgálatával. A SQL Database erőforrás Resource Health jelenleg csak rendszerhiba miatt ellenőrzi a bejelentkezési hibákat, és nem a felhasználói hibát. A Resource Health állapota 1-2 percenként frissül.

## <a name="health-states"></a>Állapotok

### <a name="available"></a>Elérhető

Az **elérhető** állapot azt jelenti, hogy a Resource Health nem észlelt bejelentkezési hibákat az SQL-erőforráson rendszerhibák miatt.

![Elérhető](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-available.jpg)

### <a name="degraded"></a>Csökkentett teljesítményű

A **csökkentett teljesítményű** állapot azt jelenti, hogy Resource Health a sikeres bejelentkezések többségét észlelte, de néhány hiba is. Ezek valószínűleg átmeneti bejelentkezési hibák. Az átmeneti bejelentkezési hibák okozta kapcsolódási problémák csökkentése érdekében hajtsa végre az [újrapróbálkozási logikát](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) a kódban.

![Csökkentett teljesítményű](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Nem érhető el

A nem **elérhető** állapot azt jelenti, hogy a Resource Health konzisztens bejelentkezési hibákat észlelt az SQL-erőforráshoz. Ha az erőforrás hosszabb ideig ebben az állapotban marad, forduljon az ügyfélszolgálathoz.

![Nem érhető el](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Ismeretlen

Az **ismeretlen** állapot azt jelzi, hogy Resource Health több mint 10 percig nem kapott információt az erőforrásról. Bár ez az állapot nem az erőforrás állapotának végleges jele, a hibaelhárítási folyamat fontos adatpontja. Ha az erőforrás a várt módon fut, az erőforrás állapota néhány perc múlva elérhetővé válik. Ha problémákat tapasztal az erőforrással kapcsolatban, az ismeretlen állapot azt sugallhatja, hogy a platformon lévő esemény hatással van az erőforrásra.

![Ismeretlen](./media/resource-health-to-troubleshoot-connectivity/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Korábbi információk

A Resource Health Health History (állapot előzményei) szakaszában akár 14 napig is elérheti az állapotot. A szakasz a Resource Health által jelentett állásidőt is tartalmazni fogja (ha elérhető). Az Azure jelenleg az adatbázis-erőforrás leállását jeleníti meg kétperces részletességgel. A tényleges állásidő valószínűleg egy percnél kevesebb, átlagosan 8 másodperc.

### <a name="downtime-reasons"></a>Leállás okai

Ha az adatbázis leállást észlel, elemzést végez az ok megállapításához. Ha elérhető, az állásidő oka a Resource Health állapotának előzményei című szakaszban található. Az állásidő okai általában az eseményt követő 30 percen belül megjelennek.

#### <a name="planned-maintenance"></a>Tervezett karbantartás

Az Azure-infrastruktúra rendszeresen hajtja végre a tervezett karbantartást – hardver-vagy szoftver-összetevők frissítése az adatközpontban. Amíg az adatbázis karbantartás alatt áll, az SQL megszakíthatja a meglévő kapcsolatokat, és elutasítja az újakat. A tervezett karbantartás során tapasztalt bejelentkezési hibák általában átmenetiek, és az [újrapróbálkozási logika](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) segít csökkenteni a hatást. Ha továbbra is bejelentkezési hibákat tapasztal, forduljon az ügyfélszolgálathoz.

#### <a name="reconfiguration"></a>Reconfiguration

Az újrakonfigurálás átmeneti feltételeknek tekintendő, és időről időre várható. Ezeket az eseményeket terheléselosztás vagy szoftver/hardver meghibásodása esetén aktiválhatja. A felhőalapú adatbázishoz csatlakozó összes ügyfél-előállító alkalmazásnak robusztus kapcsolati [újrapróbálkozási logikát](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)kell létrehoznia, mivel ez segít az ilyen helyzetek enyhítésében, és általánosságban a hibákat általában a végfelhasználók számára transzparensvé teszi.

## <a name="next-steps"></a>Következő lépések

- További információ az [átmeneti hibákra vonatkozó újrapróbálkozási logikáról](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)
- [SQL-csatlakozási hibák elhárítása, diagnosztizálása és elkerülése](troubleshoot-common-connectivity-issues.md)
- További információ a [Resource Health riasztások konfigurálásáról](../../service-health/resource-health-alert-arm-template-guide.md)
- A [Resource Health](../../application-gateway/resource-health-overview.md) áttekintése
- [Erőforrás állapota GYIK](../../service-health/resource-health-faq.md)

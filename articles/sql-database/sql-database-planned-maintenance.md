---
title: Azure-karbantartási események tervezése
description: Ismerje meg, hogyan készülhet fel az Azure SQL-adatbázisában tervezett karbantartási eseményekre.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: ba882176fbe17f7b74c786f421dde8fadd58d9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821320"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Azure karbantartási események tervezése az Azure SQL Database-ben

Ismerje meg, hogyan készülhet fel a tervezett karbantartási eseményekre az Azure SQL-adatbázisában.

## <a name="what-is-a-planned-maintenance-event"></a>Mi az a tervezett karbantartási esemény?

Az Azure SQL DB minden egyes adatbázis hoz egy kvóruma adatbázis-replikák, ahol egy replika az elsődleges. Az elsődleges replika mindenkor online karbantartási, és legalább egy másodlagos replika kifogástalan nak kell lennie. A tervezett karbantartás során az adatbázis kvórumának tagjai egyenként offline állapotba kerülnek, azzal a céllal, hogy egy válaszoló elsődleges replika és legalább egy másodlagos replika online állapotba kerüljön, hogy biztosítsa az ügyfél állásidejének hiányát. Amikor az elsődleges replika offline állapotba kell hozni, egy újrakonfigurálási/feladatátvételi folyamat történik, amelyben egy másodlagos replika lesz az új elsődleges.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Mire számíthat egy tervezett karbantartási esemény során?

Az újrakonfigurálások/feladatátvételek általában 30 másodpercen belül befejeződnek – az átlag 8 másodperc. Ha már csatlakoztatva van, az alkalmazásnak újra kell csatlakoznia az adatbázis kifogástalan ultrásúj elsődleges replikájához. Ha új kapcsolatot kísérel meg, miközben az adatbázis újrakonfigurálásalatt áll, mielőtt az új elsődleges replika online állapotba kerül, a 40613-as (adatbázis nem érhető el) hibaüzenet jelenik meg: "A(z) "{databasename}" adatbázis a(z) "{servername}" kiszolgálón jelenleg nem érhető el. Próbálkozzon később újra a kapcsolattal.". Ha az adatbázis hosszú ideig futó lekérdezéssel rendelkezik, a lekérdezés megszakad az újrakonfigurálás során, és újra kell indítani.

## <a name="retry-logic"></a>Újrapróbálkozási logika

Minden olyan ügyfél-éles alkalmazásnak, amely egy felhőalapú adatbázis-szolgáltatáshoz csatlakozik, megbízható [kapcsolat-újrapróbálkozási logikát kell megvalósítania.](sql-database-connectivity-issues.md#retry-logic-for-transient-errors) Ez segít enyhíteni ezeket a helyzeteket, és általában átláthatóvá kell tennie a hibákat a végfelhasználó számára.

## <a name="frequency"></a>Frequency

Átlagosan 1,7 tervezett karbantartási esemény fordul elő havonta.

## <a name="resource-health"></a>Resource Health

Ha az SQL-adatbázis bejelentkezési hibákat tapasztal, ellenőrizze az [Erőforrás-állapot](../service-health/resource-health-overview.md#get-started) ablakban az [Azure Portalon](https://portal.azure.com) az aktuális állapot. Az Állapotelőzmények szakasz tartalmazza az egyes események állásidő-okát (ha elérhető).


## <a name="next-steps"></a>További lépések

- További információ az SQL Database [erőforrás-állapotáról](sql-database-resource-health.md)
- Az újrapróbálkozási logikáról az [Átmeneti hibák újrapróbálkozási logikája](sql-database-connectivity-issues.md#retry-logic-for-transient-errors) című témakörben talál további információt.

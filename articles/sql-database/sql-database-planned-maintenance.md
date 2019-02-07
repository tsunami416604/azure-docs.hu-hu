---
title: Karbantartás az Azure tervezése események – Azure SQL Database |} A Microsoft Docs
description: Ismerje meg, hogyan lehet felkészülni a tervezett karbantartási események az Azure SQL Database-adatbázishoz.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
manager: craigg
ms.date: 01/30/2019
ms.openlocfilehash: 928338a911efae051df7164239dbd19f9317338a
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824203"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Az Azure SQL Database Azure karbantartási események tervezése

Ismerje meg, hogyan lehet felkészülni a tervezett karbantartási események az Azure SQL Database.

## <a name="what-is-a-planned-maintenance-event"></a>Mi az a tervezett karbantartási esemény

Minden egyes adatbázishoz Azure SQL Database egy kvórum, ha egy replika-e az elsődleges adatbázis-replikákat tart fenn. At-mindig egy elsődleges replika kell lennie az online karbantartás, és legalább egy másodlagos másodpéldány kifogástalan állapotban kell lennie. A tervezett karbantartás során az adatbázis kvórum tagjai kerül offline egyenként, azzal a szándékkal, hogy van egy elsődleges replika, és legalább egy másodlagos másodpéldány online annak érdekében, leállás nélkül ügyfél válaszol. Ha az elsődleges másodpéldány offline állapotba kerül, újrakonfigurálás/feladatátvételi folyamatot akkor fordul elő, amelyben egy másodlagos másodpéldány lesz az új elsődleges.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Mi történik, tervezett karbantartási események

Az átlagos reconfigurations/feladatátvételi teszteket általában 30 másodpercen belül hajtsa végre – 8 másodperc. Ha már csatlakozott, az alkalmazás az adatbázis megfelelő állapotú másolása új elsődleges replikán kell csatlakoznia. Ha új csatlakozási kísérlet során az adatbázis alatt áll egy újrakonfigurálás, mielőtt az új elsődleges replika online állapotban, hibaüzenetet 40613 (adatbázis nem érhető el): "A(z) {servername} kiszolgáló a(z) {databasename} adatbázis jelenleg nem áll rendelkezésre. A kapcsolat később próbálkozzon újra. ". Ha az adatbázis egy hosszan futó lekérdezést, ez a lekérdezés egy újrakonfigurálás során megszakad, és újra kell indítani kell.

## <a name="retry-logic"></a>Újrapróbálkozási logika

Minden olyan éles ügyfélalkalmazás, amely csatlakozik egy felhőbeli adatbázis-szolgáltatás meg kell valósítania egy robusztus kapcsolat [újrapróbálkozási logika](sql-database-connectivity-issues.md#retry-logic-for-transient-errors). Ez megfontolandó ezekben a helyzetekben, és általában győződjön meg a hibákat átlátható a végfelhasználók számára.

## <a name="frequency"></a>Gyakoriság

1.7 tervezett karbantartási események átlagosan havonta fordulhat elő.

## <a name="resource-health"></a>Resource Health

Ha az SQL-adatbázis bejelentkezési hibákat tapasztal, ellenőrizze a [Resource Health](../service-health/resource-health-overview.md#getting-started) ablakában a [az Azure portal](https://portal.azure.com) az aktuális állapot. Állapotelőzmények szakasz tartalmaz minden esemény (ha elérhető) üzemszünet okát.


## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Resource Health](sql-database-resource-health.md) SQL Database-hez
- Újrapróbálkozási logika kapcsolatos további információkért lásd: [újrapróbálkozási logika átmeneti hibák esetén](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)

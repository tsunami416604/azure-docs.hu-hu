---
title: Az Azure karbantartási eseményeinek tervezése
description: Megtudhatja, hogyan készítheti elő a tervezett karbantartási eseményeket a Azure SQL Database.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "73821320"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Azure karbantartási események tervezése az Azure SQL Database-ben

Ismerje meg, hogyan készítheti elő a tervezett karbantartási eseményeket az Azure SQL Database-ben.

## <a name="what-is-a-planned-maintenance-event"></a>Mi a tervezett karbantartási esemény?

Az Azure SQL DB minden adatbázis esetében az adatbázis-replikák kvórumát tárolja, ahol az egyik replika az elsődleges. Az elsődleges replika minden esetben online karbantartásnak kell lennie, és legalább egy másodlagos replikának kifogástalannak kell lennie. A tervezett karbantartás során az adatbázis Kvórumának tagjai egy időben offline állapotba kerülnek, azzal a szándékkal, hogy az ügyfél leállásának hiányában egyetlen válasz elsődleges replika és legalább egy másodlagos replika online állapotban van. Ha az elsődleges replikát offline állapotba kell helyezni, egy újrakonfigurálási/feladatátvételi folyamat fog történni, amelyben egy másodlagos replika lesz az új elsődleges.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Mire számíthat egy tervezett karbantartási esemény során

Az újrakonfigurálások/feladatátvételek általában 30 másodpercen belül befejeződik – az átlag 8 másodperc. Ha már csatlakoztatva van, az alkalmazásnak újra kapcsolódnia kell az adatbázisa új elsődleges másodpéldányához. Ha egy új kapcsolatra akkor kerül sor, amikor az adatbázis újrakonfigurálást végez az új elsődleges replika online állapotba lépését megelőzően, akkor a 40613-as hibaüzenet jelenik meg (az adatbázis nem érhető el): a (z) {servername} kiszolgáló {databasename} adatbázisa jelenleg nem érhető el. Próbálja megismételni a kapcsolatokat később. " Ha az adatbázis hosszú ideig futó lekérdezéssel rendelkezik, akkor a lekérdezés az újrakonfigurálás során megszakad, és újra kell indítani.

## <a name="retry-logic"></a>Újrapróbálkozási logika

A felhőalapú adatbázis-szolgáltatáshoz csatlakozó összes ügyfél-éles alkalmazásnak robusztus kapcsolati [újrapróbálkozási logikát](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)kell létrehoznia. Ez segít enyhíteni ezeket a helyzeteket, és általában a végfelhasználók számára transzparens módon végezze el a hibákat.

## <a name="frequency"></a>Frequency

Átlagosan 1,7 tervezett karbantartási esemény történik havonta.

## <a name="resource-health"></a>Resource Health

Ha az SQL-adatbázis bejelentkezési hibákat észlel, tekintse meg az aktuális állapot [Azure Portal](https://portal.azure.com) [Resource Health](../service-health/resource-health-overview.md#get-started) ablakát. Az állapot előzményei szakasz az egyes események állásidői okát tartalmazza (ha elérhető).


## <a name="next-steps"></a>További lépések

- További információ a SQL Database [Resource Healthről](sql-database-resource-health.md)
- Az újrapróbálkozási logikával kapcsolatos további információkért lásd az [átmeneti hibákra vonatkozó újrapróbálkozási logikát](sql-database-connectivity-issues.md#retry-logic-for-transient-errors) .

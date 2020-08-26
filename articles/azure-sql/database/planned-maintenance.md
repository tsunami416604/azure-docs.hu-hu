---
title: Az Azure karbantartási eseményeinek tervezése
description: Megtudhatja, hogyan készítheti elő a tervezett karbantartási eseményeket a Azure SQL Database és az Azure SQL felügyelt példányain.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 08/25/2020
ms.openlocfilehash: 85459f357032a7f9944d50e3e4f3929015c6dcfd
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869117"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Az Azure karbantartási eseményeinek megtervezése Azure SQL Database és az Azure SQL felügyelt példányain
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Megtudhatja, hogyan készítheti elő a tervezett karbantartási eseményeket az adatbázisában Azure SQL Database és az Azure SQL felügyelt példányain.

## <a name="what-is-a-planned-maintenance-event"></a>Mi a tervezett karbantartási esemény?

Az egyes adatbázisok esetében a Azure SQL Database és az Azure SQL felügyelt példányai az adatbázis-replikák kvórumát őrzik meg, ahol az egyik replika az elsődleges. Az elsődleges replika mindig online karbantartásnak kell lennie, és legalább egy másodlagos replikának kifogástalannak kell lennie. A tervezett karbantartás során az adatbázis Kvórumának tagjai egy időben offline állapotba kerülnek, azzal a szándékkal, hogy az ügyfél leállásának hiányában egyetlen válasz elsődleges replika és legalább egy másodlagos replika online állapotban van. Ha az elsődleges replikát offline állapotba kell helyezni, egy újrakonfigurálási/feladatátvételi folyamat fog történni, amelyben egy másodlagos replika lesz az új elsődleges.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Mire számíthat egy tervezett karbantartási esemény során

A karbantartási esemény a karbantartási esemény elején lévő elsődleges és másodlagos replikák csillagképtől függően egy vagy több feladatátvételt is képes létrehozni. Átlagosan 1,7 feladatátvétel történik egy tervezett karbantartási eseménynél. Az újrakonfigurálások/feladatátvételek általában 30 másodpercen belül befejeződik. Az átlag 8 másodperc. Ha már csatlakoztatva van, az alkalmazásnak újra kapcsolódnia kell az adatbázis új elsődleges replikához. Ha egy új kapcsolatra akkor kerül sor, amikor az adatbázis újrakonfigurálást végez az új elsődleges replika online állapotba lépését megelőzően, akkor a 40613-as hibaüzenet jelenik meg (az adatbázis nem érhető el): a (z) *{servername} kiszolgáló {databasename} adatbázisa jelenleg nem érhető el. Próbálja megismételni a kapcsolatokat később. "* Ha az adatbázis hosszú ideig futó lekérdezéssel rendelkezik, akkor a lekérdezés az újrakonfigurálás során megszakad, és újra kell indítani.

## <a name="how-to-simulate-a-planned-maintenance-event"></a>Tervezett karbantartási esemény szimulálása

Annak biztosítása, hogy az ügyfélalkalmazás az éles környezetben történő üzembe helyezést megelőzően a karbantartási események számára is rugalmas legyen, segít csökkenteni az alkalmazások hibáinak kockázatát, és hozzájárul az alkalmazások rendelkezésre állásához a végfelhasználók számára. A tervezett karbantartási események során tesztelheti az ügyfélalkalmazás viselkedését, ha a PowerShell, a CLI vagy a REST API segítségével [manuális feladatátvételt kezdeményez](https://aka.ms/mifailover-techblog) . Azonos viselkedést eredményez, mint a karbantartási esemény, amely az elsődleges replikát offline állapotba hozza.

## <a name="retry-logic"></a>Újrapróbálkozási logika

A felhőalapú adatbázis-szolgáltatáshoz csatlakozó összes ügyfél-éles alkalmazásnak robusztus kapcsolati [újrapróbálkozási logikát](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)kell létrehoznia. Ez segít a feladatátvételek transzparensvé tételében a végfelhasználók számára, vagy legalább minimálisra csökkenteni a negatív hatásokat.

## <a name="resource-health"></a>Erőforrás állapota

Ha az adatbázis bejelentkezési hibákat észlel, tekintse meg az aktuális állapot [Azure Portal](https://portal.azure.com) [Resource Health](../../service-health/resource-health-overview.md#get-started) ablakát. Az állapot előzményei szakasz az egyes események állásidői okát tartalmazza (ha elérhető).

## <a name="next-steps"></a>Következő lépések

- További információ a Azure SQL Database és az Azure SQL felügyelt példányának [Resource Healthéről](resource-health-to-troubleshoot-connectivity.md) .
- Az újrapróbálkozási logikával kapcsolatos további információkért lásd az [átmeneti hibákra vonatkozó újrapróbálkozási logikát](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).

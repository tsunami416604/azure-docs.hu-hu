---
title: Naplózás és fenyegetésészlelés engedélyezése SQL-kiszolgálón az Azure Security Centerben |} A Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan valósíthat meg az Azure Security Center javaslatait **engedélyezése naplózás és fenyegetésészlelés az SQL Server-kiszolgálók**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 042fca4d-7dab-4172-8614-e8c21ccb4960
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 5b41af83122d74fc766e6c5179d98803979269f7
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108908"
---
# <a name="enable-auditing-and-threat-detection-on-sql-servers-in-azure-security-center"></a>Naplózás és fenyegetésészlelés engedélyezése SQL-kiszolgálón az Azure Security Centerben
Az Azure Security Center javasolni fogja, hogy kapcsolja be a naplózást, és a fenyegetésészlelés, az összes adatbázis az Azure SQL-kiszolgálón, ha naplózás már nem érhető. Naplózás és fenyegetés-észlelési segíthet a jogszabályoknak való megfelelőség, adatbázis-tevékenység megértésében, valamint betekintést eltéréseket és rendellenességeket, amelyek üzleti aggályokra vagy biztonsági problémákat.

Miután bekapcsolta a naplózást konfigurálhatja a Fenyegetésészlelési beállításainak és az e-maileket a biztonsági riasztást küld. Fenyegetésészlelés észleli a rendellenes adatbázis-tevékenységek utaló esetleges biztonsági fenyegetések az adatbázishoz. Ez lehetővé teszi, hogy észlelése és azok bekövetkezésekor reagáljon a lehetséges veszélyforrásokra.

Ez a javaslat vonatkozik; csak az Azure SQL szolgáltatásba az Azure infrastruktúra-szolgáltatások (az Azure IaaS) virtuális gépeken futó SQL Server nem tartalmazza.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslatok megvalósítása
1. Az a **javaslatok** panelen válassza ki **engedélyezése naplózás és fenyegetésészlelés az SQL Server-kiszolgálók**.  Ekkor megnyílik a **engedélyezése naplózás és fenyegetésészlelés az SQL Server-kiszolgálók** panelen.

   ![SQL Serverek naplózásának engedélyezése][1]
2. Válassza ki a naplózás és fenyegetésészlelés engedélyezése SQL-kiszolgáló. Ekkor megnyílik a **naplózás és Fenyegetésészlelés** panelen.

3. Az a **naplózás és Fenyegetésészlelés** panelen válassza ki **ON** alatt **naplózási**.

   ![Kapcsolja be a naplózási beállítások][2]
4. Kövesse a [SQL database naplózási szolgáltatásával az Azure Portalon](../sql-database/sql-database-auditing-portal.md) a vizsgálati naplók tárolására szolgáló tároló konfigurálásához. Az adatgyűjtés az előfizetéshez tartozó tárfiók az alapértelmezett tárfiók.
5. Kövesse a [Ismerkedés az SQL Database Threat Detection](../sql-database/sql-database-threat-detection.md) bekapcsolásához és a Fenyegetésészlelés konfigurálása és a rendellenes tevékenységek észlelésekor biztonsági riasztást fog kapni e-maileket listájának konfigurálásához.

## <a name="see-also"></a>Lásd még
Ez a cikk bemutatta, hogyan valósíthat meg a Security Center ajánlás "Enable naplózás és fenyegetésészlelés az SQL Server-kiszolgálók." SQL-adatbázis védelme kapcsolatos további információkért tekintse meg a következőket:

* [Az SQL Database-adatbázis védelme](../sql-database/sql-database-security-overview.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – a legújabb Azure biztonsági hírek és információ.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png

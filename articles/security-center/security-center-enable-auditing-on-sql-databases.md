---
title: Naplózás és fenyegetésészlelés engedélyezése az SQL-adatbázisok az Azure Security Centerben |} A Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan valósíthat meg az Azure Security Center javaslatait **naplózás és fenyegetésészlelés engedélyezése SQL-adatbázis**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 224b6755-2b36-4ecd-9af8-139a198e0df1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 0bd49f831655f5ffaf7f1d943aca552ddd8447a0
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309157"
---
# <a name="enable-auditing-and-threat-detection-on-sql-databases-in-azure-security-center"></a>Naplózás és fenyegetésészlelés engedélyezése az SQL-adatbázisok az Azure Security Centerben
Az Azure Security Center javasolni fogja, hogy bekapcsolja a naplózás és fenyegetésészlelés az SQL-adatbázisok, ha a naplózás és fenyegetésészlelés már nem érhető. Naplózás és fenyegetés-észlelési segíthet a jogszabályoknak való megfelelőség, adatbázis-tevékenység megértésében, valamint betekintést eltéréseket és rendellenességeket, amelyek üzleti aggályokra vagy biztonsági problémákat.

Miután bekapcsolta a naplózást konfigurálhatja a Fenyegetésészlelési beállításainak és az e-maileket a biztonsági riasztást küld. Fenyegetésészlelés észleli a rendellenes adatbázis-tevékenységek utaló esetleges biztonsági fenyegetések az adatbázishoz. Ez lehetővé teszi, hogy észlelése és azok bekövetkezésekor reagáljon a lehetséges veszélyforrásokra.

Ez a javaslat vonatkozik; csak az Azure SQL szolgáltatásba a virtuális gépeken futó SQL nem tartalmazza.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslatok megvalósítása
1. Az a **javaslatok** panelen válassza ki **engedélyezése naplózás és fenyegetésészlelés az SQL-adatbázisokon**.  Ekkor megnyílik a **engedélyezése naplózás és fenyegetésészlelés az SQL-adatbázisokon** panelen.

   ![SQL-adatbázis naplózásának engedélyezése][1]
2. Válassza ki az SQL-adatbázis, a naplózás engedélyezéséhez. Ekkor megnyílik a **naplózás és Fenyegetésészlelés** panelen.

3. Az a **naplózás és Fenyegetésészlelés** panelen válassza ki **ON** alatt **naplózási**.

   ![Kapcsolja be a naplózás és fenyegetésészlelés][2]
4. Kövesse a [SQL Database Threat Detection az Azure Portalon](../sql-database/sql-database-threat-detection-portal.md) bekapcsolásához és a Fenyegetésészlelés konfigurálása és a rendellenes tevékenységek észlelésekor biztonsági riasztást fog kapni e-maileket listájának konfigurálásához.

## <a name="see-also"></a>Lásd még
Ez a cikk bemutatta, hogyan valósíthat meg a Security Center javaslatait "Enable naplózás és fenyegetésészlelés az SQL-adatbázisokon." SQL-adatbázis védelme kapcsolatos további információkért tekintse meg a következőket:

* [Az SQL Database-adatbázis védelme](../sql-database/sql-database-security-overview.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-azure-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – a legújabb Azure biztonsági hírek és információ.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png

---
title: "SQL-adatbázisok az Azure Security Centerben a naplózás és a fenyegetések észlelésére engedélyezése |} Microsoft Docs"
description: "Ez a dokumentum azt ismerteti, hogyan valósítja meg az Azure Security Center ajánlás ** engedélyezése az SQL adatbázisok ** naplózás és a fenyegetések észlelésére."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 224b6755-2b36-4ecd-9af8-139a198e0df1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: terrylan
ms.openlocfilehash: 8f4febdaa4497fee0dc690b59cd6eaa415c5e5cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="enable-auditing-and-threat-detection-on-sql-databases-in-azure-security-center"></a>SQL-adatbázisok az Azure Security Centerben a naplózás és a fenyegetések észlelésére engedélyezése
Az Azure Security Center azt javasolja, hogy bekapcsolja a naplózást, és a fenyegetések észlelésére az összes SQL-adatbázisok ha naplózás és fenyegetésészlelés nincs engedélyezve. Naplózás és a fenyegetések észlelése segítségével törvényi megfelelőség fenntartásában, ismerje meg adatbázis-tevékenység, és betekintést azok az eltérések és rendellenességek, amelyek üzleti problémát jelenthetnek, vagy a biztonság megsértésére.

Után a naplózás bekapcsolta a Fenyegetésészlelés beállítások és az e-maileket a biztonsági riasztások állíthatja be. A Fenyegetésészlelés az adatbázist érintő rendellenes tevékenységeket, amelyek esetleges biztonsági fenyegetéseket jelezhetnek a észleli. Ez lehetővé teszi, hogy észlelje és azok bekövetkezésekor reagáljon a lehetséges veszélyforrásokra.

Ez a javaslat vonatkozik az Azure SQL-szolgáltatás csak; a virtuális gépeken futó SQL nem tartoznak bele.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="implement-the-recommendation"></a>A javaslat megvalósítása
1. Az a **javaslatok** panelen válassza **naplózás engedélyezését & Threat detection SQL-adatbázisok**.  Ekkor megnyílik a **naplózás engedélyezését & Threat detection SQL-adatbázisok** panelen.

   ![SQL-adatbázis naplózásának engedélyezése][1]
2. Válassza ki az SQL-adatbázis is engedélyezi a naplózást. Ekkor megnyílik a **naplózás és Fenyegetésészlelés** panelen.

3. Az a **naplózás és Fenyegetésészlelés** panelen válassza **ON** alatt **naplózási**.

   ![Kapcsolja be a naplózás és a fenyegetések észlelése][2]
4. Kövesse a [SQL adatbázis Fenyegetésészlelés az Azure portálon](../sql-database/sql-database-threat-detection-portal.md) bekapcsolásának és konfigurálásának fenyegetések észlelése és konfigurálhatja az e-maileket, a rendellenes tevékenységek észlelésekor a biztonsági riasztásokat fogadó listáját.

## <a name="see-also"></a>Lásd még:
Ez a cikk bemutatta megvalósításához a Security Center javaslat "engedélyezése naplózási & Threat detection SQL-adatbázisok." SQL-adatbázisok védelme kapcsolatos további tudnivalókért olvassa el a következőket:

* [Az SQL Database-adatbázis védelme](../sql-database/sql-database-security-overview.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának figyelésére.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) --az Azure biztonsági legfrissebb hírek és információ.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png

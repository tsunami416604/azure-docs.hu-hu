---
title: Az Azure Security Center és Azure SQL Database szolgáltatás |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan a Security Center segíthet az Azure SQL Database-ben az adatbázisok védelmét.
services: sql-database
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rkarlin
ms.openlocfilehash: 0a889de79b6a5921007614dac8d610c1be0222d2
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114087"
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Az Azure Security Center és Azure SQL Database szolgáltatás
[Az Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) lehetővé teszi a fenyegetések megelőzését, észlelését és kezelését. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

Ez a cikk bemutatja, hogyan a Security Center segíthet az Azure SQL Database-ben az adatbázisok védelmét.

## <a name="why-use-security-center"></a>Miért használja a Security Centert?
Security Center segítségével védheti az adatokat SQL Database-ben azáltal, hogy a kiszolgálók és adatbázisok biztonsági. A Security Center segítségével:

* SQL Database-titkosítás és naplózás házirendeket határozhat meg.
* SQL-adatbázis-erőforrások figyelése az összes előfizetésére.
* Gyorsan azonosíthatja és biztonsági problémák elhárítására.
* A riasztásainak integrálása [Azure SQL Database fenyegetésészlelési](../sql-database/sql-database-threat-detection.md).

Az SQL Database-erőforrások védelmének elősegítése, a Security Center is nyújt biztonsági felügyeletében és kezelésében az Azure virtual machines, Cloud Services, alkalmazásszolgáltatások, virtuális hálózatok és több. További tudnivalók a Security Center [Itt](security-center-intro.md).

## <a name="prerequisites"></a>Előfeltételek
A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. A Security Center ingyenes szintjét engedélyezve van az előfizetéséhez. További információ a Security Center ingyenes és Standard szint: [Security Center díjszabását](https://azure.microsoft.com/pricing/details/security-center/).

A Security Center támogatja a szerepköralapú hozzáférés. Szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-ban kapcsolatos további információkért lásd: [Azure Active Directory szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md). A Security Center – gyakori kérdések információt nyújt az [engedélyek kezelésének módja a Security Center](security-center-faq.md#permissions).

## <a name="access-security-center"></a>A Security Center elérése
A Security Center az [Azure Portalról](https://azure.microsoft.com/features/azure-portal/) érhető el. [Jelentkezzen be a portálra](https://portal.azure.com/) , és válassza ki a **a Security Center beállítás**.

![A Security Center lehetőség][1]

A **a Security Center** panel nyílik meg.
![A Security Center panel][2]

## <a name="set-security-policy"></a>Biztonsági házirend beállítása
Biztonsági szabályzat határozza meg, az adott előfizetésen vagy erőforráscsoporton belüli erőforrások ajánlott vezérlők. A Security Centerben az előfizetések vagy erőforráscsoportok a vállalat biztonsági igényeinek és az alkalmazások típusához vagy az egyes előfizetések adatainak érzékenysége alapján házirendeket határozhat meg.

Beállíthat egy szabályzatot, amely az SQL-naplózás és az SQL transzparens adattitkosítás (TDE) javaslatok megjelenítése.

* Ha bekapcsolja a **SQL-naplózás és Fenyegetésészlelés**, a Security Center javasolja, hogy az Azure Database-hozzáférések naplózása engedélyezve a megfelelés, a jobb észlelés és a támadások hatékonyabb kivizsgálásához.
* Ha bekapcsolja a **SQL transzparens adattitkosítás**, a Security Center javasolja, hogy a titkosítás inaktív állapotban engedélyezni az Azure SQL Database, az azokhoz kapcsolódó biztonsági mentési és a tranzakciós naplófájlokat.

Biztonsági szabályzat beállításához válassza ki a **házirend** csempe a Security Center panel. Az a **biztonsági házirend** panelen válassza ki az előfizetést, amelyen szeretné engedélyezni a biztonsági szabályzatot. Válassza ki **megelőzési szabályzat** kapcsolja **a** a biztonsági javaslatok láthatók, amelyet az előfizetésnél használni szeretne.
![Biztonsági szabályzat][3]

További tudnivalókért lásd: [biztonsági szabályzatok beállítása](tutorial-security-policy.md).

## <a name="manage-security-recommendation"></a>Biztonsági javaslatok kezelése
A Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát. A Security Center javaslatokat hoz létre, amikor lehetséges biztonsági réseket észlel. A javaslatok végigvezetik Önt a szükséges vezérlők konfigurálásának folyamatán.

Biztonsági szabályzat beállítása után a Security Center elemzi a potenciális sebezhető pontokat azonosíthatja az erőforrások biztonsági állapotát. A javaslatok, minden sor egy adott javaslatot táblázatos formátumban jelennek meg. Referenciaként a következő táblázat használatával segítséget nyújtanak az Azure SQL Database, és az egyes javaslatok célja alkalmazásuk esetén azok a rendelkezésre álló kapcsolatos javaslatok megértése. Javaslat kiválasztásával megnyílik egy cikk, amely ismerteti a javaslat megvalósítása a Security Centerben.

| Ajánlás | Leírás |
| --- | --- |
| [Naplózás és Fenyegetésészlelés engedélyezése SQL-kiszolgálókon](security-center-enable-auditing-on-sql-servers.md) |Javasolja, hogy kapcsolja be a naplózás és fenyegetésészlelés az SQL Database-kiszolgálók. (Csak az SQL Database szolgáltatás. Nem tartalmazza a Microsoft SQL Server, a virtuális gépeken futó.) |
| [Naplózás és Fenyegetésészlelés engedélyezése SQL-adatbázis](security-center-enable-auditing-on-sql-databases.md) |Javasolja, hogy kapcsolja be a naplózás és fenyegetésészlelés az SQL Database-adatbázisok. (Csak az SQL Database szolgáltatás. Nem tartalmazza a Microsoft SQL Server, a virtuális gépeken futó.) |
| [Transzparens adattitkosítás engedélyezése](security-center-enable-transparent-data-encryption.md) |SQL-adatbázisok titkosításának engedélyezését javasolja. (Az SQL Database szolgáltatás csak.) |

Javaslatok az Azure-erőforrások megtekintéséhez válasszon a **javaslatok** csempe a Security Center panel. Az a **javaslatok** panelen válassza ki egyes javaslatokra kattintva részletesen. Ebben a példában válassza **engedélyezése naplózás és fenyegetésészlelés az SQL Server-kiszolgálók**.

![Javaslatok][4]

Alább látható módon a Security Center jeleníti meg az SQL Server-kiszolgálók, naplózás és fenyegetésészlelés nincs engedélyezve. Naplózás bekapcsolása után beállíthatja a Fenyegetésészlelési beállításainak és az e-mail-beállítások biztonsági riasztást küld. A Fenyegetésészlelés riasztást küld, ha azt észleli a rendellenes adatbázis-tevékenységek, amely potenciálisan biztonsági fenyegetést az adatbázishoz. A riasztások jelennek meg a Security Center irányítópultján.
![Naplózás és fenyegetésészlelés][5]

Kövesse a [az Azure Portalon az SQL Database fenyegetésészlelési](../sql-database/sql-database-threat-detection-portal.md) bekapcsolása és fenyegetésészlelés konfigurálása, és konfigurálhatja a listáját, amely a rendellenes tevékenységek észlelésekor biztonsági riasztást fog kapni e-maileket.

Javaslatok kapcsolatos további információkért lásd: [biztonsági javaslatok kezelése](security-center-recommendations.md).

## <a name="monitor-security-health"></a>A biztonsági állapot figyelése
Ha bekapcsolja az előfizetéshez tartozó erőforrásokra vonatkozó [biztonsági szabályzatokat](tutorial-security-policy.md), a Security Center elvégzi az erőforrások biztonsági elemzését, és azonosítja a potenciális sebezhető pontokat.  Az erőforrások biztonsági állapotát is megtekintheti a **erőforrás biztonsági állapota** csempére. Kattintva **adatok** a a **erőforrás biztonsági állapota** csempére a **adatforrások** panelt, amelynek SQL-javaslatok, például a naplózási és átlátható adatok nem engedélyezhető a titkosítás. Ezenfelül általános javaslatokat is talál itt az adatbázis állapotára vonatkozóan.
![Erőforrás biztonsági állapota][6]

További tudnivalókért lásd: [biztonsági állapotfigyelés](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>A biztonsági riasztások kezelése és a riasztásokra való válaszadás
A Security Center automatikusan gyűjti, elemzi és integrálja naplóadatait [Azure SQL-Fenyegetésészlelés](../sql-database/sql-database-threat-detection.md), valamint a más Azure-erőforrások, a valós fenyegetések észlelése és a vakriasztások számának csökkentése érdekében. A Security Centerben megtekinthető a rangsorolt biztonsági riasztások listája, ezenkívül a probléma gyors vizsgálatára vonatkozó információk és a támadás elhárításával kapcsolatos javaslatok is megjelennek.

Riasztások megtekintéséhez válassza ki a **biztonsági riasztások** csempe a Security Center panel. Az a **biztonsági riasztások** panelen, és válassza ki egy riasztást, további információ az események, amely kiváltotta a riasztást és mit, ha bármely, lépésről lépésre kell tennie a támadás elhárításával. Ebben a példában válassza **lehetséges SQL-injektálás**.
![Biztonsági riasztások][7]

Alább látható módon a Security Center biztosít, amelyek áttekintést nyújtanak a célként megadott erőforrás, amikor a alkalmazni a riasztás kiváltó okáról további részleteket a forrás IP-címet, és a fenyegetés elhárítására vonatkozó javaslatok.
![Potenciális SQL-injektálás][8]

További tudnivalókért lásd: [kezelése és válaszadás a biztonsági riasztások](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>További lépések
* [A Security Center – gyakori kérdések](security-center-faq.md) – gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [A Security Center tervezéséhez és útmutató](security-center-planning-and-operations-guide.md) - kövesse az ismertetett lépések és feladatok optimalizálhatja a Security Center használatát a szervezet biztonsági igényeinek és felhőfelügyeleti modelljének alapján.
* [Security Center által nyújtott adatbiztonság](security-center-data-security.md) – megtudhatja, hogyan a Security Center gyűjti és dolgozza fel az adatokat az Azure-erőforrások, például a konfigurációs adatokat, metaadatokat, eseménynaplókat, összeomlási memóriaképeket és további információ.
* [Biztonsági incidensek kezelése](security-center-incident.md) – útmutató a Security Center a biztonsági funkcióinak használatával segítséget nyújt a biztonsági incidensek kezelése.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png

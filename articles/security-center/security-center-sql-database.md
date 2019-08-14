---
title: Azure Security Center és Azure SQL Database szolgáltatás | Microsoft Docs
description: Ez a cikk bemutatja, hogyan segítheti a Security Center az adatbázisok védelmét Azure SQL Databaseokban.
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
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "60704554"
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Azure Security Center és Azure SQL Database szolgáltatás
[Az Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) lehetővé teszi a fenyegetések megelőzését, észlelését és kezelését. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

Ez a cikk bemutatja, hogyan segítheti a Security Center az adatbázisok védelmét Azure SQL Databaseokban.

## <a name="why-use-security-center"></a>Miért használja a Security Centert?
A Security Center a SQL Database az adatvédelmet segíti az összes kiszolgáló és adatbázis biztonságának biztosításával. A Security Center a következőket teheti:

* Szabályzatok meghatározása a SQL Database titkosításhoz és a naplózáshoz.
* SQL Database erőforrások biztonságának figyelése az összes előfizetésében.
* Gyorsan azonosíthatja és elháríthatja a biztonsági problémákat.
* Riasztások integrálása [Azure SQL Database veszélyforrások észlelésével](../sql-database/sql-database-threat-detection.md).

A SQL Database erőforrásainak védelme mellett a Security Center az Azure Virtual Machines, a Cloud Services, a App Services, a Virtual Network és más rendszerek biztonsági monitorozását és kezelését is lehetővé teszi. További információ a Security Centerről. [](security-center-intro.md)

## <a name="prerequisites"></a>Előfeltételek
A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. A Security Center ingyenes szintje engedélyezve van az előfizetésében. A Security Center ingyenes és standard csomagjaival kapcsolatos további információkért tekintse meg a [Security Center díjszabását](https://azure.microsoft.com/pricing/details/security-center/).

A Security Center támogatja a szerepköralapú hozzáférést. További információ az Azure-beli szerepköralapú hozzáférés-vezérlésről (RBAC): [Azure Active Directory szerepköralapú Access Control](../role-based-access-control/role-assignments-portal.md). A Security Center GYIK információt nyújt arról [, hogy az engedélyek hogyan kezelhetők](security-center-faq.md#permissions)a Security Centerokban.

## <a name="access-security-center"></a>A Security Center elérése
A Security Center az [Azure Portalról](https://azure.microsoft.com/features/azure-portal/) érhető el. [Jelentkezzen be a portálra](https://portal.azure.com/) , és válassza a **Security Center lehetőséget**.

![Security Center lehetőség][1]

Megnyílik a **Security Center** panel.
![Security Center panel][2]

## <a name="set-security-policy"></a>Biztonsági házirend beállítása
A biztonsági szabályzat határozza meg a megadott előfizetésben vagy erőforráscsoporthoz lévő erőforrásokhoz ajánlott vezérlők készletét. Security Center a vállalata biztonsági igényeinek és az egyes előfizetésekben szereplő alkalmazások típusának vagy az adatmennyiségnek az alapján határozza meg a szabályzatokat az előfizetésekhez vagy az erőforrás-csoportokhoz.

Beállíthat egy szabályzatot az SQL audit és az SQL transzparens adattitkosítás (TDE) javaslatainak megjelenítéséhez.

* Ha bekapcsolja az **SQL-naplózást és a fenyegetések észlelését**, Security Center javasolja, hogy a megfelelőség, a speciális észlelés és a vizsgálat érdekében engedélyezze az Azure Database-hez való hozzáférés naplózását.
* Ha bekapcsolja az **SQL transzparens**adattitkosítást, Security Center javasolja, hogy engedélyezze a titkosítást a Azure SQL Database, a társított biztonsági másolatok és a tranzakciós naplófájlok számára.

Biztonsági házirend beállításához válassza ki a **szabályzat** csempét a Security Center panelen. A **biztonsági szabályzat** panelen válassza ki azt az előfizetést, amelyen engedélyezni szeretné a biztonsági házirendet. Válassza a **megelőzési szabályzat** lehetőséget, majd kapcsolja be az előfizetéshez használni kívánt biztonsági javaslatokat.
![Biztonsági szabályzat][3]

További információt a [biztonsági házirendek beállítása](tutorial-security-policy.md)című témakörben talál.

## <a name="manage-security-recommendation"></a>Biztonsági javaslat kezelése
A Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát. A Security Center javaslatokat hoz létre, amikor lehetséges biztonsági réseket észlel. A javaslatok végigvezetik Önt a szükséges vezérlők konfigurálásának folyamatán.

A biztonsági szabályzat beállítása után Security Center elemzi az erőforrások biztonsági állapotát a lehetséges sebezhetőségek azonosítása érdekében. A javaslatok táblázatos formátumban jelennek meg, ahol minden egyes sor egy adott javaslatot képvisel. A következő táblázat hivatkozásként szolgál a Azure SQL Database rendelkezésre álló javaslatainak megismeréséhez, és azt, hogy az egyes javaslatok hogyan alkalmazhatók. Egy javaslat kiválasztásával megtudhatja, hogyan valósítja meg a javaslatot a Security Centerban.

| Ajánlás | Leírás |
| --- | --- |
| [A naplózás és a veszélyforrások észlelésének engedélyezése az SQL-kiszolgálókon](security-center-enable-auditing-on-sql-servers.md) |Javasolja, hogy kapcsolja be SQL Database-kiszolgálók naplózását és a veszélyforrások észlelését. (Csak SQL Database szolgáltatás. Nem tartalmazza a virtuális gépeken futó Microsoft SQL Server.) |
| [A naplózás és a veszélyforrások észlelésének engedélyezése SQL-adatbázisokon](security-center-enable-auditing-on-sql-databases.md) |Javasolja, hogy kapcsolja be SQL Database adatbázisok naplózását és a fenyegetések észlelését. (Csak SQL Database szolgáltatás. Nem tartalmazza a virtuális gépeken futó Microsoft SQL Server.) |
| [Transzparens adattitkosítás engedélyezése](security-center-enable-transparent-data-encryption.md) |Javasolja, hogy engedélyezze az SQL-adatbázisok titkosítását. (Csak SQL Database szolgáltatás.) |

Ha szeretné megtekinteni az Azure-erőforrásokra vonatkozó javaslatokat, válassza a Security Center panel **javaslatok** csempéjét. A **javaslatok** panelen válasszon egy javaslatot a részletek megtekintéséhez. Ebben a példában válassza **a naplózás engedélyezése & veszélyforrások észlelése SQL**-kiszolgálókon lehetőséget.

![Javaslatok][4]

Ahogy az alább látható, Security Center megjeleníti azokat az SQL-kiszolgálókat, amelyeken nincs engedélyezve a naplózás és a veszélyforrások észlelése. A naplózás bekapcsolását követően beállíthatja a veszélyforrások észlelési beállításait és az e-mail-beállításokat a biztonsági riasztások fogadására. A fenyegetések észlelése riasztást küld, ha rendellenes adatbázis-tevékenységeket észlel, amelyek az adatbázis potenciális biztonsági fenyegetéseit jelzik. A riasztások a Security Center irányítópulton jelennek meg.
![Naplózás és fenyegetésészlelés][5]

A fenyegetések észlelésének bekapcsolásához és konfigurálásához, valamint a rendellenes tevékenységek észlelése után a biztonsági riasztásokat fogadó e-mailek listájának konfigurálásához kövesse a SQL Database veszélyforrások észlelésének lépései című [részt a Azure Portal](../sql-database/sql-database-threat-detection-portal.md) .

A javaslatokkal kapcsolatos további információkért lásd: [biztonsági javaslatok kezelése](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Biztonsági állapot figyelése
Ha bekapcsolja az előfizetéshez tartozó erőforrásokra vonatkozó [biztonsági szabályzatokat](tutorial-security-policy.md), a Security Center elvégzi az erőforrások biztonsági elemzését, és azonosítja a potenciális sebezhető pontokat.  Az erőforrások biztonsági állapotát az **erőforrás biztonsági** állapota csempén tekintheti meg. Amikor az **erőforrás biztonsági állapota** csempén az adatforrások elemre kattint, megnyílik az adaterőforrások panel az SQL-javaslatokkal olyan problémák esetén, mint a naplózás és az átlátható adattitkosítás. Ezenfelül általános javaslatokat is talál itt az adatbázis állapotára vonatkozóan.
![Erőforrás biztonsági állapota][6]

További információ: [biztonsági állapot figyelése](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Biztonsági riasztások kezelése és válaszlépések megtétele
A Security Center automatikusan gyűjti, elemzi és integrálja az [Azure SQL Threat Detection](../sql-database/sql-database-threat-detection.md)és más Azure-erőforrások naplózási adatait a valós fenyegetések észlelése és a téves pozitív állapotok csökkentése érdekében. A Security Centerben megtekinthető a rangsorolt biztonsági riasztások listája, ezenkívül a probléma gyors vizsgálatára vonatkozó információk és a támadás elhárításával kapcsolatos javaslatok is megjelennek.

A riasztások megtekintéséhez válassza a **biztonsági riasztások** csempét a Security Center panelen. A **biztonsági riasztások** panelen válassza ki a riasztást, ha többet szeretne megtudni a riasztást kiváltó eseményekről, valamint arról, hogy milyen lépéseket kell tennie a támadás kijavításához. Ebben a példában a **lehetséges SQL**-injektálást választjuk.
![Biztonsági riasztások][7]

Ahogy az alább látható, Security Center további részleteket tartalmaz, amelyek betekintést nyújtanak a riasztás indítására, a célként megadott erőforrásra, a forrás IP-címére vonatkozó ajánlásokra, valamint a szervizeléssel kapcsolatos javaslatokra.
![Lehetséges SQL-injektálás][8]

További információ: [a biztonsági riasztások kezelése és válaszadás](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>További lépések
* [Security Center GYIK](security-center-faq.md) – gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Security Center tervezési és üzemeltetési útmutató](security-center-planning-and-operations-guide.md) : kövessen lépéseket és feladatokat, amelyekkel optimalizálhatja a Security Center használatát a szervezet biztonsági követelményei és a felhőalapú felügyeleti modell alapján.
* [Security Center adatbiztonság](security-center-data-security.md) – megtudhatja, Security Center hogyan gyűjti össze és dolgozza fel az Azure-erőforrások adatait, beleértve a konfigurációs információkat, a metaadatokat, az eseménynaplókat, az összeomlási memóriaképeket és egyebeket.
* [Biztonsági incidensek kezelése](security-center-incident.md) – megtudhatja, hogyan használhatja a Security Center biztonsági riasztási funkcióját, hogy segítséget nyújtson a biztonsági incidensek kezeléséhez.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png

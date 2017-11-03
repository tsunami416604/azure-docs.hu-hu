---
title: "Az Azure Security Center és az Azure SQL Database szolgáltatás |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan Security Center segítségével hozzájárulhat az adatbázisokat az Azure SQL-adatbázis."
services: sql-database
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 46dd298a5664d914e55d45c5b7599d5983287476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Az Azure Security Center és az Azure SQL Database szolgáltatás
[Az Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) lehetővé teszi a fenyegetések megelőzését, észlelését és kezelését. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

Ez a cikk bemutatja, hogyan Security Center segítségével hozzájárulhat az adatbázisokat az Azure SQL-adatbázis.

## <a name="why-use-security-center"></a>Miért használja a Security Centert?
A Security Center segít az SQL-adatbázis adatok azáltal, hogy a kiszolgálók és adatbázisok biztonsági láthatósága. A Security Center a következőket teheti:

* SQL adatbázis-titkosítás és naplózási házirendek meghatározása.
* SQL adatbázis-erőforrások biztonságának figyelheti az előfizetések között.
* Gyorsan problémák azonosítása és javítása biztonsági.
* Riasztások integrálni [Azure SQL Database fenyegetésészlelés](../sql-database/sql-database-threat-detection.md).

Védelem az SQL-adatbázis-erőforrások, mellett a Security Center is biztosít a biztonsági ellenőrzése és kezelése az Azure virtuális gépek, felhőalapú szolgáltatásokhoz, alkalmazásszolgáltatások, virtuális hálózatok, és több. További tudnivalók a Security Center [Itt](security-center-intro.md).

## <a name="prerequisites"></a>Előfeltételek
A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ingyenes szint a Security Center engedélyezve van az előfizetéshez. A Security Center szabad és a szabványos rétegek további információkért lásd: [Security Center árképzési](https://azure.microsoft.com/pricing/details/security-center/).

Biztonsági központ szerepkörön alapuló hozzáférés támogatja. Azure szerepköralapú hozzáférés-vezérlés (RBAC) kapcsolatos további információkért lásd: [Azure Active Directory szerepköralapú hozzáférés-vezérlés](../active-directory/role-based-access-control-configure.md). A Security Center: GYIK információt nyújt a [engedélyek kezelésének módja a biztonsági központban](security-center-faq.md#permissions).

## <a name="access-security-center"></a>A Security Center elérése
A Security Center az [Azure Portalról](https://azure.microsoft.com/features/azure-portal/) érhető el. [Jelentkezzen be a portálra](https://portal.azure.com/) válassza ki a **Security Center beállítás**.

![A Security Center lehetőséget][1]

A **Security Center** panel nyílik meg.
![A Security Center panel][2]

## <a name="set-security-policy"></a>Biztonsági házirend beállítása
Egy biztonsági házirend határozza meg a megadott előfizetéshez vagy erőforráscsoporthoz erőforrások ajánljuk, hogy meg. A Security Center az előfizetések vagy az erőforráscsoport-sablonok alapján a vállalat biztonsági igényeinek megfelelően, és az alkalmazások típusának vagy az egyes előfizetések adatainak érzékenysége alapján szabályzatokat határozhatók meg.

Beállíthat egy házirendet az SQL-naplózás és az SQL transzparens adattitkosítás (TDE) javaslatok megjelenítése.

* Ha bekapcsolja a **SQL-naplózás és a fenyegetések észlelésére**, a Security Center javasolja megfelelőségi speciális észlelési és nyomozás érdekében, hogy az Azure Database-hozzáférés naplózásának engedélyezve.
* Ha bekapcsolja a **SQL transzparens adattitkosítás**, a Security Center javasolja, hogy a titkosítás aktívan engedélyezhető az Azure SQL Database, a társított biztonsági másolatok és a tranzakciós naplófájlok.

A biztonsági házirend beállításához jelölje be a **házirend** a Security Center panel csempét. Az a **biztonsági házirend** panelen válassza ki az előfizetést, amelyre a biztonsági házirend engedélyezni szeretné. Válassza ki **megakadályozási szabályzat** tehetik **a** a biztonsági javaslatok az előfizetésnél használni kívánt.
![Biztonsági szabályzat][3]

További tudnivalókért lásd: [biztonsági házirendek beállítása](security-center-policies.md).

## <a name="manage-security-recommendation"></a>Biztonsági ajánlás kezelése
A Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát. A Security Center javaslatokat hoz létre, amikor lehetséges biztonsági réseket észlel. A javaslatok végigvezetik Önt a szükséges vezérlők konfigurálásának folyamatán.

Miután beállította a biztonsági házirendet, a Security Center elemzi a potenciális biztonsági réseket azonosításához az erőforrások biztonsági állapotát. A javaslatok ahol mindegyik sor jelenti-e egy adott javaslat táblázatos formátumban jelennek meg. Referenciaként a következő táblázat segítségével megismerheti a rendelkezésre álló javaslatok az Azure SQL Database, és minden javaslat funkciója Ha alkalmazza azt. Az ajánlás kiválasztása viszi cikk azt ismerteti, hogyan megvalósítását a javaslat a Security Center.

| Ajánlás | Leírás |
| --- | --- |
| [SQL-kiszolgálón a naplózás és a fenyegetések észlelésére engedélyezése](security-center-enable-auditing-on-sql-servers.md) |Javasolja, hogy kapcsolja be az SQL adatbázis-kiszolgálók naplózás és a fenyegetések észlelésére. (Csak az SQL Database szolgáltatásban. Nem tartalmazza a Microsoft SQL Server, a virtuális gépeken futó.) |
| [A naplózás és a fenyegetések észlelésére az SQL-adatbázisok engedélyezése](security-center-enable-auditing-on-sql-databases.md) |Javasolja, hogy kapcsolja be az SQL Database-adatbázisok naplózásának és a fenyegetések észlelésére. (Csak az SQL Database szolgáltatásban. Nem tartalmazza a Microsoft SQL Server, a virtuális gépeken futó.) |
| [Transzparens adattitkosítás engedélyezése](security-center-enable-transparent-data-encryption.md) |Az SQL-adatbázisok titkosítási engedélyezését javasolja. (SQL Database szolgáltatás csak.) |

Javaslatok az Azure-erőforrások megtekintéséhez válasszon a **javaslatok** a Security Center panel csempét. Az a **javaslatok** panelen válassza ajánlás olyan környezetekben, hogy adatokat. Ebben a példában most válasszon **naplózás engedélyezését & Threat detection SQL-kiszolgálón**.

![Javaslatok][4]

Alább látható módon a Security Center jeleníti meg az SQL Server, ahol a naplózás és a fenyegetések észlelésére nem engedélyezettek. Naplózás bekapcsolása után beállíthatja a Fenyegetésészlelés beállítások és az e-mail-beállításait szeretné megkapni a biztonsági riasztásokat. A Fenyegetésészlelés figyelmezteti, ha azt észleli, hogy adatbázist érintő rendellenes tevékenységeket, amelyek jelzik, az adatbázis esetleges biztonsági fenyegetéseket jelezhetnek. A riasztásokat a Security Center irányítópultjának jelennek meg.
![Naplózás és a fenyegetések észlelése][5]

Kövesse a [SQL-adatbázis a fenyegetésészlelés az Azure portálon](../sql-database/sql-database-threat-detection-portal.md) bekapcsolásának és konfigurálásának fenyegetésészlelés, és konfigurálhatja az e-maileket, a rendellenes tevékenységek észlelésekor a biztonsági riasztásokat fogadó listáját.

Javaslatok kapcsolatos további információkért lásd: [biztonsági javaslatok kezelése](security-center-recommendations.md).

## <a name="monitor-security-health"></a>A biztonsági állapot figyelése
Ha bekapcsolja az előfizetéshez tartozó erőforrásokra vonatkozó [biztonsági szabályzatokat](security-center-policies.md), a Security Center elvégzi az erőforrások biztonsági elemzését, és azonosítja a potenciális sebezhető pontokat.  Az erőforrások biztonsági állapotát megtekintheti a **erőforrás biztonsági állapota** csempére. Amikor rákattint **adatok** a a **erőforrás biztonsági állapota** csempére, a **erőforrásokat** panel nyílik meg SQL javaslatok például naplózási és átlátható adattitkosítás nem engedélyezett. Ezenfelül általános javaslatokat is talál itt az adatbázis állapotára vonatkozóan.
![Erőforrás biztonsági állapota][6]

További tudnivalókért lásd: [biztonsági állapotfigyelés](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>A biztonsági riasztások kezelése és a riasztásokra való válaszadás
A Security Center automatikusan gyűjti, elemzi és integrálja naplóadatait [Azure SQL Fenyegetésészlelés](../sql-database/sql-database-threat-detection.md), valamint az egyéb Azure-erőforrások, a valós fenyegetések észlelése és a vakriasztások számának csökkentése érdekében. A Security Centerben megtekinthető a rangsorolt biztonsági riasztások listája, ezenkívül a probléma gyors vizsgálatára vonatkozó információk és a támadás elhárításával kapcsolatos javaslatok is megjelennek.

A riasztások megjelenítéséhez jelölje ki a **biztonsági riasztások** a Security Center panel csempét. Az a **biztonsági riasztások** panelen, válassza ki a kívánt riasztást a további tudnivalók az eseményeket kiváltó a riasztást, és milyen, ha vannak ilyenek, lépésről lépésre kell venni a támadás elhárítása érdekében. Ebben a példában most válasszon **lehetséges SQL-injektálás**.
![Biztonsági riasztások][7]

Alább látható módon a Security Center biztosít, amely áttekintést nyújtanak a riasztást, a cél erőforráson, ha lehetséges kiváltó okáról további részleteket a forrás IP-cím, és az elhárítására vonatkozó javaslatokat.
![Lehetséges SQL-injektálás][8]

További tudnivalókért lásd: [kezelése és a biztonsági riasztásokra való reagálásról](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Következő lépések
* [Security Center: GYIK](security-center-faq.md) – gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [A Security Center tervezésével és műveletek útmutató](security-center-planning-and-operations-guide.md) - kövesse az ismertetett lépések és feladatok optimalizálja a Security Center a szervezete biztonsági igényeinek és felhőfelügyeleti modelljének alapján.
* [Biztonsági központ adatbiztonság](security-center-data-security.md) – megtudhatja, hogyan gyűjti a Security Center, és feldolgozza az Azure-erőforrások, például konfigurációs adatokat, metaadatokat, eseménynaplók, összeomlási memóriaképek és egyéb adatait.
* [Biztonsági incidensek kezelése](security-center-incident.md) – a biztonsági riasztás funkció használata a Security Center segítséget biztonsági incidensek kezelése.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png

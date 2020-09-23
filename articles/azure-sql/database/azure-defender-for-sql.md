---
title: Azure Defender SQL-hez
description: Ismerje meg az adatbázis-biztonsági rések kezelésének funkcióit, valamint azokat a rendellenes tevékenységeket, amelyek fenyegetést jelenthetnek az adatbázisra Azure SQL Database, Azure SQL felügyelt példányban vagy Azure Szinapszisban.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
manager: rkarlin
author: memildin
ms.reviewer: vanto
ms.date: 09/21/2020
ms.openlocfilehash: b789cd423d2260ce709a02ffb3ac5ea500997609
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935752"
---
# <a name="azure-defender-for-sql"></a>Azure Defender SQL-hez
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


Az SQL-hez készült Azure Defender egységes csomag a fejlett SQL-biztonsági funkciókhoz. Az Azure Defender a Azure SQL Database, az Azure SQL felügyelt példányaihoz és az Azure szinapszis Analyticshez érhető el. Lehetőséget nyújt a bizalmas adatok felderítésére és titkossá minősítésére, az adatbázis biztonsági réseinek feltárására és kezelésére, továbbá az adatbázisra nézve fenyegetést jelentő rendellenes tevékenységek észlelésére. Segítségével egyetlen helyen engedélyezhetők és kezelhetők ezek a képességek.

## <a name="overview"></a>Áttekintés

Az Azure Defender fejlett SQL biztonsági képességeket biztosít, beleértve az SQL sebezhetőségi felmérést és az összetett veszélyforrások elleni védelmet.
- A [sebezhetőségi felmérés](sql-vulnerability-assessment.md) egy könnyen konfigurálható szolgáltatás, amely képes felderíteni, nyomon követni és segíteni a lehetséges adatbázis-sebezhetőségeket. A szolgáltatás láthatóságot biztosít a biztonsági állapotában, valamint a biztonsági problémák megoldásához és az adatbázis-erődítmények fejlesztéséhez szükséges műveleteket is tartalmaz.
- Az [Advanced Threat Protection](threat-detection-overview.md) észleli az adatbázisai hozzáférésére és az adatbázisai biztonságának megsértésére tett szokatlan és potenciálisan kártevő szándékú kísérleteket. Folyamatosan figyeli az adatbázist a gyanús tevékenységekhez, és azonnali biztonsági riasztásokat biztosít a potenciális sebezhetőségekről, az Azure SQL-injektálási támadásokról és a rendellenes adatbázis-hozzáférési mintákról. Az Advanced Threat Protection által adott riasztások tartalmazzák a gyanús tevékenység részleteit, és javaslatot tesznek a fenyegetés kivizsgálására és mérséklésére tett műveletekre.

Engedélyezze az Azure Defender for SQL szolgáltatást egyszer az összes elérhető funkció engedélyezéséhez. Egyetlen kattintással engedélyezheti az Azure Defendert az Azure [-ban vagy](logical-servers.md) az SQL-ben felügyelt példányon lévő összes adatbázishoz. Az Azure Defender-beállítások engedélyezéséhez vagy kezeléséhez az [SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) szerepkörhöz vagy az adatbázis vagy a kiszolgáló rendszergazdai szerepköreihez kell tartoznia.

Az Azure Defender for SQL díjszabásával kapcsolatos további információkért tekintse meg a [Azure Security Center díjszabási oldalát](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-azure-defender"></a>Ismerkedés az Azure Defenderrel

A következő lépésekkel kezdheti meg az Azure Defendert.

## <a name="1-enable-azure-defender"></a>1. az Azure Defender engedélyezése

Az Azure Defender a [Azure Portalon](https://portal.azure.com)keresztül érhető el. Engedélyezze az Azure Defender számára a kiszolgáló vagy a felügyelt példány **biztonsági** fejléce alatt navigáljon a **Security Centerben** .

> [!NOTE]
> A rendszer automatikusan létrehozza és konfigurálja a **biztonsági rések felmérésének** eredményeinek tárolására szolgáló Storage-fiókot. Ha már engedélyezte az Azure Defender használatát ugyanazon erőforráscsoport és régió egy másik kiszolgálóján, akkor a rendszer a meglévő Storage-fiókot használja.
>
> Az Azure Defender díja Azure Security Center standard szintű díjszabással van összhangban, ahol a csomópont a teljes kiszolgáló vagy a felügyelt példány. Így csak egyszer kell fizetnie a kiszolgálón található összes adatbázis vagy a felügyelt példány Azure Defender szolgáltatással való védelméhez. Először kipróbálhatja az Azure Defendert az ingyenes próbaverzióval.

## <a name="2-start-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. a sebezhetőségek követésének megkezdése és a fenyegetésekkel kapcsolatos riasztások vizsgálata

Kattintson a **sebezhetőségi felmérési** kártyára a sebezhetőségi vizsgálatok és jelentések megtekintéséhez és kezeléséhez, valamint a biztonsági Terme nyomon követéséhez. Ha a rendszer megkapta a biztonsági riasztásokat, kattintson a komplex **veszélyforrások elleni védelem** kártyára a riasztások részleteinek megtekintéséhez, valamint az Azure-előfizetésben lévő összes riasztásról a Azure Security Center biztonsági riasztások lapon tekintheti meg a konszolidált jelentést.

## <a name="3-manage-azure-defender-settings"></a>3. az Azure Defender beállításainak kezelése

Az Azure Defender beállításainak megtekintéséhez és kezeléséhez a kiszolgáló vagy a felügyelt példány **biztonsági** fejléce alatt navigáljon a **Security Center** elemhez. Ezen a lapon engedélyezheti vagy letilthatja az Azure Defender szolgáltatást, valamint módosíthatja a sebezhetőségi felmérést és a komplex veszélyforrások elleni védelem beállításait a teljes kiszolgálón vagy a felügyelt példányon.

## <a name="4-manage-azure-defender-settings-for-a-database"></a>4. az Azure Defender-beállítások kezelése egy adatbázishoz

Egy adott adatbázis Azure Defender-beállításainak felülbírálásához jelölje be az **Azure Defender engedélyezése az SQL-hez az adatbázis szintjén** jelölőnégyzetet. Ezt a lehetőséget csak akkor használja, ha külön követelmény, hogy különálló, komplex veszélyforrások elleni védelmi riasztásokat vagy Sebezhetőségi felmérési eredményeket kapjon az egyes adatbázisokra vonatkozóan, a kiszolgálón vagy a felügyelt példányon lévő összes adatbázisra vonatkozóan kapott riasztások és eredmények helyett.

Ha a jelölőnégyzet be van jelölve, megadhatja a megfelelő beállításokat ehhez az adatbázishoz.

Az Azure Defender a kiszolgáló vagy a felügyelt példány SQL-beállításaihoz is elérhető az Azure Defender-adatbázis paneljén. Kattintson a **Beállítások** elemre az Azure Defender fő ablaktábláján, majd kattintson az **Azure DEFENDER megtekintése az SQL Server-beállításokhoz**lehetőségre.

## <a name="next-steps"></a>Következő lépések

- További információ a [sebezhetőségi felmérésről](sql-vulnerability-assessment.md)
- További információ a komplex [veszélyforrások elleni védelemről](threat-detection-configure.md)
- További információ a [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

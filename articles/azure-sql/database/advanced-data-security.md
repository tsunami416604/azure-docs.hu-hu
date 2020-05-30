---
title: Advanced Data Security
description: Ismerkedjen meg a bizalmas adatok felderítésével és besorolásával, az adatbázis biztonsági réseinak kezelésével, valamint a rendellenes tevékenységek észlelésével, amelyek fenyegetést jelenthetnek az adatbázisra Azure SQL Database, az Azure SQL felügyelt példányain vagy az Azure szinapszis szolgáltatásban.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
ms.author: memildin
author: memildin
manager: rkarlin
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: d600d174aa37c5c4d5d1011b9cb61e4487256c13
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84195131"
---
# <a name="advanced-data-security"></a>Advanced Data Security
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]


A speciális adatbiztonság (ADS) a fejlett SQL-alapú biztonsági képességek egységes csomagja. A hirdetések Azure SQL Database, az Azure SQL felügyelt példányaihoz és az Azure szinapszis Analyticshez érhetők el. Lehetőséget nyújt a bizalmas adatok felderítésére és titkossá minősítésére, az adatbázis biztonsági réseinek feltárására és kezelésére, továbbá az adatbázisra nézve fenyegetést jelentő rendellenes tevékenységek észlelésére. Segítségével egyetlen helyen engedélyezhetők és kezelhetők ezek a képességek.

## <a name="overview"></a>Áttekintés

A hirdetések fejlett SQL biztonsági képességeket biztosítanak, beleértve az adatfelderítési & besorolást, az SQL sebezhetőségi felmérést és az összetett veszélyforrások elleni védelmet.
- Az [Adatfelderítési & besorolása](data-discovery-and-classification-overview.md) a Azure SQL Database, az Azure SQL felügyelt példánya és az Azure szinapszis szolgáltatásba épített képességeket biztosít az adatbázisaiban található bizalmas adatok feltárásához, osztályozásához, címkézéséhez és jelentéséhez. Használatával áttekinthető az adatbázis besorolási állapota, valamint követhető a bizalmas adatokhoz való hozzáférés az adatbázison belül és azon kívül.
- A [sebezhetőségi felmérés](sql-vulnerability-assessment.md) egy könnyen konfigurálható szolgáltatás, amely képes felderíteni, nyomon követni és segíteni a lehetséges adatbázis-sebezhetőségeket. A szolgáltatás láthatóságot biztosít a biztonsági állapotában, valamint a biztonsági problémák megoldásához és az adatbázis-erődítmények fejlesztéséhez szükséges műveleteket is tartalmaz.
- Az [Advanced Threat Protection](threat-detection-overview.md) észleli az adatbázisai hozzáférésére és az adatbázisai biztonságának megsértésére tett szokatlan és potenciálisan kártevő szándékú kísérleteket. Folyamatosan figyeli az adatbázist a gyanús tevékenységekhez, és azonnali biztonsági riasztásokat biztosít a potenciális sebezhetőségekről, az Azure SQL-injektálási támadásokról és a rendellenes adatbázis-hozzáférési mintákról. Az Advanced Threat Protection által adott riasztások tartalmazzák a gyanús tevékenység részleteit, és javaslatot tesznek a fenyegetés kivizsgálására és mérséklésére tett műveletekre.

Engedélyezze a speciális adatbiztonságot az összes ilyen funkció engedélyezéséhez. Egyetlen kattintással engedélyezheti a [kiszolgáló](logical-servers.md) összes adatbázisára vonatkozó hirdetéseket az Azure-ban vagy az SQL felügyelt példányában. A hirdetési beállítások engedélyezéséhez vagy kezeléséhez az [SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) szerepkörhöz vagy az adatbázis vagy a kiszolgáló rendszergazdai szerepköreihez kell tartoznia.

A ADS díjszabása a Azure Security Center Standard csomaggal igazodik, ahol minden egyes védett kiszolgáló vagy felügyelt példány egyetlen csomópontnak számít. Az újonnan védett erőforrások Security Center standard szintű ingyenes próbaidőszakra érvényesek. További információkért tekintse meg a [Azure Security Center díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Ismerkedés a hirdetések használatába

A következő lépésekkel kezdheti meg a HIRDETÉSEKET.

## <a name="1-enable-ads"></a>1. hirdetések engedélyezése

Engedélyezze a HIRDETÉSEKET úgy, hogy a kiszolgáló vagy a felügyelt példány **biztonsági** fejlécében a **speciális adatbiztonság** elemre navigál.

> [!NOTE]
> A rendszer automatikusan létrehozza és konfigurálja a **biztonsági rések felmérésének** eredményeinek tárolására szolgáló Storage-fiókot. Ha már engedélyezte a HIRDETÉSEKET egy másik kiszolgálón ugyanabban az erőforráscsoporthoz és régióban, akkor a rendszer a meglévő Storage-fiókot használja.

![HIRDETÉSEK engedélyezése](./media/advanced-data-security/enable_ads.png)

> [!NOTE]
> A hirdetések díja Azure Security Center standard szintű díjszabáshoz igazodik, ahol a csomópont a teljes kiszolgáló vagy a felügyelt példány. Így csak egyszer kell fizetnie a kiszolgálón található összes adatbázis vagy a felügyelt példány HIRDETÉSekkel való védelméhez. Először kipróbálhatja a HIRDETÉSEKET egy ingyenes próbaverzióval.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. az adatosztályozás megkezdése, a sebezhetőségek nyomon követése és a fenyegetésekkel kapcsolatos riasztások kivizsgálása

Az **Adatfelderítési & besorolási** kártyára kattintva megtekintheti az ajánlott bizalmas oszlopokat az osztályozáshoz, és az adatok besorolását az állandó érzékenységi címkékkel. Kattintson a **sebezhetőségi felmérési** kártyára a sebezhetőségi vizsgálatok és jelentések megtekintéséhez és kezeléséhez, valamint a biztonsági Terme nyomon követéséhez. Ha a rendszer megkapta a biztonsági riasztásokat, kattintson a komplex **veszélyforrások elleni védelem** kártyára a riasztások részleteinek megtekintéséhez, valamint az Azure-előfizetésben lévő összes riasztásról a Azure Security Center biztonsági riasztások lapon tekintheti meg a konszolidált jelentést.

## <a name="3-manage-ads-settings"></a>3. a ADS beállításainak kezelése

A hirdetések beállításainak megtekintéséhez és kezeléséhez navigáljon a **speciális adatbiztonság** elemre a kiszolgáló vagy a felügyelt példány **biztonsági** fejlécében. Ezen a lapon engedélyezheti vagy letilthatja a HIRDETÉSEKET, valamint módosíthatja a sebezhetőségi felmérést és a komplex veszélyforrások elleni védelem beállításait a teljes kiszolgálón vagy a felügyelt példányon.

![Kiszolgáló beállításai](./media/advanced-data-security/server_settings.png)

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. az SQL-adatbázisok HIRDETÉSEI beállításainak kezelése

Egy adott adatbázis HIRDETÉSEI beállításainak felülbírálásához jelölje be a **speciális adatbiztonság engedélyezése az adatbázis szintjén** jelölőnégyzetet. Ezt a lehetőséget csak akkor használja, ha külön követelmény, hogy különálló, komplex veszélyforrások elleni védelmi riasztásokat vagy Sebezhetőségi felmérési eredményeket kapjon az egyes adatbázisokra vonatkozóan, a kiszolgálón vagy a felügyelt példányon lévő összes adatbázisra vonatkozóan kapott riasztások és eredmények helyett.

Ha a jelölőnégyzet be van jelölve, megadhatja a megfelelő beállításokat ehhez az adatbázishoz.

![Adatbázis és komplex veszélyforrások elleni védelem beállításai](./media/advanced-data-security/database_threat_detection_settings.png)

A kiszolgáló vagy a felügyelt példány speciális biztonsági beállításai a hirdetések adatbázis paneljéről is elérhetők. A fő hirdetések ablaktáblán kattintson a **Beállítások** elemre, majd kattintson a **speciális adatbiztonsági kiszolgáló beállításainak megtekintése**elemre.

![Adatbázis-beállítások](./media/advanced-data-security/database_settings.png)

## <a name="next-steps"></a>További lépések

- További tudnivalók az [Adatfelderítési & besorolásáról](data-discovery-and-classification-overview.md)
- További információ a [sebezhetőségi felmérésről](sql-vulnerability-assessment.md)
- További információ a komplex [veszélyforrások elleni védelemről](threat-detection-configure.md)
- További információ a [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

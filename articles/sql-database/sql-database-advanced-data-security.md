---
title: Advanced Data Security
description: Ismerje meg a bizalmas adatok felderítésének és besorolásának funkcióit, az adatbázis-sebezhetőségek kezelését, valamint az Azure SQL Database fenyegetését jelző rendellenes tevékenységek észlelését.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
ms.author: memildin
author: memildin
manager: rkarlin
ms.reviewer: vanto
ms.date: 03/31/2019
ms.openlocfilehash: aed0bcb79dedf057c5943cea9f4b4399b2f630cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80677471"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Speciális adatbiztonság a Azure SQL Database

A speciális adatbiztonság a speciális SQL-alapú biztonsági képességek egységes csomagja. Lehetőséget nyújt a bizalmas adatok felderítésére és titkossá minősítésére, az adatbázis biztonsági réseinek feltárására és kezelésére, továbbá az adatbázisra nézve fenyegetést jelentő rendellenes tevékenységek észlelésére. Segítségével egyetlen helyen engedélyezhetők és kezelhetők ezek a képességek.

## <a name="overview"></a>Áttekintés

A speciális adatbiztonság (ADS) fejlett SQL biztonsági képességeket biztosít, beleértve az adatfelderítési & besorolást, a sebezhetőségi felmérést és az összetett veszélyforrások elleni védelmet.

- Az [Adatfelderítési & besorolása](sql-database-data-discovery-and-classification.md) Azure SQL Database számára beépített képességeket biztosít az adatbázisaiban található bizalmas adatok bejelentésének felderítéséhez, besorolásához, címkézéséhez &. Használatával áttekinthető az adatbázis besorolási állapota, valamint követhető a bizalmas adatokhoz való hozzáférés az adatbázison belül és azon kívül.
- A [sebezhetőségi felmérés](sql-vulnerability-assessment.md) egy könnyen konfigurálható szolgáltatás, amely képes felderíteni és követni az adatbázisok lehetséges biztonsági réseit, és segít orvosolni azokat. Áttekinthetővé teszi az adatbázisok biztonsági állapotát, és végrehajtható lépéseket kínál a biztonsági problémák megoldására, valamint az adatbázisok védelmének fejlesztésére.
- Az [Advanced Threat Protection](sql-database-threat-detection-overview.md) észleli az adatbázisai hozzáférésére és az adatbázisai biztonságának megsértésére tett szokatlan és potenciálisan kártevő szándékú kísérleteket. A szolgáltatás folyamatosan figyeli az adatbázisokat, és azonnal értesíti a felhasználót a gyanús tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálásos támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. Az Advanced Threat Protection által adott riasztások tartalmazzák a gyanús tevékenység részleteit, és javaslatot tesznek a fenyegetés kivizsgálására és mérséklésére tett műveletekre.

Engedélyezze az SQL-HIRDETÉSEKET egyszer az összes ilyen funkció engedélyezéséhez. Egyetlen kattintással engedélyezheti a HIRDETÉSEKET a SQL Database-kiszolgálón vagy a felügyelt példányon található összes adatbázishoz. A hirdetési beállítások engedélyezéséhez vagy kezeléséhez az SQL [Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) szerepkörhöz, az SQL Database-rendszergazdai szerepkörhöz vagy az SQL Server rendszergazdai szerepkörhöz kell tartoznia. 

A ADS díjszabása a Azure Security Center Standard szinttel igazodik, ahol minden egyes védett SQL Database-kiszolgáló vagy felügyelt példány egyetlen csomópontnak számít. Az újonnan védett erőforrások Security Center standard szintű ingyenes próbaidőszakra érvényesek. További információkért tekintse meg a [Azure Security Center díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Első lépések a HIRDETÉSekkel

A következő lépésekkel kezdheti meg a HIRDETÉSEKET.

## <a name="1-enable-ads"></a>1. hirdetések engedélyezése

A HIRDETÉSEKET a SQL Database-kiszolgáló vagy a felügyelt példány **biztonsági** fejléce alatt, a **speciális adat biztonsága** területen navigálva engedélyezheti. Ha az adatbázis-kiszolgálón vagy a felügyelt példányon lévő összes adatbázishoz szeretne HIRDETÉSEKET engedélyezni, kattintson **a speciális adatbiztonság engedélyezése a kiszolgálón**lehetőségre.

> [!NOTE]
> A rendszer automatikusan létrehozza és konfigurálja a **biztonsági rések felmérésének** eredményeinek tárolására szolgáló Storage-fiókot. Ha már engedélyezte a HIRDETÉSEKET egy másik kiszolgálón ugyanabban az erőforráscsoporthoz és régióban, akkor a rendszer a meglévő Storage-fiókot használja.

![HIRDETÉSEK engedélyezése](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> A hirdetések díja Azure Security Center standard szintű díjszabáshoz igazodik, ahol a csomópont a teljes SQL Database-kiszolgáló vagy a felügyelt példány. Így csak egyszer kell fizetnie, ha az adatbázis-kiszolgálón vagy a felügyelt példányon lévő összes adatbázist a HIRDETÉSEKET szeretné védeni. Először kipróbálhatja a HIRDETÉSEKET egy ingyenes próbaverzióval.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. az adatosztályozás megkezdése, a sebezhetőségek nyomon követése és a fenyegetésekkel kapcsolatos riasztások kivizsgálása

Az **Adatfelderítési & besorolási** kártyára kattintva megtekintheti az ajánlott bizalmas oszlopokat az osztályozáshoz, és az adatok besorolását az állandó érzékenységi címkékkel. Kattintson a **sebezhetőségi felmérési** kártyára a sebezhetőségi vizsgálatok és jelentések megtekintéséhez és kezeléséhez, valamint a biztonsági Terme nyomon követéséhez. Ha a rendszer megkapta a biztonsági riasztásokat, kattintson a komplex **veszélyforrások elleni védelem** kártyára a riasztások részleteinek megtekintéséhez, valamint az Azure-előfizetésben lévő összes riasztásról a Azure Security Center biztonsági riasztások lapon tekintheti meg a konszolidált jelentést.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. a hirdetési beállítások kezelése a SQL Database-kiszolgálón vagy a felügyelt példányon

A hirdetési beállítások megtekintéséhez és kezeléséhez navigáljon a **speciális adatbiztonság** elemre a SQL Database-kiszolgáló vagy a felügyelt példány **biztonsági** fejlécében. Ezen a lapon engedélyezheti vagy letilthatja a HIRDETÉSEKET, valamint módosíthatja a sebezhetőségi felmérést és a komplex veszélyforrások elleni védelem beállításait a teljes SQL Database-kiszolgálón vagy a felügyelt példányon.

![Kiszolgáló beállításai](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. az SQL-adatbázisok HIRDETÉSEI beállításainak kezelése

Egy adott adatbázis HIRDETÉSEI beállításainak felülbírálásához jelölje be a **speciális adatbiztonság engedélyezése az adatbázis szintjén** jelölőnégyzetet. Ezt a lehetőséget csak akkor használja, ha külön követelmény, hogy különálló, komplex veszélyforrások elleni védelmi riasztásokat vagy Sebezhetőségi felmérési eredményeket kapjon az egyes adatbázisokhoz, az adatbázis-kiszolgálón vagy a felügyelt példányon található összes adatbázishoz kapott riasztások és eredmények helyett.

Ha a jelölőnégyzet be van jelölve, megadhatja a megfelelő beállításokat ehhez az adatbázishoz.
 
![Adatbázis és komplex veszélyforrások elleni védelem beállításai](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Az adatbázis-kiszolgáló vagy a felügyelt példány speciális biztonsági beállításai a ADS-adatbázis ablaktábláról is elérhetők. A fő hirdetések ablaktáblán kattintson a **Beállítások** elemre, majd kattintson a **speciális adatbiztonsági kiszolgáló beállításainak megtekintése**elemre. 

![Adatbázis-beállítások](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>További lépések 

- További tudnivalók az [Adatfelderítési & besorolásáról](sql-database-data-discovery-and-classification.md) 
- További információ a [sebezhetőségi felmérésről](sql-vulnerability-assessment.md) 
- További információ a komplex [veszélyforrások elleni védelemről](sql-database-threat-detection.md)
- További információ a [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

---
title: Speciális adatbiztonság – Azure SQL Database | Microsoft Docs
description: Ismerje meg a bizalmas adatok felderítésének és besorolásának funkcióit, az adatbázis-sebezhetőségek kezelését, valamint az Azure SQL Database fenyegetését jelző rendellenes tevékenységek észlelését.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: v-mohabe
ms.reviewer: vanto
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: ce02fee31041222c48d62ef8410b97fedf74dfdb
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297674"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Speciális adatbiztonság a Azure SQL Database

A speciális adatbiztonság a speciális SQL-alapú biztonsági képességek egységes csomagja. A szolgáltatás magában foglalja a bizalmas adatok felderítésére és besorolására, a felszínre és a lehetséges adatbázis-sebezhetőségek enyhítésére szolgáló funkciókat, valamint a rendellenes tevékenységek észlelését, amelyek fenyegetést jelenthetnek az adatbázisra. Segítségével egyetlen helyen engedélyezhetők és kezelhetők ezek a képességek.

## <a name="overview"></a>Áttekintés

A speciális adatbiztonság (ADS) fejlett SQL biztonsági képességeket biztosít, beleértve az adatfelderítési & besorolást, a sebezhetőségi felmérést és az összetett veszélyforrások elleni védelmet.

- [Adatfelderítési & besorolása](sql-database-data-discovery-and-classification.md) (jelenleg előzetes verzióban érhető el) olyan képességeket biztosít, amelyek Azure SQL Databaseba vannak építve, hogy felfedezzék, osztályozzák és címkézsék & a bizalmas adatok védelmét az adatbázisokban. Használatával áttekinthető az adatbázis besorolási állapota, valamint követhető a bizalmas adatokhoz való hozzáférés az adatbázison belül és azon kívül.
- A [sebezhetőségi felmérés](sql-vulnerability-assessment.md) egy könnyen konfigurálható szolgáltatás, amely felderítheti, nyomon követheti és javíthatja a lehetséges adatbázis-réseket. Áttekinthetővé teszi az adatbázisok biztonsági állapotát, és végrehajtható lépéseket kínál a biztonsági problémák megoldására, valamint az adatbázisok védelmének fejlesztésére.
- A komplex [veszélyforrások elleni védelem](sql-database-threat-detection-overview.md) olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázis eléréséhez vagy kiaknázásához. A szolgáltatás folyamatosan figyeli az adatbázisokat, és azonnal értesíti a felhasználót a gyanús tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálásos támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. A komplex veszélyforrások elleni védelemre vonatkozó riasztások a gyanús tevékenységek részleteit tartalmazzák, és a fenyegetés kivizsgálásával és enyhítésével kapcsolatos teendőket javasolnak.

Engedélyezze az SQL-HIRDETÉSEKET egyszer az összes ilyen funkció engedélyezéséhez. Egyetlen kattintással engedélyezheti a HIRDETÉSEKET a SQL Database-kiszolgálón vagy a felügyelt példányon található összes adatbázishoz. A hirdetési beállítások engedélyezéséhez vagy kezeléséhez az SQL [Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) szerepkörhöz, az SQL Database-rendszergazdai szerepkörhöz vagy az SQL Server rendszergazdai szerepkörhöz kell tartoznia. 

A ADS díjszabása a Azure Security Center Standard szinttel igazodik, ahol minden egyes védett SQL Database-kiszolgáló vagy felügyelt példány egyetlen csomópontnak számít. Az újonnan védett erőforrások Security Center standard szintű ingyenes próbaidőszakra érvényesek. További információkért tekintse meg a [Azure Security Center díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Első lépések a HIRDETÉSekkel

A következő lépésekkel kezdheti meg a HIRDETÉSEKET.

## <a name="1-enable-ads"></a>1. HIRDETÉSEK engedélyezése

Engedélyezze a HIRDETÉSEKET úgy, hogy a SQL Database-kiszolgáló vagy az összetett példány **biztonsági** fejléce alatt navigáljon a **speciális adatbiztonság** elemre. Ha az adatbázis-kiszolgálón vagy a felügyelt példányon lévő összes adatbázishoz szeretne HIRDETÉSEKET engedélyezni, kattintson **a speciális adatbiztonság engedélyezése a kiszolgálón**lehetőségre.

> [!NOTE]
> A rendszer automatikusan létrehozza és konfigurálja a **biztonsági rések felmérésének** eredményeinek tárolására szolgáló Storage-fiókot. Ha már engedélyezte a HIRDETÉSEKET egy másik kiszolgálón ugyanabban az erőforráscsoporthoz és régióban, akkor a rendszer a meglévő Storage-fiókot használja.

![HIRDETÉSEK engedélyezése](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> A hirdetések díja Azure Security Center standard szintű díjszabáshoz igazodik, ahol a csomópont a teljes SQL Database-kiszolgáló vagy a felügyelt példány. Így csak egyszer kell fizetnie, ha az adatbázis-kiszolgálón vagy a felügyelt példányon lévő összes adatbázist a HIRDETÉSEKET szeretné védeni. Először kipróbálhatja a HIRDETÉSEKET egy ingyenes próbaverzióval.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Az adatbesorolás megkezdése, a sebezhetőségek nyomon követése és a fenyegetésekkel kapcsolatos riasztások kivizsgálása

Az adatfelderítési **& besorolási** kártyára kattintva megtekintheti az ajánlott bizalmas oszlopokat az osztályozáshoz, és az adatok besorolását az állandó érzékenységi címkékkel. Kattintson a **sebezhetőségi felmérési** kártyára a sebezhetőségi vizsgálatok és jelentések megtekintéséhez és kezeléséhez, valamint a biztonsági Terme nyomon követéséhez. Ha a rendszer megkapta a biztonsági riasztásokat, kattintson a komplex **veszélyforrások elleni védelem** kártyára a riasztások részleteinek megtekintéséhez, valamint az Azure-előfizetésben lévő összes riasztásról a Azure Security Center biztonsági riasztások lapon tekintheti meg a konszolidált jelentést.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. A hirdetési beállítások kezelése a SQL Database-kiszolgálón vagy a felügyelt példányon

A hirdetési beállítások megtekintéséhez és kezeléséhez navigáljon a **speciális adatbiztonság** elemre a SQL Database-kiszolgáló vagy a felügyelt példány **biztonsági** fejlécében. Ezen a lapon engedélyezheti vagy letilthatja a HIRDETÉSEKET, valamint módosíthatja a sebezhetőségi felmérést és a komplex veszélyforrások elleni védelem beállításait a teljes SQL Database-kiszolgálón vagy a felügyelt példányon.

![kiszolgáló beállításai](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. Az SQL Database-adatbázisok HIRDETÉSEI beállításainak kezelése

Egy adott adatbázis HIRDETÉSEI beállításainak felülbírálásához jelölje be a **speciális adatbiztonság engedélyezése az adatbázis szintjén** jelölőnégyzetet. Ezt a lehetőséget csak akkor használja, ha külön követelmény, hogy különálló, komplex veszélyforrások elleni védelmi riasztásokat vagy Sebezhetőségi felmérési eredményeket kapjon az egyes adatbázisokhoz, a riasztások és a kapott eredmények mellett, a adatbázis-kiszolgáló vagy felügyelt példány.

Ha a jelölőnégyzet be van jelölve, megadhatja a megfelelő beállításokat ehhez az adatbázishoz.
 
![Adatbázis és komplex veszélyforrások elleni védelem beállításai](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Az adatbázis-kiszolgáló vagy a felügyelt példány speciális biztonsági beállításai a ADS-adatbázis ablaktábláról is elérhetők. A fő hirdetések ablaktáblán kattintson a **Beállítások** elemre, majd kattintson a **speciális adatbiztonsági kiszolgáló beállításainak megtekintése**elemre. 

![Adatbázis-beállítások](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>További lépések 

- További tudnivalók az [adatfelderítési & besorolásáról](sql-database-data-discovery-and-classification.md) 
- További információ a [sebezhetőségi felmérésről](sql-vulnerability-assessment.md) 
- További információ a komplex [veszélyforrások elleni védelemről](sql-database-threat-detection.md)
- További információ az [Azure Security Centerről](https://docs.microsoft.com/azure/security-center/security-center-intro)

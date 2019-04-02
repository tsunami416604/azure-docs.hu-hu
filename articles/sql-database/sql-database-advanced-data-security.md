---
title: Speciális adatbiztonság – az Azure SQL Database |} A Microsoft Docs
description: Ismerje meg a felderítése és besorolása a bizalmas adatokat, az adatbázis biztonsági rések kezelése és a rendellenes tevékenységek jelezheti az Azure SQL Database fenyegetések észlelése funkciót.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: monhaber
ms.reviewer: vanto
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: a078ac38cef5b395a19481188c474c7f908160d5
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58791609"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Az Azure SQL Database speciális Adatbiztonság

Speciális biztonsági egységes csomag egy tapasztalt SQL biztonsági funkciók. Ez magában foglalja a funkciók felderítése és besorolása a bizalmas adatok, felszínre hozza a és enyhítő adatbázis biztonsági réseinek, és az adatbázis fenyegetést jelezhet rendellenes tevékenységeket észleli. Segítségével egyetlen helyen engedélyezhetők és kezelhetők ezek a képességek.

## <a name="overview"></a>Áttekintés

Speciális biztonsági (ADS) biztosít egy speciális SQL biztonsági funkciókat, többek között az adatfelderítés és a besorolás, a sebezhetőségi felmérés és a komplex veszélyforrások elleni védelem.

- [Adatfelderítés és besorolás](sql-database-data-discovery-and-classification.md) (jelenleg előzetes verzió) biztosítja a felderítése, a besorolása, a címkézés és a az adatbázisokban a bizalmas adatok védelme az Azure SQL Database beépített funkciói. Használatával áttekinthető az adatbázis besorolási állapota, valamint követhető a bizalmas adatokhoz való hozzáférés az adatbázison belül és azon kívül.
- [A biztonságirés-értékelési](sql-vulnerability-assessment.md) egy könnyen konfigurálható szolgáltatás, amely a felderítése, nyomon követheti, és segít javítani az adatbázis biztonsági réseinek. Áttekinthetővé teszi az adatbázisok biztonsági állapotát, és végrehajtható lépéseket kínál a biztonsági problémák megoldására, valamint az adatbázisok védelmének fejlesztésére.
- [Komplex veszélyforrások elleni védelem](sql-database-threat-detection-overview.md) elérni vagy kiaknázni az adatbázis szokatlan és vélhetően kárt okozó kísérleteket jelző rendellenes tevékenységek észleli. A szolgáltatás folyamatosan figyeli az adatbázisokat, és azonnal értesíti a felhasználót a gyanús tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálásos támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. Komplex veszélyforrások elleni védelem riasztásokat a gyanús tevékenység részleteit, adja meg, és tegyen javaslatot a műveletre vizsgálata, valamint a fenyegetés.

Engedélyezze a SQL ADS után ezek mindegyikét engedélyezése foglalt szolgáltatásait. Egyetlen kattintással engedélyezheti az SQL Database-kiszolgálón az összes adatbázis ADS vagy a felügyelt példány. Engedélyezi, vagy ADS beállításainak kezelése van szüksége, tartozó a [SQL biztonságkezelő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) szerepkör, az SQL adatbázis-rendszergazdai szerepkör vagy SQL server rendszergazdai szerepkör. 

Díjszabás ADS igazítja az Azure Security Center standard szintű, ahol minden egyes védett SQL Database-kiszolgálóhoz vagy a felügyelt példány egyetlen csomópontnak számítanak. Újonnan védett erőforrások jogosultak a Security Center standard szintű az ingyenes próbaverziójára. További információkért lásd: a [az Azure Security Center díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Ismerkedés a HIRDETÉSEK

Az alábbi lépéseket az első lépések a HIRDETÉSEK.

## <a name="1-enable-ads"></a>1. HIRDETÉSEK engedélyezése

Az ADS engedélyezése **adatok biztonságú** alatt a **biztonsági** az SQL Database-kiszolgálóhoz vagy manged példány fejléc. HIRDETÉSEK engedélyezése az összes adatbázis az adatbázis-kiszolgáló vagy a felügyelt példány, kattintson a **engedélyezése speciális adatokat a kiszolgáló biztonsági**.

> [!NOTE]
> Storage-fiók az automatikusan létrehozott és tárolják a **biztonságirés-értékelési** a keresési eredményeket. Ha már engedélyezte ADS egy másik kiszolgálóra ugyanabban az erőforráscsoportban és régióban, a meglévő storage-fiók szolgál.

![HIRDETÉSEK engedélyezése](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> HIRDETÉSEK költségét igazodik az Azure Security Center standard szintű díjszabás csomópontonként, ahol egy csomópont a teljes SQL-adatbázis-kiszolgáló vagy a felügyelt példány. Így licencdíjat csak egyszer minden adatbázis az adatbázis-kiszolgáló vagy a felügyelt példány az ADS védelme érdekében. Kipróbálhatja ADS kezdetben az ingyenes próbaverziót.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Adatok besorolása, nyomon követése a biztonsági rések és fenyegetések a riasztások kivizsgálásának indítása

Kattintson a **Adatfelderítés és besorolás** megtekintéséhez bizalmas oszlopok osztályozására és állandó érzékenységi címkét az adatok osztályozására szolgáló ajánlott. Kattintson a **biztonságirés-értékelési** kártya vizsgálatot a biztonsági rések és jelentések megtekintése és kezelése, és a biztonsági helyzetében nyomon követéséhez. Ha a biztonsági riasztások beérkezett, kattintson a **komplex veszélyforrások elleni védelem** kártya megtekintéséhez a riasztások és a egy konszolidált jelentésének megtekintéséhez az Azure-előfizetésben az Azure Security Center biztonsági riasztások lapján minden riasztásról részletei .

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. HIRDETÉSEK beállításainak ellenőrzése az SQL Database-kiszolgálón vagy a felügyelt példány

Beállításainak megtekintése és kezelése HIRDETÉSEK, navigáljon a **adatok biztonságú** alatt a **biztonsági** az SQL Database-kiszolgálóhoz vagy a felügyelt példány fejléc. Ezen a lapon engedélyezése vagy ADS letilthatja és módosíthatja a sebezhetőségi felmérés és a teljes SQL Database-kiszolgálóhoz vagy a felügyelt példány komplex veszélyforrások elleni védelem beállításait.

![Kiszolgáló beállításai](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. SQL-adatbázis ADS beállításainak kezelése

Egy adott adatbázis ADS beállításainak felülbírálása, ellenőrizze a **engedélyezése adatok biztonságú az adatbázis szintjén** jelölőnégyzetet. Használja ezt a beállítást csak akkor, ha egy követelmény, amely külön komplex veszélyforrások elleni védelem riasztásokat vagy a sebezhetőségi felmérés eredmények helyére vagy a riasztások és az összes adatbázis kapott eredmények mellett az egyes adatbázis rendelkezik a az adatbázis-kiszolgáló vagy a felügyelt példány.

Ha a jelölőnégyzet be van jelölve, majd konfigurálhatja a vonatkozó beállítások az adatbázis.
 
![Adatbázis és a komplex veszélyforrások elleni védelem beállításai](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Az adatbázis-kiszolgáló vagy a felügyelt példány speciális biztonsági beállításai is a HIRDETÉSEK adatbázis panelen érhető el. Kattintson a **beállítások** az ADS fő ablaktáblán, majd kattintson a **kiszolgálóbeállítások megtekintése biztonságú adatok**. 

![Adatbázis-beállítások](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>További lépések 

- Tudjon meg többet [adatfelderítés és besorolás](sql-database-data-discovery-and-classification.md) 
- Tudjon meg többet [sebezhetőségi felmérés](sql-vulnerability-assessment.md) 
- Tudjon meg többet [komplex veszélyforrások elleni védelem](sql-database-threat-detection.md)
- Tudjon meg többet [az Azure security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)

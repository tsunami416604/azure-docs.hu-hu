---
title: SQL Advanced adatbiztonság – az Azure SQL Database |} A Microsoft Docs
description: Ismerje meg a felderítése és besorolása a bizalmas adatokat, az adatbázis biztonsági rések kezelése és a rendellenes tevékenységek jelezheti az Azure SQL Database fenyegetések észlelése funkciót.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 1/29/2019
ms.openlocfilehash: ae710d705ed843d59af0d0f9154aab467f01c4dd
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468461"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Speciális biztonsági az Azure SQL Database-hez

Az SQL Data biztonságú egységes csomag egy speciális SQL biztonsági funkciók. Ez magában foglalja a funkciók felderítése és besorolása a bizalmas adatok, felszínre hozza a és enyhítő adatbázis biztonsági réseinek, és az adatbázis fenyegetést jelezhet rendellenes tevékenységeket észleli. Segítségével egyetlen helyen engedélyezhetők és kezelhetők ezek a képességek.

## <a name="overview"></a>Áttekintés

SQL speciális adatok biztonsági (ADS) egy speciális SQL biztonsági funkciókat, többek között az Adatfelderítés és a besorolás, a sebezhetőségi felmérés és a Fenyegetésészlelés kínál.

- Az [adatfelderítés és besorolás](sql-database-data-discovery-and-classification.md) (jelenleg előzetes verzió) az Azure SQL-be épített, az adatbázisokban található bizalmas adatok felderítésére, besorolására, címkézésére és védelmére alkalmas képességeket biztosít. Használatával áttekinthető az adatbázis besorolási állapota, valamint követhető a bizalmas adatokhoz való hozzáférés az adatbázison belül és azon kívül.
- A [sebezhetőségi felmérés](sql-vulnerability-assessment.md) egy könnyen konfigurálható szolgáltatás, amely képes felderíteni és követni az adatbázisok lehetséges biztonsági réseit, és segít orvosolni azokat. Áttekinthetővé teszi az adatbázisok biztonsági állapotát, és végrehajtható lépéseket kínál a biztonsági problémák megoldására, valamint az adatbázisok védelmének fejlesztésére.
- A [fenyegetésészlelés](sql-database-threat-detection-overview.md) észleli az adatbázisok hozzáférésére és az adatbázisok biztonságának megsértésére tett szokatlan és potenciálisan kártevő szándékú kísérleteket. A szolgáltatás folyamatosan figyeli az adatbázisokat, és azonnal értesíti a felhasználót a gyanús tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálásos támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. A fenyegetésészlelés által adott riasztások tartalmazzák a gyanús tevékenység részleteit, és javaslatot tesznek a fenyegetés kivizsgálására és mérséklésére tett műveletekre.

Engedélyezze a SQL ADS után ezek mindegyikét engedélyezése foglalt szolgáltatásait. Egyetlen kattintással engedélyezheti az SQL Database-kiszolgálón az összes adatbázis ADS vagy a felügyelt példány. Engedélyezi, vagy ADS beállításainak kezelése van szüksége, tartozó a [SQL biztonságkezelő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) szerepkör, az SQL adatbázis-rendszergazdai szerepkör vagy SQL server rendszergazdai szerepkör. 

Díjszabás ADS igazítja az Azure Security Center standard szintű, ahol minden egyes védett SQL Database-kiszolgálóhoz vagy a felügyelt példány egyetlen csomópontnak számítanak. Újonnan védett erőforrások jogosultak a Security Center standard szintű az ingyenes próbaverziójára. További információkért lásd: a [az Azure Security Center díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Ismerkedés a HIRDETÉSEK

Az alábbi lépéseket az első lépések a HIRDETÉSEK.

## <a name="1-enable-ads"></a>1. HIRDETÉSEK engedélyezése

Az ADS engedélyezése **adatok biztonságú** alatt a **biztonsági** az SQL Database-kiszolgálóhoz vagy manged példány fejléc. HIRDETÉSEK engedélyezése az összes adatbázis az adatbázis-kiszolgáló vagy a felügyelt példány, kattintson a **engedélyezése speciális adatokat a kiszolgáló biztonsági**.

![HIRDETÉSEK engedélyezése](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> HIRDETÉSEK költségét igazodik az Azure Security Center standard szintű díjszabás csomópontonként, ahol egy csomópont a teljes SQL-adatbázis-kiszolgáló vagy a felügyelt példány. Így licencdíjat csak egyszer minden adatbázis az adatbázis-kiszolgáló vagy a felügyelt példány az ADS védelme érdekében. Kipróbálhatja ADS kezdetben az ingyenes próbaverziót.

## <a name="2-configure-vulnerability-assessment"></a>2. A biztonságirés-értékelési konfigurálása

A biztonságirés-értékelési használatának megkezdéséhez szüksége vizsgálati eredmények tároló tárfiók konfigurálása. Ehhez kattintson a biztonságirés-értékelési kártya.

![Sebezhetőség-Felmérési konfigurálása](./media/sql-advanced-protection/configure_va.png) 

Válassza ki, vagy hozzon létre egy tárfiókot a vizsgálati eredmények mentése. Sebezhetőségi felmérés hetente egyszer automatikus vizsgálatok futtatásához konfigurálása időszakosan ismétlődő vizsgálatok is bekapcsolhatja. Vizsgálati eredmény összefoglalását elküldi az Ön e-mail-címeit.

![Sebezhetőség-Felmérési beállításai](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. Adatok besorolása, nyomon követése a biztonsági rések és fenyegetések a riasztások kivizsgálásának indítása

Kattintson a **Adatfelderítés és besorolás** megtekintéséhez bizalmas oszlopok osztályozására és állandó érzékenységi címkét az adatok osztályozására szolgáló ajánlott. Kattintson a **biztonságirés-értékelési** kártya vizsgálatot a biztonsági rések és jelentések megtekintése és kezelése, és a biztonsági helyzetében nyomon követéséhez. Ha a biztonsági riasztások beérkezett, kattintson a **Fenyegetésészlelés** megtekintéséhez kártya részletesen, a riasztások és a egy konszolidált jelentésének megtekintéséhez az Azure-előfizetésben az Azure Security Center biztonsági riasztások lapján minden riasztásról.

## <a name="4-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>4. HIRDETÉSEK beállításainak ellenőrzése az SQL Database-kiszolgálón vagy a felügyelt példány

Beállításainak megtekintése és kezelése adatok biztonságú, navigáljon a **adatok biztonságú** alatt a **biztonsági** az SQL Database-kiszolgálóhoz vagy a felügyelt példány fejléc. Ezen a lapon engedélyezze vagy ADS letilthatja és módosíthatja a Threat Detection a teljes SQL Database-kiszolgálóhoz vagy a felügyelt példány beállításait.

![Kiszolgáló beállításai](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-ads-settings-for-a-sql-database"></a>5. SQL-adatbázis ADS beállításainak kezelése

Egy adott adatbázis ADS Fenyegetésészlelési beállításainak felülbírálása, ellenőrizze a **engedélyezése adatok biztonságú az adatbázis szintjén** jelölőnégyzetet. Használja ezt a beállítást csak akkor, ha rendelkezik olyan követelmény külön threat detection riasztásokat az egyes adatbázisok helyett vagy a riasztások mellett az érkezett az adatbázis-kiszolgáló összes adatbázisához vagy felügyelt példány fogadásához. 

Ha a jelölőnégyzet be van jelölve, kattintson **erre az adatbázisra vonatkozó Fenyegetésészlelési beállítások** , majd konfigurálja a megfelelő beállításokat ehhez az adatbázishoz.

![Adatbázis- és fenyegetés-észlelési beállításainak](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Az adatbázis-kiszolgáló vagy a felügyelt példány speciális adatok biztonsági beállításai is a HIRDETÉSEK adatbázis panelen érhető el. Kattintson a **beállítások** az ADS fő ablaktáblán, majd kattintson a **kiszolgálóbeállítások megtekintése biztonságú adatok**. 

![Adatbázis-beállítások](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>További lépések 

- Tudjon meg többet [Adatfelderítés és besorolás](sql-database-data-discovery-and-classification.md) 
- Tudjon meg többet [sebezhetőségi felmérés](sql-vulnerability-assessment.md) 
- Tudjon meg többet [Fenyegetésészlelés](sql-database-threat-detection.md)
- Tudjon meg többet [Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)

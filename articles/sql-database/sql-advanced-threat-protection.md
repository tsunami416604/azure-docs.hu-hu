---
title: Komplex veszélyforrások elleni védelem – Azure SQL Database |} A Microsoft Docs
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
ms.date: 12/16/2018
ms.openlocfilehash: 40f6b6effa509fd2cfa40ecfc758ac37e0a66778
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538616"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Az Azure SQL Database-adatbázis komplex veszélyforrások elleni védelem

Az SQL Advanced Threat Protection fejlett SQL-biztonsági funkciókat tartalmazó egyesített biztonsági csomag. Ez magában foglalja a funkciók felderítése és besorolása a bizalmas adatok, felszínre hozza a és enyhítő adatbázis biztonsági réseinek, és az adatbázis fenyegetést jelezhet rendellenes tevékenységeket észleli. Segítségével egyetlen helyen engedélyezhetők és kezelhetők ezek a képességek. 

## <a name="overview"></a>Áttekintés

SQL a speciális veszélyforrások elleni védelem (ATP) egy speciális SQL biztonsági funkciókat, többek között az Adatfelderítés és a besorolás, a sebezhetőségi felmérés és a Fenyegetésészlelés kínál. 

- Az [adatfelderítés és besorolás](sql-database-data-discovery-and-classification.md) (jelenleg előzetes verzió) az Azure SQL-be épített, az adatbázisokban található bizalmas adatok felderítésére, besorolására, címkézésére és védelmére alkalmas képességeket biztosít. Használatával áttekinthető az adatbázis besorolási állapota, valamint követhető a bizalmas adatokhoz való hozzáférés az adatbázison belül és azon kívül.
- A [sebezhetőségi felmérés](sql-vulnerability-assessment.md) egy könnyen konfigurálható szolgáltatás, amely képes felderíteni és követni az adatbázisok lehetséges biztonsági réseit, és segít orvosolni azokat. Áttekinthetővé teszi az adatbázisok biztonsági állapotát, és végrehajtható lépéseket kínál a biztonsági problémák megoldására, valamint az adatbázisok védelmének fejlesztésére.
- A [fenyegetésészlelés](sql-database-threat-detection-overview.md) észleli az adatbázisok hozzáférésére és az adatbázisok biztonságának megsértésére tett szokatlan és potenciálisan kártevő szándékú kísérleteket. A szolgáltatás folyamatosan figyeli az adatbázisokat, és azonnal értesíti a felhasználót a gyanús tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálásos támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. A fenyegetésészlelés által adott riasztások tartalmazzák a gyanús tevékenység részleteit, és javaslatot tesznek a fenyegetés kivizsgálására és mérséklésére tett műveletekre.

SQL ATP engedélyezése, ha ahhoz, hogy ezek mindegyikét tartalmazza a funkciók. A Komplex veszélyforrások elleni védelem egyetlen kattintással engedélyezhető az egész adatbázis-kiszolgálón, ezáltal a kiszolgáló minden adatbázisa védelemmel látható el. Engedélyezése és kezelése az ATP-beállításokat, tartozó igényel a [SQL biztonságkezelő](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) szerepkör, az SQL adatbázis-rendszergazdai szerepkör vagy SQL server rendszergazdai szerepkör. 

ATP díjszabás igazítja az Azure Security Center standard szintű, ahol egyes védelemmel ellátott SQL Database-kiszolgálók egyetlen csomópontnak számítanak. Újonnan védett erőforrások jogosultak a Security Center standard szintű az ingyenes próbaverziójára. További információkért lásd: a [az Azure Security Center díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="getting-started-with-atp"></a>Ismerkedés az ATP 
Az alábbi lépéseket az első lépések az ATP. 

## <a name="1-enable-atp"></a>1. ATP engedélyezése

Az ATP engedélyezése **komplex veszélyforrások elleni védelem** alatt a **biztonsági** fejléc az Azure SQL Database panelen. A kiszolgáló összes adatbázisára az ATP engedélyezéséhez kattintson **engedélyezése komplex veszélyforrások elleni védelem a kiszolgálón**.

![ATP engedélyezése](./media/sql-advanced-protection/enable_atp.png) 

> [!NOTE]
> ATP költségét igazodik az Azure Security Center standard szintű díjszabás csomópontonként, ahol egy csomópont-e a teljes SQL logikai kiszolgálón. Így licencdíjat csak egyszer az ATP-kiszolgálón az összes adatbázis védelme érdekében. Kipróbálhatja ATP kezdetben az ingyenes próbaverziót.

## <a name="2-configure-vulnerability-assessment"></a>2. A biztonságirés-értékelési konfigurálása

A biztonságirés-értékelési használatának megkezdéséhez szüksége vizsgálati eredmények tároló tárfiók konfigurálása. Ehhez kattintson a biztonságirés-értékelési kártya.

![Sebezhetőség-Felmérési konfigurálása](./media/sql-advanced-protection/configure_va.png) 

Válassza ki, vagy hozzon létre egy tárfiókot a vizsgálati eredmények mentése. Sebezhetőségi felmérés hetente egyszer automatikus vizsgálatok futtatásához konfigurálása időszakosan ismétlődő vizsgálatok is bekapcsolhatja. Vizsgálati eredmény összefoglalását elküldi az Ön e-mail-címeit.

![Sebezhetőség-Felmérési beállításai](./media/sql-advanced-protection/va_settings.png) 

## <a name="3-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>3. Adatok besorolása, nyomon követése a biztonsági rések és fenyegetések a riasztások kivizsgálásának indítása

Kattintson a **Adatfelderítés és besorolás** megtekintéséhez bizalmas oszlopok osztályozására és állandó érzékenységi címkét az adatok osztályozására szolgáló ajánlott. Kattintson a **biztonságirés-értékelési** kártya vizsgálatot a biztonsági rések és jelentések megtekintése és kezelése, és a biztonsági helyzetében nyomon követéséhez. Ha a biztonsági riasztások beérkezett, kattintson a **Fenyegetésészlelés** megtekintéséhez kártya részletesen, a riasztások és a egy konszolidált jelentésének megtekintéséhez az Azure-előfizetésben az Azure Security Center biztonsági riasztások lapján minden riasztásról.

## <a name="4-manage-atp-settings-on-your-sql-server"></a>4. Az SQL server ATP beállításainak kezelése

Megtekintheti és kezelheti a komplex veszélyforrások elleni védelem beállításait, navigáljon a **komplex veszélyforrások elleni védelem** alatt a **biztonsági** az SQL server ablaktábla a címsorában. Ezen a lapon engedélyezése vagy letiltása az ATP, és Fenyegetésészlelési beállításainak módosításához a teljes SQL-kiszolgálón.

![Kiszolgáló beállításai](./media/sql-advanced-protection/server_settings.png) 

## <a name="5-manage-atp-settings-for-a-sql-database"></a>5. SQL-adatbázis ATP beállításainak kezelése

Egy adott adatbázis ATP Fenyegetésészlelési beállításainak felülbírálása, ellenőrizze a **engedélyezése komplex veszélyforrások elleni védelem az adatbázis szintjén** jelölőnégyzetet. Használja ezt a beállítást csak akkor, ha rendelkezik olyan követelmény helyére vagy a kiszolgáló összes adatbázisára érkezett riasztások mellett az egyes adatbázis külön fenyegetésészlelési riasztásokat fogadni. 

Ha a jelölőnégyzet be van jelölve, kattintson **erre az adatbázisra vonatkozó Fenyegetésészlelési beállítások** , majd konfigurálja a megfelelő beállításokat ehhez az adatbázishoz.

![Adatbázis- és fenyegetés-észlelési beállításainak](./media/sql-advanced-protection/database_threat_detection_settings.png) 

A kiszolgáló a speciális veszélyforrások elleni védelem beállításait is az ATP adatbázis panelen érhető el. Kattintson a **beállítások** az ATP fő ablaktáblán, majd kattintson a **nézet komplex veszélyforrások elleni védelem kiszolgálóbeállításainak**. 

![Adatbázis-beállítások](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>További lépések 

- Tudjon meg többet [Adatfelderítés és besorolás](sql-database-data-discovery-and-classification.md) 
- Tudjon meg többet [sebezhetőségi felmérés](sql-vulnerability-assessment.md) 
- Tudjon meg többet [Fenyegetésészlelés](sql-database-threat-detection.md)
- Tudjon meg többet [Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)

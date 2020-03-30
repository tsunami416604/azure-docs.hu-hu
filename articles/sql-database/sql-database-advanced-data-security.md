---
title: Advanced Data Security
description: Ismerje meg a bizalmas adatok felderítésére és besorolására, az adatbázis biztonsági réseikezelésére és az Azure SQL-adatbázist fenyegető, rendellenes tevékenységek észlelésére szolgáló funkciókat.
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
ms.openlocfilehash: 1f0e6694e596dc60264dfe0789a2f80090e0da3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269132"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Speciális adatbiztonság az Azure SQL Database-hez

A fejlett adatbiztonság egy egységes csomag a fejlett SQL biztonsági képességekhez. Lehetőséget nyújt a bizalmas adatok felderítésére és titkossá minősítésére, az adatbázis biztonsági réseinek feltárására és kezelésére, továbbá az adatbázisra nézve fenyegetést jelentő rendellenes tevékenységek észlelésére. Segítségével egyetlen helyen engedélyezhetők és kezelhetők ezek a képességek.

## <a name="overview"></a>Áttekintés

A fejlett adatbiztonság (ADS) fejlett SQL biztonsági képességeket biztosít, beleértve az adatfelderítést & besorolást, a biztonsági résfelmérést és a komplex veszélyforrások elleni védelmet.

- Az Azure SQL Database-be beépített [adatfelderítési & besorolás](sql-database-data-discovery-and-classification.md) az adatbázisokban lévő bizalmas adatok felderítéséhez, besorolásához, címkézéséhez & védelméhez beépített funkciókat biztosít. Használatával áttekinthető az adatbázis besorolási állapota, valamint követhető a bizalmas adatokhoz való hozzáférés az adatbázison belül és azon kívül.
- [A biztonsági rés felmérése](sql-vulnerability-assessment.md) könnyen konfigurálható szolgáltatás, amely képes felderíteni, nyomon követni és segíteni az adatbázis esetleges biztonsági réseinek elhárításában. Áttekinthetővé teszi az adatbázisok biztonsági állapotát, és végrehajtható lépéseket kínál a biztonsági problémák megoldására, valamint az adatbázisok védelmének fejlesztésére.
- Az [Advanced Threat Protection](sql-database-threat-detection-overview.md) észleli az adatbázisai hozzáférésére és az adatbázisai biztonságának megsértésére tett szokatlan és potenciálisan kártevő szándékú kísérleteket. A szolgáltatás folyamatosan figyeli az adatbázisokat, és azonnal értesíti a felhasználót a gyanús tevékenységekről, a lehetséges biztonsági résekről, az SQL-injektálásos támadásokról, valamint a rendellenes adatbázis-hozzáférési mintákról. Az Advanced Threat Protection által adott riasztások tartalmazzák a gyanús tevékenység részleteit, és javaslatot tesznek a fenyegetés kivizsgálására és mérséklésére tett műveletekre.

Engedélyezze az SQL ADS egyszeri engedélyezését az összes ilyen funkció engedélyezéséhez. Egyetlen kattintással engedélyezheti az ADS-t az SQL Database-kiszolgálón vagy a felügyelt példányon lévő összes adatbázishoz. Az ADS-beállítások engedélyezéséhez vagy kezeléséhez az [SQL biztonsági kezelői](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) szerepkörhöz, az SQL-adatbázis felügyeleti szerepköréhez vagy az SQL-kiszolgáló felügyeleti szerepköréhez kell tartoznia. 

Az ADS-díjszabás az Azure Security Center standard rétegéhez igazodik, ahol minden egyes védett SQL-adatbázis-kiszolgáló vagy felügyelt példány egy csomópontnak számít. Az újonnan védett erőforrások a Security Center standard szintű csomagjának ingyenes próbaverziójára jogosultak. További információt az [Azure Security Center díjszabási lapján](https://azure.microsoft.com/pricing/details/security-center/)talál.

## <a name="getting-started-with-ads"></a>Az ADS – első lépések

Az alábbi lépések az ADS szolgáltatással való ismerkedéshez.

## <a name="1-enable-ads"></a>1. Hirdetések engedélyezése

Engedélyezze az ADS-t úgy, hogy az SQL Database-kiszolgáló vagy a felügyelt példány **Biztonság** fejlécében a **Speciális adatbiztonság** elemre navigál. Ha engedélyezni szeretné az ADS-t az adatbázis-kiszolgálón vagy a felügyelt példányon lévő összes adatbázishoz, kattintson **a Speciális adatbiztonság engedélyezése a kiszolgálón**elemre.

> [!NOTE]
> A rendszer automatikusan létrehoz és konfigurál egy tárfiókot a **biztonsági rés vizsgálatának** eredményei tárolására. Ha már engedélyezte az ADS-t egy másik kiszolgálóhoz ugyanabban az erőforráscsoportban és régióban, akkor a rendszer a meglévő tárfiókot használja.

![ADS engedélyezése](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> Az ADS költsége az Azure Security Center csomópontonkénti standard szintű díjszabásával van egyezve, ahol a csomópont a teljes SQL Database-kiszolgáló vagy felügyelt példány. Így csak egyszer fizet az adatbázis-kiszolgálón vagy az ADS-sel kezelt példányon található összes adatbázis védelméért. Kipróbálhatja ADS ki kezdetben egy ingyenes próbaverzió.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Az adatok besorolásának megkezdése, a biztonsági rések nyomon követése és a fenyegetésriasztások kivizsgálása

Kattintson a **Data Discovery & besorolási** kártya ajánlott bizalmas oszlopok besorolásához és az adatok állandó érzékenységi címkék osztályozásához. Kattintson a **Biztonsági rés felmérése** kártyára a biztonsági résvizsgálatai és jelentései megtekintéséhez és kezeléséhez, valamint a biztonsági állapot nyomon követéséhez. Ha biztonsági riasztások érkeztek, kattintson a **Komplex veszélyforrások elleni védelem** karton a riasztások részleteinek megtekintéséhez, és tekintse meg az Azure-előfizetésében lévő összes riasztás összevont jelentését az Azure Security Center biztonsági riasztások lapján.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. Ads-beállítások kezelése az SQL Database-kiszolgálón vagy a felügyelt példányon

Az ADS-beállítások megtekintéséhez és kezeléséhez keresse meg a **Speciális adatbiztonság** lehetőséget az SQL Database-kiszolgáló vagy a felügyelt példány **Biztonság** fejlécében. Ezen a lapon engedélyezheti vagy letilthatja az ADS-t, és módosíthatja a biztonsági rések felmérését és a komplex veszélyforrások elleni beállításokat a teljes SQL Database-kiszolgálóra vagy felügyelt példányra vonatkozóan.

![Kiszolgáló beállításai](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. SQL-adatbázis ADS-beállításainak kezelése

Egy adott adatbázis ADS-beállításainak felülbírálásához jelölje be a **Speciális adatbiztonság engedélyezése az adatbázis szintjén** jelölőnégyzetet. Csak akkor használja ezt a beállítást, ha külön követelmény, hogy az egyes adatbázisokhoz külön speciális veszélyforrások elleni riasztást vagy sebezhetőségi értékelési eredményeket kapjon, a riasztási és a kapott eredmények helyett vagy mellett a adatbázis-kiszolgáló vagy felügyelt példány.

A jelölőnégyzet bejelölését követően konfigurálhatja az adatbázis megfelelő beállításait.
 
![Adatbázis- és komplex veszélyforrások elleni védelem beállításai](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Az adatbázis-kiszolgáló vagy a felügyelt példány speciális adatbiztonsági beállításai az ADS adatbázis ablaktáblán is elérhetők. Kattintson a **Beállítások** gombra a fő ADS ablaktáblán, majd kattintson a **Speciális adatbiztonsági kiszolgáló beállításainak megtekintése parancsra.** 

![Adatbázis-beállítások](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>További lépések 

- További információ az [adatfelderítésről & besorolásról](sql-database-data-discovery-and-classification.md) 
- További információ a [sebezhetőség felméréséről](sql-vulnerability-assessment.md) 
- További információ a [komplex veszélyforrások elleni védelemről](sql-database-threat-detection.md)
- További információ az [Azure biztonsági központjáról](https://docs.microsoft.com/azure/security-center/security-center-intro)

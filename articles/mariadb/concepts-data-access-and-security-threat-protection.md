---
title: Komplex veszélyforrások elleni védelem – Azure-adatbázis a MariaDB-hez
description: A komplex veszélyforrások elleni védelem észleli az adatbázist fenyegető potenciális biztonsági fenyegetéseket jelző rendellenes adatbázis-tevékenységeket.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8485907eba75f80c8f0ed4fd0cc7368c6147b9fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532178"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Azure-adatbázis a MariaDB komplex veszélyforrások elleni védelméhez

A MariaDB-hez való komplex veszélyforrások elleni védelem észleli az adatbázisok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket jelző rendellenes tevékenységeket.

> [!IMPORTANT]
> A komplex veszélyforrások elleni védelem nyilvános előzetes verzióban érhető el.

A komplex veszélyforrások elleni védelem része a speciális adatbiztonsági ajánlatnak, amely a fejlett biztonsági képességek egységes csomagja. A komplex veszélyforrások elleni védelem az [Azure Portalon](https://portal.azure.com)keresztül érhető el és kezelhető. A szolgáltatás általános célú és memóriaoptimalizált kiszolgálókon érhető el.

> [!NOTE]
> Az Advanced Threat Protection funkció **nem** érhető el a következő Azure-kormányzat- és felhőrégiókban: US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Kérjük, látogasson el [a termékek rendelkezésre álló régió általános](https://azure.microsoft.com/global-infrastructure/services/) termék elérhetőségét.


## <a name="what-is-advanced-threat-protection"></a>Mi az advanced threat protection?

A MariaDB-hez való komplex veszélyforrások elleni védelem új biztonsági réteget biztosít, amely lehetővé teszi az ügyfelek számára, hogy észleljék a potenciális fenyegetéseket, és reagáljanak azokra a rendellenes tevékenységekre vonatkozó biztonsági riasztások biztosításával. A felhasználók riasztást kapnak a gyanús adatbázis-tevékenységekről és a potenciális biztonsági résekről, valamint a rendellenes adatbázis-hozzáférési és lekérdezési mintákról. A MariaDB-hoz való komplex veszélyforrások elleni védelem integrálja a riasztásokat az [Azure Security Centerrel,](https://azure.microsoft.com/services/security-center/)amely tartalmazza a gyanús tevékenységek részleteit, és javaslatot tesz a fenyegetés kivizsgálására és csökkentésére. A MariaDB-hez való komplex veszélyforrások elleni védelem egyszerűvé teszi az adatbázispotenciális fenyegetésekkel való kezelését anélkül, hogy biztonsági szakértőnek kellene lennie, vagy fejlett biztonsági figyelőrendszereket kell kezelnie. 

![Komplex veszélyforrások elleni védelem koncepciója](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Komplex veszélyforrások elleni riasztások 
A MariaDB-hez való komplex veszélyforrások elleni védelem észleli az adatbázisok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket jelző rendellenes tevékenységeket, és a következő riasztásokat indíthatja el:
- **Hozzáférés szokatlan helyről:** Ez a riasztás akkor aktiválódik, ha a MariaDB-kiszolgálóhoz készült Azure Database hozzáférési minta megváltozik, ahol valaki szokatlan földrajzi helyről jelentkezett be a MariaDB-kiszolgáló azure-adatbázisába. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés szokatlan Azure-adatközpontból:** Ez a riasztás akkor aktiválódik, ha a MariaDB-kiszolgáló azure-adatbázisának hozzáférési mintája megváltozik, ahol valaki egy szokatlan Azure-adatközpontból jelentkezett be a kiszolgálóra, amely az elmúlt időszakban a kiszolgálón volt látható. Bizonyos esetekben a riasztás egy legitim műveletet észlel (az új alkalmazás az Azure-ban, a Power BI-ban). Más esetekben a riasztás kártékony műveleteket észlel egy Azure-erőforrás vagy -szolgáltatás felől (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés ismeretlen egyszerű:** Ez a riasztás akkor aktiválódik, ha a változás a hozzáférési mintát az Azure Database for MariaDB-kiszolgáló, ahol valaki bejelentkezett a kiszolgálóra egy szokatlan egyszerű (Azure Database for MariaDB felhasználó). Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés potenciálisan kártékony alkalmazással**: Ez a riasztás akkor aktiválódik, ha valaki egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy gyakori támadóeszközökkel végrehajtott támadást észlel.
- **Oktatás kényszerítése Az Azure Database MariaDB hitelesítő adatok:** Ez a riasztás akkor aktiválódik, ha a bnormálisan nagy számú sikertelen bejelentkezések különböző hitelesítő adatokkal. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy találgatásos támadást észlel.

## <a name="next-steps"></a>További lépések

* További információ az [Azure Security Centerről](https://docs.microsoft.com/azure/security-center/security-center-intro)
* A díjszabásról további információt az [Azure Database for MariaDB díjszabási oldalán talál.](https://azure.microsoft.com/pricing/details/mariadb/) 
* Az Azure Database konfigurálása [a MariaDB komplex veszélyforrások elleni védelemhez](howto-database-threat-protection-portal.md) az Azure Portal használatával  

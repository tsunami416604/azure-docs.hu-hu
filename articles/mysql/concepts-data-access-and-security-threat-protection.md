---
title: Komplex veszélyforrások elleni védelem – Azure Database for MySQL-hez |} A Microsoft Docs
description: Komplex veszélyforrások elleni védelem észleli a rendellenes adatbázis-tevékenységek utaló esetleges biztonsági fenyegetések az adatbázishoz.
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 10fa2a409437c8cc48bcd1a674cc3832f086dcf2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795953"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>Azure Database for MySQL komplex veszélyforrások elleni védelem

Az Azure Database for MySQL-hez készült Komplex veszélyforrások elleni védelem észleli az adatbázisok hozzáférésére és az adatbázisok biztonságának megsértésére tett szokatlan és potenciálisan kártevő szándékú kísérleteket.

> [!NOTE]
> Komplex veszélyforrások elleni védelem jelenleg nyilvános előzetes verzióban.

Komplex veszélyforrások elleni védelem a speciális adatbiztonság ajánlat, amely egy fejlett biztonsági képességek az egyesített csomag részét képezi. Komplex veszélyforrások elleni védelem érheti el és keresztül kezeli a [az Azure portal](https://portal.azure.com) vagy [REST API-val](/rest/api/mysql/serversecurityalertpolicies). A szolgáltatás általános célú és memóriahasználatra optimalizált kiszolgálók érhető el.

> [!NOTE]
> A komplex veszélyforrások elleni védelem funkció **nem** a következő az Azure government és szuverén felhő-régiók: US Gov Texas, USA beli államigazgatás – Arizona, USA beli államigazgatás – Iowa, USA, beli államigazgatás – Virginia, USA védelmi Minisztériuma – keleti régiója, USA védelmi Minisztériuma – középső régiója, Németország közép-India, Németország Észak, kelet-Kína, kelet-Kína 2. Látogasson el [elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) általános rendelkezésre állás érdekében.


## <a name="what-is-advanced-threat-protection"></a>Mi az, hogy a komplex veszélyforrások elleni védelem?

Komplex veszélyforrások elleni védelem az Azure Database for MySQL biztonsági, amely lehetővé teszi az ügyfelek bekövetkezésük pillanatában biztonsági riasztások révén a rendellenes tevékenységekre adott reagáljon a lehetséges veszélyforrásokra, és egy új réteget biztosít. Felhasználók a gyanús adatbázis-tevékenységekről, és a lehetséges biztonsági résekről, valamint a rendellenes adatbázis-hozzáférés és a lekérdezések minták esetén riasztást küld. Komplex veszélyforrások elleni védelem az Azure Database for MySQL integrálható a riasztások [az Azure Security Center](https://azure.microsoft.com/services/security-center/), amely tartalmazza a gyanús tevékenység részleteit, és vizsgálja meg, valamint a fenyegetés művelet javasolja. Komplex veszélyforrások elleni védelem az Azure Database for MySQL-hez is egyszerűvé teszi cím potenciális fenyegetések, az adatbázis szakértői biztonsági szakértelem vagy fejlett biztonsági monitorozási rendszerek felügyelete nélkül. 

![A komplex veszélyforrások elleni védelem fogalma](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Advanced Threat Protection-riasztások 
Komplex veszélyforrások elleni védelem az Azure Database for MySQL észleli az adatbázisokat elérni vagy kiaknázni a szokatlan és vélhetően kárt okozó kísérleteket jelző rendellenes tevékenységek, és azt is aktiválhatja a következő riasztásokat:
- **Hozzáférés szokatlan helyről**: Ez a riasztás akkor aktiválódik, ha a hozzáférési mintájában, az Azure Database for MySQL-kiszolgálóhoz, amikor valaki jelentkezett be az Azure database for MySQL-kiszolgáló egy szokatlan földrajzi helyről változik. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés szokatlan Azure-beli adat központból**: Ez a riasztás akkor aktiválódik, ha a hozzáférési mintájában, az Azure Database for MySQL-kiszolgálóhoz, amikor valaki jelentkezett be a kiszolgálóra egy szokatlan Azure-beli adat központból ezen a kiszolgálón a legutóbbi időszakban látott változik. Bizonyos esetekben a riasztás jogszerű műveleteket (az Azure-ban, a Power bi-ban, Azure Database for MySQL Lekérdezésszerkesztő új alkalmazás) észlel. Más esetekben a riasztás kártékony műveleteket észlel egy Azure-erőforrás vagy -szolgáltatás felől (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés résztvevő részéről**: Ez a riasztás akkor aktiválódik, ha módosítják a hozzáférési mintájában, az Azure Database for MySQL-kiszolgálóhoz, amikor valaki jelentkezett be a kiszolgálóra egy szokatlan résztvevő (, Azure Database for MySQL-felhasználó) használatával. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés potenciálisan káros alkalmazás**: Ez a riasztás akkor aktiválódik, ha egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy gyakori támadóeszközökkel végrehajtott támadást észlel.
- **Találgatásos Azure Database for MySQL hitelesítő adatok**: Ez a riasztás akkor aktiválódik, ha van egy rendellenes magas a különböző hitelesítő adatok használatával történő sikertelen bejelentkezések száma. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy találgatásos támadást észlel.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Díjszabási információkért tekintse meg a [, Azure Database for MySQL-díjszabás](https://azure.microsoft.com/pricing/details/mysql/) 
* Konfigurálása [, Azure Database for MySQL komplex veszélyforrások elleni védelem](howto-database-threat-protection-portal.md) az Azure portal használatával  

---
title: Komplex veszélyforrások elleni védelem – Azure Database for postgresql-hez
description: Komplex veszélyforrások elleni védelem észleli a rendellenes adatbázis-tevékenységek utaló esetleges biztonsági fenyegetések az adatbázishoz.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: b01238b0ebfe07b605824a75301165e477bd05e9
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54910712"
---
# <a name="azure-database-for-postgresql-advanced-threat-protection"></a>Azure Database for PostgreSQL komplex veszélyforrások elleni védelem

Az Azure Database for PostgreSQL-hez készült Komplex veszélyforrások elleni védelem észleli az adatbázisok hozzáférésére és az adatbázisok biztonságának megsértésére tett szokatlan és potenciálisan kártevő szándékú kísérleteket.

Veszélyforrások elleni védelem a speciális veszélyforrások elleni védelem (ATP) ajánlat, amely egy fejlett biztonsági képességek az egyesített csomag részét képezi. Komplex veszélyforrások elleni védelem érheti el és keresztül kezeli a [az Azure portal](https://portal.azure.com) és jelenleg előzetes verzióban érhető el.

> [!NOTE]
> A komplex veszélyforrások elleni védelem funkció **nem** a következő az Azure government és szuverén felhő-régiók: US Gov Texas, USA beli államigazgatás – Arizona, USA beli államigazgatás – Iowa, USA, beli államigazgatás – Virginia, USA védelmi Minisztériuma – keleti régiója, USA védelmi Minisztériuma – középső régiója, Németország közép-India, Németország Észak, kelet-Kína, kelet-Kína 2. Látogasson el [elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) általános rendelkezésre állás érdekében.
>

> [!NOTE]
> Ez a funkció érhető el minden régióban az Azure, Azure Database for PostgreSQL általános célú és memóriahasználatra optimalizált kiszolgálók telepítési helyét.

## <a name="what-is-advanced-threat-protection"></a>Mi az, hogy a komplex veszélyforrások elleni védelem?

Komplex veszélyforrások elleni védelem az Azure Database for PostgreSQL biztonsági, amely lehetővé teszi az ügyfelek bekövetkezésük pillanatában biztonsági riasztások révén a rendellenes tevékenységekre adott reagáljon a lehetséges veszélyforrásokra, és egy új réteget biztosít. Felhasználók a gyanús adatbázis-tevékenységekről, és a lehetséges biztonsági résekről, valamint a rendellenes adatbázis-hozzáférés és a lekérdezések minták esetén riasztást küld. Komplex veszélyforrások elleni védelem az Azure Database for PostgreSQL integrálható a riasztások [az Azure Security Center](https://azure.microsoft.com/services/security-center/), amely tartalmazza a gyanús tevékenység részleteit, és vizsgálja meg, valamint a fenyegetés művelet javasolja. Komplex veszélyforrások elleni védelem az Azure Database for postgresql-hez is egyszerűvé teszi cím potenciális fenyegetések, az adatbázis szakértői biztonsági szakértelem vagy fejlett biztonsági monitorozási rendszerek felügyelete nélkül. 

![A komplex veszélyforrások elleni védelem fogalma](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>A speciális veszélyforrások elleni védelem riasztások 
Komplex veszélyforrások elleni védelem az Azure Database for PostgreSQL észleli az adatbázisokat elérni vagy kiaknázni a szokatlan és vélhetően kárt okozó kísérleteket jelző rendellenes tevékenységek, és azt is aktiválhatja a következő riasztásokat:
- **Hozzáférés szokatlan helyről**: Ez a riasztás akkor aktiválódik, ha a hozzáférési mintájában, az Azure Database for PostgreSQL-kiszolgálóhoz, amikor valaki jelentkezett be az Azure database for PostgreSQL-kiszolgáló egy szokatlan földrajzi helyről változik. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés szokatlan Azure-beli adat központból**: Ez a riasztás akkor aktiválódik, ha a hozzáférési mintájában, az Azure Database for PostgreSQL-kiszolgálóhoz, amikor valaki jelentkezett be a kiszolgálóra egy szokatlan Azure-beli adat központból ezen a kiszolgálón a legutóbbi időszakban látott változik. Bizonyos esetekben a riasztás jogszerű műveleteket (az Azure-ban, a Power bi-ban, Azure Database for PostgreSQL Lekérdezésszerkesztő új alkalmazás) észlel. Más esetekben a riasztás kártékony műveleteket észlel egy Azure-erőforrás vagy -szolgáltatás felől (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés résztvevő részéről**: Ez a riasztás akkor aktiválódik, ha módosítják a hozzáférési mintájában, az Azure Database for PostgreSQL-kiszolgálóhoz, amikor valaki jelentkezett be a kiszolgálóra egy szokatlan résztvevő (, Azure Database for PostgreSQL-felhasználó) használatával. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
- **Hozzáférés potenciálisan káros alkalmazás**: Ez a riasztás akkor aktiválódik, ha egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy gyakori támadóeszközökkel végrehajtott támadást észlel.
- **Találgatásos Azure Database for PostgreSQL hitelesítő adatok**: Ez a riasztás akkor aktiválódik, ha van egy rendellenes magas a különböző hitelesítő adatok használatával történő sikertelen bejelentkezések száma. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy találgatásos támadást észlel.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Díjszabási információkért tekintse meg a [, Azure Database for PostgreSQL-díjszabás](https://azure.microsoft.com/pricing/details/postgresql/) 
* Konfigurálása [, Azure Database for PostgreSQL komplex veszélyforrások elleni védelem](howto-database-threat-protection-portal.md) az Azure portal használatával  

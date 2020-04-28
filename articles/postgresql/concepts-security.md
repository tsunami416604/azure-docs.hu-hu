---
title: Biztonság Azure Database for PostgreSQL – egyetlen kiszolgáló
description: A Azure Database for PostgreSQL-egy kiszolgáló biztonsági funkcióinak áttekintése.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 02dc9e1ad9ee46b1a400e44b6ef737e70571a17a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75972588"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Biztonság Azure Database for PostgreSQL – egyetlen kiszolgáló

Több biztonsági réteg is rendelkezésre áll, amelyek a Azure Database for PostgreSQL-kiszolgálón lévő adatvédelmet biztosítanak. Ez a cikk ezeket a biztonsági beállításokat ismerteti.

## <a name="information-protection-and-encryption"></a>Adatvédelem és titkosítás

### <a name="in-transit"></a>Átvitel közben
A Azure Database for PostgreSQL az adatátvitelt a Transport Layer Security segítségével titkosítja. A titkosítás (SSL/TLS) alapértelmezés szerint ki van kényszerítve.

### <a name="at-rest"></a>Nyugalmi állapotban
A Azure Database for PostgreSQL szolgáltatás az FIPS 140-2 ellenőrzött titkosítási modult használja a REST-alapú adattárolási titkosításhoz. Az adatokat, beleértve a biztonsági másolatokat, a lemezeken titkosítva, a lekérdezések futtatásakor létrehozott ideiglenes fájlok kivételével. A szolgáltatás az Azure Storage-titkosításban található AES 256 bites titkosítást használja, és a kulcsokat a rendszer felügyeli. A tárolótitkosítás mindig be van kapcsolva, és nem tiltható le.


## <a name="network-security"></a>Hálózati biztonság
A Azure Database for PostgreSQL-kiszolgálóval létesített kapcsolatok először egy regionális átjárón keresztül irányíthatók. Az átjáró nyilvánosan elérhető IP-címmel rendelkezik, a kiszolgáló IP-címei pedig védettek. Az átjáróval kapcsolatos további információkért tekintse meg a [kapcsolati architektúra című cikket](concepts-connectivity-architecture.md).  

Az újonnan létrehozott Azure Database for PostgreSQL-kiszolgáló rendelkezik egy tűzfallal, amely blokkolja az összes külső kapcsolatot. Az átjáró elérése azonban nem engedélyezett a kiszolgálóhoz való kapcsolódáshoz. 

### <a name="ip-firewall-rules"></a>IP-tűzfalszabályok
Az IP-tűzfalszabályok az egyes kérelmek feladó IP-címe alapján biztosítanak hozzáférést a kiszolgálókhoz. További információért tekintse meg a [Tűzfalszabályok áttekintését](concepts-firewall-rules.md) .

### <a name="virtual-network-firewall-rules"></a>Virtuális hálózat tűzfalszabályai
A Virtual Network szolgáltatás végpontja kiterjeszti a virtuális hálózati kapcsolatot az Azure-gerincen. A virtuális hálózati szabályok használatával engedélyezheti a Azure Database for PostgreSQL-kiszolgáló számára a virtuális hálózatban lévő kijelölt alhálózatok kapcsolatainak engedélyezését. További információt a [Virtual Network szolgáltatás végpontjának áttekintése](concepts-data-access-and-security-vnet.md)című témakörben talál.

### <a name="private-ip"></a>Magánhálózati IP-cím
A privát hivatkozás lehetővé teszi, hogy egy privát végponton keresztül kapcsolódjon az Azure-beli Azure Database for PostgreSQL egyetlen kiszolgálóhoz. Az Azure Private link lényegében az Azure-szolgáltatásokat a privát Virtual Networkon (VNet) belül hozza elérhetővé. A Péter-erőforrások a magánhálózati IP-cím használatával ugyanúgy érhetők el, mint a VNet lévő többi erőforráshoz. További információt a [privát hivatkozások áttekintése](concepts-data-access-and-security-private-link.md) című témakörben talál.


## <a name="access-management"></a>Hozzáférés-kezelés

A Azure Database for PostgreSQL-kiszolgáló létrehozásakor meg kell adnia egy rendszergazdai szerepkör hitelesítő adatait. Ez a rendszergazdai szerepkör további [PostgreSQL-szerepkörök](https://www.postgresql.org/docs/current/user-manag.html)létrehozásához használható.

[Azure Active Directory (HRE) hitelesítés](concepts-aad-authentication.md)használatával is csatlakozhat a kiszolgálóhoz.


## <a name="threat-protection"></a>Veszélyforrások elleni védelem

A komplex [veszélyforrások elleni védelem](concepts-data-access-and-security-threat-protection.md) bekapcsolásával olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek a kiszolgálók eléréséhez vagy kiaknázásához.

A [naplózás elérhető](concepts-audit.md) az adatbázisok tevékenységeinek nyomon követéséhez. 


## <a name="next-steps"></a>További lépések
- Tűzfalszabályok engedélyezése [IP](concepts-firewall-rules.md) -címekhez vagy [virtuális hálózatokhoz](concepts-data-access-and-security-vnet.md)
- Tudnivalók a [Azure Active Directory hitelesítésről](concepts-aad-authentication.md) Azure Database for PostgreSQL

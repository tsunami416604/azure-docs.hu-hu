---
title: Biztonság – Azure Database for MariaDB
description: A Azure Database for MariaDB biztonsági funkcióinak áttekintése.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8f41fe1005e96b428337bc73b9d468962a079596
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "79527826"
---
# <a name="security-in-azure-database-for-mariadb"></a>Biztonság az Azure Database for MariaDB-ben

Több biztonsági réteg is rendelkezésre áll, amelyek a Azure Database for MariaDB-kiszolgálón lévő adatvédelmet biztosítanak. Ez a cikk ezeket a biztonsági beállításokat ismerteti.

## <a name="information-protection-and-encryption"></a>Adatvédelem és titkosítás

### <a name="in-transit"></a>Átvitel közben
A Azure Database for MariaDB az adatátvitelt a Transport Layer Security segítségével titkosítja. A titkosítás (SSL/TLS) alapértelmezés szerint ki van kényszerítve.

### <a name="at-rest"></a>Nyugalmi állapotban
A Azure Database for MariaDB szolgáltatás az FIPS 140-2 ellenőrzött titkosítási modult használja a REST-alapú adattárolási titkosításhoz. Az adatokat, beleértve a biztonsági másolatokat, a lemezeken titkosítva, a lekérdezések futtatásakor létrehozott ideiglenes fájlok kivételével. A szolgáltatás az Azure Storage-titkosításban található AES 256 bites titkosítást használja, és a kulcsokat a rendszer felügyeli. A tárolótitkosítás mindig be van kapcsolva, és nem tiltható le.


## <a name="network-security"></a>Hálózati biztonság
A Azure Database for MariaDB-kiszolgálóval létesített kapcsolatok először egy regionális átjárón keresztül irányíthatók. Az átjáró nyilvánosan elérhető IP-címmel rendelkezik, a kiszolgáló IP-címei pedig védettek. Az átjáróval kapcsolatos további információkért tekintse meg a [kapcsolati architektúra című cikket](concepts-connectivity-architecture.md).  

Az újonnan létrehozott Azure Database for MariaDB-kiszolgáló rendelkezik egy tűzfallal, amely blokkolja az összes külső kapcsolatot. Az átjáró elérése azonban nem engedélyezett a kiszolgálóhoz való kapcsolódáshoz. 

### <a name="ip-firewall-rules"></a>IP-tűzfalszabályok
Az IP-tűzfalszabályok az egyes kérelmek feladó IP-címe alapján biztosítanak hozzáférést a kiszolgálókhoz. További információért tekintse meg a [Tűzfalszabályok áttekintését](concepts-firewall-rules.md) .

### <a name="virtual-network-firewall-rules"></a>Virtuális hálózat tűzfalszabályai
A Virtual Network szolgáltatás végpontja kiterjeszti a virtuális hálózati kapcsolatot az Azure-gerincen. A virtuális hálózati szabályok használatával engedélyezheti a Azure Database for MariaDB-kiszolgáló számára a virtuális hálózatban lévő kijelölt alhálózatok kapcsolatainak engedélyezését. További információt a [Virtual Network szolgáltatás végpontjának áttekintése](concepts-data-access-security-vnet.md)című témakörben talál.


## <a name="access-management"></a>Hozzáférés-kezelés

A Azure Database for MariaDB-kiszolgáló létrehozásakor meg kell adnia egy rendszergazdai felhasználó hitelesítő adatait. Ennek a rendszergazdának a segítségével további MariaDB-felhasználókat hozhat létre.


## <a name="threat-protection"></a>Fenyegetések elleni védelem

A komplex [veszélyforrások elleni védelem](concepts-data-access-and-security-threat-protection.md) bekapcsolásával olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek a kiszolgálók eléréséhez vagy kiaknázásához.

A [naplózás elérhető](concepts-audit-logs.md) az adatbázisok tevékenységeinek nyomon követéséhez. 


## <a name="next-steps"></a>További lépések
- Tűzfalszabályok engedélyezése [IP](concepts-firewall-rules.md) -címekhez vagy [virtuális hálózatokhoz](concepts-data-access-security-vnet.md)
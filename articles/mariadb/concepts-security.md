---
title: Biztonság – Azure-adatbázis a MariaDB-hez
description: A MariaDB Azure Database biztonsági funkcióinak áttekintése.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8f41fe1005e96b428337bc73b9d468962a079596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527826"
---
# <a name="security-in-azure-database-for-mariadb"></a>Biztonság az Azure Database for MariaDB-ben

Több biztonsági réteg áll rendelkezésre az Azure Database for MariaDB-kiszolgálón lévő adatok védelmére. Ez a cikk ismerteti ezeket a biztonsági beállításokat.

## <a name="information-protection-and-encryption"></a>Információvédelem és titkosítás

### <a name="in-transit"></a>Szállítás közbeni szállítás
A MariaDB Azure Database biztonságossá teszi az adatokat azáltal, hogy titkosítja az adatokat a Transport Layer Security segítségével. A titkosítás (SSL/TLS) kényszerítése alapértelmezés szerint történik.

### <a name="at-rest"></a>Nyugalmi
Az Azure Database for MariaDB szolgáltatás a FIPS 140-2 érvényesített kriptográfiai modult használja az inaktív adatok tárolásának titkosításához. Az adatok, beleértve a biztonsági másolatokat is, titkosítva vannak a lemezen, kivéve a lekérdezések futtatása közben létrehozott ideiglenes fájlokat. A szolgáltatás az Azure storage titkosításában található AES 256 bites titkosítást használja, és a kulcsok rendszeráltal felügyeltek. A tárolótitkosítás mindig be van kapcsolva, és nem tiltható le.


## <a name="network-security"></a>Hálózati biztonság
A MariaDB-kiszolgálóazure-adatbázisával létesített kapcsolatok először egy regionális átjárón keresztül kerülnek átirányítva. Az átjáró nyilvánosan elérhető IP-címmel rendelkezik, miközben a kiszolgáló IP-címei védettek. Az átjáróval kapcsolatos további információkért látogasson el a [kapcsolódási architektúra cikkre.](concepts-connectivity-architecture.md)  

Egy újonnan létrehozott Azure Database for MariaDB-kiszolgáló tűzfallal rendelkezik, amely blokkolja az összes külső kapcsolatot. Bár elérik az átjárót, nem csatlakozhatnak a kiszolgálóhoz. 

### <a name="ip-firewall-rules"></a>IP tűzfalszabályai
Az IP-tűzfal szabályai hozzáférést biztosítanak a kiszolgálókhoz az egyes kérelmek eredeti IP-címe alapján. További információt a [tűzfalszabályok áttekintésében](concepts-firewall-rules.md) talál.

### <a name="virtual-network-firewall-rules"></a>Virtuális hálózat tűzfalszabályai
A virtuális hálózati szolgáltatás végpontjai kiterjesztik a virtuális hálózati kapcsolatot az Azure gerinchálózatán keresztül. Virtuális hálózati szabályok használatával engedélyezheti az Azure Database for MariaDB-kiszolgáló számára a virtuális hálózat kiválasztott alhálózataiból származó kapcsolatok engedélyezését. További információt a [virtuális hálózati szolgáltatás végpontjának áttekintésében talál.](concepts-data-access-security-vnet.md)


## <a name="access-management"></a>Hozzáférés-kezelés

A MariaDB-kiszolgáló Azure-adatbázisának létrehozásakor hitelesítő adatokat adhat meg egy rendszergazdai felhasználónak. Ezzel a rendszergazdával további MariaDB-felhasználókat hozhat létre.


## <a name="threat-protection"></a>Fenyegetések elleni védelem

Engedélyezheti az [Advanced Threat Protection szolgáltatást,](concepts-data-access-and-security-threat-protection.md) amely észleli a szokatlan és potenciálisan káros kiszolgálók elérésére vagy kihasználására irányuló kísérleteket jelző rendellenes tevékenységeket.

[A naplózás](concepts-audit-logs.md) az adatbázisokban végzett tevékenységek nyomon követésére érhető el. 


## <a name="next-steps"></a>További lépések
- Tűzfalszabályok engedélyezése [IP-hálózatokhoz](concepts-firewall-rules.md) vagy [virtuális hálózatokhoz](concepts-data-access-security-vnet.md)
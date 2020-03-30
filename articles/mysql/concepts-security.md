---
title: Biztonság – Azure-adatbázis a MySQL-hez
description: Az Azure Database for MySQL biztonsági funkcióinak áttekintése.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: aac2641913331095550c0e19cc587257a996fcce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537023"
---
# <a name="security-in-azure-database-for-mysql"></a>Biztonság az Azure Database for MySQL-ben

Több biztonsági réteg áll rendelkezésre az Azure Database for MySQL-kiszolgálóadatainak védelmére. Ez a cikk ismerteti ezeket a biztonsági beállításokat.

## <a name="information-protection-and-encryption"></a>Információvédelem és titkosítás

### <a name="in-transit"></a>Szállítás közbeni szállítás
Az Azure Database for MySQL biztonságossá teszi az adatokat a Transport Layer Security-vel való átvitel közbeni adatok titkosításával. A titkosítás (SSL/TLS) kényszerítése alapértelmezés szerint történik.

### <a name="at-rest"></a>Nyugalmi
Az Azure Database for MySQL szolgáltatás a FIPS 140-2 érvényesített kriptográfiai modult használja az inaktív adatok tárolásának titkosításához. Az adatok, beleértve a biztonsági másolatokat is, titkosítva vannak a lemezen, kivéve a lekérdezések futtatása közben létrehozott ideiglenes fájlokat. A szolgáltatás az Azure storage titkosításában található AES 256 bites titkosítást használja, és a kulcsok rendszeráltal felügyeltek. A tárolótitkosítás mindig be van kapcsolva, és nem tiltható le.


## <a name="network-security"></a>Hálózati biztonság
A MySQL-kiszolgálóazure-adatbázisával létesített kapcsolatok először egy regionális átjárón keresztül kerülnek átirányítva. Az átjáró nyilvánosan elérhető IP-címmel rendelkezik, miközben a kiszolgáló IP-címei védettek. Az átjáróval kapcsolatos további információkért látogasson el a [kapcsolódási architektúra cikkre.](concepts-connectivity-architecture.md)  

Egy újonnan létrehozott Azure Database for MySQL-kiszolgáló tűzfallal rendelkezik, amely blokkolja az összes külső kapcsolatot. Bár elérik az átjárót, nem csatlakozhatnak a kiszolgálóhoz. 

### <a name="ip-firewall-rules"></a>IP tűzfalszabályai
Az IP-tűzfal szabályai hozzáférést biztosítanak a kiszolgálókhoz az egyes kérelmek eredeti IP-címe alapján. További információt a [tűzfalszabályok áttekintésében](concepts-firewall-rules.md) talál.

### <a name="virtual-network-firewall-rules"></a>Virtuális hálózat tűzfalszabályai
A virtuális hálózati szolgáltatás végpontjai kiterjesztik a virtuális hálózati kapcsolatot az Azure gerinchálózatán keresztül. Virtuális hálózati szabályok használatával engedélyezheti az Azure Database for MySQL-kiszolgáló, amely lehetővé teszi a kapcsolatot a kiválasztott alhálózatok egy virtuális hálózatban engedélyezheti. További információt a [virtuális hálózati szolgáltatás végpontjának áttekintésében talál.](concepts-data-access-and-security-vnet.md)

### <a name="private-ip"></a>Magánhálózati IP-cím
A Private Link lehetővé teszi, hogy egy privát végponton keresztül csatlakozzon az Azure-beli MySQL-adatbázisához az Azure-ban. Az Azure Private Link lényegében a privát virtuális hálózaton (VNet) belül hozza az Azure-szolgáltatásokat. A PaaS-erőforrások a privát IP-cím használatával érhető el, mint bármely más erőforrás a virtuális hálózatban. További információkért tekintse meg a [privát hivatkozás áttekintését](concepts-data-access-security-private-link.md)

## <a name="access-management"></a>Hozzáférés-kezelés

Az Azure Database for MySQL-kiszolgáló létrehozása során hitelesítő adatokat adhat meg egy rendszergazdai felhasználónak. Ez a rendszergazda további MySQL-felhasználók létrehozására használható.


## <a name="threat-protection"></a>Fenyegetések elleni védelem

Engedélyezheti az [Advanced Threat Protection szolgáltatást,](concepts-data-access-and-security-threat-protection.md) amely észleli a szokatlan és potenciálisan káros kiszolgálók elérésére vagy kihasználására irányuló kísérleteket jelző rendellenes tevékenységeket.

[A naplózás](concepts-audit-logs.md) az adatbázisokban végzett tevékenységek nyomon követésére érhető el. 


## <a name="next-steps"></a>További lépések
- Tűzfalszabályok engedélyezése [IP-hálózatokhoz](concepts-firewall-rules.md) vagy [virtuális hálózatokhoz](concepts-data-access-and-security-vnet.md)
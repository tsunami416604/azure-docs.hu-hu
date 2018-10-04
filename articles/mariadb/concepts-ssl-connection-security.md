---
title: Az Azure Database for MariaDB SSL-kapcsolatok
description: Azure Database for MariaDB és megfelelően használni az SSL-kapcsolatok társított alkalmazások konfigurálását.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1d0b27a8fd7e3882a73624fa1b668ac602a85e6b
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249502"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>Az Azure Database for MariaDB SSL-kapcsolatok
Azure Database for MariaDB támogatja a Secure Sockets Layer (SSL) használó ügyfélalkalmazások az adatbázis-kiszolgálóhoz csatlakozik. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.

## <a name="default-settings"></a>Alapértelmezett beállítások
Alapértelmezés szerint az adatbázis-szolgáltatás számára konfigurálni kell, hogy SSL-kapcsolatok megkövetelése MariaDB való csatlakozáskor.  Javasoljuk, hogy az ne tiltsa le az SSL-beállítást, amikor csak lehetséges.

Egy új, Azure Database for MariaDB-kiszolgálóhoz az Azure Portalon keresztül, és a parancssori felület kiépítésekor SSL-kapcsolatok kényszerítésének alapértelmezés szerint engedélyezve van.

Kapcsolati karakterláncok különböző programozási nyelveken jelennek meg az Azure Portalon. Ezek a kapcsolati karakterláncok az adatbázishoz való csatlakozáshoz szükséges SSL paramétereket tartalmazzák. Az Azure Portalon válassza ki a kiszolgálót. Alatt a **beállítások** szakaszban kattintson a **kapcsolati karakterláncok**. Az SSL paraméter attól függően változik, az összekötőt, például "ssl = true" vagy "sslmode = szükséges" vagy "sslmode = szükséges" és egyéb változatok.

Megtudhatja, hogyan engedélyezheti vagy tilthatja le az SSL-kapcsolatot, alkalmazások fejlesztése során, tekintse meg [SSL konfigurálása](howto-configure-ssl.md).

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [kiszolgáló tűzfalszabályait](concepts-firewall-rules.md)
- Ismerje meg, hogyan [az SSL konfigurálása](howto-configure-ssl.md).

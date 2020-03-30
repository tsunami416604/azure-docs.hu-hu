---
title: SSL-kapcsolat – Azure-adatbázis a MariaDB-hez
description: Információ az Azure Database MariaDB és a kapcsolódó alkalmazások megfelelő ssl-kapcsolatokhoz való konfigurálásáról
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 36532575645d135a7abe7239798b6f2abc4246f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477068"
---
# <a name="ssl-connectivity-in-azure-database-for-mariadb"></a>SSL-kapcsolat a MariaDB Azure Database szolgáltatásában
A MariaDB-adatbázis támogatja az adatbázis-kiszolgáló ügyfélalkalmazásokhoz való csatlakoztatását a Secure Sockets Layer (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.

## <a name="default-settings"></a>Alapértelmezett beállítások
Alapértelmezés szerint az adatbázis-szolgáltatást úgy kell konfigurálni, hogy SSL-kapcsolatokat igényeljen a MariaDB-hez való csatlakozáskor.  Javasoljuk, hogy amikor csak lehetséges, ne tiltsa le az SSL opciót.

Új Azure Database mariaDB-kiszolgálóhoz való kiépítése az Azure Portalon és a CLI-n keresztül, az SSL-kapcsolatok kényszerítése alapértelmezés szerint engedélyezve van.

A különböző programozási nyelvek csatlakozási karakterláncai az Azure Portalon jelennek meg. Ezek a kapcsolati karakterláncok tartalmazzák az adatbázishoz való csatlakozáshoz szükséges SSL-paramétereket. Az Azure Portalon válassza ki a kiszolgálót. A **Beállítások** fejléc alatt jelölje ki a **Kapcsolati karakterláncokat**. Az SSL-paraméter az összekötőtől függően változik, például "ssl=true" vagy "sslmode=require" vagy "sslmode=required" és egyéb változatok.

Az SSL-kapcsolat engedélyezéséről és letiltásáról az alkalmazás fejlesztése során az [SSL konfigurálása](howto-configure-ssl.md)című dokumentumban olvashat.

## <a name="next-steps"></a>További lépések
- További információ a [kiszolgálótűzfal-szabályokról](concepts-firewall-rules.md)
- További információ az [SSL konfigurálásáról.](howto-configure-ssl.md)

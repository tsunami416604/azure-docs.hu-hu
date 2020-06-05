---
title: SSL/TLS-kapcsolat – Azure Database for MariaDB
description: Információk a Azure Database for MariaDB és a társított alkalmazások az SSL-kapcsolatok megfelelő használatához való konfigurálásához
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 4111b0b01690097535412205b60619172e2c100a
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84416655"
---
# <a name="ssltls-connectivity-in-azure-database-for-mariadb"></a>SSL/TLS-kapcsolat a Azure Database for MariaDB
A Azure Database for MariaDB a SSL (SSL) használatával támogatja az adatbázis-kiszolgáló és az ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.

## <a name="default-settings"></a>Alapértelmezett beállítások
Alapértelmezés szerint az adatbázis-szolgáltatást úgy kell konfigurálni, hogy az MariaDB-hez való csatlakozáskor SSL-kapcsolatokat igényeljen.  Javasoljuk, hogy ha lehetséges, ne tiltsa le az SSL-beállítást.

Amikor új Azure Database for MariaDB kiszolgálót épít ki a Azure Portal és a parancssori felületen, az SSL-kapcsolatok kényszerítése alapértelmezés szerint engedélyezve van.

A különböző programozási nyelvekhez tartozó kapcsolatok karakterláncai a Azure Portal láthatók. Ezek a kapcsolati karakterláncok tartalmazzák az adatbázishoz való kapcsolódáshoz szükséges SSL-paramétereket. A Azure Portal válassza ki a kiszolgálót. A **Beállítások** fejléc alatt válassza ki a **kapcsolatok karakterláncait**. Az SSL-paraméter az összekötőtől függően változik, például: "SSL = true" vagy "sslmode = require" vagy "sslmode = Required" és egyéb variációk.

Ha szeretné megtudni, hogyan engedélyezheti vagy tilthatja le az SSL-kapcsolatokat az alkalmazások fejlesztésekor, tekintse meg az [SSL konfigurálását](howto-configure-ssl.md)ismertető témakört.

## <a name="tls-enforcement-in-azure-database-for-mariadb"></a>TLS-kényszerítés Azure Database for MariaDB

Azure Database for MariaDB támogatja a titkosítást az adatbázis-kiszolgálóhoz Transport Layer Security (TLS) használatával csatlakozó ügyfelek számára. A TLS egy iparági szabványnak megfelelő protokoll, amely gondoskodik az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti biztonságos hálózati kapcsolatokról, ami lehetővé teszi a megfelelőségi követelmények betartását.

### <a name="tls-settings"></a>TLS-beállítások

A Azure Database for MariaDB lehetővé teszi az ügyfélkapcsolatok TLS-verziójának betartatását. A TLS-verzió érvénybe léptetéséhez használja a **TLS-verzió minimális** beállítását. Ehhez a beállításhoz a következő értékek engedélyezettek:

|  Minimális TLS-beállítás             | Az ügyfél TLS-verziója támogatott                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (alapértelmezett) | Nincs szükség TLS-re                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 és újabb         |
| TLS1_1                           | TLS 1,1, TLS 1,2 és újabb              |
| TLS1_2                           | TLS 1,2-es és újabb verzió                  |


Ha például a TLS 1,0-es minimális beállítás értéke a TLS, azt jelenti, hogy a kiszolgáló engedélyezi a TLS 1,0, 1,1 és 1.2 + protokollt használó ügyfelek kapcsolódását. Azt is megteheti, hogy a 1,2 értékre állítja azt, hogy csak a TLS 1.2 + protokollt használó ügyfelek kapcsolatait engedélyezzük, és a TLS 1,0 és a TLS 1,1 összes kapcsolata el lesz utasítva.

> [!Note] 
> Azure Database for MariaDB a TLS alapértelmezés szerint le van tiltva az összes új kiszolgálón. 
>
> Jelenleg a Azure Database for MariaDB által támogatott TLS-verziók a TLS 1,0, 1,1 és 1,2. Miután kényszerített egy adott minimális TLS-verzióra, a letiltottra nem módosítható.

Ha meg szeretné tudni, hogyan állíthatja be a TLS-beállítást a Azure Database for MariaDBhoz, tekintse meg a [TLS-beállítás konfigurálását](howto-tls-configurations.md)ismertető témakört.

## <a name="next-steps"></a>Következő lépések
- További információ a [kiszolgáló tűzfalszabályok szabályairól](concepts-firewall-rules.md)
- Ismerje meg, hogyan [konfigurálhatja az SSL](howto-configure-ssl.md) -t
- Útmutató a [TLS konfigurálásához](howto-tls-configurations.md)

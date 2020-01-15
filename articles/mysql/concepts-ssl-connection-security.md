---
title: SSL-kapcsolat – Azure Database for MySQL
description: Információk a Azure Database for MySQL és a társított alkalmazások az SSL-kapcsolatok megfelelő használatához való konfigurálásához
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 6c1f52c69104e19ff0a7a7cae255dd7029e1a4b8
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941792"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>SSL-kapcsolat a Azure Database for MySQL

A Azure Database for MySQL a SSL (SSL) használatával támogatja az adatbázis-kiszolgáló és az ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.

## <a name="default-settings"></a>Alapértelmezett beállítások

Alapértelmezés szerint az adatbázis-szolgáltatást úgy kell konfigurálni, hogy a MySQL-hez való csatlakozáskor SSL-kapcsolatokat igényeljen.  Javasoljuk, hogy ha lehetséges, ne tiltsa le az SSL-beállítást.

Amikor új Azure Database for MySQL kiszolgálót épít ki a Azure Portal és a parancssori felületen, az SSL-kapcsolatok kényszerítése alapértelmezés szerint engedélyezve van. 

A különböző programozási nyelvekhez tartozó kapcsolatok karakterláncai a Azure Portal láthatók. Ezek a kapcsolati karakterláncok tartalmazzák az adatbázishoz való kapcsolódáshoz szükséges SSL-paramétereket. A Azure Portal válassza ki a kiszolgálót. A **Beállítások** fejléc alatt válassza ki a **kapcsolatok karakterláncait**. Az SSL-paraméter az összekötőtől függően változik, például: "SSL = true" vagy "sslmode = require" vagy "sslmode = Required" és egyéb variációk.

> [!NOTE]
> Jelenleg a Azure Database for MySQL által támogatott TLS-verzió a TLS 1,0, a TLS 1,1, a TLS 1,2.

Ha szeretné megtudni, hogyan engedélyezheti vagy tilthatja le az SSL-kapcsolatokat az alkalmazások fejlesztésekor, tekintse meg az [SSL konfigurálását](howto-configure-ssl.md)ismertető témakört.

## <a name="next-steps"></a>Következő lépések

[Azure Database for MySQLhoz tartozó kapcsolatok kódtárai](concepts-connection-libraries.md)

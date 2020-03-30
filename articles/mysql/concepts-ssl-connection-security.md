---
title: SSL-kapcsolat – Azure Database for MySQL
description: Információ az Azure Database MySQL-hez és a kapcsolódó alkalmazásokmegfelelő SSL-kapcsolatokhoz való konfigurálásához
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 6a12ef851823ab5eff2b11905d05be1950c82ef0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474271"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>SSL-kapcsolat a MySQL-alapú Azure Databaseben

Az Azure Database for MySQL támogatja az adatbázis-kiszolgáló ügyfélalkalmazásokhoz való csatlakoztatását a Secure Sockets Layer (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.

## <a name="ssl-default-settings"></a>SSL alapértelmezett beállításai

Alapértelmezés szerint az adatbázis-szolgáltatást úgy kell konfigurálni, hogy SSL-kapcsolatokat igényeljen a MySQL-hez való csatlakozáskor.  Javasoljuk, hogy amikor csak lehetséges, ne tiltsa le az SSL opciót.

Új Azure-adatbázis kiépítése a MySQL-kiszolgáló az Azure Portalon és a CLI-n keresztül, ssl-kapcsolatok kényszerítése alapértelmezés szerint engedélyezve van. 

A különböző programozási nyelvek csatlakozási karakterláncai az Azure Portalon jelennek meg. Ezek a kapcsolati karakterláncok tartalmazzák az adatbázishoz való csatlakozáshoz szükséges SSL-paramétereket. Az Azure Portalon válassza ki a kiszolgálót. A **Beállítások** fejléc alatt jelölje ki a **Kapcsolati karakterláncokat**. Az SSL-paraméter az összekötőtől függően változik, például "ssl=true" vagy "sslmode=require" vagy "sslmode=required" és egyéb változatok.

Az SSL-kapcsolat engedélyezéséről és letiltásáról az alkalmazás fejlesztése során az [SSL konfigurálása](howto-configure-ssl.md)című dokumentumban olvashat.

## <a name="next-steps"></a>További lépések

[Kapcsolatkönyvtárak a MySQL-hez készült Azure Database kapcsolattárakhoz](concepts-connection-libraries.md)

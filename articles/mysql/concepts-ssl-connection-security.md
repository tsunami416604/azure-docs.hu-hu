---
title: "SSL-kapcsolatot a MySQL az Azure-adatbázis |} Microsoft Docs"
description: "Azure-adatbázis a MySQL és a társított alkalmazások megfelelően használni az SSL-kapcsolatok konfigurálását."
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 05/10/2017
ms.openlocfilehash: 4b03b3a2dbfad92cc0cfa84777b38ddff90452cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>SSL-kapcsolatot MySQL az Azure-adatbázis
Azure MySQL-adatbázis támogatja az adatbázis-kiszolgáló csatlakozik a Secure Sockets Layer (SSL) használó ügyfélalkalmazások. Az adatbázis-kiszolgáló és az ügyfél alkalmazások közötti SSL-kapcsolatok kényszerítése segít lánctámadások elleni védelem érdekében "man a középső" az adatfolyamot a kiszolgáló és az alkalmazás közötti titkosításával.

## <a name="default-settings"></a>Alapértelmezett beállítások
Alapértelmezés szerint az adatbázis-szolgáltatás úgy konfigurálni, hogy SSL-kapcsolatok megkövetelése MySQL történő csatlakozás során.  Javasoljuk, ne tiltsa le az SSL-beállítást, amikor csak lehetséges. 

Ha egy új Azure Database az Azure portálon keresztül MySQL-kiszolgáló és a parancssori felület, SSL-kapcsolatok végrehajtásának alapértelmezés szerint engedélyezve van. 

Hasonlóképpen a "Kapcsolati karakterláncok" beállításokat a kiszolgálón az Azure-portálon előre definiált kapcsolati karakterláncok közös nyelvek SSL használatával az adatbázis-kiszolgálóhoz való csatlakozáshoz szükséges paraméterek közé tartoznak. Az SSL-paraméter attól függően változik, az összekötő, például "ssl = true" vagy "sslmode = szükséges" vagy "sslmode = szükséges" és egyéb változatok.

Annak megismerése, hogyan engedélyezheti vagy tilthatja le az SSL-kapcsolatot, alkalmazások fejlesztése során, tekintse meg [SSL konfigurálása](howto-configure-ssl.md).

## <a name="next-steps"></a>Következő lépések
[Adatkapcsolattárak MySQL az Azure-adatbázis](concepts-connection-libraries.md)

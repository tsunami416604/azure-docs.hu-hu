---
title: "SSL-kapcsolatot a MySQL az Azure-adatbázis"
description: "Azure-adatbázis a MySQL és a társított alkalmazások megfelelően használni az SSL-kapcsolatok konfigurálását."
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: f59d5eab9772515a3c59f887a48d597d27bab135
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>SSL-kapcsolatot MySQL az Azure-adatbázis
Azure MySQL-adatbázis támogatja az adatbázis-kiszolgáló csatlakozik a Secure Sockets Layer (SSL) használó ügyfélalkalmazások. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.

## <a name="default-settings"></a>Alapértelmezett beállítások
Alapértelmezés szerint az adatbázis-szolgáltatás úgy konfigurálni, hogy SSL-kapcsolatok megkövetelése MySQL történő csatlakozás során.  Azt javasoljuk, hogy ne tiltsa le az SSL-beállítást, amikor csak lehetséges. 

Ha egy új Azure Database az Azure portálon keresztül MySQL-kiszolgáló és a parancssori felület, SSL-kapcsolatok végrehajtásának alapértelmezés szerint engedélyezve van. 

Különböző programnyelveken-kapcsolati karakterláncok az Azure-portálon jelennek meg. Ezen kapcsolati karakterláncok tartalmazza a szükséges SSL-paraméterek az adatbázishoz való kapcsolódáshoz. Az Azure portálon válassza ki a kiszolgálót. Az a **beállítások** elemcsoportban válassza ki a **kapcsolati karakterláncok**. Az SSL-paraméter attól függően változik, az összekötő, például "ssl = true" vagy "sslmode = szükséges" vagy "sslmode = szükséges" és egyéb változatok.

Annak megismerése, hogyan engedélyezheti vagy tilthatja le az SSL-kapcsolatot, alkalmazások fejlesztése során, tekintse meg [SSL konfigurálása](howto-configure-ssl.md). 

## <a name="next-steps"></a>További lépések
[Adatkapcsolattárak MySQL az Azure-adatbázis](concepts-connection-libraries.md)

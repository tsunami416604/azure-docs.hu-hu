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
ms.date: 11/02/2017
ms.openlocfilehash: 37e2bbbe1ed4b6a9cca0e0b001e5e632b9b73be2
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>SSL-kapcsolatot MySQL az Azure-adatbázis
Azure MySQL-adatbázis támogatja az adatbázis-kiszolgáló csatlakozik a Secure Sockets Layer (SSL) használó ügyfélalkalmazások. Az adatbázis-kiszolgáló és az ügyfél alkalmazások közötti SSL-kapcsolatok kényszerítése segít lánctámadások elleni védelem érdekében "man a középső" az adatfolyamot a kiszolgáló és az alkalmazás közötti titkosításával.

## <a name="default-settings"></a>Alapértelmezett beállítások
Alapértelmezés szerint az adatbázis-szolgáltatás úgy konfigurálni, hogy SSL-kapcsolatok megkövetelése MySQL történő csatlakozás során.  Azt javasoljuk, hogy ne tiltsa le az SSL-beállítást, amikor csak lehetséges. 

Ha egy új Azure Database az Azure portálon keresztül MySQL-kiszolgáló és a parancssori felület, SSL-kapcsolatok végrehajtásának alapértelmezés szerint engedélyezve van. 

Különböző programnyelveken-kapcsolati karakterláncok az Azure-portálon jelennek meg. Ezen kapcsolati karakterláncok tartalmazza a szükséges SSL-paraméterek az adatbázishoz való kapcsolódáshoz. Az Azure portálon válassza ki a kiszolgálót. Az a **beállítások** elemcsoportban válassza ki a **kapcsolati karakterláncok**. Az SSL-paraméter attól függően változik, az összekötő, például "ssl = true" vagy "sslmode = szükséges" vagy "sslmode = szükséges" és egyéb változatok.

Annak megismerése, hogyan engedélyezheti vagy tilthatja le az SSL-kapcsolatot, alkalmazások fejlesztése során, tekintse meg [SSL konfigurálása](howto-configure-ssl.md). 

## <a name="next-steps"></a>Következő lépések
[Adatkapcsolattárak MySQL az Azure-adatbázis](concepts-connection-libraries.md)

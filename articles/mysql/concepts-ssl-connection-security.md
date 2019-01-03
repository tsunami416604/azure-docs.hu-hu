---
title: Az Azure Database for MySQL-hez SSL-kapcsolatok
description: Azure Database for MySQL és a társított alkalmazások megfelelően használni az SSL-kapcsolatok konfigurálását.
author: JasonMAnderson
ms.author: janders
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 129f90d495627edb25dfafdeb1b274aa2c4c71cb
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53539834"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>Az Azure Database for MySQL-hez SSL-kapcsolatok
Azure Database for MySQL támogatja a Secure Sockets Layer (SSL) használó ügyfélalkalmazások az adatbázis-kiszolgálóhoz csatlakozik. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.

## <a name="default-settings"></a>Alapértelmezett beállítások
Alapértelmezés szerint az adatbázis-szolgáltatás számára konfigurálni kell, hogy SSL-kapcsolatok megkövetelése a MySQL-hez való kapcsolódáskor.  Javasoljuk, hogy az ne tiltsa le az SSL-beállítást, amikor csak lehetséges. 

Egy új, Azure Database for MySQL-kiszolgálóhoz az Azure Portalon keresztül, és a parancssori felület kiépítésekor SSL-kapcsolatok kényszerítésének alapértelmezés szerint engedélyezve van. 

Kapcsolati karakterláncok különböző programozási nyelveken jelennek meg az Azure Portalon. Ezek a kapcsolati karakterláncok az adatbázishoz való csatlakozáshoz szükséges SSL paramétereket tartalmazzák. Az Azure Portalon válassza ki a kiszolgálót. Alatt a **beállítások** szakaszban kattintson a **kapcsolati karakterláncok**. Az SSL paraméter attól függően változik, az összekötőt, például "ssl = true" vagy "sslmode = szükséges" vagy "sslmode = szükséges" és egyéb változatok.

Megtudhatja, hogyan engedélyezheti vagy tilthatja le az SSL-kapcsolatot, alkalmazások fejlesztése során, tekintse meg [SSL konfigurálása](howto-configure-ssl.md). 

## <a name="next-steps"></a>További lépések
[Az Azure Database for MySQL csatlakozási kódtárak](concepts-connection-libraries.md)

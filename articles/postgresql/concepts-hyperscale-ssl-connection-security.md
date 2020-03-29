---
title: SSL - Nagykapacitású (Citus) - Azure-adatbázis a PostgreSQL-hez
description: Utasítások és információk az Azure Database for PostgreSQL – Hyperscale (Citus) és a kapcsolódó alkalmazások konfigurálásához az SSL-kapcsolatok megfelelő használatához.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973985"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>SSL konfigurálása az Azure Database for PostgreSQL - Hyperscale (Citus) számára
A nagy kapacitású (Citus) koordinátori csomóponttal létesített ügyfélalkalmazás-kapcsolatokhoz secure sockets layer (SSL) szükséges. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával segít a "középre" irányuló támadások elleni védelemben.

## <a name="enforcing-ssl-connections"></a>Ssl-kapcsolatok kényszerítése
Az Azure Portalon keresztül kiépített összes Azure Database for PostgreSQL-kiszolgáló esetében az SSL-kapcsolatok kényszerítése alapértelmezés szerint engedélyezve van. 

Hasonlóképpen, a kapcsolati karakterláncok, amelyek előre meghatározott a "Kapcsolati karakterláncok" beállítások a kiszolgáló az Azure Portalon tartalmazza a szükséges paramétereket a közös nyelvek csatlakozni az adatbázis-kiszolgáló ssl használatával. Az SSL-paraméter az összekötőtől függően változik, például "ssl=true" vagy "sslmode=require" vagy "sslmode=required" és egyéb változatok.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Győződjön meg arról, hogy az alkalmazás vagy a keretrendszer támogatja az SSL-kapcsolatokat
Egyes alkalmazáskeretek, amelyek a PostgreSQL-t használják az adatbázis-szolgáltatásaikhoz, alapértelmezés szerint nem engedélyezik az SSL-t a telepítés során. Ha a PostgreSQL-kiszolgáló kényszeríti az SSL-kapcsolatokat, de az alkalmazás nincs SSL-hez konfigurálva, előfordulhat, hogy az alkalmazás nem tud csatlakozni az adatbázis-kiszolgálóhoz. Az SSL-kapcsolatok engedélyezéséről az alkalmazás dokumentációjában tájékozódhat.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Az SSL-kapcsolat tanúsítványellenőrzését igénylő alkalmazások
Bizonyos esetekben az alkalmazások biztonságos csatlakozásához megbízható hitelesítésszolgáltatói (CA) tanúsítványfájlból (.cer) létrehozott helyi tanúsítványfájlra van szükség. A PostgreSQL - Hyperscale (Citus) Azure-adatbázishoz való https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pemcsatlakozáshoz szükséges tanúsítvány a helyen található. Töltse le a tanúsítványfájlt, és mentse a kívánt helyre.

### <a name="connect-using-psql"></a>Csatlakozás a psql használatával
A következő példa bemutatja, hogyan csatlakozhat a nagykapacitású (Citus) koordinátor csomóponthoz a psql parancssori segédprogrammal. A `sslmode=verify-full` kapcsolati karakterlánc beállítással kényszerítheti az SSL-tanúsítványok ellenőrzését. Adja át a helyi `sslrootcert` tanúsítványfájl elérési útját a paraméternek.

Az alábbiakban egy példa a psql kapcsolat string:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Ellenőrizze, hogy `sslrootcert` az átadott érték megegyezik-e a mentett tanúsítvány fájlelérési útvonalának.

## <a name="next-steps"></a>További lépések
Növelje a biztonságot [az Azure Database for PostgreSQL – Hyperscale (Citus) tűzfalszabályaival.](concepts-hyperscale-firewall-rules.md)

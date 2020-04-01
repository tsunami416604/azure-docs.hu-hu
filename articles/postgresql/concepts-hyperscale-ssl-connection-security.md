---
title: TLS - Nagykapacitású (Citus) - Azure-adatbázis a PostgreSQL-hez
description: Utasítások és információk az Azure Database for PostgreSQL – Hyperscale (Citus) és a kapcsolódó alkalmazások konfigurálásához a TLS-kapcsolatok megfelelő használatához.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 8a691b6d3f28800ab1cb38a72f124e950d485084
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422336"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>TLS konfigurálása az Azure Database for PostgreSQL - Hyperscale (Citus) számára
A nagykapacitású (Citus) koordinátor-csomóponttal létesített ügyfélalkalmazás-kapcsolatokhoz átviteli rétegbiztonság (TLS), korábbi nevén Secure Sockets Layer (SSL) szükséges. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti TLS-kapcsolatok érvényesítése a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával segít a "középre" irányuló támadások elleni védelemben.

## <a name="enforcing-tls-connections"></a>TLS-kapcsolatok kényszerítése
Az Azure Portalon keresztül kiépített összes Azure Database for PostgreSQL-kiszolgáló esetében alapértelmezés szerint engedélyezve van a TLS-kapcsolatok kényszerítése. 

Hasonlóképpen, a kapcsolati karakterláncok, amelyek előre meghatározott a "Kapcsolati karakterláncok" beállítások a kiszolgáló az Azure Portalon tartalmazza a szükséges paramétereket a közös nyelvek csatlakozni az adatbázis-kiszolgálóhoz a TLS használatával. A TLS-paraméter az összekötőtől függően változik, például "ssl=true" vagy "sslmode=require" vagy "sslmode=required" és egyéb változatok.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Győződjön meg arról, hogy az alkalmazás vagy a keretrendszer támogatja a TLS-kapcsolatokat
Egyes alkalmazáskeretek, amelyek a PostgreSQL-t használják az adatbázis-szolgáltatásaikhoz, alapértelmezés szerint nem engedélyezik a TLS-t a telepítés során. Ha a PostgreSQL-kiszolgáló kényszeríti a TLS-kapcsolatokat, de az alkalmazás nincs konfigurálva a TLS-hez, előfordulhat, hogy az alkalmazás nem tud csatlakozni az adatbázis-kiszolgálóhoz. A TLS-kapcsolatok engedélyezéséről az alkalmazás dokumentációjában tájékozódhat.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>A TLS-kapcsolat tanúsítványellenőrzését igénylő alkalmazások
Bizonyos esetekben az alkalmazások biztonságos csatlakozásához megbízható hitelesítésszolgáltatói (CA) tanúsítványfájlból (.cer) létrehozott helyi tanúsítványfájlra van szükség. A PostgreSQL - Hyperscale (Citus) Azure-adatbázishoz való https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pemcsatlakozáshoz szükséges tanúsítvány a helyen található. Töltse le a tanúsítványfájlt, és mentse a kívánt helyre.

### <a name="connect-using-psql"></a>Csatlakozás a psql használatával
A következő példa bemutatja, hogyan csatlakozhat a nagykapacitású (Citus) koordinátor csomóponthoz a psql parancssori segédprogrammal. A `sslmode=verify-full` kapcsolati karakterlánc beállításával kényszerítheti a TLS-tanúsítványok ellenőrzését. Adja át a helyi `sslrootcert` tanúsítványfájl elérési útját a paraméternek.

Az alábbiakban egy példa a psql kapcsolat string:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Ellenőrizze, hogy `sslrootcert` az átadott érték megegyezik-e a mentett tanúsítvány fájlelérési útvonalának.

## <a name="next-steps"></a>További lépések
Növelje a biztonságot [az Azure Database for PostgreSQL – Hyperscale (Citus) tűzfalszabályaival.](concepts-hyperscale-firewall-rules.md)

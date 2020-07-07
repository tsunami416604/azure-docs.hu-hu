---
title: TLS-nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Útmutatás és információk a Azure Database for PostgreSQL-nagy kapacitású (Citus) és a társított alkalmazások konfigurálásához a TLS-kapcsolatok megfelelő használatához.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 791eed9419375c7245488b8ec61a1c5481be382e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82580564"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>A TLS konfigurálása Azure Database for PostgreSQL-nagy kapacitású (Citus)
Az nagy kapacitású (Citus) koordinátori csomóponthoz tartozó ügyfélalkalmazás Transport Layer Security (TLS), korábbi nevén SSL (SSL) szükséges. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti TLS-kapcsolatok érvényesítésével megvédheti a védelmet a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával.

## <a name="enforcing-tls-connections"></a>TLS-kapcsolatok kényszerítése
A Azure Portal által kiépített összes Azure Database for PostgreSQL-kiszolgáló esetében a TLS-kapcsolatok kényszerítése alapértelmezés szerint engedélyezve van. 

Hasonlóképpen, a "kapcsolati karakterláncok" beállításokban előre definiált kapcsolati karakterláncok a Azure Portalban tartalmazzák az adatbázis-kiszolgáló TLS-vel való csatlakozásához szükséges paramétereket a közös nyelvekhez. A TLS-paraméter az összekötőtől függően változik, például: "SSL = true" vagy "sslmode = require" vagy "sslmode = Required" és egyéb variációk.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Győződjön meg arról, hogy az alkalmazás vagy a keretrendszer támogatja a TLS-kapcsolatokat
Néhány alkalmazás-keretrendszer, amely a PostgreSQL-t használja az adatbázis-szolgáltatásaihoz, alapértelmezés szerint nem engedélyezi a TLS-t a telepítés során. Ha a PostgreSQL-kiszolgáló kikényszeríti a TLS-kapcsolatokat, de az alkalmazás nincs konfigurálva a TLS-hez, előfordulhat, hogy az alkalmazás nem tud csatlakozni az adatbázis-kiszolgálóhoz. A TLS-kapcsolatok engedélyezésével kapcsolatos információkért tekintse meg az alkalmazás dokumentációját.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>A TLS-kapcsolat tanúsítvány-ellenőrzését igénylő alkalmazások
Bizonyos esetekben az alkalmazásoknak egy megbízható hitelesítésszolgáltató (CA) tanúsítványfájl (. cer) alapján létrehozott helyi tanúsítványfájl szükségesek a biztonságos kapcsolódáshoz. A Azure Database for PostgreSQL-nagy kapacitású (Citus) szolgáltatáshoz való kapcsolódáshoz szükséges tanúsítvány a következő helyen található: https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem . Töltse le a tanúsítványfájl, és mentse a kívánt helyre.

### <a name="connect-using-psql"></a>Összekapcsolás a psql használatával
Az alábbi példa bemutatja, hogyan csatlakozhat a nagy kapacitású (Citus) koordinátor-csomóponthoz a psql parancssori segédprogram használatával. A `sslmode=verify-full` TLS-tanúsítvány ellenőrzésének érvényesítéséhez használja a kapcsolódási karakterlánc beállítást. Adja át a helyi tanúsítványfájl elérési útját a `sslrootcert` paraméternek.

Az alábbi példa a psql-kapcsolatok karakterláncát mutatja be:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Ellenőrizze, hogy az átadott érték megegyezik-e a `sslrootcert` mentett tanúsítvány elérési útjával.

## <a name="next-steps"></a>További lépések
[A Azure Database for PostgreSQL-nagy kapacitású (Citus) tűzfalszabályok](concepts-hyperscale-firewall-rules.md)további biztonságának fokozása.

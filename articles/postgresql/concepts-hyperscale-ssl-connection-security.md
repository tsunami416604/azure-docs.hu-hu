---
title: SSL – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Utasítások és információk a Azure Database for PostgreSQL-nagy kapacitású (Citus) és a társított alkalmazások konfigurálásához az SSL-kapcsolatok megfelelő használatához.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973985"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Az SSL konfigurálása Azure Database for PostgreSQL-nagy kapacitású (Citus)
Az nagy kapacitású (Citus) koordinátori csomóponthoz tartozó ügyfélalkalmazás-kapcsolatokhoz SSL (SSL) szükséges. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítésével megvédheti a védelmet a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával.

## <a name="enforcing-ssl-connections"></a>SSL-kapcsolatok kényszerítése
A Azure Portal által kiépített összes Azure Database for PostgreSQL-kiszolgáló esetében az SSL-kapcsolatok kényszerítése alapértelmezés szerint engedélyezve van. 

Hasonlóképpen, a "kapcsolati karakterláncok" beállításokban előre definiált kapcsolati karakterláncok a Azure Portalban tartalmazzák az adatbázis-kiszolgálóhoz az SSL használatával való csatlakozáshoz szükséges paramétereket a közös nyelvekhez. Az SSL-paraméter az összekötőtől függően változik, például: "SSL = true" vagy "sslmode = require" vagy "sslmode = Required" és egyéb variációk.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Győződjön meg arról, hogy az alkalmazás vagy a keretrendszer támogatja az SSL-kapcsolatokat
Néhány alkalmazás-keretrendszer, amely a PostgreSQL-t használja az adatbázis-szolgáltatásaihoz, alapértelmezés szerint nem engedélyezi az SSL használatát a telepítés során. Ha a PostgreSQL-kiszolgáló kikényszeríti az SSL-kapcsolatokat, de az alkalmazás nincs konfigurálva az SSL használatára, előfordulhat, hogy az alkalmazás nem tud csatlakozni az adatbázis-kiszolgálóhoz. Az SSL-kapcsolatok engedélyezésével kapcsolatos információkért tekintse meg az alkalmazás dokumentációját.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Az SSL-kapcsolat tanúsítvány-ellenőrzését igénylő alkalmazások
Bizonyos esetekben az alkalmazásoknak egy megbízható hitelesítésszolgáltató (CA) tanúsítványfájl (. cer) alapján létrehozott helyi tanúsítványfájl szükségesek a biztonságos kapcsolódáshoz. Azure Database for PostgreSQL-nagy kapacitású (Citus) szolgáltatáshoz való kapcsolódáshoz szükséges tanúsítvány a következő helyen található: https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Töltse le a tanúsítványfájl, és mentse a kívánt helyre.

### <a name="connect-using-psql"></a>Összekapcsolás a psql használatával
Az alábbi példa bemutatja, hogyan csatlakozhat a nagy kapacitású (Citus) koordinátor-csomóponthoz a psql parancssori segédprogram használatával. Az SSL-tanúsítvány ellenőrzésének érvényesítéséhez használja a `sslmode=verify-full` kapcsolati karakterlánc beállítást. Adja át a helyi tanúsítványfájl elérési útját a `sslrootcert` paraméternek.

Az alábbi példa a psql-kapcsolatok karakterláncát mutatja be:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Győződjön meg arról, hogy a `sslrootcert` átadott érték megegyezik a mentett tanúsítvány elérési útjával.

## <a name="next-steps"></a>Következő lépések
[A Azure Database for PostgreSQL-nagy kapacitású (Citus) tűzfalszabályok](concepts-hyperscale-firewall-rules.md)további biztonságának fokozása.

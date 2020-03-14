---
title: SSL – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Útmutató és információk az SSL-kapcsolat konfigurálásához Azure Database for PostgreSQL-egyetlen kiszolgáló esetén.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c235562834ae78a12b690fcd1b96d6a3640e0c66
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371664"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Az SSL-kapcsolat konfigurálása Azure Database for PostgreSQL – egyetlen kiszolgálón

Azure Database for PostgreSQL az ügyfélalkalmazások a PostgreSQL szolgáltatáshoz való csatlakoztatását részesíti előnyben SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítésével megvédheti a védelmet a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával.

Alapértelmezés szerint a PostgreSQL-adatbázis szolgáltatás az SSL-kapcsolat megkövetelésére van konfigurálva. Letilthatja az SSL megkövetelését, ha az ügyfélalkalmazás nem támogatja az SSL-kapcsolatot.

## <a name="enforcing-ssl-connections"></a>SSL-kapcsolatok kényszerítése

A Azure Portal és a CLI használatával kiépített összes Azure Database for PostgreSQL-kiszolgáló esetében az SSL-kapcsolatok kényszerítése alapértelmezés szerint engedélyezve van. 

Hasonlóképpen, a "kapcsolati karakterláncok" beállításokban előre definiált kapcsolati karakterláncok a Azure Portalban tartalmazzák az adatbázis-kiszolgálóhoz az SSL használatával való csatlakozáshoz szükséges paramétereket a közös nyelvekhez. Az SSL-paraméter az összekötőtől függően változik, például: "SSL = true" vagy "sslmode = require" vagy "sslmode = Required" és egyéb variációk.

## <a name="configure-enforcement-of-ssl"></a>Az SSL kényszerítésének konfigurálása

Igény szerint letilthatja az SSL-kapcsolat kényszerítését. Microsoft Azure azt javasolja, hogy mindig engedélyezze az **SSL-kapcsolat kikényszerített** beállítását a fokozott biztonság érdekében.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Keresse fel Azure Database for PostgreSQL-kiszolgálóját, és kattintson a **kapcsolatbiztonsági**elemre. A váltógomb használatával engedélyezheti vagy letilthatja az **SSL-kapcsolat érvényesítése** beállítást. Ezután kattintson a **Mentés**gombra.

![Kapcsolatbiztonsági – a kényszerített SSL letiltása](./media/concepts-ssl-connection-security/1-disable-ssl.png)

A beállítás megerősítéséhez tekintse meg az **Áttekintés** lapot az SSL- **érvényesítési állapot** jelző megjelenítéséhez.

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Az **SSL-kényszerítési** paramétert engedélyezheti vagy letilthatja az Azure CLI `Enabled` vagy `Disabled` értékének használatával.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Győződjön meg arról, hogy az alkalmazás vagy a keretrendszer támogatja az SSL-kapcsolatokat

Néhány alkalmazás-keretrendszer, amely a PostgreSQL-t használja az adatbázis-szolgáltatásaihoz, alapértelmezés szerint nem engedélyezi az SSL használatát a telepítés során. Ha a PostgreSQL-kiszolgáló kikényszeríti az SSL-kapcsolatokat, de az alkalmazás nincs konfigurálva az SSL használatára, előfordulhat, hogy az alkalmazás nem tud csatlakozni az adatbázis-kiszolgálóhoz. Az SSL-kapcsolatok engedélyezésével kapcsolatos információkért tekintse meg az alkalmazás dokumentációját.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Az SSL-kapcsolat tanúsítvány-ellenőrzését igénylő alkalmazások

Bizonyos esetekben az alkalmazásoknak egy megbízható hitelesítésszolgáltató (CA) tanúsítványfájl (. cer) alapján létrehozott helyi tanúsítványfájl szükségesek a biztonságos kapcsolódáshoz. Azure Database for PostgreSQL-kiszolgálóhoz való kapcsolódáshoz szükséges tanúsítvány a következő helyen található: https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Töltse le a tanúsítványfájl, és mentse a kívánt helyre.

### <a name="connect-using-psql"></a>Összekapcsolás a psql használatával

Az alábbi példa bemutatja, hogyan csatlakozhat a PostgreSQL-kiszolgálóhoz a psql parancssori segédprogram használatával. Az SSL-tanúsítvány ellenőrzésének érvényesítéséhez használja a `sslmode=verify-full` kapcsolati karakterlánc beállítást. Adja át a helyi tanúsítványfájl elérési útját a `sslrootcert` paraméternek.

A következő parancs egy példa a psql-kapcsolatok karakterláncára:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Győződjön meg arról, hogy a `sslrootcert` átadott érték megegyezik a mentett tanúsítvány elérési útjával.

## <a name="tls-connectivity-in-azure-database-for-postgresql-single-server"></a>TLS-kapcsolat Azure Database for PostgreSQL egyetlen kiszolgálón

Azure Database for PostgreSQL – az egyetlen kiszolgáló támogatja a titkosítást az adatbázis-kiszolgálóhoz Transport Layer Security (TLS) használatával csatlakozó ügyfelek számára. A TLS egy iparági szabványnak megfelelő protokoll, amely gondoskodik az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti biztonságos hálózati kapcsolatokról, ami lehetővé teszi a megfelelőségi követelmények betartását.

### <a name="tls-settings"></a>TLS-beállítások

Az ügyfelek mostantól képesek kikényszeríteni a TLS-verziót, hogy az ügyfél Azure Database for PostgreSQL egyetlen kiszolgálóhoz kapcsolódjon. A TLS beállítás használatához használja a TLS- **verzió minimális verziója** beállítást. Ehhez a beállításhoz a következő értékek engedélyezettek:

|  Minimális TLS-beállítás             | A TLS verziója támogatott                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (alapértelmezett) | Nincs szükség TLS-re                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 és újabb |
| TLS1_1                           | TLS 1,1, TLS 1,2 és újabb          |
| TLS1_2                           | TLS 1,2-es és újabb verzió           |


Ha például ezt a TLS-beállítást a TLS 1,0 értékre állítja be, akkor a kiszolgáló engedélyezi a TLS 1,0, 1,1 és 1.2 + protokollt használó ügyfelek kapcsolódását. Azt is megteheti, hogy a 1,2 értékre állítja azt, hogy csak a TLS 1,2-t használó ügyfelek kapcsolatainak engedélyezése, valamint a TLS 1,0 és a TLS 1,1 használatával létesített összes kapcsolat el lesz utasítva.

> [!Note] 
> Azure Database for PostgreSQL egyetlen kiszolgáló alapértelmezés szerint a TLS-t minden új kiszolgálón le kell tiltani.
>
> Jelenleg az byAzure-adatbázis a PostgreSQL-hez támogatott TLS-verzió a TLS 1,0, 1,1 és 1,2.

Ha meg szeretné tudni, hogyan állíthatja be a TLS-beállítást a Azure Database for PostgreSQL egyetlen kiszolgálóhoz, tekintse meg a [TLS-beállítás konfigurálását](howto-tls-configurations.md)ismertető témakört.

## <a name="next-steps"></a>Következő lépések

Tekintse át a [Azure Database for PostgreSQLhoz tartozó kapcsolódási könyvtárak](concepts-connection-libraries.md)különböző alkalmazás-csatlakozási lehetőségeit.

---
title: TLS – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Útmutatás és információk a TLS-kapcsolat konfigurálásához Azure Database for PostgreSQL – egyetlen kiszolgáló esetén.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: d0482e5205b97b5c57c41e0ba98fb9ca819e5d5f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82141748"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>TLS-kapcsolat konfigurálása Azure Database for PostgreSQL – egyetlen kiszolgálón

Azure Database for PostgreSQL az ügyfélalkalmazások a PostgreSQL szolgáltatáshoz való csatlakoztatását részesíti előnyben Transport Layer Security (TLS), korábbi nevén SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti TLS-kapcsolatok érvényesítésével megvédheti a védelmet a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával.

Alapértelmezés szerint a PostgreSQL-adatbázis szolgáltatás úgy van konfigurálva, hogy TLS-kapcsolatokat igényeljen. Dönthet úgy, hogy letiltja a TLS megkövetelését, ha az ügyfélalkalmazás nem támogatja a TLS-kapcsolatot.

## <a name="enforcing-tls-connections"></a>TLS-kapcsolatok kényszerítése

A Azure Portal és a CLI használatával kiépített összes Azure Database for PostgreSQL-kiszolgáló esetében a TLS-kapcsolatok kényszerítése alapértelmezés szerint engedélyezve van. 

Hasonlóképpen, a "kapcsolati karakterláncok" beállításokban előre definiált kapcsolati karakterláncok a Azure Portalban tartalmazzák az adatbázis-kiszolgáló TLS-vel való csatlakozásához szükséges paramétereket a közös nyelvekhez. A TLS-paraméter az összekötőtől függően változik, például: "SSL = true" vagy "sslmode = require" vagy "sslmode = Required" és egyéb variációk.

## <a name="configure-enforcement-of-tls"></a>A TLS kényszerítésének konfigurálása

Igény szerint letilthatja a TLS-kapcsolat kényszerítését. Microsoft Azure azt javasolja, hogy mindig engedélyezze az **SSL-kapcsolat kikényszerített** beállítását a fokozott biztonság érdekében.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Keresse fel Azure Database for PostgreSQL-kiszolgálóját, és kattintson a **kapcsolatbiztonsági**elemre. A váltógomb használatával engedélyezheti vagy letilthatja az **SSL-kapcsolat érvényesítése** beállítást. Ezután kattintson a **Mentés**gombra.

![Kapcsolatbiztonsági – letiltás kényszerített TLS/SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

A beállítás megerősítéséhez tekintse meg az **Áttekintés** lapot az SSL- **érvényesítési állapot** jelző megjelenítéséhez.

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Az **SSL-kényszerítési** paramétert engedélyezheti vagy letilthatja az Azure CLI-ben, illetve `Enabled` `Disabled` az értékek használatával.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Győződjön meg arról, hogy az alkalmazás vagy a keretrendszer támogatja a TLS-kapcsolatokat

Néhány alkalmazás-keretrendszer, amely a PostgreSQL-t használja az adatbázis-szolgáltatásaihoz, alapértelmezés szerint nem engedélyezi a TLS-t a telepítés során. Ha a PostgreSQL-kiszolgáló kikényszeríti a TLS-kapcsolatokat, de az alkalmazás nincs konfigurálva a TLS-hez, előfordulhat, hogy az alkalmazás nem tud csatlakozni az adatbázis-kiszolgálóhoz. A TLS-kapcsolatok engedélyezésével kapcsolatos információkért tekintse meg az alkalmazás dokumentációját.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>A TLS-kapcsolat tanúsítvány-ellenőrzését igénylő alkalmazások

Bizonyos esetekben az alkalmazásoknak egy megbízható hitelesítésszolgáltató (CA) tanúsítványfájl (. cer) alapján létrehozott helyi tanúsítványfájl szükségesek a biztonságos kapcsolódáshoz. A Azure Database for PostgreSQL kiszolgálóhoz való kapcsolódáshoz szükséges tanúsítvány a következő helyen https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pemtalálható:. Töltse le a tanúsítványfájl, és mentse a kívánt helyre.

### <a name="connect-using-psql"></a>Összekapcsolás a psql használatával

Az alábbi példa bemutatja, hogyan csatlakozhat a PostgreSQL-kiszolgálóhoz a psql parancssori segédprogram használatával. A `sslmode=verify-full` kapcsolati karakterlánc beállításával kényszerítheti a TLS/SSL-tanúsítványok ellenőrzését. Adja át a helyi tanúsítványfájl elérési útját a `sslrootcert` paraméternek.

A következő parancs egy példa a psql-kapcsolatok karakterláncára:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Ellenőrizze, hogy az átadott `sslrootcert` érték megegyezik-e a mentett tanúsítvány elérési útjával.

## <a name="next-steps"></a>További lépések

Tekintse át a [Azure Database for PostgreSQLhoz tartozó kapcsolódási könyvtárak](concepts-connection-libraries.md)különböző alkalmazás-csatlakozási lehetőségeit.

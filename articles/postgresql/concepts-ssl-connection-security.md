---
title: SSL – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Útmutató és információk az SSL-kapcsolat konfigurálásához Azure Database for PostgreSQL-egyetlen kiszolgáló esetén.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 21b4dffa135e1311be8c738c634de22304665695
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768146"
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

Az alábbi példa a psql-kapcsolatok karakterláncát mutatja be:
```
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Győződjön meg arról, hogy a `sslrootcert` átadott érték megegyezik a mentett tanúsítvány elérési útjával.


## <a name="next-steps"></a>Következő lépések
Tekintse át a [Azure Database for PostgreSQLhoz tartozó kapcsolódási könyvtárak](concepts-connection-libraries.md)különböző alkalmazás-csatlakozási lehetőségeit.

---
title: SSL - Azure-adatbázis a PostgreSQL-hez - Egykiszolgálós
description: Utasítások és információk az SSL-kapcsolat konfigurálásához az Azure Database for PostgreSQL - Single Server szolgáltatáshoz.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 303da4dcb68a79e69254f6610afc0003bf0aa22c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477000"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>SSL-kapcsolat konfigurálása az Azure Database for PostgreSQL -ban – Egykiszolgálós

A PostgreSQL Azure Database az ügyfélalkalmazások nak a PostgreSQL szolgáltatáshoz való csatlakoztatását részesíti előnyben a Secure Sockets Layer (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával segít a "középre" irányuló támadások elleni védelemben.

Alapértelmezés szerint a PostgreSQL adatbázis-szolgáltatás ssl-kapcsolat megkövetelésére van konfigurálva. Dönthet úgy, hogy letiltja az SSL megkövetelését, ha az ügyfélalkalmazás nem támogatja az SSL-kapcsolatot.

## <a name="enforcing-ssl-connections"></a>Ssl-kapcsolatok kényszerítése

Az Azure Portalon és a CLI-n keresztül kiépített összes Azure Database for PostgreSQL-kiszolgáló esetében az SSL-kapcsolatok kényszerítése alapértelmezés szerint engedélyezve van. 

Hasonlóképpen, a kapcsolati karakterláncok, amelyek előre meghatározott a "Kapcsolati karakterláncok" beállítások a kiszolgáló az Azure Portalon tartalmazza a szükséges paramétereket a közös nyelvek csatlakozni az adatbázis-kiszolgáló ssl használatával. Az SSL-paraméter az összekötőtől függően változik, például "ssl=true" vagy "sslmode=require" vagy "sslmode=required" és egyéb változatok.

## <a name="configure-enforcement-of-ssl"></a>Az SSL kényszerítésének konfigurálása

Az SSL-kapcsolat kényszerítése letiltható. A Microsoft Azure azt javasolja, hogy mindig **engedélyezze az SSL-kapcsolat kényszerítése** beállítást a fokozott biztonság érdekében.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Keresse fel az Azure Database for PostgreSQL kiszolgálót, és kattintson **a Kapcsolat biztonsága**gombra. A váltógombbal engedélyezheti vagy letilthatja az **SSL-kapcsolat kényszerítése** beállítást. Ezután kattintson a **Mentés gombra.**

![Kapcsolatbiztonság - SSL kényszerítésének letiltása](./media/concepts-ssl-connection-security/1-disable-ssl.png)

A beállítást az **Áttekintés** lapon ellenőrizheti az **SSL kényszerítési** állapotjelzőjének megtekintéséhez.

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Engedélyezheti vagy letilthatja az **SSL-kényszerítési** paramétert `Enabled` az Azure CLI használatával vagy `Disabled` értékeivel.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Győződjön meg arról, hogy az alkalmazás vagy a keretrendszer támogatja az SSL-kapcsolatokat

Egyes alkalmazáskeretek, amelyek a PostgreSQL-t használják az adatbázis-szolgáltatásaikhoz, alapértelmezés szerint nem engedélyezik az SSL-t a telepítés során. Ha a PostgreSQL-kiszolgáló kényszeríti az SSL-kapcsolatokat, de az alkalmazás nincs SSL-hez konfigurálva, előfordulhat, hogy az alkalmazás nem tud csatlakozni az adatbázis-kiszolgálóhoz. Az SSL-kapcsolatok engedélyezéséről az alkalmazás dokumentációjában tájékozódhat.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Az SSL-kapcsolat tanúsítványellenőrzését igénylő alkalmazások

Bizonyos esetekben az alkalmazások biztonságos csatlakozásához megbízható hitelesítésszolgáltatói (CA) tanúsítványfájlból (.cer) létrehozott helyi tanúsítványfájlra van szükség. A PostgreSQL-kiszolgálóhoz készült Azure-adatbázishoz https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pemvaló csatlakozástanúsítvány a helyen található. Töltse le a tanúsítványfájlt, és mentse a kívánt helyre.

### <a name="connect-using-psql"></a>Csatlakozás a psql használatával

A következő példa bemutatja, hogyan csatlakozhat a PostgreSQL szerverhez a psql parancssori segédprogrammal. A `sslmode=verify-full` kapcsolati karakterlánc beállítással kényszerítheti az SSL-tanúsítványok ellenőrzését. Adja át a helyi `sslrootcert` tanúsítványfájl elérési útját a paraméternek.

A következő parancs egy példa a psql kapcsolati karakterláncra:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Ellenőrizze, hogy `sslrootcert` az átadott érték megegyezik-e a mentett tanúsítvány fájlelérési útvonalának.

## <a name="next-steps"></a>További lépések

Tekintse át a különböző alkalmazáscsatlakozási beállításokat [az Azure Database for PostgreSQL kapcsolattárakban.](concepts-connection-libraries.md)

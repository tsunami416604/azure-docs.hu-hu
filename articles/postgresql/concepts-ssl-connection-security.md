---
title: SSL-összekapcsolhatóság konfigurálása az Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Útmutató és információ konfigurálása Azure-adatbázis PostgreSQL - egykiszolgálós és a társított alkalmazások SSL-kapcsolatok megfelelően használni.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 686adfb2998eff10ef4b9f378163b164ba970c56
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461837"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>SSL-összekapcsolhatóság konfigurálása az Azure Database for PostgreSQL – egyetlen kiszolgáló
Azure Database for PostgreSQL részesíti előnyben, csatlakozás a PostgreSQL szolgáltatás Secure Sockets Layer (SSL) használatával az ügyfélalkalmazások számára. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kényszerítése segít a kiszolgáló és az alkalmazás közötti titkosításával "man-in-the-middle" támadások elleni védelem.

Alapértelmezés szerint a PostgreSQL-adatbázisszolgáltatás van konfigurálva az SSL-csatlakozást igényeljen. Ha szeretné, ha az ügyfélalkalmazás nem támogatja az SSL-kapcsolatok az SSL megkövetelésének letiltását. 

## <a name="enforcing-ssl-connections"></a>SSL-kapcsolatok kikényszerítése
Az összes, Azure Database for PostgreSQL-kiszolgálót az Azure portal és CLI használatával hozhatók létre az SSL-kapcsolatok kényszerítése alapértelmezés szerint engedélyezve van. 

Hasonlóképpen a kapcsolati karakterláncokat, a "Kapcsolati karakterláncok" beállításokat a kiszolgálón, az Azure Portalon előre meghatározott közös nyelvek SSL-lel az adatbázis-kiszolgálóhoz való csatlakozáshoz szükséges paraméterek közé tartozik. Az SSL paraméter attól függően változik, az összekötőt, például "ssl = true" vagy "sslmode = szükséges" vagy "sslmode = szükséges" és egyéb változatok.

## <a name="configure-enforcement-of-ssl"></a>Az SSL kényszerítését konfigurálása
Igény szerint letilthatja a korlátozást előírni az SSL-kapcsolatot. A Microsoft Azure mindig engedélyezéséhez javasolja **kényszerítése SSL-kapcsolat** a biztonság beállítása.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata
Látogasson el az Azure Database for PostgreSQL-kiszolgálóhoz, és **kapcsolatbiztonság**. A váltógomb segítségével engedélyezheti vagy tilthatja le a **kényszerítése SSL-kapcsolat** beállítás. Kattintson a **mentése**. 

![Kapcsolatbiztonság – tiltsa le az SSL kényszerítése](./media/concepts-ssl-connection-security/1-disable-ssl.png)

A beállítás megtekintésével ellenőrizheti a **áttekintése** oldalt, hogy a **SSL-kényszerítés állapota** kijelző.

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Engedélyezheti vagy letilthatja a **ssl-kényszerítés** paraméter használatával `Enabled` vagy `Disabled` értékeket jelölik az Azure CLI-ben.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Ellenőrizze az alkalmazás- vagy keretrendszer által támogatott SSL-kapcsolatok
Néhány alkalmazás-keretrendszereket használó PostgreSQL adatbázis-szolgáltatásaik ne engedélyezze az SSL alapértelmezés szerint a telepítés során. Ha a PostgreSQL-kiszolgáló kikényszeríti az SSL-kapcsolatot, de az alkalmazás nincs konfigurálva SSL-hez, az alkalmazás az adatbázis-kiszolgálóhoz való csatlakozás sikertelen lehet. Tekintse meg az alkalmazás dokumentációjából megtudhatja, hogyan lehet engedélyezni az SSL-kapcsolatot.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Tanúsítvány-ellenőrzés a SSL-kapcsolatot igénylő alkalmazások
Bizonyos esetekben az alkalmazásoknak egy helyi tanúsítványfájlt, létrehozott egy megbízható hitelesítésszolgáltató (CA) tanúsítványfájlt (.cer) való biztonságos csatlakozáshoz. A tanúsítvány csatlakozni egy Azure Database for PostgreSQL-kiszolgáló a következő helyen található https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Töltse le a tanúsítványt, és az elsődleges helyre mentse. 

### <a name="connect-using-psql"></a>Csatlakozás a psql használatával
Az alábbi példa bemutatja, hogyan csatlakozhat a PostgreSQL-kiszolgálóhoz a psql parancssori segédprogram használatával. Használja a `sslmode=verify-full` kapcsolati sztring beállítása az SSL-tanúsítvány-ellenőrzés kényszerítéséhez. A helyi tanúsítványtárolóban fájl elérési útját adja át a `sslrootcert` paraméter.

Alul látható egy példa látható a psql kapcsolati karakterláncra:
```
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Győződjön meg arról, hogy az objektumnak átadott érték `sslrootcert` mentett tanúsítványt a fájlelérési útja megegyezik.


## <a name="next-steps"></a>További lépések
Tekintse át a különböző application kapcsolati lehetőségek [adatkapcsolattárak az Azure Database for PostgreSQL](concepts-connection-libraries.md).

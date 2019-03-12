---
title: SSL-összekapcsolhatóság konfigurálása az Azure Database for postgresql-hez
description: Útmutató és információ, Azure Database for PostgreSQL és a kapcsolódó alkalmazások megfelelően használni az SSL-kapcsolatok konfigurálása.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 5a0fc99052b18dc1fa837147aa914a473d27d832
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57730026"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>SSL-összekapcsolhatóság konfigurálása az Azure Database for postgresql-hez
Azure Database for PostgreSQL részesíti előnyben, csatlakozás a PostgreSQL szolgáltatás Secure Sockets Layer (SSL) használatával az ügyfélalkalmazások számára. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.

Alapértelmezés szerint a PostgreSQL-adatbázisszolgáltatás van konfigurálva az SSL-csatlakozást igényeljen. Igény szerint, az SSL megkövetelésének letiltását az adatbázis-szolgáltatása kapcsolódni, ha az ügyfélalkalmazás nem támogatja az SSL-kapcsolatok. 

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
Számos gyakori alkalmazási keretrendszerek, PostgreSQL használ az adatbázis-szolgáltatások, például a Drupal, Django, és ne engedélyezze az SSL alapértelmezés szerint a telepítés során. SSL-kapcsolatok engedélyezése kell elvégezni a telepítést követően vagy CLI-parancsok az alkalmazáshoz. Ha a PostgreSQL-kiszolgáló az SSL-kapcsolatok kikényszerítése, és az ahhoz kapcsolódó alkalmazás nem megfelelően van konfigurálva, az alkalmazás az adatbázis-kiszolgálóhoz való csatlakozás sikertelen lehet. Tekintse meg az alkalmazás dokumentációjából megtudhatja, hogyan lehet engedélyezni az SSL-kapcsolatot.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Tanúsítvány-ellenőrzés a SSL-kapcsolatot igénylő alkalmazások
Bizonyos esetekben az alkalmazásoknak egy helyi tanúsítványfájlt, létrehozott egy megbízható hitelesítésszolgáltató (CA) tanúsítványfájlt (.cer) való biztonságos csatlakozáshoz. Lásd a következő lépéseket a .cer fájl beszerzése, a tanúsítvány dekódolása, és kösse az alkalmazást.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Töltse le a tanúsítványfájlt, a hitelesítésszolgáltató (CA) 
A tanúsítvány kommunikációra SSL-Titkosítást az az Azure Database for PostgreSQL-kiszolgálót nem találnak szükséges [Itt](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt). Töltse le helyben a tanúsítványfájlt.

### <a name="install-a-cert-decoder-on-your-machine"></a>A tanúsítvány dekóder telepítse a gépen 
Használhat [OpenSSL](https://github.com/openssl/openssl) a tanúsítványfájl szükséges az alkalmazásához, biztonságosan csatlakozhat az adatbázis-kiszolgálóhoz való dekódolandó. OpenSSL telepítésével kapcsolatban lásd: a [OpenSSL telepítési utasításokat](https://github.com/openssl/openssl/blob/master/INSTALL). 


### <a name="decode-your-certificate-file"></a>A tanúsítványfájl-dekódolást.
A legfelső szintű hitelesítésszolgáltató letöltött fájl titkosított formátumban van. A tanúsítványfájl dekódolandó az openssl kódtárat használják. Ehhez a következő OpenSSL-parancs futtatásával:

```
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Csatlakozik Azure Database for postgresql-hez SSL tanúsítványalapú hitelesítéssel ellátott
Most, hogy sikeresen dekódovat a tanúsítvány, mostantól csatlakozhat az adatbázis-kiszolgáló biztonságos SSL-en keresztül. Ahhoz, hogy a kiszolgálói tanúsítvány-ellenőrzés, a tanúsítvány található a fájl ~/.postgresql/root.crt az a felhasználó kezdőkönyvtárába kell elhelyezni. (A Microsoft Windows a fájl neve % APPDATA%\postgresql\root.crt.). 

#### <a name="connect-using-psql"></a>Csatlakozás a psql használatával
Az alábbi példa bemutatja, hogyan sikerült csatlakozni a PostgreSQL-kiszolgálóhoz a psql parancssori segédprogram használatával. Használja a `root.crt` létrehozott fájlt, és a `sslmode=verify-ca` vagy `sslmode=verify-full` lehetőséget.

A PostgreSQL parancssori felületén, hajtsa végre a következő parancsot:
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=mylogin@mydemoserver"
```
Ha ez sikeres, a következő kimenetet kapja:
```bash
Password for user mylogin@mydemoserver:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

## <a name="next-steps"></a>További lépések
Tekintse át a különböző alkalmazás kapcsolódási lehetőségekről a következő [adatkapcsolattárak az Azure Database for PostgreSQL](concepts-connection-libraries.md).

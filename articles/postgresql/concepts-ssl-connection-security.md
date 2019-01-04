---
title: SSL-összekapcsolhatóság konfigurálása az Azure Database for postgresql-hez
description: Útmutató és információ, Azure Database for PostgreSQL és a kapcsolódó alkalmazások megfelelően használni az SSL-kapcsolatok konfigurálása.
author: JasonMAnderson
ms.author: janders
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: bcb6f1da78cfccb4914f1f4008609f002a7b8c2d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545433"
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

### <a name="download-and-install-openssl-on-your-machine"></a>Töltse le és telepítse az OpenSSL a gépen 
A tanúsítványfájl szükséges az alkalmazásához, biztonságosan csatlakozhat az adatbázis-kiszolgálóhoz való dekódolandó, a helyi számítógépen OpenSSL telepítenie kell.

#### <a name="for-linux-os-x-or-unix"></a>A Linux, OS X or Unix
A közvetlenül a forráskódban biztosított az OpenSSL könyvtárakat a [OpenSSL Software Foundation](https://www.openssl.org). Az alábbi utasításokat követve végigvezeti a Linux rendszerű számítógép OpenSSL telepítéséhez szükséges lépéseket. Ez a cikk ismert működését az Ubuntu 12.04-es és újabb parancsokat használja.

Nyisson meg egy terminál-munkamenetet, és töltse le az OpenSSL.
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
``` 
Csomagolja ki a fájlokat a letöltött csomag.
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
Adja meg a könyvtárat, ahol a fájlokat könyvtárban találhatók. Alapértelmezés szerint azt alábbinak kell lennie.

```bash
cd openssl-1.1.0e
```
OpenSSL konfigurálásához futtassa az alábbi parancsot. Ha egy mappában található fájlokat /usr/local/openssl eltér, ügyeljen arra, hogy módosítsa a megfelelő műveletet.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
Most, hogy OpenSSL megfelelően van konfigurálva, kell, hogy a tanúsítvány konvertálni. Fordítása, futtassa a következő parancsot:

```bash
make
```
Fordítás befejeződése után készen áll a telepítendő OpenSSL végrehajtható fájlként a következő parancs futtatásával:
```bash
make install
```
Győződjön meg arról, hogy sikeresen telepítette OpenSSL a rendszeren, futtassa a következő parancsot és ellenőrizze, hogy ugyanazzal a hibaüzenettel kap.

```bash
/usr/local/openssl/bin/openssl version
```
Ha sikeres a következő üzenetnek kell megjelennie.
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

#### <a name="for-windows"></a>Windows esetén
OpenSSL telepítése Windows rendszerű számítógépek teheti meg az alábbi módokon:
1. **(Ajánlott)**  a beépített Bash-a Windows-szolgáltatással a Windows 10 és újabb, OpenSSL alapértelmezés szerint telepítve van-e. Útmutatást nyújt a Windows 10 Bash-a Windows-funkciók engedélyezéséhez található [Itt](https://msdn.microsoft.com/commandline/wsl/install_guide).
2. A Win32/64-alkalmazások a Közösség által biztosított letöltése keresztül Bár az OpenSSL Software Foundation nem biztosít vagy bármely adott Windows-telepítők ajánlásával, elérhető telepítők listájának megadása [Itt](https://wiki.openssl.org/index.php/Binaries).

### <a name="decode-your-certificate-file"></a>A tanúsítványfájl-dekódolást.
A legfelső szintű hitelesítésszolgáltató letöltött fájl titkosított formátumban van. A tanúsítványfájl dekódolandó az openssl kódtárat használják. Ehhez a következő OpenSSL-parancs futtatásával:

```dos
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Csatlakozik Azure Database for postgresql-hez SSL tanúsítványalapú hitelesítéssel ellátott
Most, hogy sikeresen dekódovat a tanúsítvány, mostantól csatlakozhat az adatbázis-kiszolgáló biztonságos SSL-en keresztül. Ahhoz, hogy a kiszolgálói tanúsítvány-ellenőrzés, a tanúsítvány található a fájl ~/.postgresql/root.crt az a felhasználó kezdőkönyvtárába kell elhelyezni. (A Microsoft Windows a fájl neve % APPDATA%\postgresql\root.crt.). A következő, Azure Database for postgresql-hez történő összekapcsolására vonatkozó utasításokat tartalmazza.

#### <a name="using-psql-command-line-utility"></a>Psql parancssori segédprogram használatával
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

#### <a name="using-pgadmin-gui-tool"></a>A pgAdmin GUI eszköz használatával
Konfigurálás a pgAdmin SSL-en keresztül biztonságos kapcsolatot 4 megköveteli, hogy Ön a `SSL mode = Verify-CA` vagy `SSL mode = Verify-Full` módon:

![Képernyőkép a pgAdmin - kapcsolat – SSL-mód megkövetelése](./media/concepts-ssl-connection-security/2-pgadmin-ssl.png)

## <a name="next-steps"></a>További lépések
Tekintse át a különböző alkalmazás kapcsolódási lehetőségekről a következő [adatkapcsolattárak az Azure Database for PostgreSQL](concepts-connection-libraries.md).

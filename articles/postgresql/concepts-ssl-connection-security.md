---
title: SSL-kapcsolatot PostgreSQL Azure-adatbázis konfigurálása
description: Utasításokat és az információkat PostgreSQL és a társított alkalmazások megfelelően az SSL-kapcsolat használata Azure-adatbázis konfigurálása.
services: postgresql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: kfile
ms.service: postgresql
ms.custom: ''
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0a4a7041a905470f895921cfedf2bd94e8466966
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>SSL-kapcsolatot PostgreSQL Azure-adatbázis konfigurálása
Azure-adatbázis PostgreSQL inkább csatlakozás az ügyfél alkalmazásait, és a Secure Sockets Layer (SSL) használatával PostgreSQL-szolgáltatás. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.

Alapértelmezés szerint a PostgreSQL-adatbázis szolgáltatás SSL-kapcsolat megkövetelése van konfigurálva. Másik lehetőségként letilthatja kapcsolódni az adatbázis-szolgáltatás, ha az ügyfélalkalmazást nem támogatja az SSL-kapcsolatot az SSL megkövetelése. 

## <a name="enforcing-ssl-connections"></a>SSL-kapcsolatok kényszerítése
Az összes Azure Database az Azure portál és a parancssori felületen keresztül szerezhetők PostgreSQL-kiszolgálók esetén az SSL-kapcsolatok kényszerítési alapértelmezés szerint engedélyezve van. 

Hasonlóképpen a "Kapcsolati karakterláncok" beállításokat a kiszolgálón az Azure-portálon előre definiált kapcsolati karakterláncok közös nyelvek SSL használatával az adatbázis-kiszolgálóhoz való csatlakozáshoz szükséges paraméterek közé tartoznak. Az SSL-paraméter attól függően változik, az összekötő, például "ssl = true" vagy "sslmode = szükséges" vagy "sslmode = szükséges" és egyéb változatok.

## <a name="configure-enforcement-of-ssl"></a>Az SSL kényszerítésének
Igény szerint letilthatja végrehajtó SSL-kapcsolatot. A Microsoft Azure azt javasolja, hogy mindig engedélyezése **kényszerítése SSL-kapcsolat** vonatkozó fokozott biztonsági beállításait.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata
Keresse fel az Azure-adatbázis PostgreSQL-kiszolgáló, és kattintson a **kapcsolatbiztonsági**. A váltógomb segítségével engedélyezheti vagy tilthatja le a **kényszerítése SSL-kapcsolat** beállítást. Kattintson a **mentése**. 

![Kapcsolat biztonsági - Disable SSL kényszerítése](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Megtekintésével ellenőrizheti a beállítás a **áttekintése** lapot, melyen megtekintheti a **SSL kényszerítése állapot** mutató.

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Engedélyezheti vagy letilthatja a **ssl-kényszerítési** paraméter használatával `Enabled` vagy `Disabled` értékeket az Azure parancssori felület.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Ellenőrizze az alkalmazás vagy a keretrendszer támogatja SSL-kapcsolatok
Sok általános alkalmazás-keretrendszert, PostgreSQL használ az adatbázis-szolgáltatásokat, például Drupal és a Django, ne engedélyezze az SSL alapértelmezés szerint telepítése során. Engedélyezi az SSL-kapcsolatot kell elvégezni, a telepítés után, vagy a parancssori felület parancsait az alkalmazáshoz. Ha a PostgreSQL-kiszolgáló SSL-kapcsolatok kényszerít, és az ahhoz kapcsolódó alkalmazás nincs megfelelően konfigurálva, az alkalmazás sikertelen lehet az adatbázis-kiszolgálóhoz való csatlakozáshoz. Az alkalmazás dokumentációból megtudhatja, hogyan SSL-kapcsolatok engedélyezéséhez.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>A tanúsítványellenőrzés az SSL-kapcsolatot igénylő alkalmazások
Bizonyos esetekben az alkalmazásoknak a megbízható tanúsítvány hitelesítésszolgáltatói (CA) tanúsítvány (.cer) kapcsolódó fájl biztonságosan által létrehozott helyi tanúsítványfájlt. A következő lépések szerezze be a .cer fájlt, a tanúsítvány dekódolása, és kösse az alkalmazást.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Töltse le a tanúsítványt a tanúsítvány hitelesítésszolgáltatói (CA) 
Az Azure-adatbázissal SSL protokollt használó kommunikációra a PostgreSQL-kiszolgáló szükség a tanúsítvány [Itt](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt). Töltse le a fájlt helyileg.

### <a name="download-and-install-openssl-on-your-machine"></a>Töltse le és telepítse a OpenSSL a számítógépen 
A tanúsítványfájl való biztonságos kapcsolódás az adatbázis-kiszolgáló az alkalmazáshoz szükséges dekódolni, telepítendő OpenSSL a helyi számítógépen.

#### <a name="for-linux-os-x-or-unix"></a>A Linux, OS X or Unix
Az OpenSSL könyvtárakat közvetlenül a forráskód szerepelnek a [OpenSSL szoftver Foundation](http://www.openssl.org). Az alábbi utasítások alapján végigvezetik a Linux rendszerű számítógépen lévő OpenSSL telepítéséhez szükséges lépéseket. Ebben a cikkben az ismert működését az Ubuntu 12.04 és magasabb parancsok.

Nyisson meg egy terminál-munkamenetet, és töltse le a OpenSSL.
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
``` 
Bontsa ki a fájlokat a letöltött csomagban található.
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
Adja meg a könyvtárat, ha a fájlok könyvtárban találhatók. Alapértelmezés szerint az alábbinak kell lennie.

```bash
cd openssl-1.1.0e
```
OpenSSL konfigurálásához futtassa a következő parancsot. Ha szeretne egy mappában lévő fájlok /usr/local/openssl eltér, győződjön meg arról, módosíthatja a megfelelő műveletet.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
Most, hogy OpenSSL megfelelően van konfigurálva, kell, hogy a tanúsítvány konvertálni. Fordítása, futtassa a következő parancsot:

```bash
make
```
Fordítási végrehajtása után készen áll a telepítendő OpenSSL végrehajtható fájlként, a következő parancs futtatásával:
```bash
make install
```
Győződjön meg arról, hogy sikeresen telepítette OpenSSL a rendszeren, futtassa a következő parancs és a jelölőnégyzet győződjön meg arról, hogy az azonos kimenethez kap.

```bash
/usr/local/openssl/bin/openssl version
```
Ha sikeres a következő üzenetet kell megjelennie.
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

#### <a name="for-windows"></a>Windows rendszerhez
OpenSSL telepítése Windows rendszerű megteheti a következőképpen:
1. **(Ajánlott)**  Funkcióval a beépített Bash a Windows a Windows 10-es és újabb verziók, OpenSSL alapértelmezés szerint telepítve van-e. Windows 10 Bash a Windows-funkció engedélyezésével kapcsolatos útmutatást itt talál [Itt](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide).
2. A letöltés a Win32/64 alkalmazás a Közösség által meghatározott. Során a OpenSSL szoftver Foundation nem adja meg, vagy hagyja jóvá a megadott Windows Installer telepítők elérhető telepítők listáját tartalmazzák [Itt](https://wiki.openssl.org/index.php/Binaries).

### <a name="decode-your-certificate-file"></a>A tanúsítványfájl dekódolása
A legfelső szintű hitelesítésszolgáltató letöltött fájl titkosított formátumban van. A tanúsítványfájl dekódolás OpenSSL használja. Ehhez a OpenSSL parancsot:

```dos
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Azure-adatbázishoz szeretne csatlakozni a PostgreSQL SSL tanúsítványalapú hitelesítéssel ellátott
Most, hogy sikeres rendelkeznek dekódolni, a tanúsítványt, akkor is csatlakozhat az adatbázis-kiszolgáló biztonságos SSL-en keresztül. Ahhoz, hogy a kiszolgáló tanúsítványellenőrzést, a tanúsítványt kell helyezni a fájlt ~/.postgresql/root.crt az a felhasználó saját könyvtárához. (A Microsoft Windows a fájl neve % APPDATA%\postgresql\root.crt.). A következő útmutatás PostgreSQL az Azure-adatbázishoz szeretne csatlakozni.

#### <a name="using-psql-command-line-utility"></a>Psql parancssori segédprogrammal
A következő példa bemutatja, hogyan tud sikeresen csatlakozni a psql parancssori segédprogram használatával PostgreSQL-kiszolgálóhoz. Használja a `root.crt` fájl létrehozása és a `sslmode=verify-ca` vagy `sslmode=verify-full` lehetőséget.

A PostgreSQL parancssori felületén, hajtsa végre a következő parancsot:
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=mylogin@mydemoserver"
```
Ha sikeres, a következő eredmény jelenik meg:
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

#### <a name="using-pgadmin-gui-tool"></a>Grafikus felhasználói Felülettel pgAdmin eszközzel
Meg kell adnia pgAdmin biztonságos SSL Csatornán keresztül csatlakozni 4 konfigurálása számára a `SSL mode = Verify-CA` vagy `SSL mode = Verify-Full` az alábbiak szerint:

![Képernyőkép a pgAdmin - kapcsolat – SSL-mód megkövetelése](./media/concepts-ssl-connection-security/2-pgadmin-ssl.png)

## <a name="next-steps"></a>További lépések
Tekintse át a következő különböző alkalmazás kapcsolati lehetőségek [PostgreSQL az Azure-adatbázis adatkapcsolattárak](concepts-connection-libraries.md).

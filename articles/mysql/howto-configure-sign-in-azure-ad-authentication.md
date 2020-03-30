---
title: Az Azure Active Directory használata – Azure Database for MySQL
description: Tudnivalók az Azure Active Directory (Azure AD) beállításáról az Azure Database for MySQL-hez való hitelesítéshez
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 0403edadd491609c2c88d5b5ac6980d97163f8d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299005"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Az Azure Active Directory használata a MySQL-rel való hitelesítéshez

Ez a cikk bemutatja az Azure Active Directory-hozzáférés konfigurálásának lépéseit az Azure Database for MySQL szolgáltatással, és hogyan csatlakozhat egy Azure AD-jogkivonat használatával.

> [!IMPORTANT]
> Az Azure AD-hitelesítés az Azure Database for MySQL jelenleg nyilvános előzetes verzióban.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="setting-the-azure-ad-admin-user"></a>Az Azure AD-rendszergazda imassa beállítása

Csak egy Azure AD-rendszergazdai felhasználó hozhat létre/engedélyezhet felhasználókat az Azure AD-alapú hitelesítéshez. Az Azure AD-rendszergazdai felhasználók létrehozásához kövesse az alábbi lépéseket

1. Az Azure Portalon válassza ki az Azure Database for MySQL, amely engedélyezni szeretné az Azure AD példányát.
2. A Beállítások csoportban válassza az Active Directory rendszergazdája lehetőséget:

![az Azure ad administrator beállítása][2]

3. Válasszon ki egy érvényes Azure AD-felhasználót az ügyfél-bérlőben, hogy az Azure AD-rendszergazda legyen.

> [!IMPORTANT]
> A rendszergazda beállításakor egy új felhasználó kerül az Azure Database for MySQL server teljes rendszergazdai engedélyekkel.

MySQL-kiszolgálónként csak egy Azure AD-rendszergazda hozható létre, és egy másik kiválasztása felülírja a kiszolgálóhoz konfigurált meglévő Azure AD-rendszergazdát.

Egy későbbi kiadásban támogatni fogjuk egy Azure AD-csoport megadását az egyes felhasználók helyett, hogy több rendszergazdával rendelkezik, de ez jelenleg még nem támogatott.

A rendszergazda konfigurálása után bejelentkezhet:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Csatlakozás a MySQL Azure-adatbázisához az Azure AD használatával

Az alábbi magas szintű diagram összefoglalja az Azure AD-hitelesítés azure-beli Azure Database for MySQL használatával kapcsolatos munkafolyamatot:

![hitelesítési folyamat][1]

Az Azure AD-integrációt úgy alakítottuk ki, hogy olyan közös MySQL-eszközökkel működjön, mint a mysql CLI, amelyek nem azure AD-tudatosak, és csak a mySQL-hez való csatlakozáskor támogatják a felhasználónév és a jelszó megadását. Az Azure AD-tokent jelszóként adjuk át, ahogy az a fenti képen látható.

Jelenleg a következő ügyfeleket teszteltük:

- MySQLWorkbench 
- Mysql CLI

Azt is tesztelték a leggyakoribb alkalmazás-illesztőprogramok, láthatjuk részleteket a végén ezt az oldalt.

Ezek azok a lépések, amelyeket egy felhasználónak/alkalmazásnak az alábbiakban ismertetett Azure AD-vel kell végeznie:

### <a name="step-1-authenticate-with-azure-ad"></a>1. lépés: Hitelesítés az Azure AD-vel

Győződjön meg arról, hogy telepítve van az [Azure CLI.](/cli/azure/install-azure-cli)

Az Azure CLI eszköz meghívása az Azure AD-vel való hitelesítéshez. Az Azure AD felhasználói azonosítóját és jelszavát meg kell adnia.

```
az login
```

Ez a parancs egy böngészőablakot indít el az Azure AD hitelesítési lapjára.

> [!NOTE]
> Az Azure Cloud Shell segítségével is végrehajthatja ezeket a lépéseket.
> Kérjük, vegye figyelembe, hogy az Azure AD-hozzáférési jogkivonat beolvasásakor az Azure Cloud Shellben explicit módon kell hívnia, `az login` és újra be kell jelentkeznie (a külön ablakban egy kóddal). Ezt követően `get-access-token` a jel a parancs fog működni a várt módon.

### <a name="step-2-retrieve-azure-ad-access-token"></a>2. lépés: Az Azure AD-hozzáférési jogkivonat lekérése

Az Azure CLI eszköz meghívásához az Azure AD által hitelesített felhasználó hozzáférési jogkivonatot szerezzen be az 1.

Példa (nyilvános felhőesetén):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

A fenti erőforrásértéket pontosan a látható módon kell megadni. Más felhők esetén az erőforrás-érték a következők használatával kereshető ki:

```shell
az cloud show
```

Az Azure CLI 2.0.71-es és újabb verziójához a parancs az alábbi, kényelmesebb verzióban adható meg az összes felhőhöz:

```shell
az account get-access-token --resource-type oss-rdbms
```

A hitelesítés sikeres befejezése után az Azure AD egy hozzáférési jogkivonatot ad vissza:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

A jogkivonat egy Base 64 karakterlánc, amely kódolja a hitelesített felhasználó összes információt, és amely az Azure Database for MySQL szolgáltatás.

> [!NOTE]
> A hozzáférési jogkivonat érvényessége 5 perc és 60 perc között van. Azt javasoljuk, hogy a hozzáférési jogkivonatot közvetlenül az Azure Database for MySQL-be való bejelentkezés megkezdése előtt kapja meg.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>3. lépés: Token használata jelszóként a MySQL-rel való bejelentkezéshez

A csatlakozáskor a hozzáférési jogkivonatot kell használnia MySQL felhasználói jelszóként. Gui-ügyfelek, például a MySQLWorkbench használatakor a fenti módszerrel lekérheti a jogkivonatot. 

A CLI használatakor ezzel a rövid kézzel csatlakozhat: 

**Példa (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

Vegye figyelembe a "enable-cleartext-plugin" beállítást – hasonló konfigurációt kell használnia más ügyfelekkel, hogy megbizonyosodjon arról, hogy a token elküldése a kiszolgálóra anélkül, hogy kivonatoló lenne.

Most már hitelesítve van a MySQL-kiszolgálón az Azure AD-hitelesítés használatával.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Azure AD-felhasználók létrehozása a MySQL Azure Database szolgáltatásában

Ha azure AD-felhasználót szeretne hozzáadni az Azure Database for MySQL-adatbázishoz, a csatlakozás után hajtsa végre a következő lépéseket (lásd a további szakaszt a csatlakozásról):

1. Először győződjön meg `<user>@yourtenant.onmicrosoft.com` arról, hogy az Azure AD-felhasználó egy érvényes felhasználó az Azure AD-bérlőben.
2. Jelentkezzen be az Azure Database for MySQL-példány, mint az Azure AD-rendszergazdai felhasználó.
3. Hozzon `<user>@yourtenant.onmicrosoft.com` létre felhasználót az Azure Database for MySQL-ben.

**Példa:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

A 32 karakternél hosszabb felhasználónevek esetén ajánlott inkább aliast használni a csatlakozáshoz: 

Példa:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> A felhasználó hitelesítése az Azure AD-n keresztül nem ad a felhasználónak semmilyen engedélyt az Azure Database for MySQL-adatbázis objektumainak eléréséhez. A felhasználónak manuálisan kell megadnia a szükséges engedélyeket.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Azure AD-csoportok létrehozása a MySQL-hez készült Azure Database-ben

Ha engedélyezni szeretné az Azure AD-csoportot az adatbázishoz való hozzáféréshez, használja ugyanazt a mechanizmust, mint a felhasználók esetében, de adja meg a csoport nevét:

**Példa:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Bejelentkezéskor a csoport tagjai a személyes hozzáférési jogkivonataikat fogják használni, de a felhasználónévként megadott csoportnévvel jelentkeznek.

## <a name="token-validation"></a>Token érvényesítése

Az Azure AD-hitelesítés az Azure Database for MySQL biztosítja, hogy a felhasználó létezik a MySQL-kiszolgálón, és ellenőrzi a jogkivonat érvényességét a jogkivonat tartalmának ellenőrzésével. A következő jogkivonat-érvényesítési lépések hajtják végre:

-   A jogkivonatot az Azure AD írta alá, és nem módosították
-   A jogkivonatot az Azure AD adta ki a kiszolgálóhoz társított bérlőszámára
-   A token nem járt le
-   A token az Azure Database for MySQL erőforráshoz készült (és nem egy másik Azure-erőforráshoz)

## <a name="compatibility-with-application-drivers"></a>Kompatibilitás alkalmazás-illesztőprogramokkal

A legtöbb illesztőprogram támogatott, azonban győződjön meg róla, hogy a jelszó tiszta szöveges küldéséhez a beállításokat használja, így a token módosítás nélkül kerül elküldésre.

* C/C++
  * libmysqlclient: Támogatott
  * mysql-connector-c++: Támogatott
* Java
  * Csatlakozó/J (mysql-connector-java): Támogatott, `useSSL` ki kell használni a beállítást
* Python
  * Összekötő/Python: Támogatott
* Ruby
  * mysql2: Támogatott
* .NET
  * mysql-connector-net: Támogatott, hozzá kell adni a plugin mysql_clear_password
  * mysql-net/MySqlConnector: Támogatott
* Node.js
  * mysqljs: Nem támogatott (nem küld token cleartext patch nélkül)
  * node-mysql2: Támogatott
* Perl
  * DBD::mysql: Támogatott
  * Net::MySQL: Nem támogatott
* Indítás
  * go-sql-driver: Támogatott, `?tls=true&allowCleartextPasswords=true` hozzáadás a kapcsolati karakterlánchoz

## <a name="next-steps"></a>További lépések

* Az Azure Active [Directory-hitelesítés](concepts-azure-ad-authentication.md) általános fogalmainak áttekintése az Azure Database for MySQL szolgáltatással

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png

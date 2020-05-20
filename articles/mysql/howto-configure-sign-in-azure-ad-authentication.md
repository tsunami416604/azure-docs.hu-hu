---
title: Azure Active Directory-Azure Database for MySQL használata
description: Ismerje meg, hogyan állíthat be Azure Active Directory (Azure AD) a hitelesítéshez Azure Database for MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 1fa34deaa12400a164602d38b6b2d349a64850c6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652255"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Azure Active Directory használata a MySQL-sel való hitelesítéshez

Ebből a cikkből megtudhatja, hogyan konfigurálhatja Azure Active Directory hozzáférését Azure Database for MySQL és hogyan csatlakozhat Azure AD-jogkivonat használatával.

## <a name="setting-the-azure-ad-admin-user"></a>Az Azure AD-rendszergazda felhasználó beállítása

Csak az Azure AD-rendszergazda felhasználó hozhat létre/engedélyezhet felhasználókat az Azure AD-alapú hitelesítéshez. Az Azure AD-rendszergazda felhasználó létrehozásához kövesse az alábbi lépéseket

1. A Azure Portal válassza ki a Azure Database for MySQL azon példányát, amelyet engedélyezni kíván az Azure AD-ben.
2. A beállítások területen válassza a Active Directory rendszergazda elemet:

![Az Azure ad-rendszergazda beállítása][2]

3. Válasszon ki egy érvényes Azure AD-felhasználót az ügyfél-bérlőben az Azure AD-rendszergazdaként.

> [!IMPORTANT]
> A rendszergazda beállításakor a rendszer új felhasználót ad hozzá a Azure Database for MySQL-kiszolgálóhoz teljes körű rendszergazdai engedélyekkel.

MySQL-Kiszolgálónként csak egy Azure AD-rendszergazda hozható létre, és a másik lehetőség kiválasztása esetén a rendszer felülírja a kiszolgálóhoz konfigurált meglévő Azure AD-rendszergazdát.

A jövőbeli kiadásokban az egyes felhasználók helyett egy Azure AD-csoport megadását fogjuk támogatni, azonban ez jelenleg nem támogatott.

A rendszergazda konfigurálása után most bejelentkezhet:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Csatlakozás az Azure Database for MySQLhoz az Azure AD használatával

A következő magas szintű diagram összefoglalja az Azure AD-hitelesítés Azure Database for MySQL használatával történő használatának munkafolyamatát:

![hitelesítési folyamat][1]

Az Azure AD-integrációt úgy terveztük, hogy olyan közös MySQL-eszközökkel működjön, mint például a MySQL parancssori felület, amely nem ismeri az Azure AD-t, és csak a Felhasználónév és a jelszó megadását támogatja a MySQL-hez való csatlakozáskor. Az Azure AD-tokent jelszóként adjuk át a fenti képen látható módon.

Jelenleg a következő ügyfeleket teszteltük:

- MySQLWorkbench 
- MySQL parancssori felület

A leggyakoribb alkalmazás-illesztőprogramokat is teszteltük, a lap végén láthatja a részleteket.

Ezek a lépések, amelyekkel egy felhasználónak/alkalmazásnak a következőkben ismertetett Azure AD-hitelesítéssel kell rendelkeznie:

### <a name="step-1-authenticate-with-azure-ad"></a>1. lépés: hitelesítés az Azure AD-vel

Győződjön meg arról, hogy az [Azure CLI telepítve](/cli/azure/install-azure-cli)van.

Hívja meg az Azure CLI eszközt az Azure AD-vel való hitelesítéshez. Ehhez meg kell adnia az Azure AD-beli felhasználói azonosítót és a jelszót.

```
az login
```

Ezzel a paranccsal megnyílik egy böngészőablak az Azure AD-hitelesítés lapra.

> [!NOTE]
> Ezen lépések végrehajtásához Azure Cloud Shell is használhatja.
> Vegye figyelembe, hogy amikor az Azure AD hozzáférési jogkivonatot a Azure Cloud Shell beolvassa, explicit módon meg kell hívnia `az login` , majd újra be kell jelentkeznie (a külön ablakban a kóddal). A bejelentkezés után a `get-access-token` parancs a várt módon fog működni.

### <a name="step-2-retrieve-azure-ad-access-token"></a>2. lépés: az Azure AD hozzáférési jogkivonatának beolvasása

Hívja meg az Azure CLI eszközt az 1. lépésben az Azure AD hitelesített felhasználó hozzáférési jogkivonatának beszerzéséhez az Azure Database for MySQL eléréséhez.

Példa (nyilvános felhő esetén):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

A fenti erőforrás-értéket pontosan az ábrán látható módon kell megadni. Más felhők esetében az erőforrás értéke a következő használatával kereshető fel:

```shell
az cloud show
```

Az Azure CLI 2.0.71-es és újabb verziói esetén a parancs a következő kényelmesebb verzióban adható meg az összes felhőhöz:

```shell
az account get-access-token --resource-type oss-rdbms
```

A sikeres hitelesítés után az Azure AD egy hozzáférési jogkivonatot fog visszaadni:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

A jogkivonat egy alapszintű 64 karakterlánc, amely kódolja a hitelesített felhasználóra vonatkozó összes információt, és amely a Azure Database for MySQL szolgáltatásra irányul.

> [!NOTE]
> A hozzáférési jogkivonat érvényessége 5 perc és 60 perc között lehet. Javasoljuk, hogy közvetlenül a bejelentkezés megkezdése előtt szerezze be a hozzáférési jogkivonatot a Azure Database for MySQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>3. lépés: a token használata jelszóként a MySQL-ben való bejelentkezéshez

Kapcsolódáskor a hozzáférési tokent MySQL felhasználói jelszóként kell használnia. Grafikus felhasználói felületű ügyfelek (például a MySQLWorkbench) használatakor a fenti módszer használatával kérheti le a tokent. 

A CLI használatakor ez a rövid kéz a csatlakozáshoz használható: 

**Példa (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

Vegye figyelembe, hogy az "Enable-titkosítatlan-plugin" beállítással hasonló konfigurációt kell használnia más ügyfelekkel, hogy a tokent a rendszer a kivonat nélkül küldje el a kiszolgálónak.

Most már hitelesítette a MySQL-kiszolgálót az Azure AD-hitelesítés használatával.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Azure AD-felhasználók létrehozása a Azure Database for MySQLban

Ha Azure AD-felhasználót szeretne hozzáadni a Azure Database for MySQL-adatbázishoz, hajtsa végre a következő lépéseket a csatlakozás után (lásd: a kapcsolódási útmutató későbbi szakasza):

1. Először győződjön meg arról, hogy az Azure AD `<user>@yourtenant.onmicrosoft.com` -felhasználó érvényes felhasználó az Azure ad-bérlőben.
2. Jelentkezzen be az Azure Database for MySQL-példányba az Azure AD-rendszergazda felhasználóként.
3. Felhasználó létrehozása `<user>@yourtenant.onmicrosoft.com` Azure Database for MySQLban.

**Például**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

Az 32 karakternél hosszabb felhasználónevek esetében ajánlott aliast használni, ha a csatlakozáskor használni szeretné a következőt: 

Példa:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> Egy felhasználó Azure AD-n keresztüli hitelesítése nem biztosít semmilyen engedélyt a felhasználónak a Azure Database for MySQL-adatbázisban lévő objektumok eléréséhez. A szükséges engedélyeket manuálisan kell megadnia a felhasználónak.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Azure AD-csoportok létrehozása a Azure Database for MySQLban

Ha engedélyezni szeretné az Azure AD-csoport számára az adatbázishoz való hozzáférést, használja ugyanazt a mechanizmust, mint a felhasználók számára, hanem adja meg a csoport nevét:

**Például**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Bejelentkezéskor a csoport tagjai a személyes hozzáférési jogkivonatokat fogják használni, de a felhasználónévként megadott csoport nevével jelentkezhetnek be.

## <a name="token-validation"></a>Jogkivonat ellenőrzése

Azure Database for MySQL Azure AD-hitelesítés biztosítja, hogy a felhasználó létezik a MySQL-kiszolgálón, és ellenőrzi a token érvényességét a jogkivonat tartalmának ellenőrzésével. A rendszer a következő jogkivonat-ellenőrzési lépéseket hajtja végre:

-   Az Azure AD aláírja a tokent, és nem módosították
-   A tokent az Azure AD adta ki a kiszolgálóhoz társított bérlőhöz
-   A jogkivonat nem járt le
-   Token a Azure Database for MySQL erőforráshoz (és nem egy másik Azure-erőforráshoz)

## <a name="compatibility-with-application-drivers"></a>Kompatibilitás az alkalmazás-illesztőprogramokkal

A legtöbb illesztőprogram támogatott, azonban győződjön meg arról, hogy a jelszó küldésére vonatkozó beállításokat nem egyértelmű szövegként használja, így a jogkivonat a módosítás nélkül lesz elküldve.

* C/C++
  * libmysqlclient: támogatott
  * MySQL-Connector-c + +: támogatott
* Java
  * Összekötő/J (MySQL-Connector-Java): támogatott, a beállításnak kell kihasználnia `useSSL`
* Python
  * Összekötő/Python: támogatott
* Ruby
  * mysql2: támogatott
* .NET
  * MySQL-Connector-Net: támogatott, beépülő modult kell hozzáadni a mysql_clear_passwordhoz
  * MySQL-net/MySqlConnector: támogatott
* Node.js
  * mysqljs: nem támogatott (nem küld tokent a nem titkosított szövegben javítás nélkül)
  * Node-mysql2: támogatott
* Perl
  * DBD:: MySQL: támogatott
  * NET:: MySQL: nem támogatott
* Go
  * Go-SQL-illesztőprogram: támogatott, Hozzáadás `?tls=true&allowCleartextPasswords=true` a kapcsolódási karakterlánchoz

## <a name="next-steps"></a>További lépések

* Tekintse át a [Azure Active Directory hitelesítéssel](concepts-azure-ad-authentication.md) kapcsolatos általános fogalmakat Azure Database for MySQL

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png

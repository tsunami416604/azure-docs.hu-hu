---
title: Azure Active Directory-Azure Database for PostgreSQL – egyetlen kiszolgáló használata
description: Ismerje meg, hogyan állíthat be Azure Active Directory (HRE) a Azure Database for PostgreSQL – egyetlen kiszolgálóval történő hitelesítéshez
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: aacffbfdec67d7faa58cb8bd08f99963fb78263a
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299277"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Azure Active Directory használata a PostgreSQL-sel való hitelesítéshez

Ebből a cikkből megtudhatja, hogyan konfigurálhatja Azure Active Directory hozzáférését Azure Database for PostgreSQL és hogyan csatlakozhat Azure AD-jogkivonat használatával.

> [!IMPORTANT]
> A Azure Database for PostgreSQL Azure AD-hitelesítése jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Az Azure AD-rendszergazda felhasználó beállítása

Csak az Azure AD-rendszergazda felhasználó hozhat létre/engedélyezhet felhasználókat az Azure AD-alapú hitelesítéshez. Az Azure AD-rendszergazda felhasználó létrehozásához kövesse az alábbi lépéseket

1. A Azure Portal válassza ki a Azure Database for PostgreSQL azon példányát, amelyet engedélyezni kíván az Azure AD-ben.
2. A beállítások területen válassza a Active Directory rendszergazda elemet:

![Az Azure ad-rendszergazda beállítása][2]

3. Válasszon ki egy érvényes Azure AD-felhasználót az ügyfél-bérlőben az Azure AD-rendszergazdaként.

> [!IMPORTANT]
> A rendszergazda beállításakor a rendszer új felhasználót ad hozzá a Azure Database for PostgreSQL-kiszolgálóhoz teljes körű rendszergazdai engedélyekkel. Az Azure AD rendszergazdai felhasználója Azure Database for PostgreSQL a szerepkör `azure_ad_admin`.

PostgreSQL-Kiszolgálónként csak egy Azure AD-rendszergazda hozható létre, és a másik lehetőség kiválasztása esetén a rendszer felülírja a kiszolgálóhoz konfigurált meglévő Azure AD-rendszergazdát. Egy Azure AD-csoportot az egyes felhasználók helyett több rendszergazdával is megadhat. Vegye figyelembe, hogy ezt követően a csoport nevét kell bejelentkeznie adminisztrációs célokra.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Azure AD-felhasználók létrehozása a Azure Database for PostgreSQLban

Ha Azure AD-felhasználót szeretne hozzáadni a Azure Database for PostgreSQL-adatbázishoz, hajtsa végre a következő lépéseket a csatlakozás után (lásd: a kapcsolódási útmutató későbbi szakasza):

1. Először győződjön meg arról, hogy az Azure AD-felhasználó `<user>@yourtenant.onmicrosoft.com` érvényes felhasználó az Azure AD-bérlőben.
2. Jelentkezzen be az Azure Database for PostgreSQL-példányba az Azure AD-rendszergazda felhasználóként.
3. Szerepkör-`<user>@yourtenant.onmicrosoft.com` létrehozása a Azure Database for PostgreSQLban.
4. Hozzon `<user>@yourtenant.onmicrosoft.com` a szerepkör azure_ad_user tagjaként. Ezt csak az Azure AD-felhasználók kapják meg.

**Példa**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Egy felhasználó Azure AD-n keresztüli hitelesítése nem biztosít semmilyen engedélyt a felhasználónak a Azure Database for PostgreSQL-adatbázisban lévő objektumok eléréséhez. A szükséges engedélyeket manuálisan kell megadnia a felhasználónak.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Azure AD-csoportok létrehozása a Azure Database for PostgreSQLban

Ha engedélyezni szeretné az Azure AD-csoport számára az adatbázishoz való hozzáférést, használja ugyanazt a mechanizmust, mint a felhasználók számára, hanem adja meg a csoport nevét:

**Példa**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Bejelentkezéskor a csoport tagjai a személyes hozzáférési jogkivonatokat fogják használni, de a felhasználónévként megadott csoport nevével jelentkezhetnek be.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Csatlakozás az Azure Database for PostgreSQLhoz az Azure AD használatával

A következő magas szintű diagram összefoglalja az Azure AD-hitelesítés Azure Database for PostgreSQL használatával történő használatának munkafolyamatát:

![Hitelesítési folyamat][1]

Az Azure AD-integrációt úgy terveztük, hogy közös PostgreSQL-eszközökkel (például psql) működjön együtt, amelyek nem ismerik az Azure AD-t, és csak a Felhasználónév és a jelszó megadását támogatják a PostgreSQL-hez való csatlakozáskor. Az Azure AD-tokent jelszóként adjuk át a fenti képen látható módon.

Jelenleg a következő ügyfeleket teszteltük:

- psql commandline (használja a PGPASSWORD változót a jogkivonat átadásához, lásd alább)
- Azure Data Studio (a PostgreSQL bővítmény használatával)
- Más libpq-alapú ügyfelek (például közös alkalmazás-keretrendszerek és ORMs)

> [!NOTE]
> Vegye figyelembe, hogy az Azure AD-jogkivonat a pgAdmin-mel való használata jelenleg nem támogatott, mivel a jelszavakra vonatkozó 256 karakteres korlátozást tartalmaz (a jogkivonat mérete meghaladja a tokent).

Ezek a lépések, amelyekkel egy felhasználónak/alkalmazásnak a következőkben ismertetett Azure AD-hitelesítéssel kell rendelkeznie:

### <a name="step-1-authenticate-with-azure-ad"></a>1\. lépés: hitelesítés az Azure AD-vel

Győződjön meg arról, hogy az [Azure CLI telepítve](/cli/azure/install-azure-cli)van.

Hívja meg az Azure CLI eszközt az Azure AD-vel való hitelesítéshez. Ehhez meg kell adnia az Azure AD-beli felhasználói azonosítót és a jelszót.

```
az login
```

Ezzel a paranccsal megnyílik egy böngészőablak az Azure AD-hitelesítés lapra.

> [!NOTE]
> Ezen lépések végrehajtásához Azure Cloud Shell is használhatja.
> Vegye figyelembe, hogy amikor az Azure AD hozzáférési jogkivonatot a Azure Cloud Shell beolvassa, explicit módon meg kell hívnia `az login` és újra be kell jelentkeznie (a külön ablakban a kóddal). A bejelentkezés után a `get-access-token` parancs a várt módon fog működni.

### <a name="step-2-retrieve-azure-ad-access-token"></a>2\. lépés: az Azure AD hozzáférési jogkivonatának beolvasása

Hívja meg az Azure CLI eszközt az 1. lépésben az Azure AD hitelesített felhasználó hozzáférési jogkivonatának beszerzéséhez az Azure Database for PostgreSQL eléréséhez.

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

A jogkivonat egy alapszintű 64 karakterlánc, amely kódolja a hitelesített felhasználóra vonatkozó összes információt, és amely a Azure Database for PostgreSQL szolgáltatásra irányul.

> [!NOTE]
> A hozzáférési jogkivonat érvényessége 5 perc és 60 perc között lehet. Javasoljuk, hogy közvetlenül a bejelentkezés megkezdése előtt szerezze be a hozzáférési jogkivonatot a Azure Database for PostgreSQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>3\. lépés: a token használata jelszóként a PostgreSQL-ben való bejelentkezéshez

Kapcsolódáskor a hozzáférési tokent a PostgreSQL felhasználói jelszavának kell használnia.

Ha a `psql` parancssori ügyfelet használja, a hozzáférési tokent át kell adni a `PGPASSWORD` környezeti változón keresztül, mivel a hozzáférési jogkivonat meghaladja azt a jelszót, amelyet a `psql` közvetlenül el tud fogadni:

Windows-példa:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Linux/macOS példa:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Mostantól a következőhöz hasonló módon kezdeményezheti a kapcsolatokat Azure Database for PostgreSQLhoz:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

Most már hitelesítve van a PostgreSQL-kiszolgálón az Azure AD-hitelesítéssel.

## <a name="token-validation"></a>Jogkivonat ellenőrzése

Azure Database for PostgreSQL Azure AD-hitelesítés biztosítja, hogy a felhasználó létezik a PostgreSQL-kiszolgálón, és ellenőrzi a jogkivonat érvényességét a jogkivonat tartalmának ellenőrzésével. A rendszer a következő jogkivonat-ellenőrzési lépéseket hajtja végre:

-   Az Azure AD aláírja a tokent, és nem módosították
-   A tokent az Azure AD adta ki a kiszolgálóhoz társított bérlőhöz
-   A jogkivonat nem járt le
-   Token a Azure Database for PostgreSQL erőforráshoz (és nem egy másik Azure-erőforráshoz)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Meglévő PostgreSQL-felhasználók áttelepítése Azure AD-alapú hitelesítésre

Engedélyezheti az Azure AD-hitelesítést a meglévő felhasználók számára. Két esetet érdemes figyelembe venni:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>1\. eset: a PostgreSQL felhasználóneve megegyezik az Azure AD egyszerű felhasználónevével

Abban az esetben, ha a meglévő felhasználók már megfelelnek az Azure AD-beli felhasználóneveknek, a `azure_ad_user` szerepkört megadhatja számukra az Azure AD-hitelesítés engedélyezéséhez:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Mostantól képesek lesznek bejelentkezni az Azure AD hitelesítő adataival ahelyett, hogy a korábban konfigurált PostgreSQL felhasználói jelszavukat használják.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>2\. eset: a PostgreSQL felhasználóneve eltér az Azure AD egyszerű Felhasználónevetől

Ha egy PostgreSQL-felhasználó vagy nem létezik az Azure AD-ben, vagy más felhasználóneve van, akkor az Azure AD-csoportok használatával hitelesítheti magát a PostgreSQL-felhasználóként. A meglévő Azure Database for PostgreSQL felhasználókat áttelepítheti az Azure AD-ba úgy, hogy létrehoz egy olyan nevű Azure AD-csoportot, amelynek a neve megegyezik a PostgreSQL-felhasználóval, majd megadhatja a szerepkört azure_ad_user a meglévő PostgreSQL-felhasználónak:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Ez azt feltételezi, hogy létrehozott egy "DBReadUser" csoportot az Azure AD-ben. Az adott csoportba tartozó felhasználók mostantól a felhasználóként bejelentkezhetnek az adatbázisba.

## <a name="next-steps"></a>További lépések

* Tekintse át a [Azure Database for PostgreSQL – egyetlen kiszolgálóval Azure Active Directory hitelesítéssel](concepts-aad-authentication.md) kapcsolatos általános fogalmakat

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png

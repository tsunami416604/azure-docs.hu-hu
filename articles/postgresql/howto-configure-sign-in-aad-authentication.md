---
title: Azure Active Directory használata – Azure Database for PostgreSQL – Single Server
description: Tudnivalók az Azure Active Directory (AAD) beállításáról az Azure Database for PostgreSQL – Single Server használatával való hitelesítéshez
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a9f12849525daeea69ece6e81077446f062e8889
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384398"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Az Azure Active Directory használata a PostgreSQL-rel való hitelesítéshez

Ez a cikk bemutatja az Azure Active Directory-hozzáférés konfigurálásának lépéseit a PostgreSQL Azure Database használatával, és hogyan csatlakozhat egy Azure AD-jogkivonat használatával.

> [!IMPORTANT]
> Az Azure AD-hitelesítés az Azure Database for PostgreSQL jelenleg nyilvános előzetes verzióban.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="setting-the-azure-ad-admin-user"></a>Az Azure AD-rendszergazda imassa beállítása

Csak egy Azure AD-rendszergazdai felhasználó hozhat létre/engedélyezhet felhasználókat az Azure AD-alapú hitelesítéshez. Az Azure AD-rendszergazdai felhasználók létrehozásához kövesse az alábbi lépéseket

1. Az Azure Portalon válassza ki az Azure Database for PostgreSQL, amely engedélyezni szeretné az Azure AD példányát.
2. A Beállítások csoportban válassza az Active Directory rendszergazdája lehetőséget:

![az Azure ad administrator beállítása][2]

3. Válasszon ki egy érvényes Azure AD-felhasználót az ügyfél-bérlőben, hogy az Azure AD-rendszergazda legyen.

> [!IMPORTANT]
> A rendszergazda beállításakor egy új felhasználó kerül az Azure Database for PostgreSQL kiszolgáló teljes rendszergazdai engedélyekkel. Az Azure AD-rendszergazdai felhasználó az Azure Database `azure_ad_admin`for PostgreSQL lesz a szerepkör.

PostgreSQL-kiszolgálónként csak egy Azure AD-rendszergazda hozható létre, és egy másik kiválasztása felülírja a kiszolgálóhoz konfigurált meglévő Azure AD-rendszergazdát. Megadhat egy Azure AD-csoportot az egyes felhasználók helyett, hogy több rendszergazdais legyen. Vegye figyelembe, hogy ezután adminisztrációs célokra a csoport nevével jelentkezik be.

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Azure AD-felhasználók létrehozása a PostgreSQL Azure Database szolgáltatásában

Ha azure AD-felhasználót szeretne hozzáadni az Azure Database for PostgreSQL-adatbázishoz, a csatlakozás után hajtsa végre a következő lépéseket (lásd a további szakaszt a csatlakozásról):

1. Először győződjön meg `<user>@yourtenant.onmicrosoft.com` arról, hogy az Azure AD-felhasználó egy érvényes felhasználó az Azure AD-bérlőben.
2. Jelentkezzen be az Azure Database for PostgreSQL-példány, mint az Azure AD-rendszergazdai felhasználó.
3. Hozzon `<user>@yourtenant.onmicrosoft.com` létre szerepkört a PostgreSQL Azure Database-ben.
4. Legyen `<user>@yourtenant.onmicrosoft.com` tagja a szerepnek azure_ad_user. Ezt csak az Azure AD-felhasználóknak kell megadni.

**Példa:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> A felhasználó hitelesítése az Azure AD-n keresztül nem ad a felhasználónak semmilyen engedélyt az Azure Database for PostgreSQL adatbázisobjektumainak eléréséhez. A felhasználónak manuálisan kell megadnia a szükséges engedélyeket.

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Azure AD-csoportok létrehozása a PostgreSQL Azure Database szolgáltatásában

Ha engedélyezni szeretné az Azure AD-csoportot az adatbázishoz való hozzáféréshez, használja ugyanazt a mechanizmust, mint a felhasználók esetében, de adja meg a csoport nevét:

**Példa:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

Bejelentkezéskor a csoport tagjai a személyes hozzáférési jogkivonataikat fogják használni, de a felhasználónévként megadott csoportnévvel jelentkeznek.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Csatlakozás a PostgreSQL Azure-adatbázisához az Azure AD használatával

Az alábbi magas szintű diagram összefoglalja az Azure AD-hitelesítés azure-beli Azure Database for PostgreSQL használatával kapcsolatos munkafolyamatot:

![hitelesítési folyamat][1]

Úgy terveztük meg az Azure AD-integrációt, hogy olyan gyakori PostgreSQL-eszközökkel működjön, mint a psql, amelyek nem azure AD-tudatosak, és csak a felhasználónév és a jelszó megadását támogatják a PostgreSQL-hez való csatlakozáskor. Az Azure AD-tokent jelszóként adjuk át, ahogy az a fenti képen látható.

Jelenleg a következő ügyfeleket teszteltük:

- psql parancssor (használja a PGPASSWORD változóát, hogy átadja a tokent, lásd alább)
- Azure Data Studio (a PostgreSQL kiterjesztéssel)
- Libpq-alapú egyéb ügyfelek (pl. közös alkalmazási keretrendszerek és ORM-ok)

> [!NOTE]
> Kérjük, vegye figyelembe, hogy az Azure AD-token pgAdmin használatával jelenleg nem támogatott, mivel a jelszó kódolható 256 karakter (amely a jogkivonat meghaladja) kódolva van.

Ezek azok a lépések, amelyeket egy felhasználónak/alkalmazásnak az alábbiakban ismertetett Azure AD-vel kell végeznie:

### <a name="step-1-authenticate-with-azure-ad"></a>1. lépés: Hitelesítés az Azure AD-vel

Győződjön meg arról, hogy telepítve van az [Azure CLI.](/cli/azure/install-azure-cli)

Az Azure CLI eszköz meghívása az Azure AD-vel való hitelesítéshez. Az Azure AD felhasználói azonosítóját és jelszavát meg kell adnia.

```azurecli-interactive
az login
```

Ez a parancs egy böngészőablakot indít el az Azure AD hitelesítési lapjára.

> [!NOTE]
> Az Azure Cloud Shell segítségével is végrehajthatja ezeket a lépéseket.
> Kérjük, vegye figyelembe, hogy az Azure AD-hozzáférési jogkivonat beolvasásakor az Azure Cloud Shellben explicit módon kell hívnia, `az login` és újra be kell jelentkeznie (a külön ablakban egy kóddal). Ezt követően `get-access-token` a jel a parancs fog működni a várt módon.

### <a name="step-2-retrieve-azure-ad-access-token"></a>2. lépés: Az Azure AD-hozzáférési jogkivonat lekérése

Az Azure CLI eszköz meghívásához az Azure AD által hitelesített felhasználó hozzáférési jogkivonatot szerezzen be az 1.

Példa (nyilvános felhőesetén):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

A fenti erőforrásértéket pontosan a látható módon kell megadni. Más felhők esetén az erőforrás-érték a következők használatával kereshető ki:

```azurecli-interactive
az cloud show
```

Az Azure CLI 2.0.71-es és újabb verziójához a parancs az alábbi, kényelmesebb verzióban adható meg az összes felhőhöz:

```azurecli-interactive
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

A jogkivonat egy Base 64 karakterlánc, amely kódolja a hitelesített felhasználó összes információt, és amely az Azure Database for PostgreSQL szolgáltatás.

> [!NOTE]
> A hozzáférési jogkivonat érvényessége 5 perc és 60 perc között van. Azt javasoljuk, hogy a hozzáférési jogkivonatot közvetlenül a PostgreSQL Azure Database-be való bejelentkezés megkezdése előtt kapja meg.

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>3. lépés: Token használata jelszóként a PostgreSQL-rel való bejelentkezéshez

Csatlakozáskor a hozzáférési jogkivonatot postgreSQL felhasználói jelszóként kell használnia.

A `psql` parancssori ügyfél használatakor a hozzáférési jogkivonatot át kell adni a `PGPASSWORD` környezeti `psql` változón, mivel a hozzáférési jogkivonat meghaladja a közvetlenül elfogadható jelszóhosszt:

Windows példa:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Linux/macOS példa:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Most már kezdeményezhet kapcsolatot az Azure Database for PostgreSQL-lel, ahogy általában tenné:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```

Most már hitelesítve van a PostgreSQL-kiszolgálón az Azure AD-hitelesítés használatával.

## <a name="token-validation"></a>Token érvényesítése

Az Azure AD-hitelesítés az Azure Database for PostgreSQL biztosítja, hogy a felhasználó létezik a PostgreSQL-kiszolgálón, és ellenőrzi a jogkivonat érvényességét a jogkivonat tartalmának ellenőrzésével. A következő jogkivonat-érvényesítési lépések hajtják végre:

- A jogkivonatot az Azure AD írta alá, és nem módosították
- A jogkivonatot az Azure AD adta ki a kiszolgálóhoz társított bérlőszámára
- A token nem járt le
- A token az Azure Database for PostgreSQL erőforráshoz tartozik (és nem egy másik Azure-erőforráshoz)

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Meglévő PostgreSQL-felhasználók áttelepítése Az Azure AD-alapú hitelesítésre

Engedélyezheti az Azure AD-hitelesítést a meglévő felhasználók számára. Két esetet kell figyelembe venni:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>1. eset: A PostgreSQL felhasználónév megegyezik az Azure AD egyszerű felhasználónévvel

Abban a valószínűtlen esetben, ha a meglévő felhasználók már megfelelnek `azure_ad_user` az Azure AD-felhasználóneveknek, a szerepkört megadhatják nekik, hogy engedélyezze őket az Azure AD-hitelesítéshez:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Mostantól az Azure AD hitelesítő adataival is bejelentkezhetnek a korábban konfigurált PostgreSQL felhasználói jelszó használata helyett.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>2. eset: A PostgreSQL felhasználónév eltér az Azure AD egyszerű felhasználónévétól

Ha egy PostgreSQL-felhasználó vagy nem létezik az Azure AD-ben, vagy más felhasználónévvel rendelkezik, az Azure AD-csoportok segítségével hitelesítheti magát postgreSQL-felhasználóként. A PostgreSQL-felhasználók meglévő Azure-adatbázisát áttelepítheti az Azure AD-be úgy, hogy létrehoz egy Azure AD-csoportot a PostgreSQL-felhasználónak megfelelő névvel, majd szerepkört ad azure_ad_user a meglévő PostgreSQL-felhasználónak:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Ez feltételezi, hogy létrehozott egy "DBReadUser" csoportot az Azure AD-ben. Az adott csoporthoz tartozó felhasználók mostantól ezzel a felhasználóval jelentkezhetnek be az adatbázisba.

## <a name="next-steps"></a>További lépések

* Tekintse át az Azure Active Directory-hitelesítés általános fogalmait az [Azure Database for PostgreSQL - Single Server szolgáltatással](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png

---
title: Az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport biztonságának konfigurálása
description: Az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport biztonságának konfigurálása
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4f89ace7130e95ba109edcf6becca1e15c8d32c1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273200"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport biztonságának konfigurálása

Ez a dokumentum a kiszolgálócsoport biztonságával kapcsolatos különböző szempontokat ismerteti:
- Titkosítás inaktív állapotban
- Felhasználókezelés
   - Általános perspektívák
   - A _postgres_ rendszergazdai felhasználó jelszavának módosítása

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban
A titkosítást úgy is megvalósíthatja, ha titkosítja azokat a lemezeket, amelyeken az adatbázisait tárolja és/vagy az adatbázis-függvények segítségével titkosítja a beszúrt vagy frissített adatok titkosítását.

### <a name="hardware-linux-host-volume-encryption"></a>Hardver: Linux rendszerű gazdagép kötetének titkosítása
A rendszerszintű adattitkosítás megvalósítása az Azure arc-kompatibilis Data Services telepítőjének által használt lemezeken található összes adat védelméhez. A témakörről további információt talál:
- Inaktív [adatok titkosítása](https://wiki.archlinux.org/index.php/Data-at-rest_encryption) Linuxon – általánosságban 
- Lemezes titkosítás a LUKS `cryptsetup` encrypt paranccsal (Linux) ( https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) különösen, mivel az Azure Arc engedélyezve Data Services az Ön által megadott fizikai infrastruktúrán fut, Ön az infrastruktúra védelméért felelős.

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>Szoftver: használja a PostgreSQL `pgcrypto` -bővítményt a kiszolgálói csoportban
Az Azure arc telepítőjének üzemeltetéséhez használt lemezek titkosítása mellett beállíthatja az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgáló csoportját, hogy elérhetővé tegye azokat a mechanizmusokat, amelyeket az alkalmazásai használhatnak az adatbázis (ok) ban tárolt adatok titkosításához. A `pgcrypto` bővítmény a postgres bővítményeinek részét képezi, `contrib` és elérhető az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálói csoportban. A bővítmény részleteit `pgcrypto` [itt](https://www.postgresql.org/docs/current/pgcrypto.html)találja.
Összefoglalva, a következő parancsokkal engedélyezheti a bővítményt, és használhatja azt:


#### <a name="create-the-pgcrypto-extension"></a>A `pgcrypto` bővítmény létrehozása
Kapcsolódjon a kiszolgálói csoporthoz az Ön által választott ügyfél-eszközzel, és futtassa a standard PostgreSQL-lekérdezést:
```console
CREATE EXTENSION pgcrypto;
```

> A kapcsolódással kapcsolatban [itt](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md) talál részleteket.

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>Ellenőrizze, hogy a bővítmények listája használatra készen áll-e a kiszolgálói csoportban
A `pgcrypto` kiszolgáló csoportjában elérhető bővítmények listázásával ellenőrizheti, hogy a bővítmény készen áll-e a használatra.
Kapcsolódjon a kiszolgálói csoporthoz az Ön által választott ügyfél-eszközzel, és futtassa a standard PostgreSQL-lekérdezést:
```console
select * from pg_extension;
```
Meg kell jelennie, hogy `pgcrypto` engedélyezte és létrehozta a fent jelzett parancsokkal.

#### <a name="use-the-pgcrypto-extension"></a>A `pgcrypto` bővítmény használata
Most már beállíthatja az alkalmazások kódját úgy, hogy a által kínált funkciókat használják `pgcrypto` :
- Általános kivonatolási függvények
- Jelszó-kivonatolási függvények
- PGP titkosítási függvények
- Nyers titkosítási függvények
- Véletlenszerű adatkezelő függvények

Például a kivonatoló értékek létrehozásához. Futtassa a következő parancsot:

```console
Select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

A következő kivonatot adja vissza:

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

Vagy például:

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

a következő kivonatot adja vissza:

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

Vagy például a titkosított adatfájlok (például jelszó) tárolására:

Tegyük fel, hogy az alkalmazás a következő táblázatban tárolja a titkokat:

```console
create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
```

És titkosítom a jelszót a felhasználó létrehozásakor:

```console
insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
```

Most látom, hogy a jelszavam titkosított:

```console
select * from mysecrets;
```

Kimenet:

- USERid: 1
- Felhasználónév: nekem
- USERpassword: $1 $ Uc7jzZOp $ NTfcGo7F10zGOkXOwjHy31

Amikor csatlakozom az alkalmazáshoz, és elfogadok egy jelszót, megkeresi a táblázatot, és visszaadja `mysecrets` a felhasználó nevét, ha az alkalmazáshoz megadott jelszó és a táblázatban tárolt jelszavak között egyezés van. Példa:

- A helytelen jelszót adja meg:
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   Kimenet 

   ```returns
    USERname
   ---------
   (0 rows)
   ```
- Átadjuk a helyes jelszót:

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ``` 

   Kimenet:

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

Ez a kis példa azt mutatja be, hogy a postgres-bővítmény használatával titkosíthatja az inaktív adatok (titkosított adatok tárolása) az Azure arc-kompatibilis PostgreSQL-nagy kapacitású, `pgcrypto` és az alkalmazások használhatják a `pgcrypto` titkosított adatok kezelésére szolgáló függvényeket.

## <a name="user-management"></a>Felhasználókezelés
### <a name="general-perspectives"></a>Általános perspektívák
A felhasználók vagy szerepkörök létrehozásához használhatja a standard postgres-módszert. Ha azonban így tesz, ezek az összetevők csak a koordinátor szerepkörön lesznek elérhetők. Az előzetes verzió ideje alatt ezek a felhasználók/szerepkörök még nem férnek hozzá a koordinátor csomóponton és a kiszolgálócsoport munkavégző csomópontjain kívül terjesztett adatokhoz. Ennek az az oka, hogy az előzetes verzióban a felhasználó definíciója nem replikálódik a munkavégző csomópontokra.

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>A _postgres_ rendszergazdai felhasználó jelszavának módosítása
Az Azure arc engedélyezve PostgreSQL nagy kapacitású a szabványos postgres rendszergazdai felhasználói _postgres_ tartozik, amelyhez a kiszolgáló csoport létrehozásakor a jelszót állítja be.
A jelszó módosítására szolgáló parancs általános formátuma a következő:
```console
azdata arc postgres server edit --name <server group name> --admin-password
```

Ahol a--admin-password egy olyan logikai érték, amely a AZDATA_PASSWORD **munkamenet**környezeti változójában lévő értékek jelenlétére vonatkozik.
Ha a AZDATA_PASSWORD **munkamenet**környezeti változója létezik, és rendelkezik értékkel, a fenti parancs futtatásakor a postgres-felhasználó jelszava a környezeti változó értékére lesz állítva.

Ha a AZDATA_PASSWORD **munkamenet**környezeti változója létezik, de nem értékkel rendelkezik, vagy a AZDATA_PASSWORD **munkamenet**környezeti változója nem létezik, a fenti parancs futtatásakor a rendszer felszólítja a felhasználót, hogy interaktívan adja meg a jelszót.

#### <a name="changing-the-password-of-the-postgres-administrative-user-in-an-interactive-way"></a>A postgres rendszergazda felhasználó jelszavának módosítása interaktív módon:
1. Törölje a AZDATA_PASSWORD **munkamenet**környezeti változóját, vagy törölje annak értékét.
2. Futtassa a következő parancsot:
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   Példa:
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   A rendszer kérni fogja a jelszó megadását és megerősítését:
   ```console
   Postgres Server password:
   Confirm Postgres Server password:
   ```
   A jelszó frissítésekor a parancs kimenete a következőket jeleníti meg:
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```
   
#### <a name="changing-the-password-of-the-postgres-administrative-user-using-the-azdata_password-sessions-environment-variable"></a>A postgres rendszergazdai felhasználó jelszavának módosítása a AZDATA_PASSWORD **munkamenet**környezeti változójának használatával:
1. Állítsa be az AZDATA_PASSWORD- **munkamenet**környezeti változójának értékét arra a értékre, amelyet jelszóként szeretne használni.
2. Futtassa a parancsot:
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   Példa:
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   
   A jelszó frissítésekor a parancs kimenete a következőket jeleníti meg:
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```

> [!NOTE]
> Annak ellenőrzéséhez, hogy létezik-e a AZDATA_PASSWORD munkamenet környezeti változója, és hogy milyen értéket tartalmaz, futtassa a következőt:
> - Linux-ügyfélen:
> ```console
> printenv AZDATA_PASSWORD
> ```
>
> - Windows-ügyfélen PowerShell-lel:
> ```console
> echo $env:AZDATA_PASSWORD
> ```



## <a name="next-steps"></a>Következő lépések
- Olvassa el a bővítmény részleteit `pgcrypto` . [here](https://www.postgresql.org/docs/current/pgcrypto.html)
- [Itt](using-extensions-in-postgresql-hyperscale-server-group.md)olvashat részletesen arról, hogyan használhatja a postgres-bővítményeket.


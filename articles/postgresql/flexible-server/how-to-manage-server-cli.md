---
title: Kiszolgáló kezelése – Azure CLI – Azure Database for PostgreSQL – rugalmas kiszolgáló
description: Megtudhatja, hogyan kezelheti Azure Database for PostgreSQL-rugalmas kiszolgálót az Azure CLI-vel.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 0f3c21d5c7f328ddef000ca7f1eaa9d5e18e6ca9
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761889"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Azure Database for PostgreSQL rugalmas kiszolgáló kezelése az Azure CLI használatával

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Ez a cikk bemutatja, hogyan kezelheti az Azure-ban üzembe helyezett rugalmas kiszolgálót. A felügyeleti feladatok közé tartozik a számítási és tárolási skálázás, a rendszergazdai jelszó alaphelyzetbe állítása és a kiszolgáló adatainak megtekintése.

## <a name="prerequisites"></a>Előfeltételek
Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot. Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verzióját kell futtatnia helyileg. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az az [login](https://docs.microsoft.com/cli/azure/reference-index#az-login) parancs használatával kell bejelentkeznie a fiókjába. Jegyezze fel az **ID** tulajdonságot, amely az Azure-fiók **előfizetés-azonosítójára** utal.

```azurecli-interactive
az login
```

Válassza ki az adott előfizetést a fiókja alatt az [az Account set](/cli/azure/account) parancs használatával. Jegyezze fel az **azonosító** értéket az az **login** kimenetből, amelyet a parancs **előfizetés** argumentumának értékeként kíván használni. Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetés beszerzéséhez használja [az az Account List](https://docs.microsoft.com/cli/azure/account#az-account-list)lehetőséget.

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Ha még nem hozott létre rugalmas kiszolgálót, hozzon létre egyet, hogy megismerkedjen az útmutatóval.

## <a name="scale-compute-and-storage"></a>Számítási és tárolási méretezés

A számítási szintet, a virtuális mag és a tárterületet könnyedén méretezheti a következő parancs használatával. Az összes olyan kiszolgálói műveletet megtekintheti, amelyet futtat az [postgres flexibilis-Server Server Overview](https://azure.microsoft.com/services/postgresql/)

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

A fenti argumentumok részletei:

**Beállítás** | **Mintaérték** | **Leírás**
---|---|---
name | mydemoserver | Adjon meg egy egyedi nevet a kiszolgáló számára. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. 3–63 karakter hosszúságú lehet.
resource-group | myResourceGroup | Adja meg az Azure-erőforráscsoport nevét.
sku-name|Standard_D4ds_v3|Adja meg a számítási rétegek és a méret nevét. A következő konvenciót követi: Standard_ {VM size} a gyorsírásban. További információkért tekintse meg a [díjszabási szintet](../concepts-pricing-tiers.md) .
storage-size | 6144 | A kiszolgáló tárkapacitása (megabájtban megadva). A 5120 minimális és a 1024-os növekmények száma.

> [!IMPORTANT]
> A tárterület nem méretezhető le. 

## <a name="manage-postgresql-databases-on-a-server"></a>PostgreSQL-adatbázisok kezelése kiszolgálón

Számos különféle alkalmazással csatlakozhat a PostgreSQL-kiszolgálóhoz készült Azure-adatbázishoz. Ha az ügyfélszámítógépen telepítve van a PostgreSQL, akkor használhatja a [psql](https://www.postgresql.org/docs/current/static/app-psql.html) helyi példányát az Azure PostgreSQL-kiszolgálóhoz való csatalakozáshoz. Használjuk a psql parancssori segédprogramot az Azure PostgreSQL-kiszolgálóhoz való kapcsolódáshoz.

1. Futtassa a következő psql parancsot az Azure-adatbázis PostgreSQL-kiszolgálóhoz való kapcsolódáshoz

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Például a következő parancs a **postgres** nevű alapértelmezett adatbázishoz kapcsolódik a **mydemoserver.postgres.database.azure.com** PostgreSQL-kiszolgálón a hozzáférési hitelesítő adatok használatával. Adja meg a `<server_admin_password>` kiszolgálói rendszergazdai jelszót, amelyet a jelszó megadásakor választott.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   A csatlakozás után a psql segédprogram egy postgres promptot jelenít meg, ahová az SQL-parancsokat beírhatja. A rendszer a kezdeti kapcsolati kimenetben egy figyelmeztetést jeleníthet meg, mivel a psql használt verziója eltérhet az Azure Database for PostgreSQL-kiszolgáló verziójától.

   Példa psql kimenetre:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Ha a tűzfal nincs konfigurálva az ügyfél IP-címének engedélyezésére, a következő hiba jelenik meg:
   >
   > "psql: végzetes: nincs pg_hba. conf bejegyzés a gazdagéphez `<IP address>` ," myadmin "," postgres "adatbázis" ", SSL on végzetes: SSL-kapcsolat szükséges. Adja meg az SSL-beállításokat, és próbálkozzon újra.
   >
   > Győződjön meg arról, hogy az ügyfél IP-címe engedélyezve van a tűzfalszabályok fenti lépésében.

2. Hozzon létre egy "postgresdb" nevű üres adatbázist a parancssorba a következő parancs beírásával:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. A parancssorban hajtsa végre a következő parancsot az újonnan létrehozott adatbázis- **postgresdb**való kapcsolatok váltásához:

    ```bash
    \c postgresdb
    ```

4. A psql-ből való kilépéshez írja be a `\q` parancsot, majd nyomja le az Enter billentyűt.

Ezzel csatlakozott az Azure Database for PostgreSQL-kiszolgálóhoz a psql-en keresztül, és létrehozott egy üres felhasználói adatbázist.

## <a name="reset-admin-password"></a>Rendszergazdai jelszó visszaállítása
A rendszergazdai szerepkör jelszava ezzel a paranccsal módosítható
```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Ügyeljen arra, hogy a jelszó legalább 8 karakterből álljon, és legfeljebb 128 karakterből álljon.
> A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs betűk, számok és nem alfanumerikus karakterek.

## <a name="delete-a-server"></a>Kiszolgáló törlése

Ha csak szeretné törölni a PostgreSQL rugalmas kiszolgálót, futtathatja az [az postgres flexibilis-Server delete](/cli/azure/PostgreSQL/server#az-PostgreSQL-flexible-server-delete) parancsot.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Következő lépések

- [A biztonsági mentési és visszaállítási fogalmak ismertetése](concepts-backup-restore.md)
- [A kiszolgáló hangolása és figyelése](concepts-monitoring.md)

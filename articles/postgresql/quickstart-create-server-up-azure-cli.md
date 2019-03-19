---
title: Rövid útmutató – Azure-adatbázis létrehozása postgresql-kiszolgálóhoz egy egyszerű Azure CLI-paranccsal – az postgres mentése (előzetes verzió) használatával
description: A rövid útmutató hozhat létre az Azure Database for PostgreSQL-kiszolgáló használatával az Azure CLI (parancssori felület) parancsot.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2019
ms.openlocfilehash: 0db49e2c370aee37cca4181cecbe4cf0b5585c51
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136444"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-using-a-simple-azure-cli-command---az-postgres-up-preview"></a>Gyors útmutató: Hozzon létre egy Azure Database for PostgreSQL szolgáltatás egy egyszerű Azure CLI-paranccsal - be (előzetes verzió) az postgres

> [!IMPORTANT]
> A [fel az postgres](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI-paranccsal előzetes verzióban érhető el.

A PostgreSQL-hez készült Azure Database felügyelt szolgáltatás, amely lehetővé teszi a magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, kezelését és skálázását a felhőben. Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan használható a [fel az postgres](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) paranccsal hozzon létre egy Azure Database for PostgreSQL-kiszolgálóhoz az Azure CLI használatával. A kiszolgáló létrehozása mellett a `az postgres up` parancs hoz létre egy mintaadatbázist, a gyökér szintű felhasználó az adatbázisban, megnyitja a tűzfalat az Azure-szolgáltatásokhoz és az ügyfélszámítógép tűzfal-szabályokat hoz létre a alapértelmezett. Ez segít a fejlesztési folyamat meggyorsítása.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ehhez a cikkhez futtat az Azure CLI verziója a 2.0-s vagy újabb helyileg. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Jelentkezzen be a fiók használatával kell a [az bejelentkezési](/cli/azure/authenticate-azure-cli?view=interactive-log-in) parancsot. Jegyezze fel a megfelelő előfizetésnév parancskimenetéből az **id** tulajdonságot.

```azurecli
az login
```

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account) paranccsal. Helyettesítse be a **előfizetés-azonosító** tulajdonságot a **az bejelentkezési** kimeneti be az előfizetés-azonosító helyőrzője helyére.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

A parancsok használatához telepítse a [db felfelé](/cli/azure/ext/db-up) bővítmény. Ha hibát ad vissza, győződjön meg arról, telepítette az Azure CLI legújabb verzióját. Lásd: [az Azure CLI telepítése](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Hozzon létre egy Azure Database for PostgreSQL-kiszolgáló a következő paranccsal:

```azurecli
az postgres up
```

A kiszolgáló létrehozása az alábbi alapértelmezett értékekkel (kivéve, ha manuálisan felül):

**Beállítás** | **Alapértelmezett érték** | **Leírás**
---|---|---
server-name | A rendszer által létrehozott | Egy egyedi név, amely az Azure Database for PostgreSQL-kiszolgálót azonosítja.
resource-group | A rendszer által létrehozott | Egy új Azure-erőforráscsoportot.
sku-name | GP_Gen5_2 | A termékváltozat neve. A {tarifacsomag}\_{számítási generáció}\_{virtuális magok} mintát követi rövidített módon. Az alapértelmezett érték egy általános célú Gen5 kiszolgáló 2 virtuális maggal. Tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/postgresql/) a rétegek további információt.
backup-retention | 7 | Az az időtartam, ameddig egy biztonsági mentést meg kell őrizni. A mértékegysége a nap.
geo-redundant-backup | Letiltva | Azt adja meg, hogy a georedundáns biztonsági mentést engedélyezni kell-e ehhez a kiszolgálóhoz.
location | westus2 | A kiszolgáló Azure-helye.
ssl-enforcement | Letiltva | Azt adja meg, hogy engedélyezni kell-e az ssl-t ehhez a kiszolgálóhoz.
storage-size | 5120 | A kiszolgáló tárkapacitása (megabájtban megadva).
version | 10 | A PostgreSQL főverziója.
admin-user | A rendszer által létrehozott | A rendszergazda bejelentkezéshez használt felhasználóneve.
admin-password | A rendszer által létrehozott | A rendszergazda felhasználó jelszava.

> [!NOTE]
> További információ a `az postgres up` parancs, és a további paraméterek, tekintse meg a [Azure CLI dokumentációját](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up).

A kiszolgáló létrehozása után a következő beállításokkal rendelkezik:

- Egy tűzfalszabály "devbox" néven jön létre. Az Azure CLI megpróbálja észlelni a gép IP-címét a `az postgres up` parancs futtatható és engedélyezési listákkal az adott IP-cím.
- "Az Azure-szolgáltatásokhoz való hozzáférés engedélyezése" be van állítva. Ezzel a beállítással a kiszolgálója tűzfalán, hogy az összes Azure-erőforrás, beleértve az erőforrásokat az előfizetésben nem-kapcsolatok fogadására.
- Egy "sampledb" nevű üres adatbázis létrehozása
- Nevű, "root", "sampledb" jogosultságokkal rendelkező új felhasználó létrehozása

> [!NOTE]
> Azure Database for PostgreSQL 5432-es porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Rendelkezik az IT-részleg a kiszolgálóhoz való csatlakozáshoz az 5432-es port megnyitásához.

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

Miután a `az postgres up` parancs végrehajtása után, a kapcsolati karakterláncok olyan népszerű programozási nyelvet küld vissza azt. Ezek a kapcsolati karakterláncok olyan előre konfigurált az újonnan létrehozott Azure Database for PostgreSQL-kiszolgáló adott attribútumait.

Használhatja a [az postgres show-connection-string](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) paranccsal listát készíthet a kapcsolati karakterláncok újra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a rövid útmutató az alábbi parancs használatával létrehozott összes erőforrást törölni. Ez a parancs törli az Azure Database for PostgreSQL-kiszolgáló és az erőforráscsoportot.

```azurecli
az postgres down --delete-group
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja [le az postgres](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down) parancsot.

```azurecli
az postgres down
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)

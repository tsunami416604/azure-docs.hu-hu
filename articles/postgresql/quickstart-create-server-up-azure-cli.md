---
title: 'Rövid útmutató: Kiszolgáló létrehozása - az postgres up - Azure Database for PostgreSQL - Single Server'
description: Rövid útmutató az Azure Database for PostgreSQL – Single Server létrehozásához az Azure CLI (parancssori felület) fel- és felezési parancs parancsával.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: fe15c02286223ec0829b31664811b7f589cf16aa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74774832"
---
# <a name="quickstart-use-an-azure-cli-command-az-postgres-up-preview-to-create-an-azure-database-for-postgresql---single-server"></a>Rövid útmutató: Azure CLI-parancs használata, az postgres up (előzetes verzió) használatával hozzon létre egy Azure-adatbázist a PostgreSQL- Single Server számára

> [!IMPORTANT]
> Az [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI parancs előzetes verzióban érhető el.

A PostgreSQL-hez készült Azure Database felügyelt szolgáltatás, amely lehetővé teszi a magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, kezelését és skálázását a felhőben. Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan használhatja az [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) parancsot egy Azure Database for PostgreSQL-kiszolgáló hoz létre az Azure CLI használatával. A kiszolgáló létrehozása mellett `az postgres up` a parancs létrehoz egy mintaadatbázist, egy gyökérfelhasználót az adatbázisban, megnyitja a tűzfalat az Azure-szolgáltatások számára, és alapértelmezett tűzfalszabályokat hoz létre az ügyfélszámítógép számára. Ezek az alapértelmezések segítenek a fejlesztési folyamat felgyorsításában.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

Ez a cikk megköveteli, hogy az Azure CLI 2.0-s vagy újabb verzióját helyileg fut. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az [az bejelentkezési](/cli/azure/authenticate-azure-cli?view=interactive-log-in) paranccsal be kell jelentkeznie a fiókjába. Vegye figyelembe az **ID** tulajdonságot a parancskimenetből a megfelelő előfizetésnevéhez.

```azurecli
az login
```

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account) paranccsal. Helyettesítse az **előfizetés-azonosító** tulajdonságát az **az bejelentkezési** kimenetből az előfizetési azonosító helyőrzőjéhez.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

A parancsok használatához telepítse a [db-up](/cli/azure/ext/db-up) bővítményt. Ha egy hiba ad vissza, győződjön meg arról, hogy telepítette az Azure CLI legújabb verzióját. Lásd: [Azure CLI telepítése](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Hozzon létre egy Azure Database for PostgreSQL-kiszolgálót a következő paranccsal:

```azurecli
az postgres up
```

A kiszolgáló a következő alapértelmezett értékekkel jön létre (kivéve, ha manuálisan felülbírálja őket):

**Beállítás** | **Alapértelmezett érték** | **Leírás**
---|---|---
server-name | A rendszer létrehozva | Egy egyedi név, amely az Azure Database for PostgreSQL-kiszolgálót azonosítja.
resource-group | A rendszer létrehozva | Egy új Azure-erőforráscsoport.
sku-name | GP_Gen5_2 | A termékváltozat neve. A {tarifacsomag}\_{számítási generáció}\_{virtuális magok} mintát követi rövidített módon. Az alapértelmezett beállítás egy 2 virtuális maggal rendelkező Általános célú Gen5-kiszolgáló. A szintekről további információt a [díjszabási oldalunkon](https://azure.microsoft.com/pricing/details/postgresql/) talál.
backup-retention | 7 | A biztonsági mentés megőrzési ideje. A mértékegysége a nap.
geo-redundant-backup | Letiltva | Azt adja meg, hogy a georedundáns biztonsági mentést engedélyezni kell-e ehhez a kiszolgálóhoz.
location | westus2 | A kiszolgáló Azure-helye.
ssl-enforcement | Letiltva | Azt adja meg, hogy engedélyezni kell-e az ssl-t ehhez a kiszolgálóhoz.
storage-size | 5120 | A kiszolgáló tárkapacitása (megabájtban megadva).
version | 10 | A PostgreSQL főverziója.
admin-user | A rendszer létrehozva | A rendszergazda felhasználóneve.
admin-password | A rendszer létrehozva | A rendszergazda felhasználó jelszava.

> [!NOTE]
> A parancsról `az postgres up` és annak további paramétereiről az [Azure CLI dokumentációjában](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up)olvashat bővebben.

A kiszolgáló létrehozása után a következő beállításokkal érkezik:

- Létrejön a "devbox" nevű tűzfalszabály. Az Azure CLI megpróbálja észlelni a `az postgres up` gép IP-címét, ahonnan a parancs fut, és az adott IP-cím engedélyezési listáit.
- "Hozzáférés engedélyezése az Azure-szolgáltatásokhoz" beállítás be van kapcsolva. Ez a beállítás úgy konfigurálja a kiszolgáló tűzfalát, hogy az összes Azure-erőforrásból, beleértve az előfizetésben nem szereplő erőforrásokat is, fogadja a kapcsolatokat.
- Létrejön egy "sampledb" nevű üres adatbázis
- Létrejön egy "root" nevű új felhasználó, amely "sampledb" jogosultságokkal rendelkezik

> [!NOTE]
> A PostgreSQL Azure Database az 5432-es porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. A kiszolgálóhoz való csatlakozáshoz nyissa meg az informatikai részlege az 5432-es portot.

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A `az postgres up` parancs befejezése után a népszerű programozási nyelvek kapcsolati karakterláncainak listája megjelenik. Ezek a kapcsolati karakterláncok előre konfigurálva vannak az újonnan létrehozott Azure Database for PostgreSQL-kiszolgáló egyedi attribútumaival.

Az az [postgres show-connection-string](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-show-connection-string) paranccsal újra listázhatja ezeket a kapcsolati karakterláncokat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A következő paranccsal törölje a rövid útmutatóban létrehozott összes erőforrást. Ez a parancs törli az Azure Database for PostgreSQL-kiszolgálót és az erőforráscsoportot.

```azurecli
az postgres down --delete-group
```

Ha csak törölni szeretné az újonnan létrehozott kiszolgálót, futtathatja az [postgres down](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-down) parancsot.

```azurecli
az postgres down
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)

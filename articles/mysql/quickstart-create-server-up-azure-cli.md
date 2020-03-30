---
title: 'Rövid útmutató: Azure-adatbázis létrehozása a MySQL számára az az mysql up használatával'
description: Rövid útmutató az Azure Database for MySQL-kiszolgálóhoz az Azure CLI (parancssori felület) up parancs használatával történő létrehozásához.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc
ms.openlocfilehash: 7b81e88fe6f658fdf4c1857c6082100894c6f2f6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067722"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Rövid útmutató: Hozzon létre egy Azure-adatbázist a MySQL-hez egy egyszerű Azure CLI paranccsal - az mysql up (előzetes verzió)

> [!IMPORTANT]
> Az [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI parancs előzetes verzióban érhető el.

A MySQL-hez készült Azure Database egy felügyelt szolgáltatás, amely lehetővé teszi a magas rendelkezésre állású MySQL-adatbázisok futtatását, kezelését és skálázását a felhőben. Az Azure CLI azure-erőforrások létrehozására és kezelésére szolgál a parancssorból vagy parancsfájlokban. Ez a rövid útmutató bemutatja, hogyan használhatja az [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) parancsot egy Azure Database for MySQL-kiszolgáló hoz létre az Azure CLI használatával. A kiszolgáló létrehozása mellett `az mysql up` a parancs létrehoz egy mintaadatbázist, egy gyökérfelhasználót az adatbázisban, megnyitja a tűzfalat az Azure-szolgáltatások számára, és alapértelmezett tűzfalszabályokat hoz létre az ügyfélszámítógép számára. Ez segít a fejlesztési folyamat felgyorsításában.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

Ez a cikk megköveteli, hogy az Azure CLI 2.0-s vagy újabb verzióját helyileg fut. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az [az bejelentkezési](/cli/azure/authenticate-azure-cli?view=interactive-log-in) paranccsal be kell jelentkeznie a fiókjába. Jegyezze fel a megfelelő előfizetésnév parancskimenetéből az **id** tulajdonságot.

```azurecli
az login
```

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account) paranccsal. Helyettesítse az **előfizetés-azonosító** tulajdonságát az **az bejelentkezési** kimenetből az előfizetési azonosító helyőrzőjéhez.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz

A parancsok használatához telepítse a [db-up](/cli/azure/ext/db-up) bővítményt. Ha egy hiba ad vissza, győződjön meg arról, hogy telepítette az Azure CLI legújabb verzióját. Lásd: [Azure CLI telepítése](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Hozzon létre egy Azure-adatbázist a MySQL-kiszolgálóhoz a következő paranccsal:

```azurecli
az mysql up
```

A kiszolgáló a következő alapértelmezett értékekkel jön létre (kivéve, ha manuálisan felülbírálja őket):

**Beállítás** | **Alapértelmezett érték** | **Leírás**
---|---|---
server-name | A rendszer létrehozva | Egy egyedi név, amely azonosítja a MySQL-kiszolgálóhoz készült Azure-adatbázist.
resource-group | A rendszer létrehozva | Egy új Azure-erőforráscsoport.
sku-name | GP_Gen5_2 | A termékváltozat neve. A {tarifacsomag}\_{számítási generáció}\_{virtuális magok} mintát követi rövidített módon. Az alapértelmezett beállítás egy 2 virtuális maggal rendelkező Általános célú Gen5-kiszolgáló. A szintekről további információt a [díjszabási oldalunkon](https://azure.microsoft.com/pricing/details/mysql/) talál.
backup-retention | 7 | Az az időtartam, ameddig egy biztonsági mentést meg kell őrizni. A mértékegysége a nap.
geo-redundant-backup | Letiltva | Azt adja meg, hogy a georedundáns biztonsági mentést engedélyezni kell-e ehhez a kiszolgálóhoz.
location | westus2 | A kiszolgáló Azure-helye.
ssl-enforcement | Letiltva | Azt jelzi, hogy az SSL-t engedélyezni kell-e ehhez a kiszolgálóhoz.
storage-size | 5120 | A kiszolgáló tárkapacitása (megabájtban megadva).
version | 5.7 | A MySQL legújabb főverziója.
admin-user | A rendszer létrehozva | A rendszergazda bejelentkezéshez használt felhasználóneve.
admin-password | A rendszer létrehozva | A rendszergazda felhasználó jelszava.

> [!NOTE]
> A parancsról `az mysql up` és annak további paramétereiről az [Azure CLI dokumentációjában](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up)olvashat bővebben.

A kiszolgáló létrehozása után a következő beállításokkal érkezik:

- Létrejön a "devbox" nevű tűzfalszabály. Az Azure CLI megpróbálja észlelni a `az mysql up` gép IP-címét, ahonnan a parancs fut, és az adott IP-cím engedélyezési listáit.
- "Hozzáférés engedélyezése az Azure-szolgáltatásokhoz" beállítás be van kapcsolva. Ez a beállítás úgy konfigurálja a kiszolgáló tűzfalát, hogy az összes Azure-erőforrásból, beleértve az előfizetésben nem szereplő erőforrásokat is, fogadja a kapcsolatokat.
- A `wait_timeout` paraméter 8 órára van állítva
- Létrejön egy "sampledb" nevű üres adatbázis
- Létrejön egy "root" nevű új felhasználó, amely "sampledb" jogosultságokkal rendelkezik

> [!NOTE]
> Az Azure Database for MySQL a 3306-os porton keresztül kommunikál. Ha vállalati hálózaton belülről csatlakozik, előfordulhat, hogy a 3306-os porton keresztüli kimenő forgalmat a hálózat tűzfala nem engedélyezi. A kiszolgálóhoz való csatlakozáshoz nyissa meg az informatikai részlege a 3306-os portot.

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A `az mysql up` parancs befejezése után a népszerű programozási nyelvek kapcsolati karakterláncainak listája megjelenik. Ezek a kapcsolati karakterláncok előre konfigurálva vannak az újonnan létrehozott Azure Database for MySQL-kiszolgáló egyedi attribútumaival.

Az az [mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) paranccsal újra listázhatja ezeket a kapcsolati karakterláncokat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A következő paranccsal törölje a rövid útmutatóban létrehozott összes erőforrást. Ez a parancs törli az Azure Database for MySQL-kiszolgáló és az erőforráscsoport.

```azurecli
az mysql down --delete-group
```

Ha csak törölni szeretné az újonnan létrehozott kiszolgálót, futtathatja az [mysql down](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) parancsot.

```azurecli
az mysql down
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [MySQL-adatbázis tervezése az Azure CLI-vel](./tutorial-design-database-using-cli.md)

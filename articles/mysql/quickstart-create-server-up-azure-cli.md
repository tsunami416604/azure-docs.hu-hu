---
title: Rövid útmutató – Azure-adatbázis létrehozása MySQL-CLI-paranccsal egy egyszerű Azure - be (előzetes verzió) az mysql-hez
description: A rövid útmutató hozhat létre az Azure Database for MySQL-kiszolgálóhoz az Azure CLI (parancssori felület) használatával parancsot.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2019
ms.custom: mvc
ms.openlocfilehash: aa0d2a9e990faa8d99355744824f34e26aeb519e
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137912"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Gyors útmutató: Egy Azure Database for MySQL használatával egy egyszerű Azure CLI-paranccsal - be (előzetes verzió) az mysql létrehozása

> [!IMPORTANT]
> A [fel az mysql](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI-paranccsal előzetes verzióban érhető el.

A MySQL-hez készült Azure Database egy felügyelt szolgáltatás, amely lehetővé teszi a magas rendelkezésre állású MySQL-adatbázisok futtatását, kezelését és skálázását a felhőben. Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan használható a [fel az mysql](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) paranccsal hozzon létre egy Azure Database for MySQL-kiszolgálóhoz az Azure CLI használatával. A kiszolgáló létrehozása mellett a `az mysql up` parancs hoz létre egy mintaadatbázist, a gyökér szintű felhasználó az adatbázisban, megnyitja a tűzfalat az Azure-szolgáltatásokhoz és az ügyfélszámítógép tűzfal-szabályokat hoz létre a alapértelmezett. Ez segít a fejlesztési folyamat meggyorsítása.

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

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz

A parancsok használatához telepítse a [db felfelé](/cli/azure/ext/db-up) bővítmény. Ha hibát ad vissza, győződjön meg arról, telepítette az Azure CLI legújabb verzióját. Lásd: [az Azure CLI telepítése](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Hozzon létre egy Azure Database for MySQL-kiszolgáló a következő paranccsal:

```azurecli
az mysql up
```

A kiszolgáló létrehozása az alábbi alapértelmezett értékekkel (kivéve, ha manuálisan felül):

**Beállítás** | **Alapértelmezett érték** | **Leírás**
---|---|---
server-name | A rendszer által létrehozott | Egy egyedi név, amely azonosítja a MySQL-kiszolgálóhoz készült Azure-adatbázist.
resource-group | A rendszer által létrehozott | Egy új Azure-erőforráscsoportot.
sku-name | GP_Gen5_2 | A termékváltozat neve. A {tarifacsomag}\_{számítási generáció}\_{virtuális magok} mintát követi rövidített módon. Az alapértelmezett érték egy általános célú Gen5 kiszolgáló 2 virtuális maggal. Tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/mysql/) a rétegek további információt.
backup-retention | 7 | Az az időtartam, ameddig egy biztonsági mentést meg kell őrizni. A mértékegysége a nap.
geo-redundant-backup | Letiltva | Azt adja meg, hogy a georedundáns biztonsági mentést engedélyezni kell-e ehhez a kiszolgálóhoz.
location | westus2 | A kiszolgáló Azure-helye.
ssl-enforcement | Letiltva | Azt adja meg, hogy engedélyezni kell-e az ssl-t ehhez a kiszolgálóhoz.
storage-size | 5120 | A kiszolgáló tárkapacitása (megabájtban megadva).
version | 5.7 | A MySQL legújabb főverziója.
admin-user | A rendszer által létrehozott | A rendszergazda bejelentkezéshez használt felhasználóneve.
admin-password | A rendszer által létrehozott | A rendszergazda felhasználó jelszava.

> [!NOTE]
> További információ a `az mysql up` parancs, és a további paraméterek, tekintse meg a [Azure CLI dokumentációját](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

A kiszolgáló létrehozása után a következő beállításokkal rendelkezik:

- Egy tűzfalszabály "devbox" néven jön létre. Az Azure CLI megpróbálja észlelni a gép IP-címét a `az mysql up` parancs futtatható és engedélyezési listákkal az adott IP-cím.
- "Az Azure-szolgáltatásokhoz való hozzáférés engedélyezése" be van állítva. Ezzel a beállítással a kiszolgálója tűzfalán, hogy az összes Azure-erőforrás, beleértve az erőforrásokat az előfizetésben nem-kapcsolatok fogadására.
- A `wait_timeout` paraméter értéke: 8 óránként
- Egy "sampledb" nevű üres adatbázis létrehozása
- Nevű, "root", "sampledb" jogosultságokkal rendelkező új felhasználó létrehozása

> [!NOTE]
> Azure Database for MySQL-hez a 3306-os porton keresztül kommunikál. Ha egy vállalati hálózaton belül csatlakozik, a hálózati tűzfal előfordulhat, hogy nem engedélyezett a kimenő forgalom a 3306-os porton keresztül. Rendelkezik az IT-részleg, nyissa meg a kiszolgálóhoz való csatlakozáshoz a 3306-os portot.

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

Miután a `az mysql up` parancs végrehajtása után, a kapcsolati karakterláncok olyan népszerű programozási nyelvet küld vissza azt. Ezek a kapcsolati karakterláncok olyan előre konfigurált az újonnan létrehozott Azure Database for MySQL-kiszolgáló adott attribútumait.

Használhatja a [az mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) paranccsal listát készíthet a kapcsolati karakterláncok újra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez a rövid útmutató az alábbi parancs használatával létrehozott összes erőforrást törölni. Ez a parancs törli az Azure Database for MySQL-kiszolgáló és az erőforráscsoportot.

```azurecli
az mysql down --delete-group
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja [le az mysql](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) parancsot.

```azurecli
az mysql down
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [MySQL-adatbázis tervezése az Azure CLI-vel](./tutorial-design-database-using-cli.md)

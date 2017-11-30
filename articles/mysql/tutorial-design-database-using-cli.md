---
title: "Az első Azure-adatbázis MySQL-adatbázis - Azure parancssori felület kialakítása |} Microsoft Docs"
description: "Ez az oktatóanyag azt ismerteti, hogyan létrehozása és kezelése az Azure-adatbázis a MySQL-kiszolgáló és az adatbázis az Azure CLI 2.0 verziót használja a parancssorból."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 11/28/2017
ms.custom: mvc
ms.openlocfilehash: f17f2cab39b42341886ed86e1c08569ca8f5eff0
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="design-your-first-azure-database-for-mysql-database"></a>Az első Azure-adatbázis MySQL-adatbázis megtervezése

A MySQL az Azure adatbázisa MySQL Community Edition adatbázismotor alapján a Microsoft felhőalapú relációs adatbázis-szolgáltatás. Ebben az oktatóanyagban használhatja az Azure parancssori felület (parancssori felület) és egyéb segédprogramok megtudhatja, hogyan:

> [!div class="checklist"]
> * A MySQL az Azure-adatbázis létrehozása
> * A kiszolgáló tűzfal konfigurálása
> * Használjon [mysql parancssori eszköz](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) adatbázis létrehozása
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

Az Azure-felhő rendszerhéj lehet használni a böngésző vagy [Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli) ebben az oktatóanyagban a kódblokkok futtatásához a saját számítógépen.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

Ha több előfizetéssel rendelkezik, válassza a megfelelő előfizetést, amelyen az erőforrás megtalálható vagy terhelve van. Válasszon ki egy megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#az_account_set) parancs segítségével.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy [Azure erőforráscsoport](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) rendelkező [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) parancsot. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `mycliresource` helyen.

```azurecli-interactive
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz
Azure-adatbázis létrehozása a az mysql kiszolgálóval MySQL server létre parancsot. Egy kiszolgáló több adatbázist is tud kezelni. Általában külön adatbázissal rendelkezik minden projekt vagy felhasználó.

A következő példában létrehozunk egy `mycliserver` nevű Azure-adatbázist MySQL-kiszolgálóhoz a `mycliresource` erőforráscsoportban a `westus`-ben. A kiszolgáló rendelkezik egy `myadmin` nevű rendszergazdai fiókkal, amelyhez a jelszó `Password01!`. A kiszolgáló **Alapszintű** teljesítményszinttel van létrehozva, valamint **50** számítási egység van megosztva a kiszolgálón lévő összes adatbázis között. Az alkalmazás szükségleteitől függően csökkentheti vagy növelheti a számítási egységeket és a tárterületet.

```azurecli-interactive
az mysql server create --resource-group mycliresource --name mycliserver --location westus --admin-user myadmin --admin-password Password01! --performance-tier Basic --compute-units 50
```

## <a name="configure-firewall-rule"></a>Tűzfalszabály konfigurálása
Azure-adatbázis létrehozása a MySQL kiszolgálószintű tűzfalszabályt a az mysql-tűzfalszabályt a create parancs. Egy kiszolgálószintű tűzfalszabályt lehetővé teszi a külső alkalmazás, például a **mysql** parancssori eszköz vagy a MySQL-munkaterület Telepítésmegosztási csatlakozni a kiszolgálóhoz, az Azure-beli MySQL szolgáltatás tűzfalon keresztül. 

Az alábbi példa létrehoz egy előre meghatározott címtartományba egy tűzfalszabályt. Ez a példa bemutatja a teljes lehetséges az IP-címeket.

```azurecli-interactive
az mysql server firewall-rule create --resource-group mycliresource --server mycliserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat.
```azurecli-interactive
az mysql server show --resource-group mycliresource --name mycliserver
```

Az eredmény JSON formátumban van. Jegyezze fel a következőket: **fullyQualifiedDomainName** és **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mycliserver.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mycliresource/providers/Microsoft.DBforMySQL/servers/mycliserver",
  "location": "westus",
  "name": "mycliserver",
  "resourceGroup": "mycliresource",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-mysql"></a>Csatlakozzon a kiszolgálóhoz, a mysql használata
Használja a [mysql parancssori eszköz](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) kapcsolatot létrehozni a MySQL-kiszolgálóhoz tartozó Azure-adatbázis számára. Ebben a példában a parancs a következő:
```cmd
mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
```

## <a name="create-a-blank-database"></a>Hozzon létre egy üres adatbázist
Ha a kiszolgáló csatlakozik, egy üres adatbázis létrehozásához.
```sql
mysql> CREATE DATABASE mysampledb;
```

A parancssorba a következő parancs futtatásával váltson a kapcsolat az újonnan létrehozott adatbázis futtatásához:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Hozzon létre táblák az adatbázisban
Most, hogy tudja, hogyan MySQL-adatbázis az Azure-adatbázishoz való kapcsolódáshoz, néhány egyszerű feladatot kell elvégeznie:

Először hozzon létre egy táblát, és töltse be az adatokat. Hozzon létre egy tábla tárolja a leltáradatokat.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Adatok betöltése a táblákba
Most, hogy a tábla, néhány adat beviteléhez bele. A parancssor megnyitása ablakban futtassa a következő lekérdezés szúrható be néhány sornyi adatot.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Most már rendelkezik mintaadatok a táblázatba a korábban létrehozott két sorát.

## <a name="query-and-update-the-data-in-the-tables"></a>Lekérdezés, és frissítse a táblák adatait
A következő lekérdezés futtatásával adatok lekérését a adatbázistábla hajtható végre.
```sql
SELECT * FROM inventory;
```

A tábla adatait is frissítheti.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A sor ennek megfelelően frissül, az adatok.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Adatbázis visszaállítása egy korábbi időpontra
Képzelje el ezt a táblázatot véletlenül törölt. Ez a valami nem egyszerűen állíthat helyre. Azure MySQL-adatbázis lehetővé teszi térjen vissza olyan hely, az utolsó legfeljebb 35 nappal időpont, és állítsa vissza ezt a pontot az új kiszolgáló időt. Az új kiszolgáló segítségével helyreállíthatja a törölt adatokat. Az alábbi lépéseket a a minta kiszolgáló pontra visszaállításához, mielőtt a tábla hozzá lett adva.

A visszaállítás a következő információk szükségesek:

- Visszaállítási pont: Válasszon egy pont időponthoz kötött, amely akkor fordul elő, mielőtt a kiszolgáló megváltozott. Nagyobb vagy egyenlő a forrásadatbázis legrégebbi biztonsági mentési értéknek kell lennie.
- Célkiszolgáló: Adjon meg egy új kiszolgálónevet a visszaállítani kívánt
- Forráskiszolgáló: Adja meg a visszaállítani kívánt kiszolgáló nevét
- Hely: Nem választhat ki a régiót, alapértelmezés szerint ugyanaz, mint a forráskiszolgálón

```azurecli-interactive
az mysql server restore --resource-group mycliresource --name mycliserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mycliserver
```

A kiszolgáló visszaállítása és [állítsa vissza egy-időpontban](./howto-restore-server-portal.md) előtt a tábla törölve lett. Egy kiszolgáló helyreállítása egy másik pontra időben ismétlődő új kiszolgáló létrehozása a pont frissítésétől eredeti kiszolgálóként időben ad meg, feltéve, hogy a megőrzési időtartamon belül a [szolgáltatásréteg](./concepts-service-tiers.md).

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag megtanulta, hogy:
> [!div class="checklist"]
> * A MySQL az Azure-adatbázis létrehozása
> * A kiszolgáló tűzfal konfigurálása
> * Használjon [mysql parancssori eszköz](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) adatbázis létrehozása
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

> [!div class="nextstepaction"]
> [Azure-adatbázis a MySQL - Azure CLI-minták](./sample-scripts-azure-cli.md)

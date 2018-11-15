---
title: 'Oktatóanyag: Azure CLI-vel MariaDB-hez készült Azure Database tervezése'
description: Ez az oktatóanyag azt ismerteti, hogyan hozhat létre és kezelhető az Azure Database for MariaDB-kiszolgáló és az adatbázis az Azure parancssori felület használatával a parancssorból.
services: mariadb
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 11/10/2018
ms.custom: mvc
ms.openlocfilehash: 2d715f89b56af822c2c1174cca0f2a9c1b847fc0
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516337"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-azure-cli"></a>Oktatóanyag: Azure CLI-vel MariaDB-hez készült Azure Database tervezése

MariaDB-hez készült Azure Database egy MariaDB Community Edition adatbázismotorján alapul a Microsoft felhőalapú relációs adatbázis-szolgáltatás. Ebben az oktatóanyagban az Azure CLI (parancssori felület) és egyéb segédprogramok segítségével a következőket sajátíthatja el:

> [!div class="checklist"]
> * Azure Database for MariaDB létrehozása
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a [mysql parancssori eszközzel](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

Egy böngészőben az Azure Cloud Shell használatával vagy a számítógépen az [Azure CLI telepítésével]( /cli/azure/install-azure-cli) futtathatja az oktatóanyag kódblokkjait.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

Ha több előfizetéssel rendelkezik, válassza a megfelelő előfizetést, amelyen az erőforrás megtalálható vagy terhelve van. Válasszon ki egy megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#az-account-set) parancs segítségével.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy [Azure-erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) az [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myresourcegroup` helyen.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB-kiszolgáló létrehozása
MariaDB-kiszolgálóhoz készült Azure-adatbázis létrehozása a `az mariadb server create` parancsot. Egy kiszolgáló több adatbázist is tud kezelni. Általában külön adatbázissal rendelkezik minden projekt vagy felhasználó.

Az alábbi példa létrehoz egy Azure Database for MariaDB-kiszolgálóhoz `westus` erőforráscsoportban `myresourcegroup` nevű `mydemoserver`. A kiszolgáló egy `myadmin` nevű rendszergazdai bejelentkezéssel rendelkezik. Egy általános célú, 2 virtuális mag Gen 5 kiszolgáló. A `<server_admin_password>` helyére írja be saját értékét.

```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```
Az sku-name paraméter értéke a {tarifacsomag}\_{számítási generáció}\_{virtuális magok} mintát követi, a következő példákban látható módon:
+ Az `--sku-name B_Gen5_4` jelentése: Alapszintű, 5. generációs és 4 virtuális mag.
+ `--sku-name GP_Gen5_32` jelentése: Általános célú, 5. generációs és 32 virtuális mag.
+ `--sku-name MO_Gen5_2` jelentése: Memóriaoptimalizált, 5. generációs és 2 virtuális mag.

A [Tarifacsomagok](./concepts-pricing-tiers.md) dokumentumban megtekintheti az érvényes értékeket régiónként és csomagonként.

> [!IMPORTANT]
> A kiszolgáló itt megadott rendszergazdai bejelentkezési nevét és jelszavát kell majd használnia a rövid útmutató későbbi szakaszaiban a kiszolgálóra és az adatbázisaira való bejelentkezéshez. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.


## <a name="configure-firewall-rule"></a>Tűzfalszabály konfigurálása
Hozzon létre egy Azure Database for MariaDB-kiszolgálószintű tűzfalszabályt a `az mariadb server firewall-rule create` parancsot. Kiszolgálószintű tűzfalszabály lehetővé teszi, hogy külső alkalmazások, például **mysql** parancssori eszközzel vagy a MySQL Workbench csatlakozzon a kiszolgálóhoz az Azure MariaDB szolgáltatás tűzfalán keresztül.

A következő példában egy olyan `AllowMyIP` nevű tűzfalszabályt hozunk létre, amely a 192.168.0.1 IP-címről engedélyezi a kapcsolódást. Helyettesítse be a csatlakozási helyének megfelelő IP-címet vagy IP-címtartományt.

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat.
```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Az eredmény JSON formátumban van. Jegyezze fel a következőket: **fullyQualifiedDomainName** és **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-the-server-using-mysql"></a>Csatlakozás a kiszolgálóhoz a mysql használatával
A [mysql parancssori eszközzel](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) építsen ki egy kapcsolatot az Azure Database for MariaDB-kiszolgálóhoz. Példánkban a parancs a következő:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Hozzon létre egy üres adatbázist
Miután csatlakozott a kiszolgálóhoz, hozzon létre egy üres adatbázist.
```sql
mysql> CREATE DATABASE mysampledb;
```

Futtassa a parancssorban a következő parancsot a kapcsolat az újonnan létrehozott adatbázisra történő átállítására:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Táblák létrehozása az adatbázisban
Most, hogy tudja, hogyan csatlakozhat az Azure Database for MariaDB-adatbázis, végezzünk el néhány alapvető feladatot.

Először hozzunk létre egy táblát, és töltsük fel adatokkal. Hozzunk létre egy táblát leltáradatok tárolásához.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Adatok betöltése a táblákba
Most, hogy már rendelkezünk egy táblával, szúrjunk be néhány adatot. A megnyitott parancssori ablakban futtassa a következő lekérdezést néhány adatsor beszúrásához.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Most két mintaadatsorral rendelkezünk a korábban létrehozott táblában.

## <a name="query-and-update-the-data-in-the-tables"></a>A táblákban lévő adatok lekérdezése és frissítése
Hajtsa végre a következő lekérdezést az adatbázistáblában lévő információk lekéréséhez.
```sql
SELECT * FROM inventory;
```

A táblákban lévő adatokat frissítheti is.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A sor az adatok lekérésekor megfelelően frissül.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Adatbázis visszaállítása egy korábbi időpontra
Tegyük fel, hogy véletlenül töröltünk egy fontos adatbázistáblát. Ez nem olyasvalami, ami könnyen helyreállítható. MariaDB-hez készült Azure Database lehetővé teszi, hogy bármely időpontra az idő az elmúlt 35 napon belül, és ezen a ponton visszaállítása egy új kiszolgálóra. Az új kiszolgáló segítségével helyreállíthatja a törölt adatokat. Az alábbi lépések a mintakiszolgálót a tábla hozzáadása előtti időpontra állítják vissza.

A visszaállításhoz a rendszer a következő információk megadását kéri:

- Visszaállítási pont: Válasszon ki egy, a kiszolgáló módosítása előtti időpontot. Ez a pont nem lehet korábbi, mint az adatbázis legrégebbi biztonsági másolata.
- Célkiszolgáló: Adja meg az új kiszolgáló nevét, amelyre a biztonsági másolatot vissza kívánja állítani.
- Forráskiszolgáló: Adja meg a kiszolgáló nevét, amelyről a biztonsági másolatot vissza kívánja állítani.
- Hely: Nem választhatja ki a régiót – alapértelmezés szerint ugyanaz lesz, mint a forráskiszolgálóé.

```azurecli-interactive
az mariadb server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

Az `az mariadb server restore` parancshoz a következő paraméterekre van szükség:
| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az erőforráscsoport, amelyben a forráskiszolgáló található.  |
| név | mydemoserver-restored | A visszaállítási paranccsal létrehozott új kiszolgáló neve. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Válassza ki az időpontot, amelynek az állapotát vissza szeretné állítani. Ennek a dátumnak és időnek a forráskiszolgáló biztonsági mentésének megőrzési időszakán belül kell lennie. ISO8601 dátum- és időformátumot használjon. Használhatja például a saját helyi időzónáját (például `2017-04-13T05:59:00-08:00`), de UTC Zulu formátumot is használhat (`2017-04-13T13:59:00Z`). |
| source-server | mydemoserver | A forráskiszolgáló neve vagy azonosítója, amelyről a visszaállítást végzi. |

Ha egy adott időpontra állít vissza egy kiszolgálót, azzal létrehoz egy újat, amely az eredeti kiszolgáló adott időpontra jellemző állapotának másolata. A visszaállított kiszolgáló hely- és tarifacsomag-értékei ugyanazok, mint a forráskiszolgálón.

Egy szinkron parancsról van szó, amelyet a rendszer a kiszolgáló visszaállítása után visszaad. Ha a visszaállítás kész, keresse meg a létrehozott új kiszolgálót. Ellenőrizze, hogy az adatok a várt módon álltak-e vissza.

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag a következőket mutatta be:
> [!div class="checklist"]
> * Azure Database for MariaDB-kiszolgáló létrehozása
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a [mysql parancssori eszközzel](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

---
title: "Az első Azure Database for MySQL-adatbázis megtervezése – Azure CLI | Microsoft Docs"
description: "Ez az oktatóanyag azt ismerteti, hogyan hozható létre és kezelhető az Azure Database for MySQL-kiszolgáló és -adatbázis az Azure CLI 2.0 a parancssorból történő használatával."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 11/28/2017
ms.custom: mvc
ms.openlocfilehash: 5f323086ce66a504188c1834d20873a52a990311
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="design-your-first-azure-database-for-mysql-database"></a>Az első Azure Database for MySQL-adatbázis megtervezése

Az Azure Database for MySQL egy relációsadatbázis-szolgáltatás a Microsoft Cloudban, amely a MySQL Community Edition adatbázismotoron alapul. Ebben az oktatóanyagban az Azure CLI (parancssori felület) és egyéb segédprogramok segítségével a következőket sajátíthatja el:

> [!div class="checklist"]
> * Azure Database for MySQL létrehozása
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a [mysql parancssori eszközzel](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

Egy böngészőben az Azure Cloud Shell használatával vagy a számítógépen az [Azure CLI 2.0 telepítésével]( /cli/azure/install-azure-cli) futtathatja az oktatóanyag kódblokkjait.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

Ha több előfizetéssel rendelkezik, válassza a megfelelő előfizetést, amelyen az erőforrás megtalálható vagy terhelve van. Válasszon ki egy megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#az_account_set) parancs segítségével.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy [Azure-erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) az [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `mycliresource` helyen.

```azurecli-interactive
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz
Hozzon létre egy Azure Database for MySQL-kiszolgálót az az mysql server create paranccsal. Egy kiszolgáló több adatbázist is tud kezelni. Általában külön adatbázissal rendelkezik minden projekt vagy felhasználó.

A következő példában létrehozunk egy `mycliserver` nevű Azure-adatbázist MySQL-kiszolgálóhoz a `mycliresource` erőforráscsoportban a `westus`-ben. A kiszolgáló rendelkezik egy `myadmin` nevű rendszergazdai fiókkal, amelyhez a jelszó `Password01!`. A kiszolgáló **Alapszintű** teljesítményszinttel van létrehozva, valamint **50** számítási egység van megosztva a kiszolgálón lévő összes adatbázis között. Az alkalmazás szükségleteitől függően csökkentheti vagy növelheti a számítási egységeket és a tárterületet.

```azurecli-interactive
az mysql server create --resource-group mycliresource --name mycliserver --location westus --admin-user myadmin --admin-password Password01! --performance-tier Basic --compute-units 50
```

## <a name="configure-firewall-rule"></a>Tűzfalszabály konfigurálása
Hozzon létre egy Azure Database for MySQL-kiszolgálószintű tűzfalszabályt az az mysql server firewall-rule create paranccsal. A kiszolgálószintű tűzfalszabályok olyan külső alkalmazások használatát teszik lehetővé, mint a **mysql** parancssori eszköz vagy a MySQL Workbench, amelyekkel kapcsolódhat a kiszolgálóhoz az Azure-beli MySQL-szolgáltatás tűzfalán keresztül. 

A következő példában létrehozunk egy tűzfalszabályt egy előre meghatározott címtartományhoz. Ebben a példában ez az IP-címek teljes lehetséges tartományát lefedi.

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

## <a name="connect-to-the-server-using-mysql"></a>Csatlakozás a kiszolgálóhoz a mysql használatával
A [mysql parancssori eszköz](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) használatával építsen ki egy kapcsolatot az Azure Database for MySQL-kiszolgálóhoz. Példánkban a parancs a következő:
```cmd
mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
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
Most, hogy tudja, hogyan csatlakozhat az Azure Database for MySQL-adatbázishoz, végezzünk el néhány alapvető feladatot:

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
Tegyük fel, hogy véletlenül töröltünk egy fontos adatbázistáblát. Ez nem olyasvalami, ami könnyen helyreállítható. Az Azure Database for MySQL segítségével az elmúlt 35 nap bármelyik pillanatára vissza lehet térni, és ezt a pontot egy új kiszolgálóra vissza lehet állítani. Az új kiszolgáló segítségével helyreállíthatja a törölt adatokat. Az alábbi lépések a mintakiszolgálót a tábla hozzáadása előtti időpontra állítják vissza.

A visszaállításhoz a rendszer a következő információk megadását kéri:

- Visszaállítási pont: Válasszon ki egy, a kiszolgáló módosítása előtti időpontot. Ez a pont nem lehet korábbi, mint az adatbázis legrégebbi biztonsági másolata.
- Célkiszolgáló: Adja meg az új kiszolgáló nevét, amelyre a biztonsági másolatot vissza kívánja állítani.
- Forráskiszolgáló: Adja meg a kiszolgáló nevét, amelyről a biztonsági másolatot vissza kívánja állítani.
- Hely: Nem választhatja ki a régiót – alapértelmezés szerint ugyanaz lesz, mint a forráskiszolgálóé.

```azurecli-interactive
az mysql server restore --resource-group mycliresource --name mycliserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mycliserver
```

A kiszolgáló visszaállítása és [visszaállítása egy, a tábla törlését megelőző időpontra](./howto-restore-server-portal.md). A kiszolgáló egy másik időpontra való visszaállítása létrehozza a kiszolgáló a megadott időpontra vonatkozó duplikált új másolatát, amennyiben az adott pont a [szolgáltatásszint](./concepts-service-tiers.md) adatmegőrzési időszakán belül esik.

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag a következőket mutatta be:
> [!div class="checklist"]
> * Azure Database for MySQL létrehozása
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a [mysql parancssori eszközzel](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

> [!div class="nextstepaction"]
> [Azure Database for MySQL – Azure CLI-minták](./sample-scripts-azure-cli.md)

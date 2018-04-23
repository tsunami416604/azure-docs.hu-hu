---
title: 'Oktatóanyag: Azure Database for PostgreSQL tervezése az Azure CLI használatával'
description: Ez az oktatóanyag azt mutatja be, hogyan hozhatja létre, konfigurálhatja és kérdezheti le az első Azure Database for PostgreSQL-kiszolgálót az Azure CLI-vel.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 04/01/2018
ms.openlocfilehash: acba480631ba69a81da3029aadfb9cb51797549a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-design-an-azure-database-for-postgresql-using-azure-cli"></a>Oktatóanyag: Azure Database for PostgreSQL tervezése az Azure CLI használatával 
Ebben az oktatóanyagban az Azure CLI (parancssori felület) és egyéb segédprogramok segítségével a következőket sajátíthatja el:
> [!div class="checklist"]
> * Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) segédprogrammal
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

Az oktatóanyag parancsainak futtatásához használja az Azure Cloud Shellt egy böngészőben, vagy [telepítse az Azure CLI 2.0-t]( /cli/azure/install-azure-cli) a számítógépen.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

Ha több előfizetéssel rendelkezik, válassza a megfelelő előfizetést, amelyen az erőforrás megtalálható vagy terhelve van. Válasszon ki egy megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#az_account_set) parancs segítségével.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md) az [az group create](/cli/azure/group#az_group_create) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myresourcegroup` helyen.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz
Hozzon létre egy [Azure-adatbázist PostgreSQL- kiszolgálóhoz](overview.md) az [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) paranccsal. A kiszolgáló adatbázisok egy csoportját tartalmazza, amelyeket a rendszer egy csoportként kezel. 

A következő példában létrehozunk egy `mydemoserver` nevű kiszolgálót a `myresourcegroup` erőforráscsoportban `myadmin` kiszolgálói rendszergazdai bejelentkezéssel. A kiszolgáló neve DNS-névbe van leképezve, ezért globálisan egyedinek kell lennie az Azure-ban. A `<server_admin_password>` helyére írja be saját értékét. Ez egy általános célú 4. generációs kiszolgáló 2 virtuális maggal.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 9.6
```

> [!IMPORTANT]
> A kiszolgáló itt megadott rendszergazdai bejelentkezési nevét és jelszavát kell majd használnia a rövid útmutató későbbi szakaszaiban a kiszolgálóra és az adatbázisaira való bejelentkezéshez. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.

Alapértelmezés szerint a **postgres** adatbázis a kiszolgáló alatt jön létre. A [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) adatbázis egy alapértelmezett adatbázis, amelyet a felhasználók, segédprogramok és külső féltől származó alkalmazások általi használatra szántak. 


## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Hozzon létre egy Azure PostgreSQL kiszolgálószintű tűzfalszabályt az [az sql server firewall create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) paranccsal. Egy kiszolgálószintű tűzfalszabály lehetővé teszi olyan külső alkalmazások számára, mint a [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html), vagy a [PgAdmin](https://www.pgadmin.org/), hogy kapcsolódjon a kiszolgálóhoz az PostgreSQL szolgáltatás tűzfalán keresztül. 

Beállíthat egy olyan tűzfalszabályt, amely lefed egy IP-címtartományt, annak érdekében, hogy csatlakozni tudjon a saját hálózatából. A követező példában az [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) parancsot használjuk egy olyan `AllowAllIps` tűzfalszabály létrehozásához, amely minden IP-címről engedélyezi a kapcsolódást. Az összes IP-cím megnyitásához használja a 0.0.0.0 címet kezdő IP-címként és a 255.255.255.255 címet zárócímként.

Ha az Azure PostgreSQL-kiszolgáló hozzáférését a hálózaton belülre szeretné korlátozni, beállíthatja úgy a tűzfalszabályt, hogy csak a vállalati hálózat IP-címtartományáról engedélyezze a hozzáférést.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure PostgreSQL-kiszolgáló az 5432-es porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Kérje meg az informatikai részleget, hogy nyissa meg az 5432-es portot az Azure SQL Database-kiszolgálóhoz való csatlakozáshoz.
>

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

Az eredmény JSON formátumban van. Jegyezze fel a következőket: **administratorLogin** és **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
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
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"

}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Csatlakozás az Azure Database for PostgreSQL-hez psql használatával
Ha az ügyfélszámítógépen telepítve van a PostgreSQL, akkor használhatja a [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) helyi példányát vagy az Azure Cloud Console-t az Azure PostgreSQL-kiszolgálóhoz való csatlakozáshoz. Használjuk a psql parancssori segédprogramot az Azure-adatbázis PostgreSQL-kiszolgálóhoz való kapcsolódáshoz.

1. Futtassa a következő psql-parancsot az Azure Database for PostgreSQL-adatbázishoz való kapcsolódáshoz:
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Például a következő parancs a **postgres** nevű alapértelmezett adatbázishoz kapcsolódik a **mydemoserver.postgres.database.azure.com** PostgreSQL-kiszolgálón a hozzáférési hitelesítő adatok használatával. Adja meg a `<server_admin_password>` kiszolgálói rendszergazdai jelszót, amelyet a jelszó megadásakor választott.
  
  ```azurecli-interactive
psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
```

2.  Miután csatlakozott a kiszolgálóhoz, hozzon létre egy üres adatbázist, amikor a rendszer erre kéri:
```sql
CREATE DATABASE mypgsqldb;
```

3.  Amikor a rendszer kéri, hajtsa végre a következő parancsot, hogy az újonnan létrehozott **mypgsqldb** adatbázishoz kapcsolódhasson:
```sql
\c mypgsqldb
```

## <a name="create-tables-in-the-database"></a>Táblák létrehozása az adatbázisban
Most, hogy tudja, hogyan csatlakozhat az Azure Database for PostgreSQL szolgáltatáshoz, elvégezhet néhány alapvető feladatot:

Először hozzunk létre egy táblát, és töltsük fel adatokkal. Például hozzunk létre egy táblát a leltáradatok nyomon követéséhez:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Az újonnan létrehozott tábla a táblák listájában való megtekintéséhez írja be a következőt:
```sql
\dt
```

## <a name="load-data-into-the-table"></a>Adatok betöltése a táblába
Most, hogy létrehoztuk a táblát, szúrjunk be néhány adatot. A megnyitott parancssori ablakban futtassa a következő lekérdezést néhány adatsor beszúrásához:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Így két sornyi mintaadat került a korábban létrehozott táblába.

## <a name="query-and-update-the-data-in-the-tables"></a>A táblákban lévő adatok lekérdezése és frissítése
Hajtsa végre a következő lekérdezést a készlettáblában lévő információk lekéréséhez: 
```sql
SELECT * FROM inventory;
```

A készlettáblában található adatokat frissíteni is lehet:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

A frissített értékeket az adatok lekérésekor tekintheti meg:
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Adatbázis visszaállítása egy korábbi időpontra
Tegyük fel, hogy véletlenül törölt egy táblát. Ez nem olyasvalami, ami könnyen helyreállítható. Az Azure Database for PostgreSQL segítségével bármely olyan időpontra vissza lehet térni, amelyen a kiszolgálóról biztonsági mentés készült (ez a biztonsági mentések megőrzési idejéhez megadott beállításoktól függ), és az adott időpontra jellemző állapotot vissza lehet állítani egy új kiszolgálóra. Az új kiszolgáló segítségével helyreállíthatja a törölt adatokat. 

Az alábbi parancs a mintakiszolgálót egy, a tábla hozzáadása előtti időpontra állítja vissza:
```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mydemoserver
```

Az `az postgres server restore` parancshoz a következő paraméterekre van szükség:
| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  Az erőforráscsoport, amelyben a forráskiszolgáló található.  |
| név | mydemoserver-restored | A visszaállítási paranccsal létrehozott új kiszolgáló neve. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Válassza ki az időpontot, amelynek az állapotát vissza szeretné állítani. Ennek a dátumnak és időnek a forráskiszolgáló biztonsági mentésének megőrzési időszakán belül kell lennie. ISO8601 dátum- és időformátumot használjon. Használhatja például a saját helyi időzónáját (például `2017-04-13T05:59:00-08:00`), de UTC Zulu formátumot is használhat (`2017-04-13T13:59:00Z`). |
| source-server | mydemoserver | A forráskiszolgáló neve vagy azonosítója, amelyről a visszaállítást végzi. |

Ha egy adott időpontra állít vissza egy kiszolgálót, azzal létrehoz egy újat, amely az eredeti kiszolgáló adott időpontra jellemző állapotának másolata. A visszaállított kiszolgáló hely- és tarifacsomag-értékei ugyanazok, mint a forráskiszolgálón.

Egy szinkron parancsról van szó, amelyet a rendszer a kiszolgáló visszaállítása után visszaad. Ha a visszaállítás kész, keresse meg a létrehozott új kiszolgálót. Ellenőrizze, hogy az adatok a várt módon álltak-e vissza.


## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket az Azure CLI (parancssori felület) és más segédprogramok használatával:
> [!div class="checklist"]
> * Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz
> * A kiszolgáló tűzfalának konfigurálása
> * Adatbázis létrehozása a [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) segédprogrammal
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

Ha azt is meg szeretné tudni, hogyan végezhet el hasonló feladatokat az Azure Portallal, tekintse át a következő oktatóanyagot: [Az első Azure Database for PostgreSQL megtervezése az Azure Portallal](tutorial-design-database-using-azure-portal.md)

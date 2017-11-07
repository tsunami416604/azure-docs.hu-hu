---
title: "Az első Azure-adatbázis kialakítása a PostgreSQL Azure parancssori felületével |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan tervezhet az első Azure-adatbázis a PostgreSQL Azure parancssori felület használatával."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 11/03/2017
ms.openlocfilehash: d694618094679b23f1ca4a9cad1799b6b100454d
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="design-your-first-azure-database-for-postgresql-using-azure-cli"></a>Az első Azure-adatbázis kialakítása a PostgreSQL Azure parancssori felület használatával 
Ebben az oktatóanyagban használhatja az Azure parancssori felület (parancssori felület) és egyéb segédprogramok megtudhatja, hogyan:
> [!div class="checklist"]
> * Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz
> * A kiszolgáló tűzfal konfigurálása
> * Használjon [ **psql** ](https://www.postgresql.org/docs/9.6/static/app-psql.html) segédprogramot az adatbázis létrehozása
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

Az Azure-felhő rendszerhéj lehet használni a böngésző vagy [Azure CLI 2.0-s verzióját]( /cli/azure/install-azure-cli) saját számítógépen ebben az oktatóanyagban a parancsok futtatásához.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

Ha több előfizetéssel rendelkezik, válassza a megfelelő előfizetést, amelyen az erőforrás megtalálható vagy terhelve van. Válasszon ki egy megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#set) parancs segítségével.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md) az [az group create](/cli/azure/group#create) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myresourcegroup` helyen.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz
Hozzon létre egy [Azure-adatbázist PostgreSQL- kiszolgálóhoz](overview.md) az [az postgres server create](/cli/azure/postgres/server#create) paranccsal. A kiszolgáló adatbázisok egy csoportját tartalmazza, amelyeket a rendszer egy csoportként kezel. 

Az alábbi példakód létrehozza nevű kiszolgáló `mypgserver-20170401` az erőforráscsoportban `myresourcegroup` a kiszolgáló-rendszergazdai bejelentkezés `mylogin`. A kiszolgáló neve DNS-névbe van leképezve, ezért globálisan egyedinek kell lennie az Azure-ban. A `<server_admin_password>` helyére írja be saját értékét.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401 --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> A kiszolgáló itt megadott rendszergazdai bejelentkezési nevét és jelszavát kell majd használnia a rövid útmutató későbbi szakaszaiban a kiszolgálóra és az adatbázisaira való bejelentkezéshez. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.

Alapértelmezés szerint a **postgres** adatbázis a kiszolgáló alatt jön létre. A [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) adatbázis egy alapértelmezett adatbázis, amelyet a felhasználók, segédprogramok és külső féltől származó alkalmazások általi használatra szántak. 


## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Hozzon létre egy Azure PostgreSQL kiszolgálószintű tűzfalszabályt az [az sql server firewall create](/cli/azure/postgres/server/firewall-rule#create) paranccsal. Egy kiszolgálószintű tűzfalszabály lehetővé teszi olyan külső alkalmazások számára, mint a [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html), vagy a [PgAdmin](https://www.pgadmin.org/), hogy kapcsolódjon a kiszolgálóhoz az PostgreSQL szolgáltatás tűzfalán keresztül. 

Beállíthat egy olyan tűzfalszabályt, amely lefed egy IP-címtartományt, annak érdekében, hogy csatlakozni tudjon a saját hálózatából. Az alábbi példában [az postgres-tűzfalszabály létrehozása](/cli/azure/postgres/server/firewall-rule#create) egy tűzfalszabály létrehozására `AllowAllIps` , amely lehetővé teszi, hogy bármilyen IP-címről kapcsolat. Az összes IP-cím megnyitásához használja a 0.0.0.0 címet kezdő IP-címként és a 255.255.255.255 címet zárócímként.

Az Azure PostgreSQL-kiszolgálót, hogy csak a hálózati hozzáférés korlátozása, beállíthatja a tűzfalszabály csak fedik le a vállalati hálózat IP-címtartományt.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure PostgreSQL-kiszolgáló az 5432-es porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Kérje meg az informatikai részleget, hogy nyissa meg az 5432-es portot az Azure SQL Database-kiszolgálóhoz való csatlakozáshoz.
>

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

Az eredmény JSON formátumban van. Jegyezze fel a következőket: **administratorLogin** és **fullyQualifiedDomainName**.
```json
{
  "administratorLogin": "mylogin",
  "fullyQualifiedDomainName": "mypgserver-20170401.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mypgserver-20170401",
  "location": "westus",
  "name": "mypgserver-20170401",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "PGSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 51200,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>Csatlakozzon az Azure Database psql használatával PostgreSQL-adatbázishoz
Ha az ügyfélszámítógép PostgreSQL telepítve rendelkezik, egy helyi példányát használhatja [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html), vagy az Azure Cloud Console egy Azure PostgreSQL-kiszolgálóhoz való csatlakozáshoz. Használjuk a psql parancssori segédprogramot az Azure-adatbázis PostgreSQL-kiszolgálóhoz való kapcsolódáshoz.

1. A következő parancsot psql PostgreSQL-adatbázishoz egy Azure-adatbázishoz való kapcsolódáshoz:
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Például a következő parancs a **postgres** nevű alapértelmezett adatbázishoz kapcsolódik a PostgreSQL-kiszolgálón **mypgserver-20170401.postgres.database.azure.com** a hozzáférési hitelesítő adatok használatával. Adja meg a `<server_admin_password>` kiszolgálói rendszergazdai jelszót, amelyet a jelszó megadásakor választott.
  
  ```azurecli-interactive
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 ---dbname=postgres
```

2.  Miután csatlakozott a kiszolgálóhoz, hozzon létre egy üres adatbázist a parancssorba:
```sql
CREATE DATABASE mypgsqldb;
```

3.  Amikor a rendszer kéri, hajtsa végre a következő parancsot, hogy az újonnan létrehozott **mypgsqldb** adatbázishoz kapcsolódhasson:
```sql
\c mypgsqldb
```

## <a name="create-tables-in-the-database"></a>Hozzon létre táblák az adatbázisban
Most, hogy tudja, hogyan PostgreSQL az Azure-adatbázishoz való kapcsolódáshoz, azt is ismerteti, hogyan lehet néhány alapvető műveleteket elvégezni.

Először hogy hozzon létre egy táblát, és töltse be adatokkal. Hozzon létre egy táblát, amely nyomon követi a Hardverleltár-információk:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Írja be a most látható a táblák listáját az újonnan létrehozott táblázatra:
```sql
\dt
```

## <a name="load-data-into-the-table"></a>Adatok betöltése az a táblázat
Most, hogy a tábla, azt beilleszthet néhány adat azt. A parancssor megnyitása ablakban futtassa a következő lekérdezés szúrható be néhány sornyi adat:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Ezzel hozzáadta két sorát mintaadatok a táblázatba a korábban létrehozott.

## <a name="query-and-update-the-data-in-the-tables"></a>Lekérdezés, és frissítse a táblák adatait
A következő lekérdezés futtatásával adatok lekérését a táblázatra hajtható végre: 
```sql
SELECT * FROM inventory;
```

A készlet tábla is frissítheti:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Az adatok látható a frissített értékekkel:
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Adatbázis visszaállítása egy korábbi időpontra
Tegyük fel, hogy véletlenül törölt egy tábla. Ez a valami nem egyszerűen állíthat helyre. Azure-adatbázis PostgreSQL lehetővé teszi bármely-időpontban (Basic legfeljebb 7 nap) és a Standard 35 napon lépjen vissza, és állítsa vissza a-időpontban egy új kiszolgálóra. Az új kiszolgáló segítségével helyreállíthatja a törölt adatokat. 

A következő parancsot a minta kiszolgáló visszaállítása a pont előtti a tábla hozzá lett adva:
```azurecli-interactive
az postgres server restore --resource-group myResourceGroup --name mypgserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mypgserver-20170401
```

A `az postgres server restore` parancsot kell a következő paraméterekkel:
| Beállítás | Ajánlott érték | Leírás  |
| --- | --- | --- |
| --Erőforráscsoport |  myResourceGroup |  Az erőforráscsoport, amelyben a forráskiszolgálón található.  |
| --neve | mypgserver visszaállítása | A restore parancs által létrehozott új kiszolgáló neve. |
| visszaállítás--időpontban | 2017-04-13T13:59:00Z | Válassza ki a-időpontban való visszaállításához. A dátum és idő a forráskiszolgáló biztonsági mentés megőrzési időn belül kell lennie. Használjon ISO8601 dátum és idő formátumban. Például használhatja a saját helyi időzóna, például a `2017-04-13T05:59:00-08:00`, vagy használjon UTC Zulu formátum `2017-04-13T13:59:00Z`. |
| --forrás-kiszolgáló | mypgserver-20170401 | Név vagy azonosító a forráskiszolgáló visszaállítása. |

Egy kiszolgáló visszaállítása a-időpontban hoz létre egy új kiszolgálót, az eredeti kiszolgálóként frissítésétől a pont átmásolja a megadott idő. A hely és a visszaállított kiszolgáló értékeinek árképzési szint ugyanazok, mint a forráskiszolgálón.

A parancs szinkron, és a kiszolgáló helyreállítása után fog visszaadni. Miután a visszaállítás befejezését, keresse meg a létrehozott új kiszolgálót. Ellenőrizze, hogy az adatok helyreállt a várt módon.


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtanulta, hogyan használható az Azure parancssori felület (parancssori felület) és egyéb segédprogramok:
> [!div class="checklist"]
> * Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz
> * A kiszolgáló tűzfal konfigurálása
> * Használjon [ **psql** ](https://www.postgresql.org/docs/9.6/static/app-psql.html) segédprogramot az adatbázis létrehozása
> * Mintaadatok betöltése
> * Adatok lekérdezése
> * Adatok frissítése
> * Adatok visszaállítása

A következő megtudhatja, hogyan használhatja az Azure-portálon hasonló feladatokat hajthatnak végre, tekintse át az oktatóanyag: [az első Azure-adatbázis kialakítása a PostgreSQL az Azure portál használatával](tutorial-design-database-using-azure-portal.md)

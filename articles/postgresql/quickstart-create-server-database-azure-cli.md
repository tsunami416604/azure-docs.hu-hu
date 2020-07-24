---
title: 'Rövid útmutató: kiszolgáló létrehozása – Azure CLI-Azure Database for PostgreSQL – egyetlen kiszolgáló'
description: Rövid útmutató Azure Database for PostgreSQL – egyetlen kiszolgáló létrehozásához az Azure CLI használatával (parancssori felület).
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc
ms.openlocfilehash: d103ed0ebd565df77032237638c775991324ea44
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87030161"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Gyors útmutató: Azure Database for PostgreSQL egyetlen kiszolgáló létrehozása az Azure CLI használatával

> [!TIP]
> Érdemes lehet az egyszerűbb az [postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI-parancs használata (jelenleg előzetes verzióban érhető el). Próbálja [ki a rövid](./quickstart-create-server-up-azure-cli.md)útmutatót.

Ez a rövid útmutató bemutatja, hogyan használhatja az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) -parancsokat a [Azure Cloud Shellban](https://shell.azure.com) egy Azure Database for PostgreSQL-kiszolgáló öt perc alatt történő létrehozásához.  Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

>[!Note]
>Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 

## <a name="prerequisites"></a>Előfeltételek
Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verzióját kell futtatnia helyileg. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az az [login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) parancs használatával kell bejelentkeznie a fiókjába. Jegyezze fel az **ID** tulajdonságot, amely az Azure-fiók **előfizetés-azonosítójára** hivatkozik. 

```azurecli-interactive
az login
```

Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account) paranccsal. Jegyezze fel az **azonosító** értéket az az **login** kimenetből, amelyet a parancs **előfizetés** argumentumának értékeként kíván használni. Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetés beszerzéséhez használja [az az Account List](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list)lehetőséget.

```azurecli
az account set --subscription <subscription id>
```
## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) az az [Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) paranccsal, majd hozza létre a PostgreSQL-kiszolgálót az erőforráscsoport belsejében. Egyedi nevet adjon meg. A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myresourcegroup` helyen.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Hozzon létre egy [Azure-adatbázist PostgreSQL- kiszolgálóhoz](overview.md) az [az postgres server create](/cli/azure/postgres/server) paranccsal. Egy kiszolgáló több adatbázist tartalmazhat.

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
A fenti argumentumok részletei: 

**Beállítás** | **Mintaérték** | **Leírás**
---|---|---
name | mydemoserver | Válasszon egy egyedi nevet, amely azonosítja a PostgreSQL-kiszolgálóhoz készült Azure-adatbázist. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. 3–63 karakter hosszúságú lehet.
resource-group | myResourceGroup | Adja meg az Azure-erőforráscsoport nevét.
location | westus | A kiszolgáló Azure-helye.
admin-user | myadmin | A rendszergazda bejelentkezéshez használt felhasználóneve. Nem lehet **azure_superuser**, **admin**, **administrator**, **root**, **guest** vagy **public**.
admin-password | *secure password* | A rendszergazda felhasználó jelszava. A jelszó 8–128 karakterből állhat. A jelszónak legalább háromféle karaktert tartalmaznia kell a következő kategóriák közül: angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek és nem alfanumerikus karakterek.
sku-name|GP_Gen5_2|Adja meg az árképzési csomag és a számítási konfiguráció nevét. A {tarifacsomag}_{számítási generáció}_{virtuális magok} mintát követi rövidített módon. További információ: [Azure Database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/).

>[!IMPORTANT] 
>- Az alapértelmezett PostgreSQL-verzió a kiszolgálón 9,6. [Itt](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)láthatja az összes támogatott verziót.
>- Az **az postgres Server Create** parancs összes argumentumának megtekintéséhez tekintse meg ezt a [hivatkozási dokumentumot](https://docs.microsoft.com/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create)
>- Az SSL alapértelmezés szerint engedélyezve van a kiszolgálón. Az SSL további infroamtion lásd: [SSL-kapcsolat konfigurálása](./concepts-ssl-connection-security.md)

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása 
Alapértelmezés szerint a létrehozott kiszolgáló nem érhető el nyilvánosan, és a tűzfalszabályok nem védik a szabályokat. A tűzfalszabály a kiszolgálón az az [postgres Server Firewall-Rule Create](/cli/azure/postgres/server/firewall-rule) paranccsal konfigurálható, hogy a helyi környezet hozzáférjen a kiszolgálóhoz. 

A következő példában egy olyan `AllowMyIP` nevű tűzfalszabályt hozunk létre, amely a 192.168.0.1 IP-címről engedélyezi a kapcsolódást. Cserélje le azt az IP-címet vagy IP-címtartományt, amely megfelel a helyének, ahová csatlakozni fog.  Ha nem tudja, hogyan keresse meg az IP-címet, lépjen a következőre: az [https://whatismyipaddress.com/](https://whatismyipaddress.com/) IP-cím beszerzése.


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
>  Győződjön meg arról, hogy a hálózati tűzfal engedélyezi a 5432-es portot, amelyet Azure Database for PostgreSQL-kiszolgálók használnak a kapcsolódási problémák elkerülése érdekében. 

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
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-azure-database-for-postgresql-server-using-psql"></a>Kapcsolódás Azure Database for PostgreSQL kiszolgálóhoz a psql használatával
a [**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) a PostgreSQL-kiszolgálókhoz való kapcsolódáshoz használt népszerű ügyfél. A **psql** [Azure Cloud Shell](../cloud-shell/overview.md)használatával csatlakozhat a kiszolgálóhoz. Azt is megteheti, hogy a psql a helyi környezetben is használhatja, ha elérhetővé válik. Egy üres adatbázis ("postgres") már létre van hozva az új PostgreSQL-kiszolgálóval, amelyet a psql való kapcsolódáshoz használhat az alább látható módon. 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Ha inkább URL-elérési utat szeretne használni a postgres-hez való kapcsolódáshoz, az URL-cím kódolja a @ Sign nevet a következővel: `%40` . Például a psql tartozó kapcsolatok karakterlánca a következő:.
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```


## <a name="clean-up-resources"></a>Erőforrások felszabadítása
Ha nincs szüksége ezekre az erőforrásokra egy másik gyors útmutatóhoz vagy oktatóanyaghoz, akkor a következő parancs futtatásával törölheti őket: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja az [az postgres server delete](/cli/azure/postgres/server) parancsot.
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)
> 
> [Django-webalkalmazás üzembe helyezése a PostgreSQL-sel](../app-service/containers/tutorial-python-postgresql-app.md)
>
> [Node.JS alkalmazással való kapcsolat](./connect-nodejs.md)


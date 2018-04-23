---
title: Gyors útmutató – Azure Database for PostgreSQL létrehozása az Azure CLI használatával
description: Rövid útmutató az Azure-adatbázis PostgreSQL-kiszolgálóhoz az Azure CLI (parancssori felület) használatával történő létrehozásához és kezeléséhez.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 04/01/2018
ms.custom: mvc
ms.openlocfilehash: daa7ea345abb6228bee2d1ca5bfcc3850aaff9c3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="quickstart-create-an-azure-database-for-postgresql-using-the-azure-cli"></a>Gyors útmutató: Azure Database for PostgreSQL létrehozása az Azure CLI használatával
A PostgreSQL-hez készült Azure Database felügyelt szolgáltatás, amely lehetővé teszi a magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, kezelését és skálázását a felhőben. Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure-adatbázist PostgreSQL-kiszolgálóhoz az Azure CLI használatával az [Azure-erőforráscsoportban](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

A CLI helyi futtatása esetén az [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) paranccsal be kell jelentkeznie a fiókjába. Jegyezze fel a megfelelő előfizetésnév parancskimenetéből az **id** tulajdonságot.
```azurecli-interactive
az login
```

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#az_account_set) paranccsal. Az előfizetés **az login** kimenetének **id** tulajdonságát illessze be az előfizetés-azonosító helyőrzője helyére.
```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/resource-group-overview.md) az [az group create](/cli/azure/group#az_group_create) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat. Egyedi nevet adjon meg. A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myresourcegroup` helyen.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

Hozzon létre egy [Azure-adatbázist PostgreSQL- kiszolgálóhoz](overview.md) az [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) paranccsal. A kiszolgáló adatbázisok egy csoportját tartalmazza, amelyeket a rendszer egy csoportként kezel. 

A következő példában az USA nyugati régiójában létrehozunk egy `mydemoserver` nevű kiszolgálót a `myresourcegroup` erőforráscsoportban `myadmin` kiszolgálói rendszergazdai bejelentkezéssel. Ez egy **általános célú** **4. generációs** kiszolgáló 2 **virtuális maggal**. A kiszolgáló neve DNS-névbe van leképezve, ezért globálisan egyedinek kell lennie az Azure-ban. A `<server_admin_password>` helyére írja be saját értékét.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 9.6
```

> [!IMPORTANT]
> A kiszolgáló itt megadott rendszergazdai bejelentkezési nevét és jelszavát kell majd használnia a rövid útmutató későbbi szakaszaiban a kiszolgálóra és az adatbázisaira való bejelentkezéshez. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.

Alapértelmezés szerint a **postgres** adatbázis a kiszolgáló alatt jön létre. A [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) adatbázis egy alapértelmezett adatbázis, amelyet a felhasználók, segédprogramok és külső féltől származó alkalmazások általi használatra szántak. 


## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Hozzon létre egy Azure PostgreSQL kiszolgálószintű tűzfalszabályt az [az sql server firewall create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) paranccsal. Egy kiszolgálószintű tűzfalszabály lehetővé teszi olyan külső alkalmazások számára, mint a [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html), vagy a [PgAdmin](https://www.pgadmin.org/), hogy kapcsolódjon a kiszolgálóhoz az PostgreSQL szolgáltatás tűzfalán keresztül. 

Beállíthat egy olyan tűzfalszabályt, amely lefed egy IP-címtartományt, annak érdekében, hogy csatlakozni tudjon a saját hálózatából. A követező példában az [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) parancsot használjuk az IP-címtartományhoz tartozó `AllowAllIps` tűzfalszabály létrehozásához. Az összes IP-cím megnyitásához használja a 0.0.0.0 címet kezdő IP-címként és a 255.255.255.255 címet zárócímként.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure PostgreSQL-kiszolgáló az 5432-es porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Kérje meg az informatikai részleget, hogy nyissa meg az 5432-es portot az Azure PostgreSQL-kiszolgálóhoz való csatlakozáshoz.

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

## <a name="connect-to-postgresql-database-using-psql"></a>Csatlakozás a PostgreSQL-adatbázishoz a psql használatával

Ha az ügyfélszámítógépen telepítve van a PostgreSQL, akkor használhatja a [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) helyi példányát az Azure PostgreSQL-kiszolgálóhoz való csatalakozáshoz. Használjuk a psql parancssori segédprogramot az Azure PostgreSQL-kiszolgálóhoz való kapcsolódáshoz.

1. Futtassa a következő psql parancsot az Azure-adatbázis PostgreSQL-kiszolgálóhoz való kapcsolódáshoz
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Például a következő parancs a **postgres** nevű alapértelmezett adatbázishoz kapcsolódik a **mydemoserver.postgres.database.azure.com** PostgreSQL-kiszolgálón a hozzáférési hitelesítő adatok használatával. Adja meg a `<server_admin_password>` kiszolgálói rendszergazdai jelszót, amelyet a jelszó megadásakor választott.
  
  ```azurecli-interactive
psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
```

2.  Miután csatlakozott a kiszolgálóhoz, hozzon létre egy üres adatbázist, amikor a rendszer kéri.
```sql
CREATE DATABASE mypgsqldb;
```

3.  Amikor a rendszer kéri, hajtsa végre a következő parancsot, hogy az újonnan létrehozott **mypgsqldb** adatbázishoz kapcsolódhasson:
```sql
\c mypgsqldb
```

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Csatlakozás a PostgreSQL-kiszolgálóhoz a pgAdmin segítségével

A pgAdmin egy nyílt forráskódú eszköz, amely a PostgreSQL-lel együtt használható. A pgAdmin alkalmazást a [pgAdmin webhelyről](http://www.pgadmin.org/) telepítheti. Elképzelhető, hogy az Ön által használt pgAdmin-verzió eltér a jelen gyors útmutatóban használttól. Ha további információra van szüksége, tekintse meg a pgAdmin dokumentációját.

1. Nyissa meg a pgAdmin alkalmazást az ügyfélszámítógépen.

2. Az eszköztáron kattintson az **Objektum** elemre, vigye az egérmutatót a **Létrehozás** fölé, majd válassza a **Kiszolgáló** lehetőséget.

3. A **Létrehozás – Kiszolgáló** párbeszédpanel **Általános** lapján adjon meg egy egyedi rövid nevet a kiszolgáló számára, például **mydemoserver**.

   ![Az „Általános” lap](./media/quickstart-create-server-database-azure-cli/9-pgadmin-create-server.png)

4. A **Létrehozás – Kiszolgáló** párbeszédpanel **Kapcsolat** lapján töltse ki a beállítások tábláját.

   ![A „Kapcsolat” lap](./media/quickstart-create-server-database-azure-cli/10-pgadmin-create-server.png)

    pgAdmin-paraméter |Érték|Leírás
    ---|---|---
    Gazdagépnév/-cím | Kiszolgálónév | Az a kiszolgálónév, amelyet korábban az Azure Database for PostgreSQL-kiszolgáló létrehozásakor használt. A példakiszolgáló a **mydemoserver.postgres.database.azure.com.** Használja a teljes tartománynevet (**\*.postgres.database.azure.com**), ahogyan az a példában látható. Ha nem emlékszik a kiszolgáló nevére, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit. 
    Port | 5432 | Az Azure Database for PostgreSQL-kiszolgálóhoz való csatlakozáskor használt port. 
    Karbantartási adatbázis | *postgres* | A rendszer által létrehozott alapértelmezett adatbázisnév.
    Felhasználónév | Kiszolgáló-rendszergazdai bejelentkezési név | A kiszolgáló-rendszergazdai bejelentkezési felhasználónév, amelyet korábban az Azure Database for PostgreSQL-kiszolgáló létrehozásakor adott meg. Ha nem emlékszik a felhasználónévre, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit. A formátum *username@servername*.
    Jelszó | Az Ön rendszergazdai jelszava | A rövid útmutatóban a korábbiakban a kiszolgáló létrehozásakor választott jelszó.
    Szerepkör | Hagyja üresen | Itt nem kell megadni szerepkörnevet. Hagyja üresen ezt a mezőt.
    SSL-mód | *Kötelező* | Az SSL-módot a pgAdmin SSL lapján állíthatja be. Az SSL-kényszerítés alapértelmezés szerint minden újonnan létrehozott Azure Database for PostgreSQL-kiszolgálón be van kapcsolva. Az SSL-kényszerítés kikapcsolása: [SSL-kényszerítés](./concepts-ssl-connection-security.md).
    
5. Kattintson a **Mentés** gombra.

6. A bal oldali **Böngésző** panelen bontsa ki a **Kiszolgálók** csomópontot. Válassza ki például a **mydemoserver** kiszolgálót. Kattintson rá a csatlakozáshoz.

7. Bontsa ki a kiszolgáló-csomópontot, majd bontsa ki az abban található **Adatbázisok** csomópontot is. A listában szerepelnie kell a meglévő *postgres* adatbázisnak, valamint minden más létrehozott adatbázisnak. Az Azure Database for PostgreSQL segítségével kiszolgálónként több adatbázist is létrehozhat.

8. Kattintson a jobb gombbal az **Adatbázisok** elemre, majd válassza a **Létrehozás** menü **Adatbázis** elemét.

9. Írjon be egy tetszőleges adatbázisnevet az **Adatbázis** mezőbe (például **mypgsqldb2**).

10. A listából válassza ki az adatbázis **tulajdonosát**. Válassza ki a kiszolgáló-rendszergazdai bejelentkezési nevet (a példánkban ez a **my admin**).

   ![Adatbázis létrehozása a pgAdmin alkalmazásban](./media/quickstart-create-server-database-azure-cli/11-pgadmin-database.png)

11. Egy új, üres adatbázis létrehozásához válassza a **Mentés** lehetőséget.

12. A **Böngésző** panelen megtekintheti az Ön által létrehozott adatbázist az adatbázisok listájában a kiszolgáló nevét viselő területen.




## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Távolítsa el a rövid útmutató során létrehozott összes erőforrást az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) törlésével.

> [!TIP]
> Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure CLI-ben a rövid útmutatóhoz létrehozott összes erőforrást.

```azurecli-interactive
az group delete --name myresourcegroup
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja az [az postgres server delete](/cli/azure/postgres/server#az_postgres_server_delete) parancsot.
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)


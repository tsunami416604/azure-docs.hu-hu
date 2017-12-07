---
title: "Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz az Azure CLI használatával | Microsoft Docs"
description: "Rövid útmutató az Azure-adatbázis PostgreSQL-kiszolgálóhoz az Azure CLI (parancssori felület) használatával történő létrehozásához és kezeléséhez."
services: postgresql
author: sanagama
ms.author: sanagama
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 11/27/2017
ms.openlocfilehash: d462bb19b7a662f7d65529b5a2ec9f65be19d131
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="create-an-azure-database-for-postgresql-using-the-azure-cli"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz az Azure CLI használatával
A PostgreSQL-hez készült Azure Database felügyelt szolgáltatás, amely lehetővé teszi a magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, kezelését és skálázását a felhőben. Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure-adatbázist PostgreSQL-kiszolgálóhoz az Azure CLI használatával az [Azure-erőforráscsoportban](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

A CLI helyi futtatása esetén az [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) paranccsal be kell jelentkeznie a fiókjába.
```azurecli-interactive
az login
```

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válasszon ki egy megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#az_account_set) parancs segítségével.
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

A következő példában létrehozunk egy `mypgserver-20170401` nevű kiszolgálót a `myresourcegroup` erőforráscsoportban `mylogin` kiszolgálói rendszergazdai bejelentkezéssel. A kiszolgáló neve DNS-névbe van leképezve, ezért globálisan egyedinek kell lennie az Azure-ban. A `<server_admin_password>` helyére írja be saját értékét.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mypgserver-20170401  --location westus --admin-user mylogin --admin-password <server_admin_password> --performance-tier Basic --compute-units 50 --version 9.6
```

> [!IMPORTANT]
> A kiszolgáló itt megadott rendszergazdai bejelentkezési nevét és jelszavát kell majd használnia a rövid útmutató későbbi szakaszaiban a kiszolgálóra és az adatbázisaira való bejelentkezéshez. Jegyezze meg vagy jegyezze fel ezt az információt későbbi használatra.

Alapértelmezés szerint a **postgres** adatbázis a kiszolgáló alatt jön létre. A [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) adatbázis egy alapértelmezett adatbázis, amelyet a felhasználók, segédprogramok és külső féltől származó alkalmazások általi használatra szántak. 


## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Hozzon létre egy Azure PostgreSQL kiszolgálószintű tűzfalszabályt az [az sql server firewall create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) paranccsal. Egy kiszolgálószintű tűzfalszabály lehetővé teszi olyan külső alkalmazások számára, mint a [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html), vagy a [PgAdmin](https://www.pgadmin.org/), hogy kapcsolódjon a kiszolgálóhoz az PostgreSQL szolgáltatás tűzfalán keresztül. 

Beállíthat egy olyan tűzfalszabályt, amely lefed egy IP-címtartományt, annak érdekében, hogy csatlakozni tudjon a saját hálózatából. A követező példában az [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) parancsot használjuk az IP-címtartományhoz tartozó `AllowAllIps` tűzfalszabály létrehozásához. Az összes IP-cím megnyitásához használja a 0.0.0.0 címet kezdő IP-címként és a 255.255.255.255 címet zárócímként.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mypgserver-20170401 --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure PostgreSQL-kiszolgáló az 5432-es porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 5432-es porton keresztül. Kérje meg az informatikai részleget, hogy nyissa meg az 5432-es portot az Azure SQL Database-kiszolgálóhoz való csatlakozáshoz.

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

## <a name="connect-to-postgresql-database-using-psql"></a>Csatlakozás a PostgreSQL-adatbázishoz a psql használatával

Ha az ügyfélszámítógépen telepítve van a PostgreSQL, akkor használhatja a [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) helyi példányát az Azure PostgreSQL-kiszolgálóhoz való csatalakozáshoz. Használjuk a psql parancssori segédprogramot az Azure PostgreSQL-kiszolgálóhoz való kapcsolódáshoz.

1. Futtassa a következő psql parancsot az Azure-adatbázis PostgreSQL-kiszolgálóhoz való kapcsolódáshoz
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  Például a következő parancs a **postgres** nevű alapértelmezett adatbázishoz kapcsolódik a PostgreSQL-kiszolgálón **mypgserver-20170401.postgres.database.azure.com** a hozzáférési hitelesítő adatok használatával. Adja meg a `<server_admin_password>` kiszolgálói rendszergazdai jelszót, amelyet a jelszó megadásakor választott.
  
  ```azurecli-interactive
psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
```

2.  Miután csatlakozott a kiszolgálóhoz, hozzon létre egy üres adatbázist, amikor a rendszer kéri.
```sql
CREATE DATABASE mypgsqldb;
```

3.  Amikor a rendszer kéri, hajtsa végre a következő parancsot, hogy az újonnan létrehozott **mypgsqldb** adatbázishoz kapcsolódhasson:
```sql
\c mypgsqldb
```

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Csatlakozás a PostgreSQL-adatbázishoz a pgAdmin használatával

Kapcsolódás az Azure PostgreSQL-kiszolgálóhoz a _pgAdmin_ GUI-eszköz használatával
1.  Indítsa el a MySQL _pgAdmin_ alkalmazást az ügyfélszámítógépen. A _pgAdmin-t_ http://www.pgadmin.org/ oldalról telepítheti.
2.  Válassza az **Új kiszolgáló hozzáadása** lehetőséget a **Gyorshivatkozások** menüből.
3.  A **Kiszolgáló létrehozása** párbeszédpanel **Általános** lapján adjon egy egyedi rövid nevet a kiszolgálónak. Például **Azure PostgreSQL Server**.
 ![pgAdmin tool - create - server](./media/quickstart-create-server-database-azure-cli/1-pgadmin-create-server.png)
4.  A **Kiszolgáló létrehozása** párbeszédpanel **Kapcsolat** lapján:
    - Az **Állomás neve/címe** mezőben adja meg a kiszolgáló teljes nevét (például **mypgserver-20170401.postgres.database.azure.com**. 
    - A **Port** mezőben adja meg az 5432-es portot. 
    - Adja meg a rövid útmutató során korábban beszerzett **kiszolgálói-rendszergazda bejelentkezési nevet (user@mypgserver)**, és a kiszolgáló létrehozásakor megadott jelszót a **Felhasználónév** és a **Jelszó** mezőkben.
    - Az **SSL-mód** elemnél válassza a **Kötelező** lehetőséget. Alapértelmezés szerint a rendszer minden Azure PostgreSQL-kiszolgálót az SSL-kényszerítéssel bekapcsolva hoz létre. Részletek az SSL-kényszerítés kikapcsolásáról: [SSL kényszerítése](./concepts-ssl-connection-security.md).

    ![pgAdmin - Create - Server](./media/quickstart-create-server-database-azure-cli/2-pgadmin-create-server.png)
5.  Kattintson a **Save** (Mentés) gombra.
6.  A baloldali Böngésző panelen bontsa ki a **Kiszolgáló csoportok** elemet. Válassza ki az **Azure PostgreSQL Server** kiszolgálót.
7.  Válassza ki a **Kiszolgálót**, amelyhez csatlakozott, majd azon belül válassza az **Adatbázisok** elemet. 
8.  Az adatbázis létrehozásához kattintson jobb gombbal az **Adatbázisok** elemre.
9.  Válasszon egy adatbázisnevet (**mypgsqldb**) és a tulajdonosát kiszolgálói rendszergazdai bejelentkezéssel (**mylogin**).
10. Az üres adatbázis létrehozásához kattintson a **Mentés** elemre.
11. A **Böngészőben** bontsa ki a **Kiszolgálók** csoportot. Bontsa ki a létrehozott kiszolgálót, ekkor megjelenik a **mypgsqldb** adatbázis.
 ![pgAdmin - Create - Database](./media/quickstart-create-server-database-azure-cli/3-pgadmin-database.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Távolítsa el a rövid útmutató során létrehozott összes erőforrást az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) törlésével.

> [!TIP]
> Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure CLI-ben a rövid útmutatóhoz létrehozott összes erőforrást.

```azurecli-interactive
az group delete --name myresourcegroup
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja az [az postgres server delete](/cli/azure/postgres/server#az_postgres_server_delete) parancsot.
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)

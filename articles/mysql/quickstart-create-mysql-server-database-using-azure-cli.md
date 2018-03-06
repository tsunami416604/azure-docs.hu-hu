---
title: "Gyors útmutató: Azure Database for MySQL-kiszolgáló létrehozása – Azure CLI"
description: "Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure-adatbázist MySQL-kiszolgálóhoz az Azure CLI használatával az Azure-erőforráscsoportban."
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 02/28/2018
ms.custom: mvc
ms.openlocfilehash: a2efce07dac65eb8af59e6bc1bd5a51bfc62d69e
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával
Ez a rövid útmutató bemutatja, hogyan hozhat létre öt perc alatt egy Azure-adatbázist MySQL-kiszolgálóhoz az Azure CLI használatával az Azure-erőforráscsoportban. Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

Ha több előfizetéssel rendelkezik, válassza a megfelelő előfizetést, amelyen az erőforrás megtalálható vagy terhelve van. Válasszon ki egy megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#az_account_set) parancs segítségével.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy [Azure-erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) az [az group create](/cli/azure/group#az_group_create) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myresourcegroup` helyen.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="add-the-extension"></a>A bővítmény hozzáadása
A következő paranccsal adhatja hozzá a frissített Azure Database for MySQL felügyeleti bővítményt:
```azurecli-interactive
az extension add --name rdbms
``` 

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz
Hozzon létre egy Azure Database for MySQL-kiszolgálót az **[az mysql server create](/cli/azure/mysql/server#az_mysql_server_create)** paranccsal. Egy kiszolgáló több adatbázist is tud kezelni. Általában külön adatbázissal rendelkezik minden projekt vagy felhasználó.

A következő példában az USA nyugati régiójában létrehozunk egy `mydemoserver` nevű kiszolgálót a `myresourcegroup` erőforráscsoportban `myadmin` kiszolgálói rendszergazdai bejelentkezéssel. Ez egy **általános célú** **4. generációs** kiszolgáló 2 **virtuális maggal**. A kiszolgáló neve DNS-névbe van leképezve, ezért globálisan egyedinek kell lennie az Azure-ban. A `<server_admin_password>` helyére írja be saját értékét.
```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```

## <a name="configure-firewall-rule"></a>Tűzfalszabály konfigurálása
Hozzon létre egy Azure Database for MySQL-kiszolgáló szintű tűzfalszabályt az **[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create)** paranccsal. Egy kiszolgálószintű tűzfalszabály lehetővé teszi olyan külső alkalmazások használatát, mint a **mysql.exe** parancssori eszköz vagy a MySQL Workbench, amelyekkel kapcsolódhat a kiszolgálóhoz az Azure MySQL szolgáltatás tűzfalán keresztül. 

A következő példában létrehozunk egy tűzfalszabályt egy előre meghatározott címtartományhoz, amely ebben a példában az IP-címek teljes lehetséges tartományát lefedi.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
Az összes IP-cím engedélyezése nem biztonságos. Ez a példa az egyszerűséget tartja szem előtt, de valós környezetben ismernie kell a pontos IP-címtartományokat az alkalmazások és felhasználók számára történő hozzáadáshoz. 

> [!NOTE]
> A MySQL-hez készült Azure-adatbázis kapcsolatai a 3306-os porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy nem engedélyezett a kimenő forgalom a 3306-as porton keresztül. Ebben az esetben addig nem tud csatlakozni a kiszolgálóhoz, amíg az informatikai részleg meg nem nyitja a 3306-os portot.
> 


## <a name="configure-ssl-settings"></a>Az SSL-beállítások konfigurálása
Alapértelmezés szerint a kiszolgáló és az ügyfélalkalmazások közti SSL-kapcsolatok kényszerítve vannak. Ez az alapértelmezett beállítás biztosítja a „mozgó” adatok biztonságát az adatfolyam interneten keresztüli titkosításával. A rövid útmutató egyszerűsége érdekében tiltsa le az SSL-kapcsolatokat a kiszolgálóján. Ennek az SSL-nek a letiltása éles kiszolgálók esetében nem javasolt. További információkért lásd [Az SSL-kapcsolatok a MySQL-hez készült Azure Database-hez való kapcsolódásra az alkalmazásban való konfigurálását](./howto-configure-ssl.md) bemutató cikket.

A következő példában letiltjuk az SSL kényszerítését a MySQL-kiszolgálón.
 
 ```azurecli-interactive
 az mysql server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Kapcsolatadatok lekérése

A kiszolgálóhoz való kapcsolódáshoz meg kell adnia a gazdagép adatait és a hozzáférési hitelesítő adatokat.

```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Az eredmény JSON formátumban van. Jegyezze fel a következőket: **fullyQualifiedDomainName** és **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>Csatlakozás a kiszolgálóhoz a mysql.exe parancssori eszköz használatával
Csatlakozzon kiszolgálójához a **mysql.exe** parancssori eszközzel. A MySQL-t [innen](https://dev.mysql.com/downloads/) töltheti le és telepítheti számítógépén. Vagy kattinthat a kódmintákban található **Kipróbálom** gombra vagy az Azure Portal jobb felső eszköztárán található `>_` gombra is az **Azure Cloud Shell** megnyitásához.

Írja be a következő parancsokat: 

1. Csatlakozás a kiszolgálóhoz a **mysql** parancssori eszköz használatával:
```azurecli-interactive
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

2. Kiszolgáló állapotának megtekintése:
```sql
 mysql> status
```
Ha minden megfelelően működik, a parancssori eszköz a következő szöveget jeleníti meg:

```dos
C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> További parancsokért lásd: [az MySQL 5.7 referenciaútmutatójának 4.5.1-es fejezetét](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Csatlakozás a kiszolgálóhoz a MySQL Workbench GUI eszköz használatával
1.  Indítsa el a MySQL Workbench alkalmazást az ügyfélszámítógépen. A MySQL Workbench-et [innen](https://dev.mysql.com/downloads/workbench/) töltheti le és telepítheti.

2.  Az **Új kapcsolat létrehozása** párbeszédpanelen adja meg a következő információkat a **Paraméterek** lapon:

   ![új kapcsolat beállítása](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

| **Beállítás** | **Ajánlott érték** | **Leírás** |
|---|---|---|
|   Kapcsolat neve | My Connection | Adjon meg egy címkét a kapcsolathoz (tetszőlegesen kiválasztható) |
| Kapcsolati módszer | válassza a Standard (TCP/IP) lehetőséget | Csatlakozzon a MySQL-hez készült Azure Database-hez a TCP/IP protokollal> |
| Gazdanév | mydemoserver.mysql.database.azure.com | A korábban feljegyzett kiszolgálónév. |
| Port | 3306 | A rendszer a MySQL alapértelmezett portját használja. |
| Felhasználónév | myadmin@mydemoserver | A korábban feljegyzett kiszolgáló-rendszergazdai bejelentkezés. |
| Jelszó | **** | Használja a korábban beállított rendszergazdaifiók-jelszót. |

Kattintson a **Kapcsolat tesztelése** lehetőségre, hogy tesztelje, minden paraméter helyesen lett-e konfigurálva.
Ezután a kapcsolatra való kattintva sikeresen csatlakozhat a kiszolgálóhoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha ezekre az erőforrásokra már nincs szüksége más gyorsútmutatókhoz/oktatóanyagokhoz, a következő paranccsal törölheti őket: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja az [az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete) parancsot.
```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [MySQL-adatbázis tervezése az Azure CLI-vel](./tutorial-design-database-using-cli.md)

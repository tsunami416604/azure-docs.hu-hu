---
title: 'Rövid útmutató: Azure Database for MariaDB-kiszolgáló létrehozása – Azure CLI'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure Database MariaDB-kiszolgálót az Azure CLI használatával egy Azure-erőforráscsoportban.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 9e87dacb80aa7fc5f5b073e631fc06ae74b3ad00
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996604"
---
# <a name="create-an-azure-database-for-mariadb-server-using-azure-cli"></a>Azure Database for MariaDB-kiszolgáló létrehozása az Azure CLI használatával
Ez a rövid útmutató bemutatja, hogyan hozhat létre öt perc alatt egy Azure Database for MariaDB-kiszolgálót az Azure CLI használatával egy Azure-erőforráscsoportban. Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

Ha több előfizetéssel rendelkezik, válassza a megfelelő előfizetést, amelyen az erőforrás megtalálható vagy terhelve van. Válasszon ki egy megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#az-account-set) parancs segítségével.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy [Azure-erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) az [az group create](/cli/azure/group#az-group-create) paranccsal. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és csoportként kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myresourcegroup` helyen.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB-kiszolgáló létrehozása
Hozzon létre egy Azure Database for MariaDB-kiszolgálót az **[az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create)** paranccsal. Egy kiszolgáló több adatbázist is tud kezelni. Általában külön adatbázissal rendelkezik minden projekt vagy felhasználó.

**Beállítás** | **Mintaérték** | **Leírás**
---|---|---
név | mydemoserver | Válasszon egy egyedi nevet, amely azonosítja az Azure Database for MariaDB-kiszolgálót. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. 3–63 karakter hosszúságú lehet.
resource-group | myResourceGroup | Adja meg az Azure-erőforráscsoport nevét.
sku-name | GP_Gen5_2 | A termékváltozat neve. A {tarifacsomag}_{számítási generáció}_{virtuális magok} mintát követi rövidített módon. Az sku-name paraméterről az alábbi táblázatban talál további információt.
backup-retention | 7 | Az az időtartam, ameddig egy biztonsági mentést meg kell őrizni. A mértékegysége a nap. A tartomány 7-35. 
geo-redundant-backup | Letiltva | Azt adja meg, hogy a georedundáns biztonsági mentést engedélyezni kell-e ehhez a kiszolgálóhoz. Megengedett értékek: Engedélyezve, Letiltva.
location | westus | A kiszolgáló Azure-helye.
ssl-enforcement | Engedélyezve | Azt adja meg, hogy engedélyezni kell-e az ssl-t ehhez a kiszolgálóhoz. Megengedett értékek: Engedélyezve, Letiltva.
storage-size | 51 200 | A kiszolgáló tárkapacitása (megabájtban megadva). Az érvényes storage-size paraméter legkisebb értéke 5120 MB, és 1024 MB-os egységekben növekszik. A tárterület korlátairól a [tarifacsomagokról szóló](./concepts-pricing-tiers.md) dokumentumban találhat további információkat. 
version | 10.2 | A MariaDB fő motorjának verziója.
admin-user | myadmin | A rendszergazda bejelentkezéshez használt felhasználóneve. Nem lehet **azure_superuser**, **admin**, **administrator**, **root**, **guest** vagy **public**.
admin-password | Password123 | A rendszergazda felhasználó jelszava. A jelszó 8–128 karakterből állhat. A jelszónak legalább háromféle karaktert tartalmaznia kell a következő kategóriák közül: angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek és nem alfanumerikus karakterek.

Az sku-name paraméter értéke a {tarifacsomag}\_{számítási generáció}\_{virtuális magok} mintát követi, a következő példákban látható módon:
+ Az `--sku-name B_Gen5_4` jelentése: Alapszintű, 5. generációs és 4 virtuális mag.
+ `--sku-name GP_Gen5_32` jelentése: Általános célú, 5. generációs és 32 virtuális mag.
+ `--sku-name MO_Gen5_2` jelentése: Memóriaoptimalizált, 5. generációs és 2 virtuális mag.

A [Tarifacsomagok](./concepts-pricing-tiers.md) dokumentumban megtekintheti az érvényes értékeket régiónként és csomagonként.

A következő példában az USA nyugati régiójában létrehozunk egy `mydemoserver` nevű kiszolgálót a `myresourcegroup` erőforráscsoportban `myadmin` kiszolgálói rendszergazdai bejelentkezéssel. Ez egy **általános célú** **5. generációs** kiszolgáló 2 **virtuális maggal**. A kiszolgáló neve DNS-névbe van leképezve, ezért globálisan egyedinek kell lennie az Azure-ban. A `<server_admin_password>` helyére írja be saját értékét.
```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

## <a name="configure-firewall-rule"></a>Tűzfalszabály konfigurálása
Hozzon létre egy Azure Database for MariaDB-kiszolgálószintű tűzfalszabályt az **[az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create)** paranccsal. A kiszolgálószintű tűzfalszabályok olyan külső alkalmazások használatát teszik lehetővé, mint a **mysql** parancssori eszköz vagy a MySQL Workbench, amelyekkel kapcsolódhat a kiszolgálóhoz az Azure-beli MariaDB-szolgáltatás tűzfalán keresztül. 

A következő példában egy olyan `AllowMyIP` nevű tűzfalszabályt hozunk létre, amely a 192.168.0.1 IP-címről engedélyezi a kapcsolódást. Helyettesítse be a csatlakozási helyének megfelelő IP-címet vagy IP-címtartományt. 

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Az Azure Database for MariaDB kapcsolatai a 3306-os porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy nem engedélyezett a kimenő forgalom a 3306-as porton keresztül. Ebben az esetben addig nem tud csatlakozni a kiszolgálóhoz, amíg az informatikai részleg meg nem nyitja a 3306-os portot.
> 

## <a name="configure-ssl-settings"></a>Az SSL-beállítások konfigurálása
Alapértelmezés szerint a kiszolgáló és az ügyfélalkalmazások közti SSL-kapcsolatok kényszerítve vannak. Ez az alapértelmezett beállítás biztosítja a „mozgó” adatok biztonságát az adatfolyam interneten keresztüli titkosításával. A rövid útmutató egyszerűsége érdekében tiltsa le az SSL-kapcsolatokat a kiszolgálóján. Ennek az SSL-nek a letiltása éles kiszolgálók esetében nem javasolt. További információ: [Az SSL-kapcsolatok konfigurálása az alkalmazásban az Azure Database for MariaDB-hez való biztonságos kapcsolódásra](./howto-configure-ssl.md).

A következő példában letiltjuk az SSL kényszerítését a MariaDB-kiszolgálón.
 
 ```azurecli-interactive
 az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
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
  "earliestRestoreDate": null,
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

## <a name="connect-to-server-using-mysql-command-line"></a>Csatlakozás a kiszolgálóhoz a mysql parancssori eszközzel
Csatlakozzon a kiszolgálójához a **mysql** parancssori eszközzel. A parancssori eszközt [innen](https://dev.mysql.com/downloads/) töltheti le és telepítheti a számítógépén. Vagy kattinthat a kódmintákban található **Kipróbálom** gombra vagy az Azure Portal jobb felső eszköztárán található `>_` gombra is az **Azure Cloud Shell** megnyitásához.

Írja be a következő parancsokat: 

1. Csatlakozás a kiszolgálóhoz a **mysql** parancssori eszköz használatával:
```azurecli-interactive
 mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

2. Kiszolgáló állapotának megtekintése a mysql> parancssorban:
```sql
status
```
Ha minden megfelelően működik, a parancssori eszköz a következő szöveget jeleníti meg:

```bash
C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.39.0 MariaDB Server

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

Connection id:          64681
Current database:
Current user:           myadmin@40.118.201.21
SSL:                    Cipher in use is AES256-SHA
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.6.39.0 MariaDB Server
Protocol version:       10
Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    utf8
Conn.  characterset:    utf8
TCP port:               3306
Uptime:                 1 day 3 hours 28 min 50 sec

Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
--------------

mysql>
```

> [!TIP]
> További parancsokért lásd: [az MySQL 5.7 referenciaútmutatójának 4.5.1-es fejezetét](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-server-using-mysql-workbench"></a>Csatlakozás a kiszolgálóhoz a MySQL Workbench használatával
1.  Indítsa el a MySQL Workbench alkalmazást az ügyfélszámítógépen. A MySQL Workbench-et [innen](https://dev.mysql.com/downloads/workbench/) töltheti le és telepítheti.

2.  Az **Új kapcsolat létrehozása** párbeszédpanelen adja meg a következő információkat a **Paraméterek** lapon:

   ![új kapcsolat beállítása](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

| **Beállítás** | **Ajánlott érték** | **Leírás** |
|---|---|---|
|   Kapcsolat neve | My Connection | Adjon meg egy címkét a kapcsolathoz (tetszőlegesen kiválasztható) |
| Kapcsolati módszer | válassza a Standard (TCP/IP) lehetőséget | Csatlakozás az Azure Database for MariaDB-hez a TCP/IP protokollal |
| Gazdanév | mydemoserver.mariadb.database.azure.com | A korábban feljegyzett kiszolgálónév. |
| Port | 3306 | A rendszer a MariaDB alapértelmezett portját használja. |
| Felhasználónév | myadmin@mydemoserver | A korábban feljegyzett kiszolgáló-rendszergazdai bejelentkezés. |
| Jelszó | **** | Használja a korábban beállított rendszergazdaifiók-jelszót. |

Kattintson a **Kapcsolat tesztelése** lehetőségre, hogy tesztelje, minden paraméter helyesen lett-e konfigurálva.
Ezután a kapcsolatra való kattintva sikeresen csatlakozhat a kiszolgálóhoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha ezekre az erőforrásokra már nincs szüksége más gyorsútmutatókhoz/oktatóanyagokhoz, a következő paranccsal törölheti őket: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja az **[az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete)** parancsot.
```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

<!--
## Next steps

> [!div class="nextstepaction"]
> [Design a MariaDB Database with Azure CLI](./tutorial-design-database-using-cli.md)
-->
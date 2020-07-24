---
title: 'Gyors útmutató: kiszolgáló létrehozása – Azure CLI – Azure Database for MySQL'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre Azure-adatbázist MySQL-kiszolgálóhoz az Azure CLI használatával az Azure-erőforráscsoportban.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/15/2020
ms.custom: mvc
ms.openlocfilehash: 638fa5af3af1e81020e79c7c70f0c91f06676daf
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076686"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-using-azure-cli"></a>Rövid útmutató: Azure Database for MySQL-kiszolgáló létrehozása az Azure CLI-vel

> [!TIP]
> Érdemes lehet az egyszerűbb az [MySQL up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) Azure CLI-parancs használata (jelenleg előzetes verzióban érhető el). Próbálja [ki a rövid](./quickstart-create-server-up-azure-cli.md)útmutatót.

Ez a rövid útmutató bemutatja, hogyan használható az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) -parancsok a [Azure Cloud Shellban](https://shell.azure.com) egy Azure Database for MySQL-kiszolgáló öt perc alatt történő létrehozásához. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

> [!NOTE]
> Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli). 

## <a name="prerequisites"></a>Előfeltételek
Ehhez a cikkhez az Azure CLI 2,0-es vagy újabb verzióját kell futtatnia helyileg. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az az [login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) parancs használatával kell bejelentkeznie a fiókjába. Jegyezze fel az **ID** tulajdonságot, amely az Azure-fiók **előfizetés-azonosítójára** utal. 

```azurecli-interactive
az login
```

Válassza ki az adott előfizetést a fiókja alatt az [az Account set](/cli/azure/account) parancs használatával. Jegyezze fel az **azonosító** értéket az az **login** kimenetből, amelyet a parancs **előfizetés** argumentumának értékeként kíván használni. Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Az összes előfizetés beszerzéséhez használja [az az Account List](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list)lehetőséget.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz
Hozzon létre egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) az az [Group Create](/cli/azure/group) paranccsal, majd hozzon létre egy MySQL-kiszolgálót az erőforráscsoport belsejében. Egyedi nevet adjon meg. A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myresourcegroup` helyen.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

Hozzon létre egy Azure Database for MySQL-kiszolgálót az [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) paranccsal. Egy kiszolgáló több adatbázist tartalmazhat.

```azurecli
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```

A fenti argumentumok részletei: 

**Beállítás** | **Mintaérték** | **Leírás**
---|---|---
name | mydemoserver | Adjon meg egy egyedi nevet a Azure Database for MySQL-kiszolgálónak. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. 3–63 karakter hosszúságú lehet.
resource-group | myResourceGroup | Adja meg az Azure-erőforráscsoport nevét.
location | westus | A kiszolgáló Azure-helye.
admin-user | myadmin | A rendszergazda bejelentkezéshez használt felhasználóneve. Nem lehet **azure_superuser**, **admin**, **administrator**, **root**, **guest** vagy **public**.
admin-password | *secure password* | A rendszergazda felhasználó jelszava. A jelszó 8–128 karakterből állhat. A jelszónak legalább háromféle karaktert tartalmaznia kell a következő kategóriák közül: angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek és nem alfanumerikus karakterek.
sku-name|GP_Gen5_2|Adja meg az árképzési csomag és a számítási konfiguráció nevét. A {tarifacsomag}_{számítási generáció}_{virtuális magok} mintát követi rövidített módon. További információkért tekintse meg a [díjszabási szintet](./concepts-pricing-tiers.md) .

>[!IMPORTANT] 
>- A kiszolgálón a MySQL alapértelmezett verziója 5,7. Jelenleg a 5,6-es és a 8,0-es verzió is elérhető.
>- Az **az MySQL Server Create** parancs összes argumentumának megtekintéséhez tekintse meg ezt a [hivatkozási dokumentumot](/cli/azure/mysql/server#az-mysql-server-create).
>- Az SSL alapértelmezés szerint engedélyezve van a kiszolgálón. Az SSL további infroamtion lásd: [SSL-kapcsolat konfigurálása](howto-configure-ssl.md)

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása 
Alapértelmezés szerint a létrehozott új kiszolgáló tűzfal-szabályokkal van védve, és nem érhető el nyilvánosan. Az az [MySQL Server Firewall-Rule Create](/cli/azure/mysql/server/firewall-rule) paranccsal konfigurálhatja a tűzfal szabályát a kiszolgálón. Ez lehetővé teszi, hogy helyileg kapcsolódjon a kiszolgálóhoz.

A következő példában egy olyan `AllowMyIP` nevű tűzfalszabályt hozunk létre, amely a 192.168.0.1 IP-címről engedélyezi a kapcsolódást. Cserélje le azt az IP-címet, amelyről csatlakozni fog. Szükség esetén az IP-címek tartományát is használhatja. Nem tudja, hogyan keresse meg az IP-címet, majd válassza az [https://whatismyipaddress.com/](https://whatismyipaddress.com/) IP-cím beszerzése lehetőséget.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> A MySQL-hez készült Azure-adatbázis kapcsolatai a 3306-os porton keresztül kommunikálnak. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy nem engedélyezett a kimenő forgalom a 3306-as porton keresztül. Ebben az esetben addig nem tud csatlakozni a kiszolgálóhoz, amíg az informatikai részleg meg nem nyitja a 3306-os portot.

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
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Kapcsolódás Azure Database for MySQL kiszolgálóhoz a MySQL parancssori ügyfélprogram használatával
Csatlakozhat a kiszolgálóhoz egy népszerű ügyfél-eszköz használatával, **[mysql.exe](https://dev.mysql.com/downloads/)** parancssori eszközt [Azure Cloud Shell](../cloud-shell/overview.md). Másik lehetőségként a MySQL parancssort is használhatja a helyi környezetben.
```bash
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="clean-up-resources"></a>Erőforrások felszabadítása
Ha ezekre az erőforrásokra már nincs szüksége más gyorsútmutatókhoz/oktatóanyagokhoz, a következő paranccsal törölheti őket: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Ha csak az újonnan létrehozott kiszolgálót szeretné törölni, futtathatja az [az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete) parancsot.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
>[Php-alkalmazás létrehozása Windowson MySQL](../app-service/app-service-web-tutorial-php-mysql.md) 
> -sel [Php-alkalmazás létrehozása Linuxon a MySQL](../app-service/containers/tutorial-php-mysql-app.md) 
> -vel [Java-alapú Spring app létrehozása MySQL](https://docs.microsoft.com/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash) -sel

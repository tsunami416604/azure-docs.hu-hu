---
title: Tűzfalszabályok kezelése - Azure CLI - Azure Database for MySQL
description: Ez a cikk ismerteti, hogyan hozhat létre és kezelhet Azure Database for MySQL tűzfalszabályok at Azure CLI parancssorhasználatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 11aa4a80deba4df14c239e69910ea38bac1b9c55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063518"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Azure Database for MySQL tűzfalszabályok létrehozása és kezelése az Azure CLI használatával
A kiszolgálószintű tűzfalszabályok segítségével kezelheti a MySQL Server Azure-adatbázisához való hozzáférést egy adott IP-címről vagy IP-címek tartományából. Kényelmes Azure CLI-parancsokkal létrehozhat, frissíthet, törölhet, listázhat és megjeleníthet tűzfalszabályokat a kiszolgáló kezeléséhez. Az Azure Database for MySQL tűzfalak áttekintését az [Azure Database for MySQL server tűzfalszabályai](./concepts-firewall-rules.md)című témakörben találja.

Virtuális hálózati (VNet) szabályok is használható a kiszolgálóhoz való hozzáférés biztonságossá tétele. További információ a [virtuális hálózati szolgáltatás végpontjainak és szabályainak létrehozásáról és kezeléséről az Azure CLI használatával.](howto-manage-vnet-using-cli.md)

## <a name="prerequisites"></a>Előfeltételek
* [Telepítse az Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli).
* [Azure-adatbázis a MySQL-kiszolgálóhoz és adatbázishoz.](quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="firewall-rule-commands"></a>Tűzfalszabály-parancsok:
Az **az mysql server firewall-rule** parancs az Azure CLI-ből tűzfalszabályok létrehozására, törlésére, listára, megjelenítésére és frissítésére szolgál.

Parancsok:
- **létrehozás**: Hozzon létre egy Azure MySQL-kiszolgáló tűzfalszabályát.
- **delete**: Egy Azure MySQL-kiszolgáló tűzfalszabályának törlése.
- **list :** Sorolja fel az Azure MySQL-kiszolgáló tűzfalszabályait.
- **show**: Az Azure MySQL-kiszolgáló tűzfalszabályának részleteinek megjelenítése.
- **update**: Frissítsen egy Azure MySQL-kiszolgáló tűzfalszabályát.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Jelentkezzen be az Azure-ba, és sorolja fel az Azure-adatbázist a MySQL-kiszolgálókhoz
Biztonságosan csatlakoztassa az Azure CLI-t az Azure-fiókjához az **az bejelentkezési** parancs használatával.

1. A parancssorból futtassa a következő parancsot:
    ```azurecli
    az login
    ```
   Ez a parancs a következő lépésben használandó kódot adja ki.

2. A lap [https://aka.ms/devicelogin](https://aka.ms/devicelogin)megnyitásához használjon webböngészőt, majd írja be a kódot.

3. A kérdés, jelentkezzen be az Azure-hitelesítő adatok használatával.

4. A bejelentkezés engedélyezése után a rendszer kinyomtatja az előfizetések listáját a konzolon. Másolja a kívánt előfizetés azonosítóját az aktuális előfizetés használatára beállításához. Használja az [az fiókkészlet parancsot.](/cli/azure/account#az-account-set)
    ```azurecli-interactive
    az account set --subscription <your subscription id>
    ```

5. Sorolja fel az Azure-adatbázisok mySQL-kiszolgálók az előfizetés és az erőforrás-csoport, ha nem biztos a neveket. Használja az [az mysql server list](/cli/azure/mysql/server#az-mysql-server-list) parancsot.

    ```azurecli-interactive
    az mysql server list --resource-group myresourcegroup
    ```

   Jegyezze fel a névattribútumot a listában, amelyen meg kell adnia azt a MySQL-kiszolgálót, amelyen dolgozni szeretne. Ha szükséges, erősítse meg a kiszolgáló adatait, és használja a name attribútumot, hogy megbizonyosodjon a helyességről. Használja az [az mysql server show](/cli/azure/mysql/server#az-mysql-server-show) parancsot.

    ```azurecli-interactive
    az mysql server show --resource-group myresourcegroup --name mydemoserver
    ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Tűzfalszabályok listázása a MySQL Server Azure Database adatbázisában 
A kiszolgáló neve és az erőforráscsoport neve alapján sorolja fel a kiszolgáló tűzfalának meglévő szabályait a kiszolgálón. Használja az [az mysql server firewall list](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-list) parancsot.  Figyelje meg, hogy a kiszolgálónév attribútum a **--server** kapcsolóban van megadva, és nem a **--name** kapcsolóban. 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A kimenet ijedt szabályai (alapértelmezés szerint) JSON formátumban vannak. A **--output táblakapcsoló** segítségével olvashatóbb táblaformátumban adja ki az eredményeket.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Tűzfalszabály létrehozása az Azure Database for MySQL Server szolgáltatásban
Az Azure MySQL-kiszolgáló nevét és az erőforráscsoport nevét, hozzon létre egy új tűzfalszabályt a kiszolgálón. Használja az [az mysql szerver tűzfal create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) parancsot. Adja meg a szabály nevét, valamint a kezdő IP-címet és a záró IP-címet (az IP-címek tartományához való hozzáférés biztosításához).
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Egyetlen IP-cím elérésének engedélyezéséhez adja meg ugyanazt az IP-címet, mint a Kezdő IP és a Záró IP, mint ebben a példában.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Ha engedélyezni szeretné, hogy az Azure IP-címekből származó alkalmazások csatlakozzanak az Azure Database for MySQL-kiszolgálóhoz, adja meg a 0.0.0.0 IP-címet a kezdő IP és a záró IP-címként, mint ebben a példában.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

Sikeres en minden létrehozási parancs kimenete felsorolja a létrehozott tűzfalszabály részleteit JSON formátumban (alapértelmezés szerint). Hiba esetén a kimeneten hibaüzenet szövege jelenik meg.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Tűzfalszabály frissítése az Azure Database for MySQL-kiszolgálón 
Az Azure MySQL-kiszolgáló nevét és az erőforráscsoport nevét, frissítse a meglévő tűzfalszabály a kiszolgálón. Használja az [az mysql server firewall update](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-update) parancsot. Adja meg a meglévő tűzfalszabály nevét bemenetként, valamint a frissítendő kezdő IP- és záró IP-attribútumokat.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Sikeres en a parancs kimenete a frissített tűzfalszabály részleteit listázza JSON formátumban (alapértelmezés szerint). Hiba esetén a kimeneten hibaüzenet szövege jelenik meg.

> [!NOTE]
> Ha a tűzfalszabály nem létezik, a szabályt a frissítési parancs hozlétre.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Tűzfalszabály részleteinek megjelenítése a MySQL Server Azure Database adatbázisában
Az Azure MySQL-kiszolgáló nevét és az erőforráscsoport nevét, a kiszolgáló meglévő tűzfalszabály adatait jeleníti meg. Használja az [az mysql szerver tűzfal show](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-show) parancsot. Adja meg a meglévő tűzfalszabály nevét bemenetként.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Sikeres esetben a parancs kimenete a megadott tűzfalszabály részleteit listázza JSON formátumban (alapértelmezés szerint). Hiba esetén a kimeneten hibaüzenet szövege jelenik meg.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Tűzfalszabály törlése a MySQL Server Azure Database szolgáltatásában
Az Azure MySQL-kiszolgáló nevét és az erőforráscsoport nevét, távolítsa el a meglévő tűzfalszabálya a kiszolgálóról. Használja az [az mysql szerver tűzfal törlés](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-delete) parancsot. Adja meg a meglévő tűzfalszabály nevét.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
A siker után nincs kimenet. Hiba esetén a hibaüzenet szövege megjelenik.

## <a name="next-steps"></a>További lépések
- További információk [az Azure Database for MySQL Server tűzfalszabályairól.](./concepts-firewall-rules.md)
- [Az Azure Database for MySQL tűzfalszabályok létrehozása és kezelése az Azure Portal használatával.](./howto-manage-firewall-using-portal.md)
- További biztonságos hozzáférés a kiszolgálóhoz [virtuális hálózati szolgáltatás végpontjainak és szabályainak létrehozásával és kezelésével az Azure CLI használatával.](howto-manage-vnet-using-cli.md)
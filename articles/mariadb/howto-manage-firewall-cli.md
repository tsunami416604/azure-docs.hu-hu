---
title: Tűzfalszabályok kezelése - Azure CLI – Azure Database for MariaDB
description: Ez a cikk bemutatja, hogyan hozhat létre és kezelhet Azure Database for MariaDB tűzfalszabályokat az Azure CLI parancssorhasználatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 898b65f07140bca04bd97ff7314b01920b783914
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530631"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Azure Database for MariaDB tűzfalszabályok létrehozása és kezelése az Azure CLI használatával
A kiszolgálószintű tűzfalszabályok segítségével kezelheti a MariaDB Server Azure-adatbázisához való hozzáférést egy adott IP-címről vagy IP-címek tartományából. Kényelmes Azure CLI-parancsokkal létrehozhat, frissíthet, törölhet, listázhat és megjeleníthet tűzfalszabályokat a kiszolgáló kezeléséhez. Az Azure Database for MariaDB tűzfalak áttekintését az [Azure Database for MariaDB kiszolgáló tűzfalszabályai](./concepts-firewall-rules.md)című témakörben találja.

Virtuális hálózati (VNet) szabályok is használható a kiszolgálóhoz való hozzáférés biztonságossá tétele. További információ a [virtuális hálózati szolgáltatás végpontjainak és szabályainak létrehozásáról és kezeléséről az Azure CLI használatával.](howto-manage-vnet-cli.md)

## <a name="prerequisites"></a>Előfeltételek
* [Telepítse az Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli).
* [A MariaDB-kiszolgáló és -adatbázis Azure-adatbázisa.](quickstart-create-mariadb-server-database-using-azure-cli.md)

## <a name="firewall-rule-commands"></a>Tűzfalszabály-parancsok:
Az **az mariadb server firewall-rule** parancs az Azure CLI-ből tűzfalszabályok létrehozására, törlésére, listára, megjelenítésére és frissítésére szolgál.

Parancsok:
- **létrehozás**: Hozzon létre egy Azure MariaDB-kiszolgáló tűzfalszabályát.
- **delete**: Egy Azure MariaDB-kiszolgáló tűzfalszabályának törlése.
- **list :** Sorolja fel az Azure MariaDB kiszolgáló tűzfalszabályait.
- **show**: Az Azure MariaDB-kiszolgáló tűzfalszabályának részleteinek megjelenítése.
- **frissítés**: Frissítsen egy Azure MariaDB-kiszolgáló tűzfalszabályát.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Jelentkezzen be az Azure-ba, és sorolja fel a MariaDB-kiszolgálók Azure-adatbázisát
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

5. Sorolja fel az Azure-adatbázisok MariaDB-kiszolgálók az előfizetés és az erőforráscsoport, ha nem biztos a neveket. Használja az [az mariadb kiszolgálólista](/cli/azure/mariadb/server#az-mariadb-server-list) parancsot.

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Jegyezze fel a névattribútumot a listában, amelyen meg kell adnia azt a MariaDB-kiszolgálót, amelyen dolgozni szeretne. Ha szükséges, erősítse meg a kiszolgáló adatait, és használja a name attribútumot, hogy megbizonyosodjon a helyességről. Használja az [az mariadb server show](/cli/azure/mariadb/server#az-mariadb-server-show) parancsot.

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Tűzfalszabályok listázása a MariaDB Server Azure Database szolgáltatásában 
A kiszolgáló neve és az erőforráscsoport neve alapján sorolja fel a kiszolgáló tűzfalának meglévő szabályait a kiszolgálón. Használja az [az mariadb server firewall list](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list) parancsot.  Figyelje meg, hogy a kiszolgálónév attribútum a **--server** kapcsolóban van megadva, és nem a **--name** kapcsolóban. 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A kimenet ijedt szabályai (alapértelmezés szerint) JSON formátumban vannak. A **--output táblakapcsoló** segítségével olvashatóbb táblaformátumban adja ki az eredményeket.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Tűzfalszabály létrehozása a MariaDB Server Azure Database szolgáltatásában
Az Azure MariaDB-kiszolgáló nevének és az erőforráscsoport nevének használatával hozzon létre egy új tűzfalszabályt a kiszolgálón. Használja az [az mariadb server firewall create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) parancsot. Adja meg a szabály nevét, valamint a kezdő IP-címet és a záró IP-címet (az IP-címek tartományához való hozzáférés biztosításához).
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Egyetlen IP-cím elérésének engedélyezéséhez adja meg ugyanazt az IP-címet, mint a Kezdő IP és a Záró IP, mint ebben a példában.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Ha engedélyezni szeretné, hogy az Azure IP-címekből származó alkalmazások csatlakozzanak az Azure Database for MariaDB-kiszolgálóhoz, adja meg a 0.0.0.0 IP-címet a kezdő IP-cím és a záró IP-címként, mint ebben a példában.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

Sikeres en minden létrehozási parancs kimenete felsorolja a létrehozott tűzfalszabály részleteit JSON formátumban (alapértelmezés szerint). Hiba esetén a kimeneten hibaüzenet szövege jelenik meg.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Tűzfalszabály frissítése a MariaDB-kiszolgálóAzure-adatbázisában 
Az Azure MariaDB-kiszolgáló nevét és az erőforráscsoport nevét használva frissítsen egy meglévő tűzfalszabályt a kiszolgálón. Használja az [az mariadb server firewall update](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update) parancsot. Adja meg a meglévő tűzfalszabály nevét bemenetként, valamint a frissítendő kezdő IP- és záró IP-attribútumokat.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Sikeres en a parancs kimenete a frissített tűzfalszabály részleteit listázza JSON formátumban (alapértelmezés szerint). Hiba esetén a kimeneten hibaüzenet szövege jelenik meg.

> [!NOTE]
> Ha a tűzfalszabály nem létezik, a szabályt a frissítési parancs hozlétre.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Tűzfalszabály részleteinek megjelenítése a MariaDB Server Azure Database szolgáltatásában
Az Azure MariaDB-kiszolgáló nevének és az erőforráscsoport nevének használatával jelenítse meg a kiszolgáló meglévő tűzfalszabályának részleteit. Használja az [az mariadb szerver tűzfal show](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show) parancsot. Adja meg a meglévő tűzfalszabály nevét bemenetként.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Sikeres esetben a parancs kimenete a megadott tűzfalszabály részleteit listázza JSON formátumban (alapértelmezés szerint). Hiba esetén a kimeneten hibaüzenet szövege jelenik meg.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Tűzfalszabály törlése a MariaDB Server Azure Database szolgáltatásában
Az Azure MariaDB-kiszolgáló nevének és az erőforráscsoport nevének használatával távolítsa el a meglévő tűzfalszabályt a kiszolgálóról. Használja az [az mariadb kiszolgáló tűzfaltörlése](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete) parancsot. Adja meg a meglévő tűzfalszabály nevét.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
A siker után nincs kimenet. Hiba esetén a hibaüzenet szövege megjelenik.

## <a name="next-steps"></a>További lépések
- További információk [az Azure Database for MariaDB Server tűzfalszabályairól.](./concepts-firewall-rules.md)
- [Hozzon létre és kezeljen Azure Database for MariaDB tűzfalszabályokat az Azure Portal használatával.](./howto-manage-firewall-portal.md)
- További biztonságos hozzáférés a kiszolgálóhoz [virtuális hálózati szolgáltatás végpontjainak és szabályainak létrehozásával és kezelésével az Azure CLI használatával.](howto-manage-vnet-cli.md)

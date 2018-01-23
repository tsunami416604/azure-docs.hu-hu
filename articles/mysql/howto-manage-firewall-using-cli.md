---
title: "Hozzon létre és kezelheti az Azure-adatbázis MySQL tűzfalszabályok Azure parancssori felület használatával |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan létrehozása és kezelése az Azure Database az Azure CLI-vel parancssori MySQL tűzfalszabályok."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 01/18/2018
ms.openlocfilehash: 1738fdd85391135357d34fefa878538866f21b91
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Létrehozása és kezelése az Azure-adatbázis a MySQL tűzfalszabályok az Azure parancssori felület használatával
Kiszolgálószintű tűzfal-szabályokat a rendszergazdák hozzáférésének kezelése az Azure-adatbázis MySQL-kiszolgáló egy adott IP-cím vagy egy adott IP-címeket. Tetszés szerinti Azure parancssori felület parancsait használva hozhat létre, frissítése, törlése, a listában, és kezelheti a kiszolgálót a tűzfalszabályok megjelenítése. Az áttekintést az Azure-adatbázis MySQL tűzfalak, lásd: [a MySQL-kiszolgáló tűzfalszabályainak Azure-adatbázis](./concepts-firewall-rules.md)

## <a name="prerequisites"></a>Előfeltételek
* [Telepítse az Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Telepítse az Azure Python SDK PostgreSQL és a MySQL-szolgáltatások.
* Az Azure CLI összetevő PostgreSQL és a MySQL-szolgáltatások telepítését.
* MySQL-kiszolgáló Azure-adatbázis létrehozása.

## <a name="firewall-rule-commands"></a>Tűzfal szabály parancsokat:
A **az mysql-tűzfalszabályt** parancs segítségével az Azure parancssori felületen létrehozása, törlése, listában megjelenítése, és tűzfalszabályainak frissítése.

Parancsok:
- **Hozzon létre**: hozzon létre egy Azure-beli MySQL tűzfalszabály létrehozása.
- **Törlés**: az Azure-beli MySQL tűzfalszabály törlése.
- **lista**: az Azure-beli MySQL-kiszolgáló tűzfalszabályainak listában.
- **megjelenítése**: tűzfalszabály létrehozása az Azure-beli MySQL-kiszolgálók részleteinek megjelenítése.
- **frissítés**: az Azure-beli MySQL tűzfalszabály módosítása.

## <a name="log-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Jelentkezzen be az Azure és a MySQL-kiszolgálók az Azure-adatbázis listázása
Biztonságosan csatlakozzon az Azure CLI fel fiókjával használatával a **az bejelentkezési** parancsot.

1. A parancssorból futtassa a következő parancsot:
```azurecli
az login
```
Ez a parancs kimenete a következő lépésben használandó kódot.

2. Nyissa meg a lap egy webböngésző segítségével [https://aka.ms/devicelogin](https://aka.ms/devicelogin), és írja be a kódját.

3. Amikor a rendszer felkéri, jelentkezzen be az Azure-beli hitelesítő adataival.

4. Miután a bejelentkezési azonosító jogosult, előfizetések listája nyomtatása a konzolon. Másolja át a jelenlegi előfizetés használatára állítsa be a kívánt előfizetés azonosítója. Használja a [fiók beállítása az](/cli/azure/account#az_account_set) parancsot.
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Az előfizetés és az erőforrás csoport MySQL-kiszolgálók az Azure adatbázisok listában, ha bizonytalan nevét. Használja a [az mysql kiszolgálólista](/cli/azure/mysql/server#az_mysql_server_list) parancsot.

   ```azurecli-interactive
   az mysql server list --resource-group myResourceGroup
   ```

   Vegye figyelembe a listát, amelynek meg kell adnia a MySQL-kiszolgáló működését az név attribútuma. Ha szükséges, erősítse meg az adatokat, az adott kiszolgálón és a megfelelő legyen a name attribútummal. Használja a [az mysql server megjelenítése](/cli/azure/mysql/server#az_mysql_server_show) parancsot.

   ```azurecli-interactive
   az mysql server show --resource-group myResourceGroup --name mysqlserver4demo
   ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Tűzfalszabályok listája az Azure Database-MySQL-kiszolgáló 
Az a kiszolgáló nevét és az erőforráscsoport neve, a kiszolgálón a meglévő kiszolgáló tűzfalszabályainak listában. Használja a [az mysql kiszolgálólista tűzfal](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_list) parancsot.  Figyelje meg, hogy a server name attribútum szerepel a **– kiszolgáló** kapcsoló és a nem a **--neve** kapcsoló. 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server-name mysqlserver4demo
```
A kimeneti szabályokat, sorolja fel, ha bármely, a JSON formátumban (alapértelmezés). Használhatja a **--eredménytábla** kapcsolót, hogy az eredményeket a tábla olvashatóbb formátumban.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server-name mysqlserver4demo --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Hozzon létre egy tűzfalszabályt az Azure Database-MySQL-kiszolgáló
Az az Azure-beli MySQL-kiszolgáló nevét és az erőforráscsoport neve, hozzon létre egy új tűzfalszabályt a kiszolgálón. Használja a [az mysql kiszolgáló tűzfal létrehozása](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) parancsot. Adjon meg egy nevet a szabályt, valamint a kezdő IP, és a befejező IP-cím (kötése biztosít hozzáférést egy adott IP-címek) a szabályhoz.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup --server-name mysqlserver4demo --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Engedélyezi a hozzáférést egy IP-cím, adja meg az azonos IP-címre a kezdő IP- és a záró IP-, ebben a példában látható módon.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup --server-name mysqlserver4demo --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Lehetővé teszik az alkalmazások Azure IP-címekről a MySQL-kiszolgáló Azure-adatbázishoz való kapcsolódáshoz, adja meg az IP-cím 0.0.0.0, mint a kezdő IP- és a záró IP-, ebben a példában látható módon.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  
--server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

Sikeres, akkor minden egyes létrehozása parancs kimeneti sorolja fel a létrehozott, JSON formátumban (alapértelmezés) tűzfalszabály részleteit. Ha hiba történik, a kimeneti helyette látható hibaüzenet-szöveg.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Egy tűzfalszabály MySQL-kiszolgáló az Azure Database-frissítés 
Az az Azure-beli MySQL-kiszolgáló nevét és az erőforráscsoport neve, frissítse a meglévő tűzfalszabály a kiszolgálón. Használja a [az mysql kiszolgáló tűzfal frissítés](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_update) parancsot. Adja meg a nevét a meglévő tűzfalszabályt, bemeneti, valamint a kezdő frissítése IP-cím és a záró IP-attribútumok.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myResourceGroup --server-name mysqlserver4demo --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Sikeres, akkor a parancs kimenete frissítette, JSON formátumban (alapértelmezés) tűzfalszabály részleteit sorolja fel. Ha hiba történik, a kimeneti helyette látható hibaüzenet-szöveg.

> [!NOTE]
> Ha a tűzfalszabály nem létezik, a szabály a frissítés parancs jön létre.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Tűzfal szabály részleteinek megjelenítése az Azure Database-MySQL-kiszolgáló
Az az Azure-beli MySQL-kiszolgáló nevét és az erőforráscsoport neve, a meglévő tűzfal szabály részleteinek megjelenítése a kiszolgálóról. Használja a [az mysql kiszolgáló tűzfal megjelenítése](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_show) parancsot. A meglévő tűzfalszabály neve meg bemeneti adatként.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myResourceGroup --server-name mysqlserver4demo --name FirewallRule1
```
Sikeres, akkor a parancs kimenetét a megadott, JSON formátumban (alapértelmezés) tűzfalszabály részleteit sorolja fel. Ha hiba történik, a kimeneti helyette látható hibaüzenet-szöveg.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>MySQL-kiszolgáló az Azure Database-tűzfalszabály törlése
Meglévő tűzfalszabály használ az Azure-beli MySQL-kiszolgáló nevét és az erőforráscsoport neve, távolítsa el a kiszolgálóról. Használja a [az mysql kiszolgálótűzfal törlése](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_delete) parancsot. Adja meg a meglévő tűzfalszabály nevét.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myResourceGroup --server-name mysqlserver4demo --name FirewallRule1
```
Sikeres, akkor nincs nincs kimenete. Hiba esetén a következő hibaüzenet-szöveg jeleníti meg.

## <a name="next-steps"></a>További lépések
- Több megismerkedett [Azure-adatbázis a MySQL-kiszolgáló tűzfalszabályainak](./concepts-firewall-rules.md).
- [Hozzon létre és kezelheti az Azure-adatbázis MySQL tűzfalszabályokat az Azure portál használatával](./howto-manage-firewall-using-portal.md).

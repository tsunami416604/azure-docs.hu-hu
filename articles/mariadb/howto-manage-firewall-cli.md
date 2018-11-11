---
title: Hozzon létre és kezelhető az Azure Database for MariaDB-tűzfalszabályok Azure CLI használatával
description: Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhető az Azure Database for MariaDB tűzfalszabályok az Azure CLI parancssori.
services: mariadb
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/10/2018
ms.openlocfilehash: 1dba4b99dc50d1908b7b3d0488ba6ebf2f85c2a4
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516230"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-cli"></a>Hozzon létre és kezelhető az Azure Database for MariaDB tűzfalszabályok az Azure CLI-vel
Kiszolgálószintű tűzfalszabályok lehetővé teszik a rendszergazdák hozzáférés kezelésére egy Azure Database for MariaDB-kiszolgáló megadott IP-címet vagy egy IP-címtartományt. Kényelmes megoldás az Azure parancssori felület parancsait használva, létrehozhat, frissítése, törlése, a listában, és kezelheti a kiszolgálót a tűzfalszabályok megjelenítése. Azure Database for MariaDB tűzfalak áttekintése, lásd: [, Azure Database for MariaDB-kiszolgáló tűzfalszabályait](./concepts-firewall-rules.md)

## <a name="prerequisites"></a>Előfeltételek
* [Telepítse az Azure parancssori felületét (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Egy [, Azure Database for MariaDB-kiszolgáló és az adatbázis](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Tűzfal szabály parancsok:
A **az mariadb server firewall-rule** parancs segítségével az Azure parancssori felületen létrehozása, törlése, listázása, megjelenítése és tűzfalszabályainak frissítése.

Parancsok:
- **Hozzon létre**: hozzon létre egy Azure MariaDB kiszolgálói tűzfalszabályt.
- **Törlés**: egy Azure MariaDB tűzfalszabály törlése.
- **lista**: az Azure MariaDB-kiszolgáló tűzfalszabályainak listázása.
- **Megjelenítés**: tűzfalszabály létrehozása az Azure MariaDB-kiszolgáló részleteinek megjelenítéséhez.
- **frissítés**: egy Azure MariaDB tűzfalszabály módosítása.

## <a name="log-in-to-azure-and-list-your-azure-database-for-mariadb-servers"></a>Jelentkezzen be az Azure-ba, és listázása az Azure Database for MariaDB-kiszolgálók
Biztonságos csatlakozás az Azure CLI az Azure-fiókkal használatával a **az bejelentkezési** parancsot.

1. A parancssorból futtassa a következő parancsot:
```azurecli
az login
```
Ez a parancs a következő lépésben használandó kódot jelenít meg.

2. Nyissa meg a webböngésző használata [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin), majd írja be a kódot.

3. Amikor a rendszer felkéri, jelentkezzen be az Azure-beli hitelesítő adataival.

4. A bejelentkezési azonosító jogosult, miután az előfizetések listája, nyomtatása a konzolon. Másolja ki a kívánt előfizetés azonosítóértékét azonosítója beállítása a jelenlegi használni kívánt előfizetést. Használja a [fiók beállítása az](/cli/azure/account#az-account-set) parancsot.
   ```azurecli-interactive
   az account set --subscription <your subscription id>
   ```

5. Az Azure-adatbázisok, az előfizetésben és erőforráscsoportban csoportban MariaDB-kiszolgálók listája, ha bizonytalan a nevek. Használja a [az mariadb kiszolgálólista](/cli/azure/mariadb/server#az-mariadb-server-list) parancsot.

   ```azurecli-interactive
   az mariadb server list --resource-group myresourcegroup
   ```

   Vegye figyelembe a name attribútum listáján, és meg kell adnia a MariaDB-kiszolgáló működik. Szükség esetén ellenőrizze a részleteket az adott kiszolgálón és a name attribútum használatával, hogy helyes-e. Használja a [az mariadb server show](/cli/azure/mariadb/server#az-mariadb-server-show) parancsot.

   ```azurecli-interactive
   az mariadb server show --resource-group myresourcegroup --name mydemoserver
   ```

## <a name="list-firewall-rules-on-azure-database-for-mariadb-server"></a>Lista, Azure Database for MariaDB-kiszolgáló tűzfalszabályait 
A meglévő kiszolgáló tűzfalszabályainak a kiszolgálón a kiszolgáló nevét és az erőforráscsoport nevét használja, listája. Használja a [az mariadb tűzfal kiszolgálólista](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-list) parancsot.  Figyelje meg, hogy a kiszolgáló neve attribútum van megadva a a **– kiszolgáló** váltson, és nem a **--neve** váltson. 
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A kimenet a szabályokat sorolja fel, ha bármely, a JSON formátumban (alapértelmezés szerint). Használhatja a **--eredménytábla** kapcsolót, hogy az eredményeket táblázatos olvashatóbb formátumban.
```azurecli-interactive
az mariadb server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Hozzon létre egy tűzfalszabályt az Azure Database for MariaDB-kiszolgáló
Használja az Azure MariaDB-kiszolgáló nevét és az erőforráscsoport neve, hozzon létre egy új tűzfalszabályt a kiszolgálón. Használja a [az mariadb kiszolgálótűzfal létrehozása](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) parancsot. Adjon meg egy nevet a szabálynak, valamint a kezdő IP-cím és a záró IP-címet (adjon meg egy IP-címtartományhoz való hozzáférés) a szabályhoz.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Engedélyezi a hozzáférést az egyetlen IP-címet, adja meg a megegyező IP-címre a kezdő IP- és a záró IP-cím, ebben a példában látható módon.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Ahhoz, hogy az alkalmazások az Azure IP-címekről az Azure Database for MariaDB-kiszolgálóhoz való kapcsolódáshoz, adja meg az IP-cím 0.0.0.0 kezdő IP-és záró IP-cím, ebben a példában látható módon.
```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mariadb --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

Követően sikeres a parancs kimenete listázza a létrehozott, JSON-formátumban (alapértelmezés szerint) tűzfalszabály részletei létrehozni. Ha hiba történik, a kimeneti inkább látható hiba üzenet szövege.

## <a name="update-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Egy tűzfalszabályt, Azure database for MariaDB-kiszolgáló frissítése 
Használja az Azure MariaDB-kiszolgáló nevét és az erőforráscsoport neve, frissítse a meglévő tűzfalszabály a kiszolgálón. Használja a [az mariadb kiszolgálói tűzfal frissítése](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-update) parancsot. Adja meg a nevét a meglévő tűzfalszabály, bemeneti, valamint a kezdő IP-cím és a záró IP attribútumok frissítése.
```azurecli-interactive
az mariadb server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Követően sikeres a parancs kimenete listázza a frissítése után JSON formátumban (alapértelmezés szerint) a tűzfalszabály részletei. Ha hiba történik, a kimeneti inkább látható hiba üzenet szövege.

> [!NOTE]
> Ha a tűzfalszabály nem létezik, a szabály által az update parancs jön létre.

## <a name="show-firewall-rule-details-on-azure-database-for-mariadb-server"></a>Tűzfal szabály részleteinek megjelenítéséhez az Azure Database for MariaDB-kiszolgáló
Használja az Azure MariaDB-kiszolgáló nevét és az erőforráscsoport neve, a meglévő tűzfal szabály részleteinek megjelenítéséhez a kiszolgálóról. Használja a [az mariadb kiszolgálói tűzfal show](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-show) parancsot. Adja meg a nevét a meglévő tűzfalszabály bemenetként.
```azurecli-interactive
az mariadb server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Követően sikeres a parancs kimenete listázza a megadott, JSON-formátumban (alapértelmezés szerint) a tűzfalszabály részletei. Ha hiba történik, a kimeneti inkább látható hiba üzenet szövege.

## <a name="delete-a-firewall-rule-on-azure-database-for-mariadb-server"></a>Azure Database tűzfalszabály MariaDB-kiszolgáló törlése
Meglévő tűzfalszabály használja az Azure MariaDB-kiszolgáló nevét és az erőforráscsoport neve, távolítsa el a kiszolgálóról. Használja a [az mariadb kiszolgálótűzfal törlése](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-delete) parancsot. Adja meg a meglévő tűzfalszabály nevét.
```azurecli-interactive
az mariadb server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Sikeres, Befejezés esetén nem semmilyen kimenet. Hiba esetén a következő hibaüzenetek jeleníti meg.

## <a name="next-steps"></a>További lépések
- Ismerje meg jobban az kapcsolatos [, Azure Database for MariaDB-kiszolgáló tűzfalszabályainak](./concepts-firewall-rules.md).
- [Hozzon létre és kezelhető az Azure Database for MariaDB tűzfalszabályok az Azure portal használatával](./howto-manage-firewall-portal.md).
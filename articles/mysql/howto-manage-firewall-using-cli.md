---
title: Hozzon létre és kezelhető az Azure Database for MySQL-tűzfalszabályok Azure CLI használatával
description: Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhető az Azure Database for MySQL tűzfalszabályok az Azure CLI parancssori.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: dca7d09a5358f5e8b4025dc5e35e4465e21d77a2
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59488165"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-cli"></a>Hozzon létre és kezelhető az Azure Database for MySQL tűzfalszabályok az Azure CLI-vel
Kiszolgálószintű tűzfalszabályok segítségével hozzáférésének kezelése az Azure Database for MySQL-kiszolgáló megadott IP-címet vagy egy IP-címtartományt. Kényelmes megoldás az Azure parancssori felület parancsait használva, létrehozhat, frissítése, törlése, a listában, és kezelheti a kiszolgálót a tűzfalszabályok megjelenítése. Azure Database for MySQL-tűzfalak áttekintése, lásd: [, Azure Database for MySQL-kiszolgáló tűzfalszabályainak](./concepts-firewall-rules.md).

Virtuális hálózat (VNet) szabályok is használható a kiszolgálóhoz való hozzáférés biztonsága érdekében. Tudjon meg többet [létrehozása és kezelése a virtuális hálózati szolgáltatás végpontjai és az Azure CLI használatával szabályok](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Előfeltételek
* [Telepítse az Azure parancssori felületét (CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Egy [, Azure Database for MySQL-kiszolgáló és adatbázis](quickstart-create-mysql-server-database-using-azure-cli.md).

## <a name="firewall-rule-commands"></a>Tűzfal szabály parancsok:
A **az mysql server firewall-rule** parancs segítségével az Azure parancssori felületen létrehozása, törlése, listázása, megjelenítése és tűzfalszabályainak frissítése.

Parancsok:
- **Hozzon létre**: Hozzon létre egy Azure-beli MySQL-kiszolgáló tűzfalszabályt.
- **Törlés**: Egy Azure-beli MySQL-kiszolgáló tűzfalszabály törlése.
- **Lista**: Az Azure-beli MySQL-kiszolgáló tűzfalszabályainak listája.
- **Megjelenítés**: Tűzfalszabály létrehozása az Azure-beli MySQL-kiszolgáló részleteinek megjelenítéséhez.
- **Frissítés**: Frissítse az Azure-beli MySQL-kiszolgálói tűzfalszabályt.

## <a name="sign-in-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Jelentkezzen be az Azure-ba, és listázása az Azure Database for MySQL-kiszolgálók
Biztonságos csatlakozás az Azure CLI az Azure-fiókkal használatával a **az bejelentkezési** parancsot.

1. A parancssorból futtassa a következő parancsot:
    ```azurecli
    az login
    ```
   Ez a parancs a következő lépésben használandó kódot jelenít meg.

2. Nyissa meg a webböngésző használata [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin), majd írja be a kódot.

3. Amikor a rendszer kéri jelentkezzen be Azure hitelesítő adatait.

4. A bejelentkezési azonosító jogosult, miután az előfizetések listája, nyomtatása a konzolon. Másolja ki a kívánt előfizetés azonosítóértékét azonosítója beállítása a jelenlegi használni kívánt előfizetést. Használja a [fiók beállítása az](/cli/azure/account#az-account-set) parancsot.
    ```azurecli-interactive
    az account set --subscription <your subscription id>
    ```

5. Ha bizonytalan a nevek, listázza a Azure database for MySQL-kiszolgálók az előfizetésben és erőforráscsoportban csoportban. Használja a [az mysql server list](/cli/azure/mysql/server#az-mysql-server-list) parancsot.

    ```azurecli-interactive
    az mysql server list --resource-group myresourcegroup
    ```

   Vegye figyelembe a name attribútum listáján, és meg kell adnia a MySQL-kiszolgáló működjön. Szükség esetén ellenőrizze a részleteket az adott kiszolgálón és a name attribútum használatával, hogy helyes-e. Használja a [az mysql server show](/cli/azure/mysql/server#az-mysql-server-show) parancsot.

    ```azurecli-interactive
    az mysql server show --resource-group myresourcegroup --name mydemoserver
    ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Lista, Azure Database for MySQL-kiszolgáló tűzfalszabályait 
A meglévő kiszolgáló tűzfalszabályainak a kiszolgálón a kiszolgáló nevét és az erőforráscsoport nevét használja, listája. Használja a [az mysql server firewall list](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-list) parancsot.  Figyelje meg, hogy a kiszolgáló neve attribútum van megadva a a **– kiszolgáló** váltson, és nem a **--neve** váltson. 
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A kimenet a szabályokat sorolja fel, ha bármely, a JSON formátumban (alapértelmezés szerint). Használhatja a **--eredménytábla** kapcsolót, hogy az eredményeket táblázatos olvashatóbb formátumban.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-a-firewall-rule-on-azure-database-for-mysql-server"></a>Hozzon létre egy tűzfalszabályt az Azure Database for MySQL-kiszolgáló
Használja az Azure-beli MySQL-kiszolgáló nevét és az erőforráscsoport neve, hozzon létre egy új tűzfalszabályt a kiszolgálón. Használja a [az mysql server firewall létrehozása](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) parancsot. Adjon meg egy nevet a szabálynak, valamint a kezdő IP-cím és a záró IP-címet (adjon meg egy IP-címtartományhoz való hozzáférés) a szabályhoz.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Engedélyezi a hozzáférést az egyetlen IP-címet, adja meg a megegyező IP-címre a kezdő IP- és a záró IP-cím, ebben a példában látható módon.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```

Ahhoz, hogy az alkalmazások az Azure IP-címekről az Azure Database for MySQL-kiszolgálóhoz csatlakozni, adja meg az IP-cím 0.0.0.0 kezdő IP-és záró IP-cím, ebben a példában látható módon.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mysql --name "AllowAllWindowsAzureIps" --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

Követően sikeres a parancs kimenete listázza a létrehozott, JSON-formátumban (alapértelmezés szerint) tűzfalszabály részletei létrehozni. Ha hiba történik, a kimeneti inkább látható hiba üzenet szövege.

## <a name="update-a-firewall-rule-on-azure-database-for-mysql-server"></a>Egy tűzfalszabály, Azure database for MySQL-kiszolgáló frissítése 
Használja az Azure-beli MySQL-kiszolgáló nevét és az erőforráscsoport neve, frissítse a meglévő tűzfalszabály a kiszolgálón. Használja a [az mysql server firewall update](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-update) parancsot. Adja meg a nevét a meglévő tűzfalszabály, bemeneti, valamint a kezdő IP-cím és a záró IP attribútumok frissítése.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Követően sikeres a parancs kimenete listázza a frissítése után JSON formátumban (alapértelmezés szerint) a tűzfalszabály részletei. Ha hiba történik, a kimeneti inkább látható hiba üzenet szövege.

> [!NOTE]
> Ha a tűzfalszabály nem létezik, a szabály által az update parancs jön létre.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Tűzfal szabály részleteinek megjelenítéséhez az Azure Database for MySQL-kiszolgáló
Használja az Azure-beli MySQL-kiszolgáló nevét és az erőforráscsoport neve, a meglévő tűzfal szabály részleteinek megjelenítéséhez a kiszolgálóról. Használja a [az mysql server firewall show](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-show) parancsot. Adja meg a nevét a meglévő tűzfalszabály bemenetként.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Követően sikeres a parancs kimenete listázza a megadott, JSON-formátumban (alapértelmezés szerint) a tűzfalszabály részletei. Ha hiba történik, a kimeneti inkább látható hiba üzenet szövege.

## <a name="delete-a-firewall-rule-on-azure-database-for-mysql-server"></a>Törölje egy tűzfalszabályt, Azure database for MySQL-kiszolgáló
Meglévő tűzfalszabály használ az Azure-beli MySQL-kiszolgáló nevét és az erőforráscsoport neve, távolítsa el a kiszolgálóról. Használja a [az mysql server firewall törlése](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-delete) parancsot. Adja meg a meglévő tűzfalszabály nevét.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name FirewallRule1
```
Sikeres, Befejezés esetén nem semmilyen kimenet. Hiba esetén a következő hibaüzenetek jeleníti meg.

## <a name="next-steps"></a>További lépések
- Ismerje meg jobban az kapcsolatos [, Azure Database for MySQL-kiszolgáló tűzfalszabályainak](./concepts-firewall-rules.md).
- [Hozzon létre és kezelhető az Azure Database for MySQL tűzfalszabályok az Azure portal használatával](./howto-manage-firewall-using-portal.md).
- További való biztonságos hozzáférést a kiszolgáló által [létrehozása és kezelése a virtuális hálózati szolgáltatás végpontjai és az Azure CLI használatával szabályok](howto-manage-vnet-using-cli.md).
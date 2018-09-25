---
title: Hozzon létre és kezelhető az Azure Database for PostgreSQL-tűzfalszabályok Azure CLI használatával
description: Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhető az Azure Database for PostgreSQL tűzfalszabályok az Azure CLI parancssori.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 05/4/2018
ms.openlocfilehash: f5133b5da055710208390bfe7fd5d6d7d85696df
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965347"
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Hozzon létre és kezelhető az Azure Database for PostgreSQL-tűzfalszabályok Azure CLI használatával
Kiszolgálószintű tűzfalszabályok engedélyezése a rendszergazdák számára, hogy hozzáférésének kezelése az Azure Database for PostgreSQL-kiszolgáló egy adott IP-cím vagy IP-címtartományt. Kényelmes megoldás az Azure parancssori felület parancsait használva, létrehozhat, frissítése, törlése, a listában, és kezelheti a kiszolgálót a tűzfalszabályok megjelenítése. Azure Database for PostgreSQL-tűzfalszabályok áttekintése, lásd: [, Azure Database for PostgreSQL-kiszolgáló tűzfalszabályait](concepts-firewall-rules.md)

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- Telepítés [Azure CLI-vel](/cli/azure/install-azure-cli) parancssori segédprogram, vagy használja az Azure Cloud Shellt a böngészőben.
- Egy [, Azure Database for PostgreSQL-kiszolgáló és adatbázis](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Tűzfalszabályok konfigurálása az Azure Database for postgresql-hez
A [az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) parancsok tűzfalszabályok konfigurálásához használt.

## <a name="list-firewall-rules"></a>Tűzfal-szabályok listája 
A meglévő kiszolgáló tűzfalszabályait felsorolásához futtassa az [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_list) parancsot.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A kimenet a tűzfalszabályok sorolja fel, ha bármely, alapértelmezés szerint JSON formátumban. Előfordulhat, hogy használja a kapcsolót `--output table` a kimenetként, olvashatóbb táblázatos formában.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Tűzfalszabály létrehozása
Hozzon létre egy új tűzfalszabályt a kiszolgálón, futtassa a [az postgres server firewall-rule létrehozása](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) parancsot. 

Mint 0.0.0.0 megadásával a `--start-ip-address` és a 255.255.255.255, mint a `--end-ip-address` tartományt, az alábbi példa lehetővé teszi, hogy a kiszolgálón az összes IP-címek **mydemoserver.postgres.database.azure.com**
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
Ahhoz, hogy egyetlen IP-címnek a hozzáférést, adja meg ugyanazt a címet a `--start-ip-address` és `--end-ip-address`, amint az ebben a példában.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Ahhoz, hogy az alkalmazások az Azure IP-címekről az Azure Database for PostgreSQL-kiszolgálóhoz csatlakozni, adja meg az IP-cím 0.0.0.0 kezdő IP-és záró IP-cím, ebben a példában látható módon.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

Követően sikeres a parancs kimenete listázza a létrehozott, alapértelmezés szerint JSON formátumban tűzfalszabály részletei. Ha hiba történik, a kimenet egy hibaüzenetet jelenít meg helyette.

## <a name="update-firewall-rule"></a>Tűzfalszabályok frissítése 
Az a kiszolgálóra történő meglévő tűzfalszabály módosítása [az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_update) parancsot. Adja meg a nevét a meglévő tűzfalszabály kimenetként, és a kezdő IP-cím és a záró IP attribútumok frissítése.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.255
```
Követően sikeres a parancs kimenete listázza a frissítése után alapértelmezés szerint JSON formátumban a tűzfalszabály részletei. Ha hiba történik, a kimenet egy hibaüzenetet jelenít meg helyette.
> [!NOTE]
> Ha nem létezik a tűzfalszabályt, a frissítés paranccsal létrejön.

## <a name="show-firewall-rule-details"></a>Tűzfal szabály részleteinek megjelenítéséhez.
Meglévő kiszolgálószintű tűzfalszabály részleteit jeleníti meg a futó [az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_show) parancsot.
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Követően sikeres a parancs kimenete listázza a megadott, alapértelmezés szerint JSON formátumban tűzfalszabály részletei. Ha hiba történik, a kimenet egy hibaüzenetet jelenít meg helyette.

## <a name="delete-firewall-rule"></a>Tűzfalszabály törlése
Visszavonni a hozzáférést egy IP-címtartományt a kiszolgálóhoz, meglévő tűzfalszabály törlése úgy, hogy végrehajtja a [az postgres server firewall-rule törlése](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_delete) parancsot. Adja meg a meglévő tűzfalszabály nevét.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Sikeres, Befejezés esetén nem semmilyen kimenet. Hiba esetén a hibaüzenet szövege adja vissza.

## <a name="next-steps"></a>További lépések
- Ehhez hasonlóan használhatja egy webböngészőben [hozzon létre és kezelhető az Azure Database for PostgreSQL tűzfalszabályok az Azure portal használatával](howto-manage-firewall-using-portal.md).
- Ismerje meg jobban az kapcsolatos [, Azure Database for PostgreSQL-kiszolgáló tűzfalszabályainak](concepts-firewall-rules.md).
- Segítségre van szüksége az Azure Database for PostgreSQL-kiszolgálóhoz csatlakozik, lásd: [adatkapcsolattárak az Azure Database for PostgreSQL](concepts-connection-libraries.md).

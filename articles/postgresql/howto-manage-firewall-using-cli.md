---
title: Tűzfalszabályok kezelése - Azure CLI - Azure Database for PostgreSQL - Single Server
description: Ez a cikk bemutatja, hogyan hozhat létre és kezelhet tűzfalszabályokat az Azure Database for PostgreSQL – Single Server rendszerben az Azure CLI parancssorával.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4af0fb288961689fb051bab8091c838f793cfcc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74765647"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>Tűzfalszabályok létrehozása és kezelése az Azure Database for PostgreSQL-ben – Egyetlen kiszolgáló az Azure CLI használatával
A kiszolgálószintű tűzfalszabályok segítségével kezelheti a PostgreSQL Server Azure-adatbázisához való hozzáférést egy adott IP-címről vagy IP-címtartományból. Kényelmes Azure CLI-parancsokkal létrehozhat, frissíthet, törölhet, listázhat és megjeleníthet tűzfalszabályokat a kiszolgáló kezeléséhez. Az Azure Database for PostgreSQL tűzfalszabályok áttekintését az [Azure Database for PostgreSQL Server tűzfalszabályai című témakörben találja.](concepts-firewall-rules.md)

Virtuális hálózati (VNet) szabályok is használható a kiszolgálóhoz való hozzáférés biztonságossá tétele. További információ a [virtuális hálózati szolgáltatás végpontjainak és szabályainak létrehozásáról és kezeléséről az Azure CLI használatával.](howto-manage-vnet-using-cli.md)

## <a name="prerequisites"></a>Előfeltételek
Az útmutató útmutatón való átlépéshez a következőkre van szükség:
- Telepítse [az Azure CLI](/cli/azure/install-azure-cli) parancssori segédprogramot, vagy használja az Azure Cloud Shellt a böngészőben.
- [Egy Azure-adatbázis a PostgreSQL kiszolgálóhoz és adatbázishoz.](quickstart-create-server-database-azure-cli.md)

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Tűzfalszabályok konfigurálása a PostgreSQL Azure Database szolgáltatásához
Az [az postgres kiszolgáló tűzfal-szabály](/cli/azure/postgres/server/firewall-rule) parancsai a tűzfalszabályok konfigurálására szolgálnak.

## <a name="list-firewall-rules"></a>Tűzfalszabályok listázása 
A meglévő kiszolgálói tűzfalszabályok listázásához futtassa az [az postgres kiszolgáló tűzfalszabálylistájának](/cli/azure/postgres/server/firewall-rule) parancsát.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
A kimenet iszon-formátumban a tűzfalszabályokat sorolja fel, ha vannak ilyenek. A kapcsolót `--output table` olvashatóbb táblázatformátumhoz használhatja kimenetként.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Tűzfalszabály létrehozása
Ha új tűzfalszabályt szeretne létrehozni a kiszolgálón, futtassa az [az postgres kiszolgáló tűzfal-szabály létrehozási](/cli/azure/postgres/server/firewall-rule) parancsát. 

```
To allow access to a singular IP address, provide the same address in the `--start-ip-address` and `--end-ip-address`, as in this example, replacing the IP shown here with your specific IP.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Ha engedélyezni szeretné, hogy az Azure IP-címekből származó alkalmazások csatlakozzanak az Azure Database for PostgreSQL-kiszolgálóhoz, adja meg a 0.0.0.0 IP-címet a kezdő IP és a záró IP-címként, mint ebben a példában.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

Sikeres en a parancs kimenete alapértelmezés szerint JSON formátumban sorolja fel a létrehozott tűzfalszabály részleteit. Hiba esetén a kimenet egy hibaüzenetet jelenít meg.

## <a name="update-firewall-rule"></a>Tűzfalszabály frissítése 
Frissítsen egy meglévő tűzfalszabályt a kiszolgálón az [az postgres kiszolgáló tűzfalszabály-frissítési](/cli/azure/postgres/server/firewall-rule) parancsával. Adja meg a meglévő tűzfalszabály nevét bemenetként, valamint a frissítendő kezdő IP- és záróIP-attribútumokat.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
Sikeres en a parancs kimenete felsorolja a frissített tűzfalszabály részleteit, alapértelmezés szerint JSON formátumban. Hiba esetén a kimenet egy hibaüzenetet jelenít meg.
> [!NOTE]
> Ha a tűzfalszabály nem létezik, a frissítési parancs létrehozza.

## <a name="show-firewall-rule-details"></a>Tűzfalszabály részleteinek megjelenítése
Egy meglévő kiszolgálószintű tűzfalszabály részleteit az az [postgres kiszolgáló tűzfalszabály-show](/cli/azure/postgres/server/firewall-rule) parancs futtatásával is megjelenítheti.
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Sikeres esetben a parancs kimenete felsorolja a megadott tűzfalszabály részleteit, alapértelmezés szerint JSON formátumban. Hiba esetén a kimenet egy hibaüzenetet jelenít meg.

## <a name="delete-firewall-rule"></a>Tűzfalszabály törlése
Ha vissza szeretne vonni egy IP-tartományhoz való hozzáférést a kiszolgálóhoz, töröljön egy meglévő tűzfalszabályt az [az postgres kiszolgáló tűzfalszabályának törlése](/cli/azure/postgres/server/firewall-rule) parancs végrehajtásával. Adja meg a meglévő tűzfalszabály nevét.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
A siker után nincs kimenet. Hiba esetén a hibaüzenet szövege jelenik meg.

## <a name="next-steps"></a>További lépések
- Hasonlóképpen webböngészővel [hozhat létre és kezelhet Azure Database for PostgreSQL tűzfalszabályokat az Azure Portal használatával.](howto-manage-firewall-using-portal.md)
- További információk [az Azure Database for PostgreSQL Server tűzfalszabályairól.](concepts-firewall-rules.md)
- További biztonságos hozzáférés a kiszolgálóhoz [virtuális hálózati szolgáltatás végpontjainak és szabályainak létrehozásával és kezelésével az Azure CLI használatával.](howto-manage-vnet-using-cli.md)
- Ha segítségre van szüksége a PostgreSQL-adatbázishoz való csatlakozáshoz, olvassa el [a Kapcsolattárak az Azure Database for PostgreSQL kapcsolattárak](concepts-connection-libraries.md)című témakört.

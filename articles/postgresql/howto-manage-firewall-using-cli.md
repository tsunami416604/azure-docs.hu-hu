---
title: "Hozzon létre és kezelheti az Azure-adatbázis PostgreSQL-tűzfalszabályok Azure parancssori felület használatával |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan létrehozásához és kezeléséhez Azure Database az Azure CLI parancssorból PostgreSQL tűzfalszabályokat."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.openlocfilehash: 05e645ebafd8c8b9f3078524b76732b3e1be05f8
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Hozzon létre és kezelheti az Azure-adatbázis PostgreSQL-tűzfalszabályok Azure parancssori felület használatával
Kiszolgálószintű tűzfal-szabályok lehetővé teszik a rendszergazdák hozzáférésének kezelése az Azure-adatbázis PostgreSQL-kiszolgáló egy adott IP-cím vagy az IP-címek. Tetszés szerinti Azure parancssori felület parancsait használva hozhat létre, frissítése, törlése, a listában, és kezelheti a kiszolgálót a tűzfalszabályok megjelenítése. Az áttekintést az Azure-adatbázis PostgreSQL tűzfalszabályaira vonatkozó, lásd: [PostgreSQL-kiszolgáló tűzfalszabályainak az Azure-adatbázis](concepts-firewall-rules.md)

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató Útmutató lépéseit, az alábbiak szükségesek:
- Egy [PostgreSQL-kiszolgáló és adatbázis Azure-adatbázis](quickstart-create-server-database-azure-cli.md).
- Telepítés [Azure CLI 2.0](/cli/azure/install-azure-cli) sor segédprogram parancsot, vagy használja az Azure-felhő rendszerhéj a böngészőben.

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Tűzfalszabályok konfigurálása az Azure Database PostgreSQL
A [az postgres-tűzfalszabályt](/cli/azure/postgres/server/firewall-rule) -parancsok segítségével tűzfalszabályok konfigurálása.

## <a name="list-firewall-rules"></a>Tűzfalszabályok listája 
A meglévő kiszolgáló tűzfalszabályainak felsorolásához futtassa az [az postgres tűzfalszabály kiszolgálólista](/cli/azure/postgres/server/firewall-rule#list) parancsot.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
A kimeneti tűzfal szabályokat sorolja fel, ha vannak ilyenek, alapértelmezés szerint a JSON formátumban. A kapcsoló segítségével `--output table` a tábla kimeneteként olvashatóbb formátumban.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>Tűzfalszabály létrehozása
Új szabály létrehozása a kiszolgálón, futtassa a [az postgres-tűzfalszabály létrehozása](/cli/azure/postgres/server/firewall-rule#create) parancsot. 

Mint 0.0.0.0 megadásával a `--start-ip-address` és 255.255.255.255, mint a `--end-ip-address` tartomány, az alábbi példában lehetővé teszi, hogy az összes IP-címek a kiszolgálóhoz való hozzáféréshez **mypgserver-20170401.postgres.database.azure.com**
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
Engedélyezi a hozzáférést az egyes IP-címnek, adja meg ugyanazt a címet a `--start-ip-address` és `--end-ip-address`, a példában szereplő.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "AllowSingleIpAddress" --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Sikeres, akkor a parancs kimenete létrehozott, alapértelmezés szerint a JSON formátum tűzfalszabály részleteit sorolja fel. Hiba történik, ha a kimeneti mezőben hibaüzenet jelenik meg helyette.

## <a name="update-firewall-rule"></a>Tűzfalszabály módosítása 
A kiszolgáló használja a meglévő tűzfalszabály módosítása [az postgres server tűzfalszabály frissítés](/cli/azure/postgres/server/firewall-rule#update) parancsot. Adja meg a nevét a meglévő tűzfalszabály bemeneti és a kezdő IP-cím és a záró IP attribútumok frissítése.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.255
```
Sikeres, akkor a parancs kimenete frissítette, alapértelmezés szerint a JSON formátum tűzfalszabály részleteit sorolja fel. Hiba történik, ha a kimeneti mezőben hibaüzenet jelenik meg helyette.
> [!NOTE]
> Ha a tűzfalszabály nem létezik, az a frissítés parancs végrehajtásakor létrejön.

## <a name="show-firewall-rule-details"></a>Tűzfal szabály részleteinek megjelenítése
Akkor is megjelenhet a meglévő kiszolgálószintű tűzfalszabály részletei futtatásával [az postgres server tűzfalszabály megjelenítése](/cli/azure/postgres/server/firewall-rule#show) parancsot.
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
Sikeres, akkor a parancs kimenetét a megadott, alapértelmezés szerint a JSON formátum tűzfalszabály részleteit sorolja fel. Hiba történik, ha a kimeneti mezőben hibaüzenet jelenik meg helyette.

## <a name="delete-firewall-rule"></a>Tűzfalszabály törlése
A visszavonni a hozzáférést egy IP-címtartomány a kiszolgálóra, törölje a meglévő tűzfalszabály hajtja végre a [az postgres-tűzfalszabály törlése](/cli/azure/postgres/server/firewall-rule#delete) parancsot. Adja meg a meglévő tűzfalszabály nevét.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "AllowIpRange"
```
Sikeres, akkor nincs nincs kimenete. Hiba esetén a hibaüzenet-szöveg adja vissza.

## <a name="next-steps"></a>Következő lépések
- Hasonlóképpen, használhatja a webböngészőt a [hozzon létre és kezelheti az Azure-adatbázis az Azure portál használatával PostgreSQL tűzfalszabályok](howto-manage-firewall-using-portal.md).
- Több megismerkedett [PostgreSQL-kiszolgáló tűzfalszabályainak az Azure-adatbázis](concepts-firewall-rules.md).
- Egy PostgreSQL-kiszolgáló Azure-adatbázishoz szeretne csatlakozni a témakörben talál segítséget [PostgreSQL az Azure-adatbázis adatkapcsolattárak](concepts-connection-libraries.md).

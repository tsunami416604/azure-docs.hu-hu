---
title: Hozzon létre és kezelhető az Azure Database for MariaDB virtuális hálózati Szolgáltatásvégpontok és szabályok az Azure parancssori felületével |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhető az Azure Database for MariaDB virtuális hálózati Szolgáltatásvégpontok és szabályok használatával az Azure CLI parancssori.
services: mariaDB
author: ajlam
ms.author: andrela
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: article
ms.date: 11/20/2018
ms.openlocfilehash: 7f39eb2b3020016d1fb14e8c611ae00dfab07c11
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336826"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Hozzon létre és kezelhető az Azure Database for MariaDB virtuális hálózati Szolgáltatásvégpontok az Azure CLI használatával

> [!IMPORTANT]
> A virtuális hálózatok szolgáltatásvégponti funkciója jelenleg nyilvános előzetes verzióban.

Virtuális hálózat (VNet) szolgáltatások végpontok és a szabályok egy virtuális hálózat magáncímterét kiterjesztheti az Azure Database for MariaDB-kiszolgáló. Kényelmes megoldás az Azure parancssori felület (CLI) parancs használatával, is létrehozása, frissítése, törlése, listázása és megjelenítése a virtuális hálózati Szolgáltatásvégpontok és szabályok kezelheti a kiszolgálót. Azure database for MariaDB virtuális hálózati Szolgáltatásvégpontok, korlátai, beleértve lásd [, Azure Database for MariaDB-kiszolgáló virtuális hálózati Szolgáltatásvégpontok](concepts-data-access-security-vnet.md). Virtuális hálózati Szolgáltatásvégpontok az Azure Database for MariaDB minden támogatott régióban érhetők el.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- Telepítés [az Azure CLI](/cli/azure/install-azure-cli) vagy az Azure Cloud Shellt a böngészőben.
- Egy [, Azure Database for MariaDB-kiszolgáló és az adatbázis](quickstart-create-mariadb-server-database-using-azure-cli.md).

> [!NOTE]
> Virtuális hálózati Szolgáltatásvégpontok támogatása csak az általános célú és memóriahasználatra optimalizált kiszolgálók esetében érhető el.

## <a name="configure-vnet-service-endpoints"></a>Virtuális hálózati Szolgáltatásvégpontok konfigurálása
A [az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) parancsok segítségével konfigurálhatja a virtuális hálózatok.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

A CLI helyi futtatása esetén az [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) paranccsal be kell jelentkeznie a fiókjába. Jegyezze fel a megfelelő előfizetésnév parancskimenetéből az **id** tulajdonságot.
```azurecli-interactive
az login
```

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) paranccsal. Az előfizetés **az login** kimenetének **id** tulajdonságát illessze be az előfizetés-azonosító helyőrzője helyére.

- A fióknak rendelkeznie kell egy virtuális hálózat és egy szolgáltatásvégpont létrehozásához szükséges engedélyekkel.

A Szolgáltatásvégpontok a virtuális hálózatokon külön konfigurálhatók, a virtuális hálózathoz írási jogosultsággal rendelkező felhasználó által.

Biztonságos Azure-szolgáltatási erőforrások virtuális hálózathoz, a felhasználó hozzáadott alhálózatokra vonatkozó "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" engedéllyel kell rendelkeznie. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.

További információk a [beépített szerepkörökről](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) és a bizonyos engedélyek [egyéni szerepkörökhöz](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) való hozzárendeléséről.

A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha a virtuális hálózat és az Azure-szolgáltatási erőforrások különböző előfizetésekhez tartoznak, az erőforrások ugyanahhoz az Active Directory (AD) bérlőhöz kell tartozniuk.

> [!IMPORTANT]
> Ez a cikk szolgáltatáskonfiguráció endpoint és megfontolandó szempontok a Szolgáltatásvégpontok konfigurálása előtt erősen ajánlott. **Virtuális hálózati szolgáltatásvégpont:** A [virtuális hálózati szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) egy alhálózat, amelynek a következők: egy vagy több hivatalos Azure-szolgáltatás nevét. Virtuális hálózat services végpontjainak használja a szolgáltatás neve **Microsoft.Sql**, amely hivatkozik az Azure-szolgáltatás SQL-adatbázis neve. Ez a szolgáltatás címke az Azure SQL Database, Azure Database for MariaDB, PostgreSQL és MySQL-szolgáltatásokra is vonatkozik. Fontos, hogy alkalmazása esetén vegye figyelembe a **Microsoft.Sql** szolgáltatáscímke egy szolgáltatásvégpont, konfigurálja a szolgáltatás végpontja forgalmat az összes Azure-adatbázis szolgáltatás, így az Azure SQL Database, Azure Database for PostgreSQL, Azure Database for MariaDB, és az Azure Database for MySQL-kiszolgálók az alhálózaton.

### <a name="sample-script"></a>Példaszkript

Ez a példaszkript egy Azure Database for MariaDB-kiszolgáló létrehozása, hozzon létre egy Vnetet, virtuális hálózati szolgáltatásvégpont és az alhálózathoz, virtuális hálózatok közötti szabállyal a kiszolgáló védelmét szolgál. Ez a példaszkript módosítsa a rendszergazdai felhasználónevét és jelszavát. Cserélje le a használt a `az account set --subscription` parancsot a saját előfizetés-azonosító.

```azurecli-interactive
# To find the name of an Azure region in the CLI run this command: az account list-locations
# Substitute  <subscription id> with your identifier
az account set --subscription <subscription id>

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a MariaDB server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mariadb server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2

# Get available service endpoints for Azure region output is JSON
# Use the command below to get the list of services supported for endpoints, for an Azure region, say "westus".
az network vnet list-endpoint-services \
-l westus

# Add Azure SQL service endpoint to a subnet *mySubnet* while creating the virtual network *myVNet* output is JSON
az network vnet create \
-g myresourcegroup \
-n myVNet \
--address-prefixes 10.0.0.0/16 \
-l westus

# Creates the service endpoint
az network vnet subnet create \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet \
--address-prefix 10.0.1.0/24 \
--service-endpoints Microsoft.SQL

# View service endpoints configured on a subnet
az network vnet subnet show \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet

# Create a VNet rule on the sever to secure it to the subnet Note: resource group (-g) parameter is where the database exists. VNet resource group if different should be specified using subnet id (URI) instead of subnet, VNet pair.
az mariadb server vnet-rule create \
-n myRule \
-g myresourcegroup \
-s mydemoserver \
--vnet-name myVNet \
--subnet mySubnet
```

<!-- 
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the `az account set --subscription` command with your own subscription identifier.
[!code-azurecli-interactive[main](../../cli_scripts/mariadb/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MariaDB, VNet, VNet service endpoint, and VNet rule.")]
-->

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name myresourcegroup
```


<!--
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
-->
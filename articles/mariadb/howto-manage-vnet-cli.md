---
title: Virtuálishálózati végpontok kezelése - Azure CLI – Azure Database for MariaDB
description: Ez a cikk bemutatja, hogyan hozhat létre és kezelhet Azure Database for MariaDB VNet szolgáltatás végpontok és szabályok az Azure CLI parancssorhasználatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 46bfab6935d08ac28ced7f392892ade6f68a0492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530852"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Azure-adatbázis létrehozása és kezelése a MariaDB virtuálishálózat szolgáltatásvégpontjaihoz az Azure CLI használatával

A virtuális hálózat (VNet) szolgáltatásvégpontjai és szabályai kiterjesztik a virtuális hálózat magánhálózati címtartományát az Azure Database for MariaDB-kiszolgálóra. Kényelmes Azure parancssori felület (CLI) parancsokkal virtuális hálózat végpontjait és szabályait hozhatja létre, frissítheti, törölheti, listázhatja és jelenítheti meg a kiszolgáló kezeléséhez szükséges virtuális hálózat végpontjait és szabályait. Az Azure Database for MariaDB VNet szolgáltatásvégpontok áttekintését, beleértve a korlátozásokat, az [Azure Database for MariaDB Server VNet szolgáltatásvégpontjai című témakörben olvashat.](concepts-data-access-security-vnet.md) A VNet-szolgáltatás végpontjai a MariaDB-hez való Azure Database összes támogatott régiójában elérhetők.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató útmutatón való átlépéshez a következőkre van szükség:
- Telepítse [az Azure CLI-t,](/cli/azure/install-azure-cli) vagy használja az Azure Cloud Shellt a böngészőben.
- [A MariaDB-kiszolgáló és -adatbázis Azure-adatbázisa.](quickstart-create-mariadb-server-database-using-azure-cli.md)

> [!NOTE]
> A Virtuálishálózat-szolgáltatás végpontjainak támogatása csak általános célú és memóriaoptimalizált kiszolgálókhoz érhető el.

## <a name="configure-vnet-service-endpoints"></a>VNet-szolgáltatás végpontjainak konfigurálása
A [hálózati virtuális hálózatok](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) konfigurálására a virtuális hálózatok konfigurálására szolgálnak.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

A CLI helyi futtatása esetén az [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) paranccsal be kell jelentkeznie a fiókjába. Jegyezze fel a megfelelő előfizetésnév parancskimenetéből az **id** tulajdonságot.
```azurecli-interactive
az login
```

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-set) paranccsal. Az előfizetés **az login** kimenetének **id** tulajdonságát illessze be az előfizetés-azonosító helyőrzője helyére.

- A fióknak rendelkeznie kell a megfelelő engedélyekkel egy virtuális hálózat és egy szolgáltatásvégpont létrehozásához.

A szolgáltatásvégpontokat a virtuális hálózatokon egymástól függetlenül konfigurálhatja a virtuális hálózathoz írási hozzáféréssel rendelkező felhasználó.

Az Azure-szolgáltatás erőforrásainak virtuális hálózathoz való biztonságossá tétele érdekében a felhasználónak engedéllyel kell rendelkeznie a "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" használatára a hozzáadott alhálózatok hoz. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.

További információk a [beépített szerepkörökről](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) és a bizonyos engedélyek [egyéni szerepkörökhöz](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) való hozzárendeléséről.

A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha a virtuális hálózat és az Azure-szolgáltatás erőforrásai különböző előfizetésekben vannak, az erőforrásoknak ugyanahhoz az Active Directory (AD) bérlőhöz kell tartozniuk. Győződjön meg arról, hogy mindkét előfizetés rendelkezik a **Microsoft.Sql** erőforrás-szolgáltató regisztrálva. További információkért lásd [az erőforrás-kezelő-regisztrációt][resource-manager-portal]

> [!IMPORTANT]
> A szolgáltatásvégpontok konfigurálása előtt ajánlott elolvasni ezt a cikket a szolgáltatásvégpontok konfigurációjáról és szempontokról. **Virtuális hálózat szolgáltatás végpontja:** A [virtuális hálózati szolgáltatás végpontja](../virtual-network/virtual-network-service-endpoints-overview.md) egy alhálózat, amelynek tulajdonságértékei egy vagy több hivatalos Azure-szolgáltatástípus-neveket tartalmaznak. A VNet-szolgáltatások végpontjai a **Microsoft.Sql**szolgáltatástípust használják, amely az SQL Database nevű Azure-szolgáltatásra hivatkozik. Ez a szolgáltatáscímke az Azure SQL Database, az Azure Database for MariaDB, a PostgreSQL és a MySQL szolgáltatásokra is vonatkozik. Fontos megjegyezni, ha a **Microsoft.Sql** szolgáltatáscímke egy virtuális hálózat szolgáltatás végpontja konfigurálja a szolgáltatás végpontforgalmát az összes Azure Database-szolgáltatások, beleértve az Azure SQL-adatbázis, az Azure Database for PostgreSQL, az Azure Database for MariaDB és az Azure Database for MySQL-kiszolgálók az alhálózaton.

### <a name="sample-script"></a>Példaszkript

Ez a mintaparancsfájl a MariaDB-kiszolgáló Azure-adatbázisának létrehozására, virtuális hálózat szolgáltatásvégpont létrehozására, valamint a kiszolgáló virtuálishálózati szabálysal való védelmére szolgál. Ebben a mintaparancsfájlban módosítsa a rendszergazdai felhasználónevet és a jelszót. Cserélje le a `az account set --subscription` parancsban használt SubscriptionID azonosítót a saját előfizetési azonosítójára.

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

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
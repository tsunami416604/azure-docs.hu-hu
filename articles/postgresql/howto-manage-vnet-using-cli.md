---
title: Hozzon létre és kezelhető az Azure Database for PostgreSQL virtuális hálózati Szolgáltatásvégpontok és szabályok az Azure parancssori felületével |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhető az Azure Database for PostgreSQL virtuális hálózati Szolgáltatásvégpontok és szabályok az Azure CLI-parancssor használatával.
services: postgresql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: a0dfd40785e8a3ef9f8c29bacdb08f93f7867197
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955423"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-using-azure-cli"></a>Hozzon létre és kezelhető az Azure Database for PostgreSQL virtuális hálózati Szolgáltatásvégpontok az Azure CLI használatával
Virtuális hálózat (VNet) szolgáltatások végpontok és a szabályok egy virtuális hálózat magáncímterét kiterjesztheti az Azure Database for PostgreSQL-kiszolgáló. Kényelmes megoldás az Azure parancssori felület (CLI) parancs használatával, is létrehozása, frissítése, törlése, listázása és megjelenítése a virtuális hálózati Szolgáltatásvégpontok és szabályok kezelheti a kiszolgálót. Azure database for PostgreSQL virtuális hálózati Szolgáltatásvégpontok, többek között a korlátozásokat, áttekintést lásd: [, Azure Database for PostgreSQL-kiszolgáló virtuális hálózati Szolgáltatásvégpontok](concepts-data-access-and-security-vnet.md). Virtuális hálózati Szolgáltatásvégpontok az Azure Database for PostgreSQL minden támogatott régióban érhetők el.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- Telepítés [az Azure CLI](/cli/azure/install-azure-cli) vagy az Azure Cloud Shellt a böngészőben.
- Egy [, Azure Database for PostgreSQL-kiszolgáló és adatbázis](quickstart-create-server-database-azure-cli.md).

> [!NOTE]
> Virtuális hálózati Szolgáltatásvégpontok támogatása csak az általános célú és memóriahasználatra optimalizált kiszolgálók esetében érhető el.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Virtuális hálózati Szolgáltatásvégpontok konfigurálása az Azure Database for postgresql-hez
A [az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) parancsok segítségével konfigurálhatja a virtuális hálózatok.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). 

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
> Erősen javasoljuk, hogy ez a cikk szolgáltatáskonfiguráció endpoint és megfontolandó szempontok az alábbi, a minta parancsfájl futtatása előtt vagy a Szolgáltatásvégpontok konfigurálása. **Virtuális hálózati szolgáltatásvégpont:** A [virtuális hálózati szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) egy alhálózat, amelynek a következők: egy vagy több hivatalos Azure-szolgáltatás nevét. Virtuális hálózat services végpontjainak használja a szolgáltatás neve **Microsoft.Sql**, amely hivatkozik az Azure-szolgáltatás SQL-adatbázis neve. Ez a szolgáltatás címke az Azure SQL Database, Azure Database for PostgreSQL és MySQL-szolgáltatásokra is vonatkozik. Fontos, hogy alkalmazása esetén vegye figyelembe a **Microsoft.Sql** szolgáltatáscímke egy szolgáltatásvégpont, konfigurálja a szolgáltatás végpontja forgalmat az összes Azure-adatbázis szolgáltatás, így az Azure SQL Database, Azure Database for PostgreSQL és Azure Database for MySQL-kiszolgálók az alhálózaton. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Mintaparancsfájl hozzon létre egy Azure Database for PostgreSQL-adatbázis, egy virtuális hálózati szolgáltatásvégpont létrehozása és az alhálózathoz, virtuális hálózatok közötti szabállyal a kiszolgáló védelmét
A példaszkriptben módosítsa a kiemelt sorokat, és adja meg azokban a rendszergazdai felhasználónevét és jelszavát. Cserélje le a használt a `az account set --subscription` parancsot a saját előfizetés-azonosító.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

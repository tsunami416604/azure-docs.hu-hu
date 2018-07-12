---
title: Hozzon létre és kezelhető az Azure Database for MySQL virtuális hálózati Szolgáltatásvégpontok és szabályok az Azure parancssori felületével |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhető az Azure Database for MySQL virtuális hálózati Szolgáltatásvégpontok és szabályok az Azure CLI-parancssor használatával.
services: mysql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: fd8b21d1273b1bd02b0a949894be53cdc4a5c3c0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38597880"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Hozzon létre és kezelhető az Azure Database for MySQL virtuális hálózati Szolgáltatásvégpontok az Azure CLI használatával
Virtuális hálózat (VNet) szolgáltatások végpontok és a szabályok egy virtuális hálózat magáncímterét kiterjesztheti az Azure Database for MySQL-kiszolgáló. Kényelmes megoldás az Azure parancssori felület (CLI) parancs használatával, is létrehozása, frissítése, törlése, listázása és megjelenítése a virtuális hálózati Szolgáltatásvégpontok és szabályok kezelheti a kiszolgálót. Azure Database MySQL virtuális hálózati Szolgáltatásvégpontok, korlátai, beleértve az áttekintést lásd: [, Azure Database for MySQL-kiszolgáló virtuális hálózati Szolgáltatásvégpontok](concepts-data-access-and-security-vnet.md). Virtuális hálózati Szolgáltatásvégpontok nyilvános előzetes verzióban az összes támogatott régióban az Azure Database for MySQL-hez érhetők el.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató lépéseinek, az alábbiak szükségesek:
- Telepítés [Azure CLI 2.0](/cli/azure/install-azure-cli) parancssori segédprogram, vagy használja az Azure Cloud Shellt a böngészőben.
- Egy [, Azure Database for MySQL-kiszolgáló és adatbázis](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!NOTE]
> Virtuális hálózati Szolgáltatásvégpontok támogatása csak az általános célú és memóriahasználatra optimalizált kiszolgálók esetében érhető el.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Virtuális hálózati Szolgáltatásvégpontok konfigurálása az Azure Database for MySQL-hez
A [az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) parancsok segítségével konfigurálhatja a virtuális hálózatok.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

A CLI helyi futtatása esetén az [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) paranccsal be kell jelentkeznie a fiókjába. Jegyezze fel a megfelelő előfizetésnév parancskimenetéből az **id** tulajdonságot.
```azurecli-interactive
az login
```

A CLI-bővítmény telepítése az Azure Database for MySQL virtuális hálózati Szolgáltatásvégpontok használatával a `az extension add --name rdbms-vnet` parancsot. 
```azurecli-interactive
az extension add --name rdbms-vnet
```

Futtassa a `az extension list` parancsot a CLI-bővítmény telepítésének ellenőrzése.
```azurecli-interactive
az extension list
```
A parancs kimenete listázza az összes telepített bővítmények. Az Azure Database for MySQL CLI-bővítmény a következő:

 {"extensionType": "whl", "name": "rdbms-vnet", "verziójú": "10.0.0"}

> [!NOTE]
> Futtassa a CLI-bővítmény eltávolítása a `az extension remove -n rdbms-vnet` parancsot. 

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#az_account_set) paranccsal. Az előfizetés **az login** kimenetének **id** tulajdonságát illessze be az előfizetés-azonosító helyőrzője helyére.

- A fióknak rendelkeznie kell egy virtuális hálózat és egy szolgáltatásvégpont létrehozásához szükséges engedélyekkel.

A Szolgáltatásvégpontok a virtuális hálózatokon külön konfigurálhatók, a virtuális hálózathoz írási jogosultsággal rendelkező felhasználó által.

Ahhoz, hogy egy felhasználó Azure-szolgáltatási erőforrásokat rendelhessen egy virtuális hálózathoz, rendelkeznie kell a hozzáadott alhálózatokra vonatkozó „Microsoft.Network/JoinServicetoaSubnet” engedéllyel. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.

További információk a [beépített szerepkörökről](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) és a bizonyos engedélyek [egyéni szerepkörökhöz](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) való hozzárendeléséről.

A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha a virtuális hálózat és az Azure-szolgáltatási erőforrások különböző előfizetésekhez tartoznak, az erőforrások alatt ugyanahhoz az Active Directory (AD) bérlőhöz kell lennie az előzetes verzió idején.

> [!IMPORTANT]
> Erősen javasoljuk, hogy ez a cikk szolgáltatáskonfiguráció endpoint és megfontolandó szempontok az alábbi, a minta parancsfájl futtatása előtt vagy a Szolgáltatásvégpontok konfigurálása. **Virtuális hálózati szolgáltatásvégpont:** A [virtuális hálózati szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) egy alhálózat, amelynek a következők: egy vagy több hivatalos Azure-szolgáltatás nevét. Virtuális hálózat services végpontjainak használja a szolgáltatás neve **Microsoft.Sql**, amely hivatkozik az Azure-szolgáltatás SQL-adatbázis neve. Ez a szolgáltatás címke az Azure SQL Database, Azure Database for PostgreSQL és MySQL-szolgáltatásokra is vonatkozik. Fontos, hogy alkalmazása esetén vegye figyelembe a **Microsoft.Sql** szolgáltatáscímke egy szolgáltatásvégpont, konfigurálja a szolgáltatás végpontja forgalmat az összes Azure-adatbázis szolgáltatás, így az Azure SQL Database, Azure Database for PostgreSQL és Azure Database for MySQL-kiszolgálók az alhálózaton. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Mintaparancsfájl hozzon létre egy Azure Database for MySQL-adatbázis, egy virtuális hálózati szolgáltatásvégpont létrehozása és az alhálózathoz, virtuális hálózatok közötti szabállyal a kiszolgáló védelmét
A példaszkriptben módosítsa a kiemelt sorokat, és adja meg azokban a rendszergazdai felhasználónevét és jelszavát. Cserélje le a használt a `az account set --subscription` parancsot a saját előfizetés-azonosító.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

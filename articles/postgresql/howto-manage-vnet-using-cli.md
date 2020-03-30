---
title: Virtuális hálózati szabályok használata - Azure CLI - Azure Database for PostgreSQL - Single Server
description: Ez a cikk ismerteti, hogyan hozhat létre és kezelhet virtuálishálózat-szolgáltatásvégpontokat és szabályokat az Azure Database for PostgreSQL azure CLI parancssorával.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 5e80b48e43e72bc3eda16a5dcc64f980065c923f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974123"
---
# <a name="create-and-manage-vnet-service-endpoints-for-azure-database-for-postgresql---single-server-using-azure-cli"></a>Virtuálishálózati szolgáltatásvégpontok létrehozása és kezelése az Azure Database for PostgreSQL -Single Server for Azure CLI használatával
Virtuális hálózat (VNet) szolgáltatások végpontok és szabályok kiterjeszti a privát címtér egy virtuális hálózat az Azure Database for PostgreSQL-kiszolgáló. Kényelmes Azure parancssori felület (CLI) parancsokkal virtuális hálózat végpontjait és szabályait hozhatja létre, frissítheti, törölheti, listázhatja és jelenítheti meg a kiszolgáló kezeléséhez szükséges virtuális hálózat végpontjait és szabályait. Az Azure Database for PostgreSQL vNet szolgáltatásvégpontok áttekintését, beleértve a korlátozásokat, a [PostgreSQL Server virtuálishálózat-végpontokhoz készült Azure Database című témakörben olvashat.](concepts-data-access-and-security-vnet.md) VNet szolgáltatás végpontok érhetők el az összes támogatott régiókban az Azure Database for PostgreSQL.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató útmutatón való átlépéshez a következőkre van szükség:
- Telepítse [az Azure CLI-t,](/cli/azure/install-azure-cli) vagy használja az Azure Cloud Shellt a böngészőben.
- [Egy Azure-adatbázis a PostgreSQL kiszolgálóhoz és adatbázishoz.](quickstart-create-server-database-azure-cli.md)

> [!NOTE]
> A Virtuálishálózat-szolgáltatás végpontjainak támogatása csak általános célú és memóriaoptimalizált kiszolgálókhoz érhető el.
> Virtuális hálózati társviszony-létesítés esetén, ha a forgalom egy szolgáltatás-végpontokkal rendelkező közös virtuális hálózati átjárón keresztül áramlik, és a társnak kell áramlania, hozzon létre egy ACL/VNet szabályt, amely lehetővé teszi, hogy az Azure virtuális gépek az átjáró virtuális hálózatában hozzáférjenek az Azure Database for PostgreSQL-kiszolgálóhoz.


## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Vnet szolgáltatásvégpontok konfigurálása a PostgreSQL Azure Database szolgáltatásához
A [hálózati virtuális hálózatok](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) konfigurálására a virtuális hálózatok konfigurálására szolgálnak.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). 

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
> Erősen ajánlott elolvasni ezt a cikket a szolgáltatás végpontkonfigurációk és szempontok futtatása előtt az alábbi parancsfájl, vagy a szolgáltatás végpontok konfigurálása. **Virtuális hálózat szolgáltatás végpontja:** A [virtuális hálózati szolgáltatás végpontja](../virtual-network/virtual-network-service-endpoints-overview.md) egy alhálózat, amelynek tulajdonságértékei egy vagy több hivatalos Azure-szolgáltatástípus-neveket tartalmaznak. A VNet-szolgáltatások végpontjai a **Microsoft.Sql**szolgáltatástípust használják, amely az SQL Database nevű Azure-szolgáltatásra hivatkozik. Ez a szolgáltatáscímke az Azure SQL Database, az Azure Database for PostgreSQL és a MySQL-szolgáltatásokra is vonatkozik. Fontos megjegyezni, ha a **Microsoft.Sql** szolgáltatáscímke egy Virtuálishálózat-szolgáltatás végpontra alkalmazza a szolgáltatásvégpont-forgalmat az összes Azure Database-szolgáltatáshoz, beleértve az Azure SQL Database-t, az Azure Database for PostgreSQL-t és az Azure Database for MySQL-kiszolgálókat az alhálózaton. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Mintaparancsfájl a PostgreSQL-adatbázis Azure-adatbázisának létrehozásához, virtuális hálózat, virtuális hálózat szolgáltatásvégpont létrehozásához és a kiszolgáló alhálózathoz való védelméhez egy virtuális hálózat-szabállyal
A példaszkriptben módosítsa a kiemelt sorokat, és adja meg azokban a rendszergazdai felhasználónevét és jelszavát. Cserélje le a `az account set --subscription` parancsban használt SubscriptionID azonosítót a saját előfizetési azonosítójára.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
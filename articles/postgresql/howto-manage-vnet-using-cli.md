---
title: Létrehozása és kezelése az Azure-adatbázis PostgreSQL VNet Szolgáltatásvégpontok és szabályok az Azure parancssori felület használatával |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan létrehozása és kezelése az Azure-adatbázis PostgreSQL VNet Szolgáltatásvégpontok és az Azure parancssori felület parancssorból szabályok.
services: postgresql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 7312000d1f22af3eb0091b46caac2c9607231513
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736626"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-using-azure-cli"></a>Létrehozása és kezelése az Azure-adatbázis PostgreSQL VNet szolgáltatás végpontok Azure parancssori felület használatával
Virtuális hálózathoz (VNet) services végpontjainak és szabályok terjed ki a saját címtartomány a virtuális hálózat az Azure-adatbázis PostgreSQL-kiszolgáló. Tetszés szerinti Azure parancssori felület (CLI) parancsok, segítségével létrehozása, frissítése, törlése, listában, és virtuális hálózat Szolgáltatásvégpontok és kezelheti a kiszolgálót szabályok megjelenítése. Az Azure-adatbázis PostgreSQL VNet Szolgáltatásvégpontok korlátozások, beleértve az áttekintést lásd: [PostgreSQL Server VNet szolgáltatás végpontok Azure adatbázis](concepts-data-access-and-security-vnet.md). Virtuális hálózat Szolgáltatásvégpontok érhetők el minden támogatott régióban nyilvános előzetes PostgreSQL az Azure-adatbázis.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató Útmutató lépéseit, az alábbiak szükségesek:
- Telepítés [Azure CLI 2.0](/cli/azure/install-azure-cli) parancssori segédprogram, vagy használja az Azure-felhő rendszerhéj a böngészőben.
- Egy [PostgreSQL-kiszolgáló és adatbázis Azure-adatbázis](quickstart-create-server-database-azure-cli.md).

> [!NOTE]
> Virtuális hálózat Szolgáltatásvégpontok támogatása csak olyan általános célú és a Memóriaoptimalizált van.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Virtuális hálózat Szolgáltatásvégpontok PostgreSQL Azure-adatbázis konfigurálása
A [az hálózati vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) parancsok segítségével konfigurálhatja a virtuális hálózatok.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A telepített verziók megtekintéséhez futtassa az `az --version` parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

A CLI helyi futtatása esetén az [az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) paranccsal be kell jelentkeznie a fiókjába. Jegyezze fel a megfelelő előfizetésnév parancskimenetéből az **id** tulajdonságot.
```azurecli-interactive
az login
```
A CLI-kiterjesztés telepítése az Azure Database PostgreSQL VNet szolgáltatás végpontok használata a `az extension add --name rdbms-vnet` parancsot. 
```azurecli-interactive
az extension add --name rdbms-vnet
```

Futtassa a `az extension list` parancsot a CLI-bővítmény telepítésének ellenőrzése.
```azurecli-interactive
az extension list
```
A parancs felsorolja az összes telepített bővítmény. Az Azure PostgreSQL CLI-kiterjesztés adatbázisa:

 {"extensionType": "whl", "name": "rdbms-vnet", "version": "10.0.0"}

> [!NOTE]
> A Futtatás CLI-kiterjesztés eltávolítása a `az extension remove -n rdbms-vnet` parancsot. 

Ha több előfizetéssel rendelkezik válassza ki a megfelelő előfizetést, amelyre az erőforrást terhelni szeretné. Válassza ki a megadott előfizetés-azonosítót a fiókja alatt az [az account set](/cli/azure/account#az_account_set) paranccsal. Az előfizetés **az login** kimenetének **id** tulajdonságát illessze be az előfizetés-azonosító helyőrzője helyére.

- A fióknak rendelkeznie kell egy virtuális hálózat és a szolgáltatási végpont létrehozásához szükséges engedélyekkel.

Végpontok konfigurálható virtuális hálózatok egymástól függetlenül, a virtuális hálózati írási hozzáféréssel rendelkező felhasználó.

Ahhoz, hogy egy felhasználó Azure-szolgáltatási erőforrásokat rendelhessen egy virtuális hálózathoz, rendelkeznie kell a hozzáadott alhálózatokra vonatkozó „Microsoft.Network/JoinServicetoaSubnet” engedéllyel. Ez az engedély alapértelmezés szerint bele van foglalva a beépített szolgáltatás-rendszergazdai szerepkörökbe, és egyéni szerepkörök létrehozásával módosítható.

További információk a [beépített szerepkörökről](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) és a bizonyos engedélyek [egyéni szerepkörökhöz](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) való hozzárendeléséről.

A virtuális hálózatok és az Azure-szolgáltatási erőforrások tartozhatnak egyazon előfizetéshez, vagy különböző előfizetésekhez is. Ha a virtuális hálózat és az Azure szolgáltatás erőforrás különböző előfizetésekhez, az erőforrások kell az ugyanazon Active Directory (AD) adott bérlőn, ez az előzetes kiadás időpontjában.

> [!IMPORTANT]
> Kifejezetten ajánljuk, hogy az alábbi, a minta-parancsprogram futtatása előtt olvassa el a szolgáltatási végpont, valamint szempontok cikkben vagy Szolgáltatásvégpontok konfigurálása. **Virtuális hálózati szolgáltatási végpont:** A [virtuális hálózati szolgáltatási végpont](../virtual-network/virtual-network-service-endpoints-overview.md) egy alhálózat, amelyek a következők: egy vagy több hivatalos Azure-szolgáltatás neve. Virtuális hálózat services végpontjainak használja a szolgáltatás típusának neve **Microsoft.Sql**, amely hivatkozik az Azure-szolgáltatás SQL-adatbázis neve. A szolgáltatás címke az Azure SQL Database, PostgreSQL-és MySQL az Azure-adatbázis is vonatkozik. Fontos figyelembe venni, amikor alkalmazza a **Microsoft.Sql** címke szolgáltatás a virtuális hálózat szolgáltatásvégpont konfigurálja a szolgáltatási végpont forgalmat az összes Azure-adatbázis szolgáltatás, beleértve az Azure SQL Database, PostgreSQL az Azure-adatbázis és Az Azure-adatbázis a MySQL-kiszolgálók az alhálózaton. 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Mintaparancsfájl PostgreSQL-adatbázishoz tartozó Azure-adatbázis létrehozása, hozzon létre egy Vnetet, a virtuális hálózat szolgáltatási végpont és a VNet szabály alhálózathoz server védelmének biztosítása
A példaszkriptben módosítsa a kiemelt sorokat, és adja meg azokban a rendszergazdai felhasználónevét és jelszavát. Cserélje le az előfizetés-azonosító szerepel a `az account set --subscription` parancsot a saját előfizetés-azonosító.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása
A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]

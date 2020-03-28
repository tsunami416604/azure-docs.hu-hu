---
title: CLI példa - Feladatátvételi csoport – Az Azure SQL Database felügyelt példánya
description: Azure CLI példaparancsfájl egy Azure SQL Database felügyelt példány létrehozásához, adja hozzá egy feladatátvételi csoporthoz, és tesztelje a feladatátvételt.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 8ffe40662ffaf8a1fb35a3d31acfaea78ea0fbeb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061924"
---
# <a name="use-cli-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>A CLI használatával Azure SQL Database felügyelt példányt adhat hozzá egy feladatátvételi csoporthoz

Ez az Azure CLI-példában két felügyelt példányt hoz létre, hozzáadja őket egy feladatátvételi csoporthoz, majd teszteli a feladatátvételt az elsődleges felügyelt példányból a másodlagos felügyelt példányba.

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az erőforráscsoport és a hozzá tartozó összes erőforrás eltávolításához használja a következő parancsot. Az erőforráscsoportot kétszer kell eltávolítania. Ha először távolítja el az erőforráscsoportot, akkor eltávolítja a felügyelt `az group delete : Long running operation failed with status 'Conflict'.`példányt és a virtuális fürtöket, de a hibaüzenet sikertelen lesz. Futtassa az az csoport törlési parancsot még egyszer a fennmaradó erőforrások és az erőforráscsoport eltávolításához.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Mintahivatkozás

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| | |
|---|---|
| [az hálózati virtuális hálózat](/cli/azure/network/vnet) | Virtuális hálózati parancsok.  |
| [az hálózati virtuális hálózat alhálózata](/cli/azure/network/vnet/subnet) | Virtuális hálózati alhálózati parancsok. |
| [az hálózat nsg](/cli/azure/network/nsg) | Hálózati biztonsági csoport parancsai. |
| [az hálózati útvonaltábla](/cli/azure/network/route-table) | Irányított táblaparancsok. |
| [az sql mi](/cli/azure/sql/mi) | Felügyelt példányparancsok. |
| [az hálózat nyilvános-ip](/cli/azure/network/public-ip) | Hálózati nyilvános IP-címparancsok. |
| [az hálózati vnet-átjáró](/cli/azure/network/vnet-gateway) | Virtuális hálózati átjáró parancsok. |
| [az sql instance-feladatátvételi csoport](/cli/azure/sql/instance-failover-group) | Felügyelt példány feladatátvételi csoport parancsai. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../sql-database-cli-samples.md) találhat.

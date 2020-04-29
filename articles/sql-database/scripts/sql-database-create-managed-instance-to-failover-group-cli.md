---
title: CLI-példa – feladatátvételi csoport – Azure SQL Database felügyelt példány
description: Azure CLI-példa parancsfájl egy Azure SQL Database felügyelt példány létrehozásához, a feladatátvételi csoporthoz való hozzáadásához és a feladatátvételi teszthez.
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
ms.openlocfilehash: 792746ec3bfcf429afb7919458b9ac7ec8446b60
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80061842"
---
# <a name="use-cli-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>Azure SQL Database felügyelt példány hozzáadása feladatátvételi csoporthoz a CLI használatával

Ez az Azure CLI-példa két felügyelt példányt hoz létre, hozzáadja azokat egy feladatátvételi csoporthoz, majd teszteli a feladatátvételt az elsődleges felügyelt példányról a másodlagos felügyelt példányra.

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-scripts"></a>Mintaparancsfájlok

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást. Az erőforráscsoportot kétszer kell eltávolítania. Az erőforráscsoport eltávolításakor a rendszer először eltávolítja a felügyelt példányt és a virtuális fürtöket, de a hibaüzenettel meghiúsul `az group delete : Long running operation failed with status 'Conflict'.`. Futtassa az az Group delete parancsot a második alkalommal, hogy eltávolítsa a fennmaradó erőforrásokat és az erőforráscsoportot.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Minta leírása

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| | |
|---|---|
| [az Network vnet](/cli/azure/network/vnet) | Virtuális hálózati parancsok.  |
| [az Network vnet subnet](/cli/azure/network/vnet/subnet) | Virtuális hálózati alhálózati parancsok. |
| [az Network NSG](/cli/azure/network/nsg) | Hálózati biztonsági csoport parancsai. |
| [az Network NSG Rule](/cli/azure/network/nsg/rule)| Hálózati biztonsági szabály parancsai. |
| [az Network Route-Table](/cli/azure/network/route-table) | Útválasztási táblázat parancsai |
| [az SQL mi](/cli/azure/sql/mi) | Felügyelt példányok parancsai. |
| [az Network Public-IP](/cli/azure/network/public-ip) | Hálózati nyilvános IP-címek parancsai. |
| [az Network vnet-Gateway](/cli/azure/network/vnet-gateway) | Virtual Network átjáró parancsai |
| [az SQL instance-feladatátvétel-Group](/cli/azure/sql/instance-failover-group) | Felügyelt példány feladatátvételi csoportjának parancsai. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../sql-database-cli-samples.md) találhat.

---
title: CLI-példa – felügyelt példány létrehozása Azure SQL Database
description: Azure CLI-példa parancsfájl egy felügyelt példány létrehozásához Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: f52a460be33b23da3da0cb32075ab5f75bb8d129
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772624"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>Azure SQL Database felügyelt példány létrehozása a parancssori felület használatával

Ez az Azure CLI-parancsfájl egy Azure SQL Database felügyelt példányt hoz létre egy dedikált alhálózaton egy új virtuális hálózaton belül. Emellett egy útválasztási táblázatot és egy hálózati biztonsági csoportot is konfigurál a virtuális hálózathoz. A szkript sikeres futtatása után a felügyelt példány a virtuális hálózatról vagy egy helyszíni környezetből is elérhető. Lásd: az [Azure virtuális gép konfigurálása Azure SQL Database felügyelt példányhoz való kapcsolódáshoz](../sql-database-managed-instance-configure-vm.md) , valamint [pont – hely kapcsolat konfigurálása egy Azure SQL Database felügyelt példányhoz a helyszínen](../sql-database-managed-instance-configure-p2s.md).

> [!IMPORTANT]
> Korlátozásokkal kapcsolatban lásd: [támogatott régiók](../sql-database-managed-instance-resource-limits.md#supported-regions) és [támogatott előfizetési típusok](../sql-database-managed-instance-resource-limits.md#supported-subscription-types).

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő parancs használatával távolítsa el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Minta leírása

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| | |
|---|---|
| [az Network vnet](/cli/azure/network/vnet) | Virtuális hálózati parancsok. |
| [az Network vnet subnet](/cli/azure/network/vnet/subnet) | Virtuális hálózati alhálózati parancsok. |
| [az Network Route-Table](/cli/azure/network/route-table) | Hálózati útválasztási táblázat parancsai. |
| [az SQL mi](/cli/azure/sql/mi) | Felügyelt példányok parancsai. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../sql-database-cli-samples.md) találhat.

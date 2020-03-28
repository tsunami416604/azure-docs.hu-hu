---
title: CLI példa – felügyelt példány létrehozása az Azure SQL Database-ben
description: Azure CLI példaparancsfájl felügyelt példány létrehozásához az Azure SQL Database-ben
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 998bd8e39863dd9520d1a05e7fff52095c3be6c1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067445"
---
# <a name="use-cli-to-create-an-azure-sql-database-managed-instance"></a>Azure SQL Database felügyelt példányának létrehozása a CLI segítségével

Ez az Azure CLI-parancsfájl-példa egy Azure SQL Database felügyelt példányt hoz létre egy új virtuális hálózaton belüli dedikált alhálózatban. Egy útvonaltáblát és egy hálózati biztonsági csoportot is konfigurál a virtuális hálózathoz. A parancsfájl sikeres futtatása után a felügyelt példány a virtuális hálózaton belülről vagy egy helyszíni környezetből érhető el. Lásd: [Az Azure VM konfigurálása egy Azure SQL Database felügyelt példányhoz való csatlakozáshoz,](../sql-database-managed-instance-configure-vm.md) és [konfigurálása egy pont-hely kapcsolat egy Azure SQL-adatbázis felügyelt példánya a helyszíni.](../sql-database-managed-instance-configure-p2s.md)

> [!IMPORTANT]
> A korlátozásokat a [Támogatott régiók](../sql-database-managed-instance-resource-limits.md#supported-regions) és a [támogatott előfizetéstípusok című témakörben](../sql-database-managed-instance-resource-limits.md#supported-subscription-types)tésszet.

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az erőforráscsoport és a hozzá tartozó összes erőforrás eltávolításához használja a következő parancsot.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Mintahivatkozás

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| | |
|---|---|
| [az hálózati virtuális hálózat](/cli/azure/network/vnet) | Virtuális hálózati parancsok. |
| [az hálózati virtuális hálózat alhálózata](/cli/azure/network/vnet/subnet) | Virtuális hálózati alhálózati parancsok. |
| [az hálózati útvonaltábla](/cli/azure/network/route-table) | Hálózati útvonaltábla-parancsok. |
| [az sql mi](/cli/azure/sql/mi) | Felügyelt példányparancsok. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../sql-database-cli-samples.md) találhat.

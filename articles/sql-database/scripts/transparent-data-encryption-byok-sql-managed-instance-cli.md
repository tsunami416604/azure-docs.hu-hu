---
title: CLI példa - BYOK TDE engedélyezése - Azure SQL Database felügyelt példány
description: Megtudhatja, hogyan konfigurálhat egy Azure SQL felügyelt példányt a BYOK transzparens adattitkosítás (TDE) használatára a PowerShell használatával való inaktív titkosításhoz.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: azurecli
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: 8e8c0e2db1f87cca52c44d33ce14d7ce4f00e895
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061736"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Átlátható adattitkosítás kezelése felügyelt példányban saját kulccsal az Azure Key Vaultból

Ez az Azure CLI parancsfájl-példa az Azure SQL felügyelt példány ügyfél által felügyelt kulccsal konfigurálja az átlátszó adattitkosítást (TDE) az Azure Key Vault egyik kulcsának használatával. Ezt gyakran nevezik a Bring Your Own Key forgatókönyv tde. Ha többet szeretne megtudni a TDE-ről az ügyfél által felügyelt kulccsal, olvassa el [a TDE Saját kulcs hozása az Azure SQL-be című témakört.](../transparent-data-encryption-byok-azure-sql.md)

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0-s vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

## <a name="sample-script"></a>Példaszkript

### <a name="prerequisites"></a>Előfeltételek

Egy meglévő felügyelt példány, [lásd: Azure CLI használata azure SQL Database felügyelt példány létrehozásához.](sql-database-create-configure-managed-instance-cli.md)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>A szkript futtatása

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.sh "Set up BYOK TDE for SQL Managed Instance")]

### <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az erőforráscsoport és a hozzá tartozó összes erőforrás eltávolításához használja a következő parancsot.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Mintahivatkozás

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| | |
|---|---|
| [az sql db](/cli/azure/sql/db) | Adatbázis-parancsok. |
| [az sql feladatátvételi csoport](/cli/azure/sql/failover-group) | Feladatátvételi csoport parancsai. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További SQL Database CLI-példaszkripteket az [Azure SQL Database dokumentációjában](../sql-database-cli-samples.md) találhat.

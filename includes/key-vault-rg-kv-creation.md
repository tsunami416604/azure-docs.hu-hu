---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 2b5533da1e81cf37dfba47bf84bd05f083d62dea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87013107"
---
Ez a rövid útmutató egy előre létrehozott Azure Key vaultot használ. Kulcstartó létrehozásához kövesse az [Azure CLI](/azure/key-vault/general/quick-create-cli)gyors üzembe helyezésének lépéseit, [Azure PowerShell](/azure/key-vault/general/quick-create-powershell)a gyors üzembe helyezést, vagy [Azure Portal](/azure/key-vault/general/quick-create-portal)a gyors üzembe helyezést. 

Azt is megteheti, hogy egyszerűen futtatja az Azure CLI-t vagy Azure PowerShell-parancsokat.

> [!Important]
> Minden kulcstartónak egyedi névvel kell rendelkeznie. A következő példákban cserélje le a <az egyedi-kulcstartó-Name> a Key Vault nevét.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name <your-unique-keyvault-name> -ResourceGroupName "myResourceGroup" -Location "EastUS"
```
---
title: "Azure CLI-példaszkript – Batch-fiók létrehozása – Felhasználói előfizetés | Microsoft Docs"
description: "Azure CLI-példaszkript – Batch-fiók létrehozása felhasználói előfizetési módban"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: 0923512d62a1efe31e08377b6c074df2e0618552
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>CLI-példa: Batch-fiók létrehozása felhasználói előfizetési módban

Ez a szkript létrehoz egy Azure Batch-fiókot felhasználói előfizetési módban. Az olyan fiókokat, amelyek számítási csomópontokat foglalnak le az előfizetésében, egy Azure Active Directory-tokennel kell hitelesíteni. A lefoglalt számítási csomópontok beleszámítanak az előfizetés vCPU- (mag-) kvótájába. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0.20-as vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az role assignment create](/cli/azure/role#az_role_assignment_create) | Létrehoz egy új szerepkör-hozzárendelést egy felhasználóhoz, csoporthoz vagy egyszerű szolgáltatáshoz. |
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_create) | Létrehoz egy kulcstárolót. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#az_keyvault_set_policy) | Frissíti a megadott kulcstároló biztonsági szabályzatát. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Létrehoz egy Batch-fiókot.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Hitelesíti a megadott Batch-fiókot további parancssori felületi interakcióhoz.  |
| [az group delete](/cli/azure/group#az_group_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

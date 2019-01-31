---
title: Azure CLI-példaszkript – Batch-fiók létrehozása – Batch szolgáltatás | Microsoft Docs
description: Azure CLI-példaszkript – Batch-fiók létrehozása Batch szolgáltatás módban
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: lahugh
ms.openlocfilehash: 67504d9597eb68faceb67a3e5a1d4d7abc7079c1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55476519"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>CLI-példa: Batch-fiók létrehozása Batch szolgáltatás módban

Ezt a szkript létrehoz egy Azure Batch-fiókot Batch szolgáltatás módban, és bemutatja, hogyan lehet lekérdezni vagy frissíteni a fiók különböző tulajdonságait. Amikor létrehozunk egy Batch-fiókot az alapértelmezett Batch szolgáltatás módban, a számítási csomópontok hozzárendelését a Batch szolgáltatás belsőleg végzi el. A lefoglalt számítási csomópontokra egy külön vCPU- (mag-) kvóta vonatkozik, a fiók hitelesítéséhez pedig megosztott kulcsos hitelesítő adatok vagy egy Azure Active Directory-token használható.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure CLI 2.0.20-as vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Létrehoz egy Batch-fiókot. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Létrehoz egy tárfiókot. |
| [az batch account set](/cli/azure/batch/account#az-batch-account-set) | Frissíti a Batch-fiók tulajdonságait.  |
| [az batch account show](/cli/azure/batch/account#az-batch-account-show) | Lekérdezi a megadott Batch-fiók adatait.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az-batch-account-keys-list) | Lekérdezi a megadott Batch-fiók hozzáférési kulcsait.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Hitelesíti a megadott Batch-fiókot további parancssori felületi interakcióhoz.  |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

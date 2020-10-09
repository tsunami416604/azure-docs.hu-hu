---
title: Azure CLI parancsfájl-példa – Linux-készlet a Batch-ben
description: Ez a szkript az Azure CLI-ben elérhető egyes parancsokat mutatja be, hogy a Linux számítási csomópontok készletét hozza létre és felügyelje Azure Batchban.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7aba6e64fdf39a069eb010ef032fcc5391fd47ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87494383"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>CLI-példa: Linux-készlet létrehozása és kezelése az Azure Batch szolgáltatásban

Ez a szkript az Azure CLI-ben elérhető egyes parancsokat mutatja be, hogy a Linux számítási csomópontok készletét hozza létre és felügyelje Azure Batchban.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.20-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Létrehoz egy Batch-fiókot. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Hitelesíti a megadott Batch-fiókot további parancssori felületi interakcióhoz.  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | Felsorolja a csomóponti ügynök elérhető SKU-inak és rendszerképeinek adatait.  |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Létrehozza számítási csomópontok egy készletét.  |
| [az batch pool resize](/cli/azure/batch/pool#az-batch-pool-resize) | Átméretezi a futó virtuális gépek számát a megadott készletben.  |
| [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) | Megjeleníti egy készlet tulajdonságait.  |
| [az batch node list](/cli/azure/batch/node#az-batch-node-list) | Felsorolja a megadott készlet összes számítási csomópontját.  |
| [az batch node reboot](/cli/azure/batch/node#az-batch-node-reboot) | Újraindítja a megadott számítási csomópontot.  |
| [az batch node delete](/cli/azure/batch/node#az-batch-node-delete) | Törli a felsorolt csomópontokat a megadott készletből.  |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

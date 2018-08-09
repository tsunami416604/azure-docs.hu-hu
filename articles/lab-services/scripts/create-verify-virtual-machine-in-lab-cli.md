---
title: Azure CLI-példaszkript – Virtuális gép létrehozása és ellenőrzése tesztkörnyezetben | Microsoft Docs
description: Ez az Azure CLI-példaszkript létrehoz egy virtuális gépet egy tesztkörnyezetben, majd ellenőrzi, hogy elérhető-e.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2018
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: b8d48f221dc54a3cd96bf2dbec08e40a047b7940
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439998"
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Virtuális gép létrehozása és elérhetőségének ellenőrzése Azure DevTest Labs tesztkörnyezetben az Azure CLI használatával

Ez az Azure CLI-példaszkript létrehoz egy tesztkörnyezetben lévő virtuális gépet (VM). A virtuális gép egy SSH-hitelesítéssel rendelkező, Marketplace-ről származó rendszerkép alapján jön létre. Ezután a szkript ellenőrzi, hogy használható-e a virtuális gép. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja:

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az lab vm create ](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-create) | Létrehoz egy virtuális gépet (VM) egy tesztkörnyezetben. |
| [az lab vm show](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-show) | Megjeleníti a tesztkörnyezetben lévő virtuális gép állapotát. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Lab Services CLI-példaszkripteket az [Azure Lab Services CLI-mintáiban](../samples-cli.md) találhat.

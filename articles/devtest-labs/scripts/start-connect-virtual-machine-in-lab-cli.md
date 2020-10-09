---
title: Azure CLI-példaszkript – Virtuális gép elindítása egy tesztkörnyezetben | Microsoft Docs
description: Ez az Azure CLI-példaszkript elindít egy virtuális gépet az Azure DevTest Labs tesztkörnyezetben.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 793027916fb0d923cfd4052a4ecfc36fb24c7db7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88136124"
---
# <a name="use-azure-cli-to-start-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Virtuális gép indítása Azure DevTest Labs tesztkörnyezetben az Azure CLI használatával

Ez az Azure CLI-példaszkript elindít egy virtuális gépet (VM) a tesztkörnyezetben. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/start-connect-virtual-machine-in-lab/start-connect-virtual-machine-in-lab.sh "Start a VM")]


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja:

| Parancs | Jegyzetek |
|---|---|
| [az Lab VM Start](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-start) | Elindít egy virtuális gépet (VM) egy tesztkörnyezetben. A művelet elvégzése eltarthat egy ideig. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Lab Services CLI-példaszkripteket az [Azure Lab Services CLI-mintáiban](../samples-cli.md) találhat.

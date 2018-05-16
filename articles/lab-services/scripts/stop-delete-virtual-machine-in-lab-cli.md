---
title: Azure CLI-példaszkript – Virtuális gép leállítása és törlése egyéni tesztkörnyezetben | Microsoft Docs
description: Ez az Azure CLI-példaszkript leállít és töröl egy egyéni tesztkörnyezetben lévő virtuális gépet.
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
ms.openlocfilehash: 0da99bb5e029f4d1c6be74d09ad11c6017121a7c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-custom-lab"></a>Virtuális gép leállítása és törlése az Azure CLI használatával egyéni tesztkörnyezetben

Ez az Azure CLI-példaszkript leállít és töröl egy egyéni tesztkörnyezetben lévő virtuális gépet (VM). 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a custom lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja:

| Parancs | Megjegyzések |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-stop) | Leállít egy egyéni tesztkörnyezetben lévő virtuális gépet (VM). A művelet elvégzése eltarthat egy ideig. |
| [az lab vm delete](/cli/azure/lab/vm?view=azure-cli-latest#az-lab-vm-delete) | Töröl egy egyéni tesztkörnyezetben lévő virtuális gépet (VM). A művelet elvégzése eltarthat egy ideig. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Lab Services PowerShell-példaszkripteket az [Azure Lab Services CLI-mintáiban](../samples-cli.md) találhat.
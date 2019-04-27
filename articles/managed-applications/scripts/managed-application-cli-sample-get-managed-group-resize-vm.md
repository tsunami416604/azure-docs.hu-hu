---
title: Azure CLI-példaszkript – Felügyelt erőforráscsoport lekérése és virtuális gépek átméretezése | Microsoft Docs
description: Azure CLI-példaszkript – Felügyelt erőforráscsoport lekérése és virtuális gépek átméretezése
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: bbf03a0d53769c93a8aab304d3128ae0cc875a8f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61365894"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Felügyelt erőforráscsoport erőforrásainak lekérése és virtuális gépek átméretezése az Azure CLI használatával

Ez a parancsfájl erőforrásokat kér le egy felügyelt erőforráscsoportból, és átméretezi az erőforráscsoportban található virtuális gépeket.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy felügyelt alkalmazás üzembe helyezéséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az managedapp list](https://docs.microsoft.com/cli/azure/managedapp#az-managedapp-list) | Listázza a felügyelt alkalmazásokat. Lekérdezési értékeket ad meg az eredmények szűréséhez. |
| [az resource list](https://docs.microsoft.com/cli/azure/resource#az-resource-list) | Listázza az erőforrásokat. Erőforráscsoportot és lekérdezési értékeket ad meg az eredmények szűréséhez. |
| [az vm resize](https://docs.microsoft.com/cli/azure/vm#az-vm-resize) | Frissíti egy virtuális gép méretét. |


## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

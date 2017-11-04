---
title: "Az Azure CLI parancsfájl-mintában – kezelt erőforráscsoport lekérése és méretezze át a virtuális gépek |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - felügyelt erőforráscsoport lekérése, majd módosítsa a virtuális gépek"
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
ms.openlocfilehash: c78d2646471e40d60972cf91cb5bbd351f71a66c
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2017
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-azure-cli"></a>Erőforrások lekérése a felügyelt erőforráscsoportban, majd módosítsa a virtuális gépek Azure parancssori felülettel

Ez a parancsfájl erőforrások beolvassa a felügyelt erőforráscsoport, és átméretezi a virtuális gépeket az erőforráscsoport.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli[main](../../../cli_scripts/managed-applications/get-application/get-application.sh "Get application")]


## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat a kezelt alkalmazás központi telepítése. Minden egyes parancsa a tábla-parancs-specifikus dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az managedapp listája](https://docs.microsoft.com/cli/azure/managedapp#az_managedapp_list) | Felügyelt alkalmazások listáját. Adja meg az eredmények figyelmet lekérdezés értékét. |
| [az erőforrások listája](https://docs.microsoft.com/cli/azure/resource#az_resource_list) | Lista erőforrásokat. Adja meg egy erőforrás csoport és a lekérdezés eredménye figyelmet. |
| [az vm átméretezése](https://docs.microsoft.com/cli/azure/vm#az_vm_resize) | Frissítse a virtuális gép méretét. |


## <a name="next-steps"></a>Következő lépések

* Felügyelt alkalmazások bemutatása, lásd: [Azure kezelt alkalmazás – áttekintés](../overview.md).
* További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

---
title: Azure-beli virtuális gép sablonjának letöltése
description: Töltse le a virtuális gép sablonját a portál vagy a PowerShell használatával.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: af6905f0ba62a9053e44134348721312ade6b9d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085382"
---
# <a name="download-the-template-for-a-vm"></a>Virtuálisgép-sablon letöltése
Amikor a portál vagy a PowerShell használatával hoz létre egy virtuális gépet az Azure-ban, a rendszer automatikusan létrehoz egy Resource Manager-sablont. A sablon használatával gyorsan duplikálhat egy központi telepítést. A sablon az erőforráscsoport összes erőforrásával kapcsolatos információkat tartalmaz. A virtuális gépek esetében ez azt jelenti, hogy a sablon tartalmaz mindent, ami az adott erőforráscsoporthoz tartozó virtuális gép támogatásában jön létre, beleértve a hálózati erőforrásokat is.

## <a name="download-the-template-using-the-portal"></a>A sablon letöltése a portál használatával
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. A bal oldali menüben válassza a **Virtual Machines**lehetőséget.
3. Válassza ki a virtuális gépet a listából.
4. Válassza a **sablon exportálása**lehetőséget.
5. Válassza a **Letöltés** lehetőséget a felső menüben, és mentse a. zip fájlt a helyi számítógépre.
6. Nyissa meg a. zip fájlt, és bontsa ki a fájlokat egy mappába. A. zip fájl a következőket tartalmazza:
   
   * Parameters. JSON
   * sablon. JSON

A sablon. JSON fájl a sablon.

## <a name="download-the-template-using-powershell"></a>Sablon letöltése a PowerShell használatával
A. JSON sablonfájl az [export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) parancsmaggal is letölthető. A `-path` paraméterrel megadhatja a. JSON fájl fájlnevét és elérési útját. Ebből a példából megtudhatja, hogyan töltheti le a **myResourceGroup** nevű erőforráscsoport sablonját a helyi számítógép **C:\users\public\downloads** mappájába.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>További lépések
Az erőforrások sablonok használatával történő üzembe helyezésével kapcsolatos további információkért lásd: [Resource Manager-sablonok áttekintése](../../azure-resource-manager/resource-manager-template-walkthrough.md).


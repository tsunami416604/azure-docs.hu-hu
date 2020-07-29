---
title: Azure-beli virtuális gép sablonjának letöltése
description: Töltse le a virtuális gép sablonját a portál vagy a PowerShell használatával.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 5b7e50ebe6f09de2555af03a47641ef6ca92e92a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288293"
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
   
   * parameters.jsbekapcsolva
   * template.jsbekapcsolva

A fájl template.jsa sablon.

## <a name="download-the-template-using-powershell"></a>Sablon letöltése a PowerShell használatával
A. JSON sablonfájl az [export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup) parancsmaggal is letölthető. A `-path` paraméterrel megadhatja a. JSON fájl fájlnevét és elérési útját. Ebből a példából megtudhatja, hogyan töltheti le a **myResourceGroup** nevű erőforráscsoport sablonját a helyi számítógép **C:\users\public\downloads** mappájába.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>További lépések
Az erőforrások sablonok használatával történő üzembe helyezésével kapcsolatos további információkért lásd: [Resource Manager-sablonok áttekintése](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

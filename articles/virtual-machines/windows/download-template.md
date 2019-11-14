---
title: Azure-beli virtuális gép sablonjának letöltése
description: Töltse le a templatefor egy virtuális gépre, amely segítséget nyújt az üzembe helyezések automatizálásához a Resource Manager-alapú üzemi modellben
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: c73026515f0d7fde4e2f82838696700b1bb17c77
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033552"
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
   * template.json

A sablon. JSON fájl a sablon.

## <a name="download-the-template-using-powershell"></a>Sablon letöltése a PowerShell használatával
A. JSON sablonfájl az [export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) parancsmaggal is letölthető. A. JSON fájl fájlnevének és elérési útjának megadásához a `-path` paramétert használhatja. Ebből a példából megtudhatja, hogyan töltheti le a **myResourceGroup** nevű erőforráscsoport sablonját a helyi számítógép **C:\users\public\downloads** mappájába.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Következő lépések
Az erőforrások sablonok használatával történő üzembe helyezésével kapcsolatos további információkért lásd: [Resource Manager-sablonok áttekintése](../../azure-resource-manager/resource-manager-template-walkthrough.md).


---
title: Azure-beli virtuális gép sablonjának letöltése | Microsoft Docs
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
ms.openlocfilehash: 37e833e5ba2fcbc9fcfe0a27d5b607a5c6cdd55b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079537"
---
# <a name="download-the-template-for-a-vm"></a>Virtuálisgép-sablon letöltése
Amikor a portál vagy a PowerShell használatával hoz létre egy virtuális gépet az Azure-ban, a rendszer automatikusan létrehoz egy Resource Manager-sablont. A sablon használatával gyorsan duplikálhat egy központi telepítést. A sablon az erőforráscsoport összes erőforrásával kapcsolatos információkat tartalmaz. A virtuális gépek esetében ez azt jelenti, hogy a sablon tartalmaz mindent, ami az adott erőforráscsoporthoz tartozó virtuális gép támogatásában jön létre, beleértve a hálózati erőforrásokat is.

## <a name="download-the-template-using-the-portal"></a>A sablon letöltése a portál használatával
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali menüben válassza a **Virtual Machines**lehetőséget.
3. Válassza ki a virtuális gépet a listából.
4. Válassza a **sablon exportálása**lehetőséget.
5. Válassza a **Letöltés** lehetőséget a felső menüben, és mentse a. zip fájlt a helyi számítógépre.
6. Nyissa meg a. zip fájlt, és bontsa ki a fájlokat egy mappába. A. zip fájl a következőket tartalmazza:
   
   * Deploy. ps1
   * deploy.sh 
   * Deploy. RB
   * DeploymentHelper.cs
   * Parameters. JSON
   * template.json

A sablon. JSON fájl a sablon.

## <a name="download-the-template-using-powershell"></a>Sablon letöltése a PowerShell használatával
A. JSON sablonfájl az [export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) parancsmaggal is letölthető. `-path` A paraméterrel megadhatja a. JSON fájl fájlnevét és elérési útját. Ebből a példából megtudhatja, hogyan töltheti le a **myResourceGroup** nevű erőforráscsoport sablonját a helyi számítógép **C:\users\public\downloads** mappájába.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>További lépések
Az erőforrások sablonok használatával történő üzembe helyezésével kapcsolatos további információkért lásd: [Resource Manager-sablonok áttekintése](../../azure-resource-manager/resource-manager-template-walkthrough.md).


---
title: "Töltse le a sablont egy Azure virtuális gép |} Microsoft Docs"
description: "Töltse le a virtuális gép számára a Resource Manager üzembe helyezési modellel központi telepítésének automatizálása templatefor"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 93ed84cb146119c877c3a143c5f7af9ca8ba0656
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2017
---
# <a name="download-the-template-for-a-vm"></a>Virtuálisgép-sablon letöltése
Egy virtuális Gépet az Azure-ban a portál vagy PowerShell létrehozásakor Resource Manager-sablon automatikusan létrejön. Ez a sablon használatával gyorsan ismétlődő egy központi telepítést. A sablon összes erőforrást erőforráscsoportban információt tartalmaz. A virtuális gép esetén ez azt jelenti, a sablon tartalmaz mindent, létrejön a virtuális gép az erőforráscsoport, beleértve a hálózati erőforrások támogatásához.

## <a name="download-the-template-using-the-portal"></a>Töltse le a sablont, a portál használatával
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válasszon egyet a bal oldali menüben **virtuális gépek**.
3. Válassza ki a virtuális gépet a listából.
4. Válassza ki **automatizálási parancsfájl**.
5. Válassza ki **letöltése** a menüből a lap tetején, és mentse a .zip fájlt a helyi számítógépen.
6. Nyissa meg a .zip fájlt, és bontsa ki a fájlt egy mappába. A zip-fájlt tartalmaz:
   
   * Deploy.ps1
   * Deploy.SH 
   * deployer.RB
   * DeploymentHelper.cs
   * Parameters.JSON
   * Template.JSON

A template.json fájl nem a sablon.

## <a name="download-the-template-using-powershell"></a>Töltse le a sablon PowerShell használatával
A .JSON kiterjesztésű sablon fájl használatával is letöltheti a [Export-AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx) parancsmag. Használhatja a `-path` paramétert adja meg a fájlnevet és elérési útja a .JSON kiterjesztésű fájlt. Ez a példa bemutatja, hogyan nevű erőforráscsoport a sablon letöltése **myResourceGroup** számára a **C:\users\public\downloads** mappát a helyi számítógépen.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Következő lépések
Sablonok használata erőforrások telepítésével kapcsolatos további tudnivalókért lásd: [Resource Manager sablonokhoz](../../azure-resource-manager/resource-manager-template-walkthrough.md).


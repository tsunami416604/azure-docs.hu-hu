---
title: Egy Azure-beli Virtuálisgép-sablon letöltése |} A Microsoft Docs
description: Töltse le a virtuális gép a Resource Manager-alapú üzemi modellben üzemelő példányok automatizálása érdekében templatefor
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 4ef0c09d2d2ea2ed06708342ab45abcaf149c23e
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58903114"
---
# <a name="download-the-template-for-a-vm"></a>Virtuálisgép-sablon letöltése
Az Azure-ban a portál vagy a PowerShell használatával egy virtuális Gépet hoz létre, amikor egy Resource Manager-sablon automatikusan létrejön az Ön számára. Ez a sablon használatával gyorsan ismétlődő egy központi telepítést. A sablon egy erőforráscsoportba tartozó összes információt tartalmazza. Virtuális gép esetén ez azt jelenti, a sablon tartalmaz mindent, ami jön létre a virtuális Gépet, az erőforráscsoport, beleértve a hálózati erőforrások támogatásához.

## <a name="download-the-template-using-the-portal"></a>Töltse le a sablont a portálról
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza a bal oldali menüben, egy **virtuális gépek**.
3. Válassza ki a virtuális gépet a listából.
4. Válassza ki **sablon exportálása**.
5. Válassza ki **letöltése** a menü tetején, és mentse a .zip-fájlt a helyi számítógépen.
6. Nyissa meg a .zip-fájlt, és bontsa ki a fájlokat egy mappában. A .zip-fájlban található:
   
   * deploy.ps1
   * Deploy.SH 
   * deployer.rb
   * DeploymentHelper.cs
   * parameters.json
   * template.json

A template.json fájlt az sablon.

## <a name="download-the-template-using-powershell"></a>Töltse le a sablon PowerShell használatával
A .json sablon fájl használatával is letöltheti a [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) parancsmagot. Használhatja a `-path` paramétert adja meg a nevét és elérési útját a .JSON kiterjesztésű fájlt. Ez a példa bemutatja, hogyan töltheti le az erőforráscsoport neve a sablon **myResourceGroup** , a **C:\users\public\downloads** mappát a helyi számítógépen.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>További lépések
Az üzembe helyezni erőforrásokat sablonok használatával kapcsolatos további információkért lásd: [Resource Manager sablonokhoz](../../azure-resource-manager/resource-manager-template-walkthrough.md).


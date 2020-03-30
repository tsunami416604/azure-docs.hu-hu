---
title: Az Azure-beli virtuális gép sablonjának letöltése
description: Töltse le a sablont egy virtuális géphez, amely segít az Erőforrás-kezelő telepítési modelljében a központi telepítések automatizálásához
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033552"
---
# <a name="download-the-template-for-a-vm"></a>Virtuálisgép-sablon letöltése
Amikor virtuális gép az Azure-ban a portál vagy a PowerShell használatával, egy Erőforrás-kezelő sablon automatikusan létrejön az Ön számára. Ezzel a sablonnal gyorsan duplikálhatja a központi telepítést. A sablon az erőforráscsoport összes erőforrására vonatkozó információt tartalmaz. Egy virtuális gép, ez azt jelenti, hogy a sablon mindent tartalmaz, amely az erőforráscsoportban a virtuális gép támogatására létrehozott, beleértve a hálózati erőforrásokat is.

## <a name="download-the-template-using-the-portal"></a>A sablon letöltése a portál használatával
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Az egyik a bal oldali menüben válassza a **Virtuális gépek**lehetőséget.
3. Válassza ki a virtuális gépet a listából.
4. Válassza **a Sablon exportálása lehetőséget.**
5. Válassza **Download** a letöltés lehetőséget a felső menüből, és mentse a .zip fájlt a helyi számítógépre.
6. Nyissa meg a .zip fájlt, és bontsa ki a fájlokat egy mappába. A .zip fájl a következőket tartalmazza:
   
   * paraméterek.json
   * sablon.json

A template.json fájl a sablon.

## <a name="download-the-template-using-powershell"></a>A sablon letöltése a PowerShell használatával
A .json sablonfájlt az [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) parancsmag használatával is letöltheti. A paraméter `-path` segítségével megadhatja a .json fájl fájlnevét és elérési útját. Ez a példa bemutatja, hogyan töltheti le a **myResourceGroup** nevű erőforráscsoport sablonját a helyi számítógép **C:\users\public\downloads** mappájába.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>További lépések
Az erőforrások sablonok használatával történő központi telepítéséről az [Erőforrás-kezelő sablonforgatókönyve.](../../azure-resource-manager/resource-manager-template-walkthrough.md)


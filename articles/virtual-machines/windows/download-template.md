---
title: Az Azure-beli virtuális gép sablonjának letöltése
description: Töltse le a sablont egy virtuális gép a portál vagy a PowerShell használatával.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: af6905f0ba62a9053e44134348721312ade6b9d7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085382"
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


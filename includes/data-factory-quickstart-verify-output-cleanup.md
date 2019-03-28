---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 309ef92b33d5bbdf8e8aed6b162ed9428a669c87
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505810"
---
## <a name="verify-the-output"></a>Kimenet ellenőrzése
A folyamat automatikusan létrehozza a kimeneti mappát az adftutorial blobtárolóban. Ezután átmásolja az emp.txt fájlt a bemeneti mappából a kimeneti mappába. 

1. Az Azure Portal **adftutorial** tároló lapján kattintson a **Frissítés** elemre a kimeneti mappa megtekintéséhez. 
    
    ![Frissítés](media/data-factory-quickstart-verify-output-cleanup/output-refresh.png)
2. Kattintson a mappalista **kimenet** elemére. 
2. Ellenőrizze, hogy az **emp.txt** fájl bekerült-e a kimeneti mappába. 

    ![Frissítés](media/data-factory-quickstart-verify-output-cleanup/output-file.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Kétféleképpen távolíthatja el a rövid útmutatóban létrehozott erőforrásokat. Törölheti az [Azure-erőforráscsoportot](../articles/azure-resource-manager/resource-group-overview.md), amely tartalmazza az erőforráscsoportban lévő összes erőforrást. Ha a többi erőforrást érintetlenül szeretné hagyni, csak az ebben az oktatóanyagban létrehozott adat-előállítót törölje.

Egy erőforráscsoport törlése a csoportban található összes erőforrást törli, beleértve az adat-előállítókat is. Az alábbi parancsot futtassa a teljes erőforráscsoport törléséhez: 
```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Megjegyzés: az erőforráscsoport elvetése eltarthat egy ideig. Várjon türelmesen, amíg a folyamat befejeződik

Ha csak az adat-előállítót szeretné törölni, nem pedig a teljes erőforráscsoportot, futtassa az alábbi parancsot: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```
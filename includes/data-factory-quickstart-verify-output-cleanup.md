---
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
author: linda33wj
ms.author: jingwang
ms.openlocfilehash: c0fcdf1cf69c55f63288138bc7377a78295cb2be
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86544430"
---
## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A folyamat automatikusan létrehozza a kimeneti mappát az adftutorial blobtárolóban. Ezután átmásolja az emp.txt fájlt a bemeneti mappából a kimeneti mappába. 

1. A Azure Portal a **adftutorial** -tároló lapon kattintson a **frissítés** elemre a kimeneti mappa megtekintéséhez. 
    
    ![Frissítés](media/data-factory-quickstart-verify-output-cleanup/output-refresh.png)

2. Válassza a **kimenet** lehetőséget a mappalistában. 

3. Ellenőrizze, hogy az **emp.txt** fájl bekerült-e a kimeneti mappába. 

    ![Frissítés](media/data-factory-quickstart-verify-output-cleanup/output-file.png)

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Kétféleképpen távolíthatja el a rövid útmutatóban létrehozott erőforrásokat. Törölheti az [Azure-erőforráscsoportot](../articles/azure-resource-manager/management/overview.md), amely tartalmazza az erőforráscsoportban lévő összes erőforrást. Ha a többi erőforrást érintetlenül szeretné hagyni, csak az ebben az oktatóanyagban létrehozott adat-előállítót törölje.

Egy erőforráscsoport törlése a csoportban található összes erőforrást törli, beleértve az adat-előállítókat is. Az alábbi parancsot futtassa a teljes erőforráscsoport törléséhez: 

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

> [!Note]
> Egy erőforráscsoport eldobása hosszabb időt is igénybe vehet. Várjon türelmesen, amíg a folyamat befejeződik

Ha csak az adat-előállítót szeretné törölni, nem pedig a teljes erőforráscsoportot, futtassa az alábbi parancsot: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```
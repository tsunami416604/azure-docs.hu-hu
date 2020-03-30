---
title: Virtuális gép hozzáadása megosztott lemezkép használatával az Azure DevTest Labsben | Microsoft dokumentumok
description: Megtudhatja, hogyan adhat hozzá virtuális gépet (VM) az Azure DevTest Labs csatolt megosztott képgalériájából származó kép használatával
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: c13b2c3d00a0ab0af0f7785feae645d907ffdaa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68775590"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>Virtuális gép hozzáadása a csatolt megosztott képtárból származó kép használatával
Az Azure DevTest Labs lehetővé teszi, hogy egy megosztott képgalériát csatoljon a laborhoz, majd a katalógusban lévő képeket használja a laborban létrehozott virtuális gépek alapjaiként. Ha tudni szeretné, hogyan csatolhat megosztott képgalériát a tesztkörnyezethez, olvassa el [a Megosztott képgaléria konfigurálása című témakört.](configure-shared-image-gallery.md) Ez a cikk bemutatja, hogyan adhat hozzá egy virtuális gép a laborban a csatolt megosztott képtár egy alap ként a képtár használatával. 

## <a name="azure-portal"></a>Azure portál
Ebben a szakaszban megtudhatja, hogyan használhatja az Azure Portalon egy virtuális gép hozzáadása a laborba a csatolt megosztott képtárból származó kép alapján. Ez a szakasz nem nyújt részletes, részletes, lépésenkénti útmutatást a virtuális gép az Azure Portalhasználatával történő létrehozásához. Ezekért a részletekért olvassa [el a Virtuálisgép létrehozása – Azure Portal.](devtest-lab-add-vm.md) Csak azokat a lépéseket emeli ki, amelyekben a csatolt megosztott képtárból kiválaszt egy képet, és kiválasztja a használni kívánt kép egy verzióját. 

Amikor virtuális gépet ad hozzá a laborhoz, alapképként kiválaszthat egy képet a csatolt megosztott képtárból: 

![Megosztott kép kiválasztása az alaphoz](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

Ezután a Labor erőforrás **létrehozása** lap **Speciális beállítások** lapján kiválaszthatja az alapképként használni kívánt lemezkép verzióját:

![Képverzió kiválasztása](./media/add-vm-use-shared-image/select-version-shared-image.png)

A virtuális gép létrehozása után átválthat a lemezkép egy másik verziójára. 

## <a name="resource-manager-template"></a>Resource Manager-sablon
Ha egy Azure Resource Manager-sablonhasználatával hozzon létre egy virtuális gépet egy megosztott képgaléria-lemezkép használatával, adja meg a **sharedImageId** értékét a **Tulajdonságok** szakaszban. Lásd a következő példát: 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

Egy teljes Erőforrás-kezelő sablon példa: [Hozzon létre egy virtuális gépet egy megosztott képtár képminta](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) a GitHub-tárházban. 

## <a name="rest-api"></a>REST API

1. Először be kell szereznie a kép azonosítóját a megosztott képgalériában. Ennek egyik módja, hogy a következő GET paranccsal sorolja fel az összes képet a csatolt megosztott képgalériában. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. Hívja meg a PUT metódust a virtuális gépeken úgy, hogy átadja `properties.SharedImageId`az előző hívásból származó megosztott lemezkép azonosítóját a számára.

## <a name="next-steps"></a>További lépések
Ha tudni szeretné, hogyan csatolhat megosztott képgyűjteményt egy tesztkörnyezethez, és hogyan konfigurálhatja azt, olvassa el [a Megosztott képgaléria konfigurálása című témakört.](configure-shared-image-gallery.md)
---
title: Azure DevTest Labs egyéni rendszerkép létrehozása virtuális gépről | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egyéni rendszerképet Azure DevTest Labs egy kiépített virtuális gépről a Azure Portal használatával
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2020
ms.author: spelluru
ms.openlocfilehash: 3c11c8a4076f868e3f8896dac80147a485f166d3
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84752900"
---
# <a name="create-a-custom-image-from-a-vm"></a>Egyéni rendszerkép létrehozása virtuális gépből

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Lépésenkénti utasítások

Létrehozhat egy egyéni rendszerképet egy kiépített virtuális gépről, majd ezt követően az egyéni rendszerkép használatával azonos virtuális gépeket hozhat létre. A következő lépések bemutatják, hogyan hozhat létre egyéni rendszerképet egy virtuális gépről:

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza a **minden szolgáltatás**lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.

1. A laborok listájából válassza ki a kívánt labort.  

1. A labor fő ablaktábláján válassza a **saját virtuális gépek**lehetőséget.
 
1. A **saját virtuális gépek** ablaktáblán válassza ki azt a virtuális gépet, amelyről létre kívánja hozni az egyéni rendszerképet.

1. A virtuális gép felügyeleti paneljén válassza az **Egyéni rendszerkép létrehozása** a **műveletek**alatt lehetőséget.

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image.png" alt-text="Egyéni rendszerkép létrehozása menüelem":::
1. Az **Egyéni rendszerkép** ablaktáblán adja meg az egyéni rendszerkép nevét és leírását. A virtuális gép létrehozásakor ezek az információk jelennek meg az alapok listájában. Az egyéni rendszerkép tartalmazni fogja az operációsrendszer-lemezt és a virtuális géphez csatolt összes adatlemezt.

    :::image type="content" source="./media/devtest-lab-create-template/create-custom-image-blade.png" alt-text="Egyéni rendszerkép létrehozása lap":::
1. Válassza ki, hogy a Sysprep fut-e a virtuális gépen. Ha a Sysprep nem fut a virtuális gépen, adja meg, hogy szeretné-e futtatni a sysprept a virtuális gépen az egyéni rendszerkép létrehozásakor.
1. Az egyéni rendszerkép létrehozásához kattintson **az OK gombra** .

    Néhány perc elteltével létrejön az egyéni rendszerkép, amely a labor Storage-fiókjában tárolódik. Ha egy tesztkörnyezet egy új virtuális gépet szeretne létrehozni, a lemezkép az alaplemezképek listájában érhető el.

    :::image type="content" source="./media/devtest-lab-create-template/custom-image-available-as-base.png" alt-text="az alapképek listájában elérhető egyéni rendszerkép":::

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni lemezképek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Egyéni rendszerképek másolása Azure DevTest Labs között](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>További lépések

- [Virtuális gép hozzáadása a laborhoz](devtest-lab-add-vm.md)

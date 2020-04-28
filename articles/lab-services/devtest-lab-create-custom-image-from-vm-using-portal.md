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
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 07f3b60b9218f74bb3a778daa27f31687c4538b2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "60868452"
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

1. A virtuális gép felügyeleti paneljén válassza az **Egyéni rendszerkép létrehozása (VHD)** lehetőséget.

    ![Egyéni rendszerkép létrehozása menüelem](./media/devtest-lab-create-template/create-custom-image.png)

1. Az **Egyéni rendszerkép** ablaktáblán adja meg az egyéni rendszerkép nevét és leírását. A virtuális gép létrehozásakor ezek az információk jelennek meg az alapok listájában. Az egyéni rendszerkép tartalmazni fogja az operációsrendszer-lemezt és a virtuális géphez csatolt összes adatlemezt.

    ![Egyéni rendszerkép létrehozása panel](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Válassza ki, hogy a Sysprep fut-e a virtuális gépen. Ha a Sysprep nem fut a virtuális gépen, adja meg, hogy szeretné-e futtatni a sysprept a virtuális gépen az egyéni rendszerkép létrehozásakor.

1. Az egyéni rendszerkép létrehozásához kattintson **az OK gombra** .

Néhány perc elteltével létrejön az egyéni rendszerkép, amely a labor Storage-fiókjában tárolódik. Ha egy tesztkörnyezet egy új virtuális gépet szeretne létrehozni, a lemezkép az alaplemezképek listájában érhető el.

![Az alapképek listájában elérhető egyéni rendszerkép](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni lemezképek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Egyéni rendszerképek másolása Azure DevTest Labs között](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>További lépések

- [Virtuális gép hozzáadása a laborhoz](devtest-lab-add-vm.md)

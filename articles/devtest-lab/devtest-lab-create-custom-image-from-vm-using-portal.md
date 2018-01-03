---
title: "Egy Azure DevTest Labs egyéni lemezképet létrehozni egy virtuális |} Microsoft Docs"
description: "Útmutató egyéni lemezkép létrehozása a Azure DevTest Labs szolgáltatásban az Azure portál használatával kiépített VM"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: v-craic
ms.openlocfilehash: ce854024df5c6974873cf3574bf2de282946f6d0
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="create-a-custom-image-from-a-vm"></a>Egy egyéni lemezképet létrehozni egy virtuális

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Részletes útmutató

Létrehozhat egyéni rendszerképeket kiosztott virtuális gépről, és ezt követően a egyéni lemezkép használatával hozzon létre azonos virtuális gépek. A következő lépések bemutatják, hogyan lehet egy egyéni lemezképet létrehozni egy virtuális:

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza a **További szolgáltatások**, majd a **DevTest Labs** elemet a listából.

1. Válassza ki a kívánt labor labs listájának megtekintéséhez.  

1. A labor paneljén válassza **a virtuális gépek**.
 
1. Az a **a virtuális gépek** panelen válassza ki a virtuális gép, amelyből el kívánja az egyéni lemezkép létrehozásához.

1. A virtuális gép paneljén válassza **egyéni kép létrehozása (VHD)**.

    ![Hozzon létre egyéni lemezkép menüpont](./media/devtest-lab-create-template/create-custom-image.png)

1. Az a **kép létrehozása** panelen adjon nevet és leírást az egyéni lemezképet. Ezt az információt az adatbázisok listája jelenik meg a virtuális gépek létrehozásakor.

    ![Hozzon létre egyéni lemezkép panel](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Adja meg, hogy a sysprep futtatása a virtuális Gépen. Ha a sysprep nem futott le a virtuális Gépre, adja meg, hogy egy virtuális gép létrehozásakor a egyéni lemezképből futtatja a sysprep.

1. Válassza ki **OK** amikor befejeződött az egyéni lemezkép létrehozásához.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni lemezképek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Az Azure DevTest Labs között egyéni lemezképek másolása](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>További lépések

- [A virtuális gépek hozzáadása a tesztkörnyezet](devtest-lab-add-vm.md)

---
title: Az Azure DevTest Labs egyéni lemezkép készítése egy virtuális gép |} A Microsoft Docs
description: Ismerje meg, hogyan hozzon létre egy egyéni rendszerképet az Azure DevTest Labs szolgáltatásban üzembe helyezett virtuális gép az Azure portal használatával
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
ms.openlocfilehash: 22f1579b2df2acdc736ed4c1d5cee64d096c320a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38635337"
---
# <a name="create-a-custom-image-from-a-vm"></a>Egyéni lemezkép készítése egy virtuális Gépről

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Részletes útmutató

Egy egyéni lemezkép készítése egy üzembe helyezett virtuális Gépet, és ezt követően hozzon létre azonos virtuális gépek egyéni rendszerkép használatával. A következő lépések bemutatják, hogyan lehet egyéni lemezkép készítése egy virtuális Gépről:

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.

1. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben.  

1. Válassza ki a fő panelen a labor **a virtual machines**.
 
1. Az a **a virtual machines** ablaktáblán válassza ki a virtuális Gépet, amelyről szeretné létrehozni az egyéni rendszerkép.

1. Válassza ki a virtuális gép kezelése panelen **egyéni kép létrehozása (VHD)**.

    ![Hozzon létre egyéni rendszerkép menüpont](./media/devtest-lab-create-template/create-custom-image.png)

1. Az a **egyéni lemezkép** panelen adjon meg egy nevet és leírást az egyéni rendszerkép. Ez az információ jelenik meg az adatbázisok listájában egy virtuális Gépet hoz létre. Az egyéni rendszerkép tartalmazza az operációsrendszer-lemez és a virtuális géphez csatlakoztatott összes adatlemezét.

    ![Egyéni lemezkép létrehozása](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Válassza ki, hogy a sysprep a virtuális gépen futtattuk. Ha a sysprep nem futott le a virtuális gépen, adja meg, hogy a sysprep futtatása a virtuális gép létrehozásakor az egyéni rendszerképpel.

1. Válassza ki **OK** amikor befejeződött az egyéni rendszerkép létrehozására.

Néhány perc elteltével az egyéni rendszerkép jön létre, és a labor tárfiók belül tárolja. A lab-felhasználó használni szeretne egy új virtuális gép létrehozása, amikor a lemezkép alaplemezképek listája érhető el.

![Egyéni lemezkép érhető el az alap-rendszerképek listája](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni rendszerképek vagy képleteket?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Egyéni rendszerképek az Azure DevTest Labs közötti másolása](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>További lépések

- [A labor virtuális gép hozzáadása](devtest-lab-add-vm.md)

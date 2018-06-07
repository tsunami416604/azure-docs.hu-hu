---
title: Egy Azure DevTest Labs egyéni lemezképet létrehozni egy virtuális |} Microsoft Docs
description: Útmutató egyéni lemezkép létrehozása a Azure DevTest Labs szolgáltatásban az Azure portál használatával kiépített VM
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
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635916"
---
# <a name="create-a-custom-image-from-a-vm"></a>Egy egyéni lemezképet létrehozni egy virtuális

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Részletes útmutató

Létrehozhat egyéni rendszerképeket kiosztott virtuális gépről, és ezt követően a egyéni lemezkép használatával hozzon létre azonos virtuális gépek. A következő lépések bemutatják, hogyan lehet egy egyéni lemezképet létrehozni egy virtuális:

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.

1. Válassza ki a kívánt labor labs listájának megtekintéséhez.  

1. A labor főpanelen válassza **a virtuális gépek**.
 
1. Az a **a virtuális gépek** ablaktáblán válassza ki a virtuális gép, amelyből el kívánja az egyéni lemezkép létrehozásához.

1. A virtuális gép felügyeleti panelén válassza ki a **egyéni kép létrehozása (VHD)**.

    ![Hozzon létre egyéni lemezkép menüpont](./media/devtest-lab-create-template/create-custom-image.png)

1. Az a **egyéni lemezkép** panelen adjon nevet és leírást az egyéni lemezképet. Ezt az információt az adatbázisok listája jelenik meg a virtuális gépek létrehozásakor. Az egyéni rendszerképet az operációsrendszer-lemezképet, és a virtuális géphez csatolt minden adatlemezek tartalmazza.

    ![Egyéni lemezkép létrehozása](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Adja meg, hogy a sysprep futtatása a virtuális Gépen. Ha a sysprep nem futott le a virtuális Gépre, adja meg, hogy egy virtuális gép létrehozásakor a egyéni lemezképből futtatja a sysprep.

1. Válassza ki **OK** amikor befejeződött az egyéni lemezkép létrehozásához.

Néhány perc elteltével az egyéni rendszerképet jön létre, és a tesztkörnyezet tárfiókja belül tárolja. Hozzon létre egy új virtuális Gépet kíván lab-felhasználó, amikor a kép érhető el alap képek listájában.

![Kép: egyéni elérhető alap képek listája](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni lemezképek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Az Azure DevTest Labs között egyéni lemezképek másolása](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>További lépések

- [A virtuális gépek hozzáadása a tesztkörnyezet](devtest-lab-add-vm.md)

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
ms.date: 01/09/2018
ms.author: v-craic
ms.openlocfilehash: dc315bcc625ea98244bb5804ce6ff1c13d0ec7b1
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
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

1. Az a **egyéni lemezkép** panelen adjon nevet és leírást az egyéni lemezképet. Ezt az információt az adatbázisok listája jelenik meg a virtuális gépek létrehozásakor.

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

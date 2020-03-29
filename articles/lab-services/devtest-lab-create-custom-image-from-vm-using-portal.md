---
title: Hozzon létre egy Azure DevTest Labs egyéni lemezképet virtuális gépről | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre egyéni lemezképet az Azure DevTest Labs-ben egy kiépített virtuális gépről az Azure Portalon keresztül
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60868452"
---
# <a name="create-a-custom-image-from-a-vm"></a>Egyéni rendszerkép létrehozása virtuális gépből

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>Lépésenkénti utasítások

Egyéni lemezképet hozhat létre egy kiépített virtuális gépről, és ezt követően használhatja ezt az egyéni lemezképet azonos virtuális gépek létrehozásához. Az alábbi lépések bemutatják, hogyan hozhat létre egyéni lemezképet virtuális gépről:

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)

1. Válassza a **Minden szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.

1. A laborok listájából válassza ki a kívánt labort.  

1. A labor fő ablaktábláján válassza a **Saját virtuális gépek**lehetőséget.
 
1. A **Saját virtuális gépek** ablaktáblán válassza ki azt a virtuális gépet, amelyből létre szeretné hozni az egyéni lemezképet.

1. A virtuális gép felügyeleti ablaktábláján válassza **az Egyéni lemezkép létrehozása (VHD) lehetőséget.**

    ![Egyéni képmenüelem létrehozása](./media/devtest-lab-create-template/create-custom-image.png)

1. Az **Egyéni kép** ablaktáblán adja meg az egyéni lemezkép nevét és leírását. Ez az információ a virtuális gép létrehozásakor megjelenik az alapok listájában. Az egyéni lemezkép tartalmazza az operációs rendszer lemezét és a virtuális géphez csatlakoztatott összes adatlemezt.

    ![Egyéni képablaktábla létrehozása](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. Adja meg, hogy a sysprep futott-e a virtuális számítógépen. Ha a sysprep nem futott a virtuális gépen, adja meg, hogy az egyéni lemezkép létrehozásakor a sysprep futtatását a virtuális számítógépen kívánja-e futtatni.

1. Az egyéni lemezkép létrehozásához válassza az **OK gombot,** amikor végzett.

Néhány perc múlva létrejön az egyéni lemezkép, és a labor tárfiókban tárolja. Ha egy tesztkörnyezet-felhasználó új virtuális gép létrehozásához szeretne létrehozni, a lemezkép elérhető az alaplemezképek listájában.

![Az alapképek listájában elérhető egyéni kép](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni képek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Egyéni lemezképek másolása az Azure DevTest Labs között](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>További lépések

- [Virtuális gép hozzáadása a laborhoz](devtest-lab-add-vm.md)

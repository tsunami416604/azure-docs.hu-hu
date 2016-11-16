---
title: "Labor létrehozása az Azure DevTest Labs szolgáltatásban | Microsoft Docs"
description: "Labor létrehozása virtuális gépekhez az Azure DevTest Labs szolgáltatásban"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f2e607924f17b42bba73672a2d24257e672e1134


---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Labor létrehozása az Azure DevTest Labs szolgáltatásban
## <a name="prerequisites"></a>Előfeltételek
Labor létrehozásához a következőkre van szüksége:

* Azure-előfizetés. Az Azure megvásárlási lehetőségeinek ismertetése: [Az Azure megvásárlása](https://azure.microsoft.com/pricing/purchase-options/) vagy [Ingyenes egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/). Az előfizetés tulajdonosának kell lennie a labor létrehozásához.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>A labor létrehozásának lépései az Azure DevTest Labs szolgáltatásban
A következő lépések bemutatják, hogyan használhatja az Azure Portalt labor létrehozására az Azure DevTest Labs szolgáltatásban. 

1. Jelentkezzen be az [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza a **További szolgáltatások**, majd a **DevTest Labs** elemet a listából.
3. A **DevTest Labs** panelen válassza a **Hozzáadás** lehetőséget.
   
    ![Labor hozzáadása](./media/devtest-lab-create-lab/add-lab-button.png)
4. A **Fejlesztési és tesztelési labor létrehozása** panelen:
   
   1. Adja meg a **Labor nevét**.
   2. Válassza ki a laborhoz társítani kívánt **Előfizetést**.
   3. Válassza ki a labor tárolásának **Helyét**.
   4. Válassza az **Automatikus leállítás** elemet annak megadásához, hogy engedélyezi-e az automatikus leállítást a labor összes virtuális gépénél (valamint az automatikus leállítás paramétereit is megadhatja).
   5. Válassza a **Tárolótípus** elemet a labor virtuális gépéhez tartozó tárolólemez típusának jelöléséhez. 
   6. Kattintson a **Létrehozás** gombra.
      
      ![Laborpanel létrehozása](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Következő lépések
A labor létrehozása után ezeket a lépéseket érdemes figyelembe venni:

* [Biztonságos hozzáférés a laborokhoz](devtest-lab-add-devtest-user.md).
* [Laborházirendek megadása](devtest-lab-set-lab-policy.md).
* [Laborsablon létrehozása](devtest-lab-create-template.md).
* [Egyéni összetevők létrehozása a virtuális géphez](devtest-lab-artifact-author.md).
* [Összetevőkkel rendelkező virtuális gép hozzáadása egy laborhoz](devtest-lab-add-vm-with-artifacts.md).




<!--HONumber=Nov16_HO2-->



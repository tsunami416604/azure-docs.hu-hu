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
ms.date: 05/30/2017
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 015782373e59d1aaf10a7b089c84c982031b36b2
ms.contentlocale: hu-hu
ms.lasthandoff: 05/31/2017


---
<a id="create-a-lab-in-azure-devtest-labs" class="xliff"></a>

# Labor létrehozása az Azure DevTest Labs szolgáltatásban
Az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet erőforráscsoportokat, például virtuális gépeket (VM-eket) magában foglaló infrastruktúra, amely korlátok és kvóták meghatározásával segíti ezen erőforráscsoportok jobb kezelését. Ez a cikk végigvezeti a tesztkörnyezet Azure Portalon való létrehozásának folyamatán.

<a id="prerequisites" class="xliff"></a>

## Előfeltételek
Labor létrehozásához a következőkre van szüksége:

* Azure-előfizetés. Az Azure megvásárlási lehetőségeinek ismertetése: [Az Azure megvásárlása](https://azure.microsoft.com/pricing/purchase-options/) vagy [Ingyenes egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/). Az előfizetés tulajdonosának kell lennie a labor létrehozásához.

<a id="steps-to-create-a-lab-in-azure-devtest-labs" class="xliff"></a>

## A labor létrehozásának lépései az Azure DevTest Labs szolgáltatásban
A következő lépések bemutatják, hogyan használhatja az Azure Portalt labor létrehozására az Azure DevTest Labs szolgáltatásban. 

1. Jelentkezzen be az [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. A bal oldali főmenüben kattintson a lista alján látható **További szolgáltatások** elemre.

    ![További szolgáltatások menüpont](./media/devtest-lab-create-lab/more-services-menu-option.png)

1. Válassza ki a **DevTest Labs** lehetőséget az elérhető szolgáltatások listájából.
1. A **DevTest Labs** panelen válassza a **Hozzáadás** lehetőséget.
   
    ![Labor hozzáadása](./media/devtest-lab-create-lab/add-lab-button.png)

1. A **Fejlesztési és tesztelési labor létrehozása** panelen:
   
    1. Adja meg a **Labor nevét**.
    2. Válassza ki a laborhoz társítani kívánt **Előfizetést**.
    3. Válassza ki a labor tárolásának **Helyét**.
    4. Válassza az **Automatikus leállítás** elemet annak megadásához, hogy engedélyezi-e az automatikus leállítást a labor összes virtuális gépénél (valamint az automatikus leállítás paramétereit is megadhatja). Az automatikus rendszerleállítási funkció elsősorban költségkímélő szolgáltatás, amelynek segítségével megadhatja a virtuális gép automatikus leállásának időpontját. A tesztkörnyezet létrehozása után módosíthatja az automatikus rendszerleállítás beállításait, ha követi az [Manage all policies for a lab in Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown) (Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet szabályzatainak kezelése) témakörben leírt lépéseket.
    5. Ha azt szeretné, hogy a labor parancsikonja megjelenjen a portál irányítópultján, válassza a **Rögzítés az irányítópulton** elemet.
    6. Az **Automatizálási beállítások** lehetőséget választva elérheti az Azure Resource Manager-sablonokat a konfigurálás automatizálásához. 
    7. Kattintson a **Létrehozás** gombra. A **Létrehozás** gombra kattintva megjelenítheti a **DevTest Labs** panelt. A tesztkörnyezet létrehozásának folyamatát az **Értesítések** területen figyelheti. A művelet végeztével frissítse az oldalt, ekkor a tesztkörnyezetek listájában megjelenik az újonnan létrehozott tesztkörnyezet.  
    
    ![Laborpanel létrehozása](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

<a id="next-steps" class="xliff"></a>

## Következő lépések
A labor létrehozása után ezeket a lépéseket érdemes figyelembe venni:

* [Biztonságos hozzáférés a laborokhoz](devtest-lab-add-devtest-user.md).
* [Laborházirendek megadása](devtest-lab-set-lab-policy.md).
* [Laborsablon létrehozása](devtest-lab-create-template.md).
* [Egyéni összetevők létrehozása a virtuális géphez](devtest-lab-artifact-author.md).
* [Összetevőkkel rendelkező virtuális gép hozzáadása egy laborhoz](devtest-lab-add-vm-with-artifacts.md).



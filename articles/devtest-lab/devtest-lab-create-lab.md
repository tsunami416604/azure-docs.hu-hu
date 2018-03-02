---
title: "Labor létrehozása az Azure DevTest Labs szolgáltatásban | Microsoft Docs"
description: "Labor létrehozása virtuális gépekhez az Azure DevTest Labs szolgáltatásban"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: v-craic
ms.openlocfilehash: a2148f8891c94f457a95282b8a3b7067c86c62ed
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Labor létrehozása az Azure DevTest Labs szolgáltatásban
Az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet erőforráscsoportokat, például virtuális gépeket (VM-eket) magában foglaló infrastruktúra, amely korlátok és kvóták meghatározásával segíti ezen erőforráscsoportok jobb kezelését. Ez a cikk végigvezeti a tesztkörnyezet Azure Portalon való létrehozásának folyamatán.

## <a name="prerequisites"></a>Előfeltételek
Labor létrehozásához a következőkre van szüksége:

* Azure-előfizetés. Az Azure megvásárlási lehetőségeinek ismertetése: [Az Azure megvásárlása](https://azure.microsoft.com/pricing/purchase-options/) vagy [Ingyenes egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/). Az előfizetés tulajdonosának kell lennie a labor létrehozásához.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>A labor létrehozásának lépései az Azure DevTest Labs szolgáltatásban
A következő lépések bemutatják, hogyan használhatja az Azure Portalt labor létrehozására az Azure DevTest Labs szolgáltatásban. 

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. A bal oldali főmenüben kattintson a lista tetején látható **Minden szolgáltatás** elemre.

    ![Minden szolgáltatás menüpont](./media/devtest-lab-create-lab/more-services-menu-option.png)

1. Válassza ki a **DevTest Labs** lehetőséget az elérhető szolgáltatások listájából.
1. A **DevTest Labs** területen válassza a **Hozzáadás** lehetőséget.
   
    ![Labor hozzáadása](./media/devtest-lab-create-lab/add-lab-button.png)

1. A **Fejlesztési és tesztelési labor létrehozása** területen:
   
    1. Adja meg a **Labor nevét**.
    2. Válassza ki a laborhoz társítani kívánt **Előfizetést**.
    3. Válassza ki a labor tárolásának **Helyét**.
    4. Válassza az **Automatikus leállítás** elemet annak megadásához, hogy engedélyezi-e az automatikus leállítást a labor összes virtuális gépénél (valamint az automatikus leállítás paramétereit is megadhatja). Az automatikus rendszerleállítási funkció elsősorban költségkímélő szolgáltatás, amelynek segítségével megadhatja a virtuális gép automatikus leállásának időpontját. A tesztkörnyezet létrehozása után módosíthatja az automatikus rendszerleállítás beállításait, ha követi az [Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet szabályzatainak kezelését](./devtest-lab-set-lab-policy.md#set-auto-shutdown) ismertető témakörben leírt lépéseket.
    1. Írja be a **Címkék** **NÉV** és **ÉRTÉK** információit, ha a laborban létrehozni kívánt minden erőforráshoz hozzáadott egyéni címkéket szeretne létrehozni. A címkék hasznos segítséget nyújtanak a laborerőforrások kategória alapján való kezeléséhez és rendezéséhez. A címkékről további információért (beleértve a labor létrehozása után a címkék hozzáadását) lásd: [Címke hozzáadása laborhoz](devtest-lab-add-tag.md).
    5. Ha azt szeretné, hogy a labor parancsikonja megjelenjen a portál irányítópultján, válassza a **Rögzítés az irányítópulton** elemet.
    6. Az **Automatizálási beállítások** lehetőséget választva elérheti az Azure Resource Manager-sablonokat a konfigurálás automatizálásához. 
    7. Kattintson a **Létrehozás** gombra. A tesztkörnyezet létrehozásának folyamatát az **Értesítések** területen figyelheti. A művelet végeztével frissítse az oldalt, ekkor a tesztkörnyezetek listájában megjelenik az újonnan létrehozott tesztkörnyezet.  
    
    ![A DevTest Labs labor szakaszának létrehozása](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
A labor létrehozása után ezeket a lépéseket érdemes figyelembe venni:

* [Biztonságos hozzáférés a laborokhoz](devtest-lab-add-devtest-user.md)
* [Laborszabályzatok megadása](devtest-lab-set-lab-policy.md)
* [Laborsablon létrehozása](devtest-lab-create-template.md)
* [Egyéni összetevők létrehozása a virtuális géphez](devtest-lab-artifact-author.md)
* [Virtuális gép hozzáadása laborhoz](devtest-lab-add-vm.md)


---
title: Labor létrehozása az Azure DevTest Labs szolgáltatásban | Microsoft Docs
description: Ez a cikk bemutatja a labor létrehozásának folyamatát az Azure Portalon és az Azure DevTest Labs használatával.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 5cd675823b85e975dcb8dfe152c27b2d30463c1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759737"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Labor létrehozása az Azure DevTest Labs szolgáltatásban
Az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet erőforráscsoportokat, például virtuális gépeket (VM-eket) magában foglaló infrastruktúra, amely korlátok és kvóták meghatározásával segíti ezen erőforráscsoportok jobb kezelését. Ez a cikk végigvezeti a tesztkörnyezet Azure Portalon való létrehozásának folyamatán.

## <a name="prerequisites"></a>Előfeltételek
Labor létrehozásához a következőkre van szüksége:

* Azure-előfizetés. Az Azure megvásárlási lehetőségeinek ismertetése: [Az Azure megvásárlása](https://azure.microsoft.com/pricing/purchase-options/) vagy [Ingyenes egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/). Az előfizetés tulajdonosának kell lennie a labor létrehozásához.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>A labor létrehozásának lépései az Azure DevTest Labs szolgáltatásban
A következő lépések bemutatják, hogyan használhatja az Azure Portalt labor létrehozására az Azure DevTest Labs szolgáltatásban. 

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
1. A bal oldali főmenüben kattintson a lista tetején látható **Minden szolgáltatás** elemre. Válassza ki a * (csillag) mellett **DevTest Labs** a **DEVOPS** szakaszban. Ez a művelet hozzáadja a **DevTest Labs-t** a bal oldali navigációs menühöz, így a következő alkalommal könnyen elérheti azt. 

    ![Minden szolgáltatás - válassza a DevTest Labs lehetőséget](./media/devtest-lab-create-lab/all-services-select.png)
2. Most válassza **a DevTest Labs** lehetőséget a bal oldali navigációs menüben. Az eszköztáron válassza a **Hozzáadás** gombot. 
   
    ![Labor hozzáadása](./media/devtest-lab-create-lab/add-lab-button.png)
1. A **DevTest Lab létrehozása** lapon tegye a következő műveleteket: 
    1. Adja meg a labor **nevét.**
    2. Válassza ki a laborhoz társítani kívánt **Előfizetést**.
    3. Adja meg a tesztkörnyezet **erőforráscsoportjának nevét.** 
    4. Válassza ki azt a **helyet,** ahol a labort tárolni szeretné.
    4. Válassza az **Automatikus leállítás** elemet annak megadásához, hogy engedélyezi-e az automatikus leállítást a labor összes virtuális gépénél (valamint az automatikus leállítás paramétereit is megadhatja). Az automatikus rendszerleállítási funkció elsősorban költségkímélő szolgáltatás, amelynek segítségével megadhatja a virtuális gép automatikus leállásának időpontját. A tesztkörnyezet létrehozása után módosíthatja az automatikus rendszerleállítás beállításait, ha követi az [Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet szabályzatainak kezelését](./devtest-lab-set-lab-policy.md#set-auto-shutdown) ismertető témakörben leírt lépéseket.
    1. Írja be a **Címkék****NÉV** és **ÉRTÉK** információit, ha a laborban létrehozni kívánt minden erőforráshoz hozzáadott egyéni címkéket szeretne létrehozni. A címkék hasznos segítséget nyújtanak a laborerőforrások kategória alapján való kezeléséhez és rendezéséhez. A címkékről további információért (beleértve a labor létrehozása után a címkék hozzáadását) lásd: [Címke hozzáadása laborhoz](devtest-lab-add-tag.md).
    6. Az **Automatizálási beállítások** lehetőséget választva elérheti az Azure Resource Manager-sablonokat a konfigurálás automatizálásához. 
    7. Kattintson a **Létrehozás** gombra. A tesztkörnyezet létrehozásának folyamatát az **Értesítések** területen figyelheti. 
    
        ![A DevTest Labs labor szakaszának létrehozása](./media/devtest-lab-create-lab/create-devtestlab-blade.png)
    8. Miután elkészült, válassza az Ugrás az **erőforráshoz** lehetőséget az értesítésben. Másik lehetőségként frissítse a **DevTest Labs** lapot az újonnan létrehozott laborok megtekintéséhez a laborok listájában.  Válassza ki a tesztkörnyezetet a listában. Megjelenik a labor kezdőlapja. 

        ![A labor kezdőlapja](./media/devtest-lab-create-lab/lab-home-page.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
A labor létrehozása után ezeket a lépéseket érdemes figyelembe venni:

* [Biztonságos hozzáférés a laborhoz](devtest-lab-add-devtest-user.md)
* [Laborházirendek beállítása](devtest-lab-set-lab-policy.md)
* [Laborsablon létrehozása](devtest-lab-create-template.md)
* [Egyéni összetevők létrehozása a virtuális gépekhez](devtest-lab-artifact-author.md)
* [Virtuális gép hozzáadása laborhoz](devtest-lab-add-vm.md)


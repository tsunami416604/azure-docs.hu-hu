---
title: Labor létrehozása az Azure DevTest Labs szolgáltatásban | Microsoft Docs
description: Labor létrehozása virtuális gépekhez az Azure DevTest Labs szolgáltatásban
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
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: c54b97bdf69908f32015631a9e527c6e289d1d2a
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55080497"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Labor létrehozása az Azure DevTest Labs szolgáltatásban
Az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet erőforráscsoportokat, például virtuális gépeket (VM-eket) magában foglaló infrastruktúra, amely korlátok és kvóták meghatározásával segíti ezen erőforráscsoportok jobb kezelését. Ez a cikk végigvezeti a tesztkörnyezet Azure Portalon való létrehozásának folyamatán.

## <a name="prerequisites"></a>Előfeltételek
Labor létrehozásához a következőkre van szüksége:

* Azure-előfizetés. Az Azure megvásárlási lehetőségeinek ismertetése: [Az Azure megvásárlása](https://azure.microsoft.com/pricing/purchase-options/) vagy [Ingyenes egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/). Az előfizetés tulajdonosának kell lennie a labor létrehozásához.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>A labor létrehozásának lépései az Azure DevTest Labs szolgáltatásban
A következő lépések bemutatják, hogyan használhatja az Azure Portalt labor létrehozására az Azure DevTest Labs szolgáltatásban. 

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. A bal oldali főmenüben kattintson a lista tetején látható **Minden szolgáltatás** elemre. Válassza ki * (csillag) melletti **DevTest Labs** a a **fejlesztési és üzemeltetési** szakaszban. Ez a művelet felveszi **DevTest Labs** , a bal oldali navigációs menü, hogy hozzá tud férni egyszerűen legközelebb. 

    ![Minden szolgáltatás – válassza ki a DevTest Labs szolgáltatásban](./media/devtest-lab-create-lab/all-services-select.png)
2. Most válassza ki **DevTest Labs** a bal oldali navigációs menüben. Válassza ki **Hozzáadás** az eszköztáron. 
   
    ![Labor hozzáadása](./media/devtest-lab-create-lab/add-lab-button.png)
1. Az a **fejlesztési és tesztelési labor létrehozása** lapon, tegye a következőket: 
    1. Adjon meg egy **neve** a tesztkörnyezethez.
    2. Válassza ki a laborhoz társítani kívánt **Előfizetést**.
    3. Adjon meg egy **az erőforráscsoport neve** a tesztkörnyezethez. 
    4. Válassza ki a **hely** , amelyben a labor tárolásának.
    4. Válassza az **Automatikus leállítás** elemet annak megadásához, hogy engedélyezi-e az automatikus leállítást a labor összes virtuális gépénél (valamint az automatikus leállítás paramétereit is megadhatja). Az automatikus rendszerleállítási funkció elsősorban költségkímélő szolgáltatás, amelynek segítségével megadhatja a virtuális gép automatikus leállásának időpontját. A tesztkörnyezet létrehozása után módosíthatja az automatikus rendszerleállítás beállításait, ha követi az [Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet szabályzatainak kezelését](./devtest-lab-set-lab-policy.md#set-auto-shutdown) ismertető témakörben leírt lépéseket.
    1. Írja be a **Címkék** **NÉV** és **ÉRTÉK** információit, ha a laborban létrehozni kívánt minden erőforráshoz hozzáadott egyéni címkéket szeretne létrehozni. A címkék hasznos segítséget nyújtanak a laborerőforrások kategória alapján való kezeléséhez és rendezéséhez. A címkékről további információért (beleértve a labor létrehozása után a címkék hozzáadását) lásd: [Címke hozzáadása laborhoz](devtest-lab-add-tag.md).
    6. Az **Automatizálási beállítások** lehetőséget választva elérheti az Azure Resource Manager-sablonokat a konfigurálás automatizálásához. 
    7. Kattintson a **Létrehozás** gombra. A tesztkörnyezet létrehozásának folyamatát az **Értesítések** területen figyelheti. 
    
        ![A DevTest Labs labor szakaszának létrehozása](./media/devtest-lab-create-lab/create-devtestlab-blade.png)
    8. Ha kész, válassza ki a **erőforrás megnyitása** az értesítésben. Azt is megteheti, frissítse a **DevTest Labs** oldalon tekintheti meg az újonnan létrehozott tesztkörnyezet, a tesztkörnyezetek listájában.  Válassza ki a labor létrehozása a listából. A tesztkörnyezethez tekintse meg a kezdőlapján. 

        ![A labor kezdőlapja](./media/devtest-lab-create-lab/lab-home-page.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
A labor létrehozása után ezeket a lépéseket érdemes figyelembe venni:

* [Biztonságos hozzáférés a laborokhoz](devtest-lab-add-devtest-user.md)
* [Laborszabályzatok megadása](devtest-lab-set-lab-policy.md)
* [Laborsablon létrehozása](devtest-lab-create-template.md)
* [Egyéni összetevők létrehozása a virtuális géphez](devtest-lab-artifact-author.md)
* [Virtuális gép hozzáadása laborhoz](devtest-lab-add-vm.md)


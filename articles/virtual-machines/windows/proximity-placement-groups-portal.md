---
title: Proximity elhelyezési csoport létrehozása a portál használatával
description: Megtudhatja, hogyan hozhat létre közelségi elhelyezési csoportot a Azure Portal használatával.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: aaecfbd14289840e795c6323737877e267586e16
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82098645"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Proximity elhelyezési csoport létrehozása a portál használatával

Ha a lehető legközelebb szeretné lekérni a virtuális gépeket, a lehető legkevesebb késést kell megvalósítania, egy [közelségi elhelyezési csoporton](co-location.md#proximity-placement-groups)belül kell telepítenie.

A közelségi elhelyezési csoport olyan logikai csoport, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag közel legyenek egymáshoz. A közelségi csoportok olyan munkaterhelések esetén hasznosak, ahol az alacsony késés követelmény.


## <a name="create-the-proximity-placement-group"></a>A közelségi elhelyezési csoport létrehozása

1. Írja be a következőt: **Proximity elhelyezési csoport** a keresésben.
1. A keresési eredmények között található **szolgáltatások** területen válassza a **közelségi csoportok**lehetőséget.
1. A **közelség elhelyezése csoportok** lapon válassza a **Hozzáadás**lehetőséget.
1. Az **alapvető beállítások** lap **projekt részletei**területén ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva.
1. Az **erőforráscsoport** területen válassza az **új létrehozása** lehetőséget egy új csoport létrehozásához, vagy válasszon ki egy meglévő erőforráscsoportot a legördülő menüből.
1. A **régió területen** válassza ki azt a helyet, ahová a közelségi elhelyezési csoportot létre szeretné hozni.
1. A **Proximity elhelyezési csoport neve** mezőbe írjon be egy nevet, majd válassza a **felülvizsgálat + létrehozás**elemet.
1. Az ellenőrzés után a **Létrehozás** gombra kattintva hozza létre a közelségi elhelyezési csoportot.

    ![Képernyőkép a közelségi elhelyezési csoport létrehozásáról](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Amikor létrehoz egy virtuális gépet a portálon, lépjen a **speciális** lapra. 
1. A **közelség elhelyezése csoport** kiválasztása területen válassza ki a megfelelő elhelyezési csoportot. 

    ![Képernyőfelvétel a közelségi csoportok szakaszról, amikor új virtuális gépet hoz létre a portálon](./media/ppg/vm-ppg.png)

1. Ha végzett az összes többi kötelező beállítással, válassza a **felülvizsgálat + létrehozás**lehetőséget.
1. Az érvényesítést követően a **Létrehozás** gombra kattintva telepítheti a virtuális gépet az elhelyezési csoportban.




## <a name="next-steps"></a>További lépések

A [Azure PowerShell](proximity-placement-groups.md) is használhatja közelségi elhelyezési csoportok létrehozásához.


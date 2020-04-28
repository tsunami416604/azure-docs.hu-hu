---
title: Proximity elhelyezési csoport létrehozása a portál használatával
description: Megtudhatja, hogyan hozhat létre közelségi elhelyezési csoportot a Azure Portal használatával.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/24/2020
ms.author: cynthn
ms.openlocfilehash: 6a14e2bd7385430c3d0fbec06259a876af556e38
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190401"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Proximity elhelyezési csoport létrehozása a portál használatával

Ha a lehető legközelebb szeretné lekérni a virtuális gépeket, a lehető legkevesebb késést kell megvalósítania, egy [közelségi elhelyezési csoporton](co-location.md#proximity-placement-groups)belül kell telepítenie.

A közelségi elhelyezési csoport olyan logikai csoport, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag közel legyenek egymáshoz. A közelségi csoportok olyan munkaterhelések esetén hasznosak, ahol az alacsony késés követelmény.

> [!NOTE]
> A közelségi elhelyezési csoportok nem használhatók dedikált gazdagépekkel.
>
> Ha az elhelyezési csoportokkal együtt szeretné használni a rendelkezésre állási zónákat, meg kell győződnie arról, hogy az elhelyezési csoportban lévő virtuális gépek szintén ugyanabban a rendelkezésre állási zónában vannak.
>

## <a name="create-the-proximity-placement-group"></a>A közelségi elhelyezési csoport létrehozása

1. Írja be a következőt: **Proximity elhelyezési csoport** a keresésben.
1. A keresési eredmények között található **szolgáltatások** területen válassza a **közelségi csoportok**lehetőséget.
1. A **közelség elhelyezése csoportok** lapon válassza a **Hozzáadás**lehetőséget.
1. Az **alapvető beállítások** lap **projekt részletei**területén ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva.
1. Az **erőforráscsoport** területen válassza az **új létrehozása** lehetőséget egy új csoport létrehozásához, vagy válasszon ki egy már létező üres erőforráscsoportot a legördülő menüből. 
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


## <a name="add-vms-in-an-availability-set-to-a-proximity-placement-group"></a>Virtuális gépek hozzáadása a rendelkezésre állási csoportba egy közelségi elhelyezési csoportba

Ha a virtuális gép a rendelkezésre állási csoport része, akkor a virtuális gépek hozzáadása előtt hozzá kell adnia a rendelkezésre állási csoportot az elhelyezési csoportba.

1. A [portálon](https://portal.azure.com) keresse meg a *rendelkezésre állási csoportokat* , és válassza ki a rendelkezésre állási csoportot az eredmények közül.
1. A virtuális gép kiválasztásával Stop\deallocate az egyes virtuális gépeket a rendelkezésre állási csoportba, majd válassza a **Leállítás** elemet a virtuális gép lapján, majd kattintson **az OK** gombra a virtuális gép leállításához.
1. A rendelkezésre állási csoport lapján ellenőrizze, hogy az összes virtuális gép rendelkezik-e **leállított (** fel nem sorolt) **állapottal** .
1. A bal oldali menüben válassza a **Konfigurálás**lehetőséget.
1. A **közelség elhelyezése**csoportban válasszon ki egy elhelyezési csoportot a legördülő listából, majd válassza a **Mentés**lehetőséget.
1. A bal oldali menüben válassza az **Áttekintés** lehetőséget a virtuális gépek listájának megjelenítéséhez. 
1. Válassza ki az egyes virtuális gépeket a rendelkezésre állási csoportból, majd válassza az **Indítás** lehetőséget az egyes virtuális gépek lapon. 


## <a name="add-existing-vm-to-placement-group"></a>Meglévő virtuális gép felvétele az elhelyezési csoportba 


1. A virtuális gép lapján válassza a **Leállítás**lehetőséget.
1. Ha a virtuális gép állapota **leállítva (fel van foglalva)**, válassza a bal oldali menü **konfiguráció** elemét.
1. A **közelség elhelyezése**csoportban válasszon ki egy elhelyezési csoportot a legördülő listából, majd válassza a **Mentés**lehetőséget.
1. Válassza az **Áttekintés** lehetőséget a bal oldali menüben, majd kattintson a **Start** gombra a virtuális gép újraindításához.

 

## <a name="next-steps"></a>További lépések

A [Azure PowerShell](proximity-placement-groups.md) is használhatja közelségi elhelyezési csoportok létrehozásához.


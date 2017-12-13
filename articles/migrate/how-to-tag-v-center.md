---
title: "Virtuális gépek csoport VMware vCenter címkézés |} Microsoft Docs"
description: "Hozzon létre egy csoportot az Azure áttelepítése szolgáltatással értékelését futtatása előtt ismerteti."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 967c2a1fc0e5144c6c7d5c8c4a81cec3d77ffdb5
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="group-vms-with-vcenter-tagging"></a>Csoport virtuális gépek vCenter-címkézés

Ez a cikk ismerteti a gépcsoport létrehozása [Azure áttelepítése](migrate-overview.md) assessment a VMware vcenter programban címkézést. Megadhatja a címke kategóriáját, amikor létrehoz egy csoportot használni kívánt. 

## <a name="set-up-tagging"></a>Címkézés beállítása

A telepítés Azure áttelepítése során egy helyszíni Azure át virtuális gép vCenter-kiszolgáló által kezelt ESXi-gazdagépeken futó gépek deríti fel. Állítsa be a felderítési folyamat címkézés vCenter kell.

1. A VMware vSphere webes ügyféllel navigáljon a vCenter-kiszolgáló.
2. Kattintson a **címkék**, áttekintheti az aktuális címkéket.
3. Címke egy virtuális Gépet, jelölje be **kapcsolódó objektumok** > **virtuális gépek**, majd válassza ki a virtuális gép kívánt címkét.
4. A **összegzés** > **címkék**, kattintson a **hozzárendelése**. 
5. Kattintson a **új címke**, és adja meg a címke nevét és leírását.
6. Egy kategóriát a címke crate, jelölje be **új kategória** a legördülő listában.
7. Adja meg a kategória neve, leírása és a cardinality. Ezután kattintson az **OK** gombra.

    ![Virtuális gép címkék](./media/how-to-tag-v-center/vm-tag.png)

## <a name="use-tagging-to-create-groups"></a>Csoportok létrehozása a címkézés segítségével

1. Állítsa be a helyszíni gépeket felderítése, lásd: a [VMware értékelési útmutató](tutorial-assessment-vmware.md#run-the-collector-to-discover-vms).
2. A **címke kategória csoportosításhoz**, válassza ki a vCenter címke kategóriát a assessment csoport kell alapjául. Az Azure áttelepítése automatikusan létrehoz egy csoportot, a kiválasztott kategória.

    

## <a name="next-steps"></a>Következő lépések

[VMware virtuális gép értékelés beállítása](tutorial-assessment-vmware.md).

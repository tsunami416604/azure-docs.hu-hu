---
title: "Virtuális gépek csoport VMware vCenter címkézés |} Microsoft Docs"
description: "Hozzon létre egy csoportot az Azure áttelepítése szolgáltatással értékelését futtatása előtt ismerteti."
services: migration-planner
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5c279804-aa30-4946-a222-6b77c7aac508
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: db33e31cdef143aa70809442457e447446a1681a
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
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

---
title: Engedélyezze a licencelt lemezkép a laborban a Azure DevTest Labs szolgáltatásban |} Microsoft Docs
description: 'Útmutató: Azure DevTest Labs szolgáltatásban az Azure portál használatával egy licencelt lemezképet engedélyezése'
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 221390d2-8d3b-4e1f-b454-43d33f8072b7
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 0e5de93f8a10d27c28b3f07567f9b6fa7e41d482
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>A tesztkörnyezet a Azure DevTest Labs szolgáltatásban egy licencelt lemezképet engedélyezése

A Azure DevTest Labs szolgáltatásban a licencelt lemezkép egyike, amely tartalmazza a feltételek és kikötések – általában a harmadik fél –, amelyek kell fogadnia a lemezkép érhető el, amikor a felhasználók számára. Az alábbi szakaszok azt ismertetik, hogyan használható licencelt lemezképek, hogy a virtuális gépek létrehozásához használhatók legyenek.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Meghatározása, hogy a felhasználók számára rendelkezésre áll-e a licencelt lemezkép
Lehetővé teszi a felhasználók egy licencelt lemezképből virtuális gépek létrehozásához először győződjön meg arról, hogy a feltételek és kikötések lett elfogadva a licencelt lemezkép. A következő lépések bemutatják, hogyan licencelt kép ajánlat állapotának megtekintése és, ha szükséges, fogadja el a használati feltételeket.

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.

1. Válassza ki a kívánt labor labs listájának megtekintéséhez.  

1. A bal oldali panel alatt **beállítások**, jelölje be **konfigurációs és házirendek**.

1. A bal oldali panel alatt **virtuális gép KÖRREL**, jelölje be **piactéren elérhető rendszerkép**. 

    ![Piactér-lemezképek menüpont](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Az összes rendelkezésre álló piactéren elérhető rendszerkép listájának látható, beleértve a **ajánlat állapota** az egyes lemezképek.

    ![Az ajánlat állapota az egyes lemezképek megjelenítő piactéren elérhető rendszerkép listájának](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    A licenccel rendelkező lemezkép az ajánlat állapotát jeleníti meg. 
    
    - **Elfogadott feltételeket:** a licencelt kép érhető el a felhasználók számára virtuális gépek létrehozásához. 
    - **Tekintse át a szükséges feltételeket:** a licencelt kép már nem érhető el a felhasználók számára. A licenc feltételeit kell fogadnia labor felhasználók használhatja virtuális gépek létrehozásához. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>A licenccel rendelkező lemezkép elérhetővé tételére labor számára
Legyen a licencelt lemezkép lab-felhasználó számára elérhető, a tesztkörnyezet tulajdonosa rendszergazdai engedélyekkel kell először fogadja el a használati feltételeket, hogy licencelt lemezkép. Az előfizetés automatikusan egy licencelt lemezképhez tartozó a programozott telepítés engedélyezése a jogi feltételeket és adatvédelmi nyilatkozatából lemezkép fogad el. [Piactér-lemezképek használata az Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) piactéren elérhető rendszerkép a programozott telepítés további információkkal szolgál.

Következő lépések végrehajtásával engedélyezheti a licencelt lemezképek a programozott telepítés:

1. Az a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040), keresse fel a listája **piactéren elérhető rendszerkép**.

1. Kívánt felhasználók számára, hogy rendelkezik hozzáféréssel a licencelt lemezkép azonosításához, de akiknek nem elfogadását. Például láthatja a adatok tudományos rendelkező virtuális gépek vagy állapotának megjelenítése **elfogadott feltételeket** vagy **felülvizsgálat szükséges feltételeket**.

    ![A programozott telepítés ablak beállítása](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Adatok tudományos virtuális gépek az Azure virtuálisgép-lemezképeket, előre telepített, konfigurálásához és számos népszerű eszköz adatelemzés, gépi tanulási és AI képzési általánosan használt tesztelése. [Bevezetés az Azure Data tudományos virtuális gép Linux és Windows](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) nagyfokú DSVMs kapcsolatos információkat biztosít.
   >
   >

1. Az a **ajánlat állapota** a lemezképet, jelölje be az oszlop **felülvizsgálat szükséges feltételeket**.

1. Válassza a programozott telepítés konfigurálása ablak **engedélyezése**.

    ![A programozott telepítés ablak beállítása](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > A programozott telepítés konfigurálása ablakban felsorolt több előfizetéssel jelenhet meg. Győződjön meg arról, hogy engedélyezi a programozott telepítés csak az adott előfizetéshez tartozó.
   >
   >


1. Kattintson a **Mentés** gombra. 

    A piactéren elérhető rendszerkép listájának megtekintéséhez, amely most látható kép **elfogadott feltételeket** és a felhasználók virtuális gépek létrehozásához.

> [!NOTE]
> Felhasználók hozhatnak létre egyéni lemezkép licencelt lemezképéről. Lásd: [létrehozhat egyéni rendszerképeket a VHD-fájl](devtest-lab-create-template.md) további információt.
>
>


## <a name="related-blog-posts"></a>Kapcsolódó blogbejegyzések

- [Egyéni lemezképek vagy képletek?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Az Azure DevTest Labs között egyéni lemezképek másolása](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>További lépések

- [Egy egyéni lemezképet létrehozni egy virtuális](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Létrehozhat egyéni rendszerképeket a VHD-fájl](devtest-lab-create-template.md)
- [A virtuális gépek hozzáadása a tesztkörnyezet](devtest-lab-add-vm.md)
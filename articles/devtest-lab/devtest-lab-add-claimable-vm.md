---
title: "Claimable virtuális gép hozzáadása egy laborhoz a Azure DevTest Labs szolgáltatásban |} Microsoft Docs"
description: "Megtudhatja, hogyan claimable virtuális gép hozzáadása egy laborhoz a Azure DevTest Labs szolgáltatásban"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: tarcher
ms.openlocfilehash: 98950d72e90b0e178bae2fffa7644fd824a25eea
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Claimable virtuális gép hozzáadása egy laborhoz a Azure DevTest Labs szolgáltatásban
Ad hozzá egy claimable virtuális labor be, hogyan hasonló módon, [szabványos virtuális gép hozzáadása](devtest-lab-add-vm.md) – a egy *alap* , amely vagy egy [egyéni lemezkép](devtest-lab-create-template.md), [képlet](devtest-lab-manage-formulas.md) , vagy [Piactéri lemezképhez](devtest-lab-configure-marketplace-images.md). Ez az oktatóanyag végigvezeti az Azure portál használata claimable virtuális gép hozzáadása egy laborhoz a DevTest Labs szolgáltatásban, és bemutatja a felhasználó a következő igényelni a virtuális gép.

> [!NOTE]
> Ha a labor virtuális gépeken keresztül telepít [Azure Resource Manager-sablonok](devtest-lab-create-environment-from-arm.md), claimable virtuális gépeket hozhat létre úgy, hogy a **allowClaim** tulajdonság igaz értékű a Tulajdonságok szakaszának.
>
>

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Ismerteti a végrehajtás lépéseit claimable virtuális gép hozzáadása egy laborhoz a Azure DevTest Labs szolgáltatásban
1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **több szolgáltatások**, majd válassza ki **DevTest Labs** a listából.
1. Labs listában jelölje ki a labor kívánja claimable virtuális gép létrehozása.  
1. A tesztlabor a **áttekintése** panelen válassza **+ Hozzáadás**.  

    ![Adja hozzá a virtuális gép gomb](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Az a **base válasszon** panelen válassza ki a megfelelő alapja a virtuális gép számára.
1. Az a **virtuális gép** panelen adjon meg egy nevet az új virtuális gép a **virtuálisgép-nevet** szövegmezőben.

    ![Labor VM panel](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Adjon meg egy **felhasználónév** kap, amely rendszergazdai jogosultságokkal a virtuális gépen.  
1. Ha azt szeretné tárolni a jelszó használatát a [titkos tároló](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store), jelölje be **mentett titkos kulcs használata**, és adja meg a kulcs értéke, amely megfelel a titkos kulcs (jelszó). Ellenkező esetben a feliratú szövegmezőbe írja be a jelszót **írjon be egy értéket**.
1. A **virtuális gép lemeztípus** határozza meg, hogy milyen típusú a virtuális gépek a tesztkörnyezetben.
1. Válassza ki **virtuálisgép-méret** , és válassza ki a Processzormagok RAM memória méretét és a merevlemez mérete a virtuális gép létrehozásához adjon meg előre meghatározott elemek.
1. Válassza ki **összetevők** és az összetevők listájában válassza ki és konfigurálja az alapjául szolgáló lemezképhez hozzáadni kívánt összetevők. Ha ismerkedik a DevTest Labs szolgáltatásban, vagy tekintse meg az összetevők, konfigurálása a [meglévő összetevő felvétele a virtuális gépek](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) szakaszt, és térjen vissza ide befejezésekor.
1. Válassza ki **speciális beállítások** a virtuális gép hálózati beállításai és a lejárati beállítások konfigurálása. A **jogcím-beállítások**, válassza a **Igen** claimable ellenőrizze a gép számára.

  ![Válassza a virtuális gép claimable legyen.](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. Ha szeretné megtekinteni, illetve az Azure Resource Manager sablon másolása, tekintse meg a [mentése Azure Resource Manager-sablon](devtest-lab-add-vm.md#save-azure-resource-manager-template) szakaszt, és térjen vissza ide, ha befejeződött.
1. Válassza ki **létrehozása** a megadott virtuális gép hozzáadása a labor.
1. A tesztkörnyezet panel állapotát jeleníti meg a virtuális gép létrehozás - először mint **létrehozása**, majd, mint a **futtató** a virtuális gép elindítása után.


## <a name="using-a-claimable-vm"></a>A claimable virtuális gépek használata

A felhasználó igényelhet a virtuális gép "Claimable virtual machines" közül lépések egyikének végrehajtásával:

* "Claimable virtuális gépnek" a tesztlabor áttekintése panel alján a listából, kattintson a jobb gombbal a virtuális gépeket, a lista egyik, és válassza a **jogcím gép**.

 ![Egy adott claimable VM kérelmet.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Felső részén a **áttekintése** paneljén válassza **minden jogcím**. Egy véletlenszerű virtuális géphez claimable virtuális gépek közül van hozzárendelve.

 ![Bármely claimable VM kérelmet.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Miután a felhasználó a jogcímek egy virtuális Gépet, a átkerül az "A virtuális gépnek" tartalmazó, és már nem claimable, amelyet semmilyen más felhasználó.

## <a name="next-steps"></a>Következő lépések
* A virtuális gép létrehozása után keresztül csatlakozhat a virtuális gép kiválasztásával **Connect** a virtuális gép paneljén.
* Megismerkedhet a [DevTest Labs Azure Resource Manager gyorsindítási sablonok gyűjteménye](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)

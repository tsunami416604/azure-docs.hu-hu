---
title: Létrehozása és kezelése az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet igényelhető virtuális gépek |} A Microsoft Docs
description: Ismerje meg, hogyan igényelhető virtuális gép hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2018
ms.author: spelluru
ms.openlocfilehash: 3bfb674fa66f0701a099d237f4e760453c7b6a6e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232127"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Létrehozása és kezelése az Azure DevTest Labs szolgáltatásban igényelhető virtuális gépek
Igényelhető virtuális gép egy laborhoz hasonló módon, hogy miként hozzáadása, [standard virtuális gép hozzáadása](devtest-lab-add-vm.md) – egy *alap* , amely vagy egy [egyéni rendszerkép](devtest-lab-create-template.md), [képlet](devtest-lab-manage-formulas.md) , vagy [Piactéri lemezképhez](devtest-lab-configure-marketplace-images.md). Ez az oktatóanyag végigvezeti a DevTest Labs szolgáltatásban létrehozott tesztkörnyezet igényelhető virtuális gép hozzáadása az Azure portal használatával, és bemutatja a folyamatok, a felhasználó követi, és a virtuális gép mintaszkript.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Igényelhető virtuális gép hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet lépései
1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
1. Tesztkörnyezetek listájában jelölje ki a tesztkörnyezetben, amelyben meg szeretné a igényelhető virtuális gép létrehozásához.  
1. A laborgyakorlat **áttekintése** ablaktáblán válassza előbb **+ Hozzáadás**.  

    ![Virtuális gép gomb hozzáadása](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. Az a **vyberte bázi** területen válasszon ki egy alap virtuális gép.
1. Az a **virtuális gép** panelen adjon meg egy nevet az új virtuális gép a **virtuális gép neve** szövegmező.

    ![Lab VM panel](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. Adjon meg egy **felhasználónév** kap, amely rendszergazdai jogosultságokkal a virtuális gépen.  
1. Ha azt szeretné, a tárolt jelszó használatát a [titkoskód-tárolót](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store)válassza **mentett titkos kulcs használata**, és adja meg a kulcs értékét, amely megfelel a titkos kód (jelszó). Ellenkező esetben a címkével ellátott szövegmezőbe írja be a jelszót **adjon meg egy értéket**.
1. A **virtuálisgép-lemeztípust** határozza meg, melyik tárolólemez-típusba engedélyezett a virtuális gépek a tesztkörnyezetben.
1. Válassza ki **virtuálisgép-méret** válasszon ki egy, az előre definiált elemek, amelyek a Processzormagok, memória mérete és a merevlemez mérete, a virtuális gép létrehozásához.
1. Válassza ki **összetevők** és összetevők a listából válassza ki és konfigurálja az alaprendszerképet hozzáadni kívánt összetevők. Ha most ismerkedik a DevTest Labs, vagy konfigurálása összetevők, tekintse meg a [hozzáadása egy meglévő összetevő egy virtuális Géphez](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) szakaszt, és ezután térjen vissza ide, ha befejeződött.
1. Válassza ki **speciális beállítások** a virtuális gép hálózati beállításai, és a lejárati beállítások konfigurálása. A **jogcím-beállítások**, válassza a **Igen** , hogy a gép igényelhető.

  ![Válassza ki, hogy a virtuális gép igényelhető.](./media/devtest-lab-add-vm/devtestlab-claim-VM-option.png)

1. Ha szeretné megtekintése és másolása az Azure Resource Manager-sablont, tekintse meg a [mentése az Azure Resource Manager-sablon](devtest-lab-add-vm.md#save-azure-resource-manager-template) szakaszt, és térjen vissza ide, ha befejeződött.
1. Válassza ki **létrehozás** a megadott virtuális gép hozzáadása a tesztkörnyezetben.

   A virtuális gép létrehozásának állapota jelenik meg, mint első **létrehozása**, majd **futó** a virtuális gép elindítása után.

> [!NOTE]
> – A labor virtuális gépek telepítésekor [Azure Resource Manager-sablonok](devtest-lab-create-environment-from-arm.md), igényelhető virtuális gépek úgy hozhat létre a **allowClaim** tulajdonság igaz értékre a Tulajdonságok szakaszában.
>
>

## <a name="using-a-claimable-vm"></a>Igényelhető virtuális gépek használatával

A felhasználó igényelhet bármely virtuális Gépet a listából a "Igényelhető virtuális gépek" egyet az alábbi lépések végrehajtásával:

* "Igényelhető virtuális gépnek" a labor "Áttekintés" panel alján a listából, kattintson a jobb gombbal a virtuális gépek, a lista egyik, és válassza a **jogcím gép**.

 ![A kérelem egy adott igényelhető virtuális gép.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Válassza az "Áttekintés" panel tetején lévő **minden jogcím**. Egy véletlenszerű virtuális gépnek kioszt igényelhető virtuális gépek listájából.

 ![Igényelhető virtuális Gépeket kérelmet.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Után egy felhasználói jogcímek egy virtuális Gépet, a "Saját virtuális gépnek" listájába kerül, és már nem igényelhető, amelyet semmilyen más felhasználó.

## <a name="unclaim-a-vm"></a>Virtuális gép mintaszkript

Amikor egy felhasználó igényelt virtuális gép használatának befejezése után, és szeretné, valaki más számára elérhető legyen, azok visszatérhet a hivatkozott virtuális gép igényelhető virtuális gépek listáját az alábbi lépések elvégzésével:

- "A virtuális gépnek" a listából, kattintson a jobb gombbal a lista – a virtuális gépek egyik vagy válassza a három pontra (...) –, majd **Unclaim**.

  ![A virtuális gépek listájának a virtuális gép mintaszkript.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- A "Saját virtuális gépnek" listájából válassza ki a felügyeleti panel megnyitásához, majd válassza ki a virtuális gép **Unclaim** a felső menüsorban.

  ![Egy virtuális Gépet a VM-kezelés panel mintaszkript.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Amikor egy felhasználó unclaims egy virtuális Gépet, azokat engedélyeket arra, hogy adott laborbeli virtuális gép többé nem kell.

### <a name="transferring-the-data-disk"></a>Az adatlemez átvitele
Ha egy igényelhető virtuális gépen, és a egy felhasználó csatlakozik adatlemez unclaims azt, az adatlemezt a virtuális gép mindig fennmarad. Ha egy másik felhasználó majd jogcímek, hogy a virtuális Gépen, hogy új felhasználói jogcímek az adatlemezt, valamint a virtuális gép.

Ez az úgynevezett "átvitele az adatlemez". Az adatlemez lesz elérhető az új felhasználó listájában **Moje datové disky** kezelheti azokat.

![Adatlemezek mintaszkript.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>További lépések
* A létrehozást követően csatlakozhat a virtuális gép kiválasztásával **Connect** annak-kezelés panel.
* Fedezze fel a [DevTest Labs Azure Resource Manager gyorsindítási sablon galéria](https://github.com/Azure/azure-devtestlab/tree/master/Samples).

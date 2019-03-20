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
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: f83ee40c97e49add539a44a6ba764024a8e47bfa
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58081491"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Létrehozása és kezelése az Azure DevTest Labs szolgáltatásban igényelhető virtuális gépek
Igényelhető virtuális gép egy laborhoz hasonló módon, hogy miként hozzáadása, [standard virtuális gép hozzáadása](devtest-lab-add-vm.md) – egy *alap* , amely vagy egy [egyéni rendszerkép](devtest-lab-create-template.md), [képlet](devtest-lab-manage-formulas.md) , vagy [Piactéri lemezképhez](devtest-lab-configure-marketplace-images.md). Ez az oktatóanyag végigvezeti a DevTest Labs szolgáltatásban létrehozott tesztkörnyezet igényelhető virtuális gép hozzáadása az Azure portal használatával, és bemutatja a folyamatok, a felhasználó követi, és a virtuális gép mintaszkript.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Igényelhető virtuális gép hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet lépései
1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a a **fejlesztési és üzemeltetési** szakaszban. Ha * (csillag) melletti **DevTest Labs** a a **fejlesztési és üzemeltetési** szakaszban. Ez a művelet felveszi **DevTest Labs** , a bal oldali navigációs menü, hogy hozzá tud férni egyszerűen legközelebb. Ezután kiválaszthatja **DevTest Labs** a bal oldali navigációs menüben.

    ![Minden szolgáltatás – válassza ki a DevTest Labs szolgáltatásban](./media/devtest-lab-create-lab/all-services-select.png)
1. Tesztkörnyezetek listájában jelölje ki a tesztkörnyezetben, amelyben meg szeretné a virtuális gép létrehozásához.  
2. A laborgyakorlat **áttekintése** lapon jelölje be **+ Hozzáadás**.  

    ![Virtuális gép gomb hozzáadása](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Az a **vyberte bázi** lapra, jelölje be a Piactéri lemezképet a virtuális gép számára.
1. Az a **alapbeállítások** lapján a **virtuális gép** lapon, tegye a következőket: 
    1. Adjon meg egy nevet a virtuális géphez a **virtuális gép neve** szövegmezőben. A szövegmező előre kitöltött, az Ön számára automatikusan létrehozott egyedi nevére. A felhasználónév az e-mail-címét és egy egyedi 3-jegyű szám belül felel meg a nevét. Ez a funkció menti az idő, gondolja át, hogy a számítógép nevét, és írja be, minden alkalommal, amikor a gép létrehozása. Ha szeretné felül lehet bírálni ezt automatikusan kitölti a mezőt a választott nevét. Felülbírálja a virtuális gép automatikusan kitöltött nevét, adja meg egy nevet a a **virtuális gép neve** szövegmezőben.
    2. Adjon meg egy **felhasználónév** kap, amely rendszergazdai jogosultságokkal a virtuális gépen. A **felhasználónév** a gép van előre kitöltött egyedi automatikusan létrehozott névvel. A név felel meg az e-mail-címét a felhasználói nevet. Ez a funkció menti az idő dönthet arról, hogy a felhasználónév minden alkalommal, amikor létrehoz egy új gépet. Felülbírálhatja ismét ezt automatikusan kitölti a mezőt egy tetszőleges felhasználónevet, ha szeretné. Felhasználónév automatikusan kitöltött értékének felülbírálásához adjon meg egy értéket a **felhasználónév** szövegmezőben. Ez a felhasználó számára biztosított **rendszergazda** jogosultságokkal a virtuális gépen.
    3. Ha az első virtuális gép létrehozása a lab-ben esetén adja meg egy **jelszó** a felhasználó számára. Mentse ezt a jelszót a labor társított Azure key vaultban lévő alapértelmezett jelszóként, jelölje be a **alapértelmezett jelszó mentése**. Az alapértelmezett jelszó mentése a key vaulttal és a neve: **VmPassword**. Amikor további virtuális gépek létrehozása a lab-ben próbál **VmPassword** automatikusan ki van jelölve az a **jelszó**. Bírálja felül az értéket, törölje a jelet a **mentett titkos kulcs használata** jelölőnégyzetet, majd adjon meg egy jelszót. 

        Is mentheti a titkos kulcsok a key vaultban először, és majd használata közben a virtuális gép létrehozása a lab-ben. További információkért lásd: [Store titkos kulcsok a key vault](devtest-lab-store-secrets-in-key-vault.md). A key vaultban tárolt jelszó használatához válassza **mentett titkos kulcs használata**, és adja meg a kulcs értékét, amely megfelel a titkos kód (jelszó).
    4. Az a **további beállítások** szakaszban jelölje be **méretének módosítása**. Válassza ki, amelyek a Processzormagok, memória mérete és a merevlemez mérete, a virtuális gép létrehozása az előre meghatározott lehetőségek közül.
    5. Válassza ki **hozzáadása vagy eltávolítása összetevők**. Válassza ki, és konfigurálja az alaprendszerképet hozzáadni kívánt összetevők.
    **Megjegyzés:** Ha most ismerkedik a DevTest Labs, vagy konfigurálása összetevők, tekintse meg a [hozzáadása egy meglévő összetevő egy virtuális Géphez](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) szakaszt, és ezután térjen vissza ide, ha befejeződött.
2. Váltson a **speciális beállítások** felső részén látható, és végezze el a következő műveleteket:
    1. Ha módosítani szeretné a virtuális hálózat, amely a virtuális gép, jelölje be a **módosítsa a virtuális hálózat**. 
    2. Az alhálózat módosításához válasszon **alhálózat módosítása**. 
    3. Adja meg, hogy a virtuális gép IP-címét **nyilvános, magán és megosztott**. 
    4. A virtuális gép automatikusan törléséhez adja meg a **lejárati dátumát és időpontját**. 
    5. Válassza ki, hogy a virtuális gép igényelhető a lab-felhasználó, **Igen** a **győződjön meg arról, a gép igényelhető** lehetőséget. 
    6. Adja meg a **Virtuálisgép-példányok** , hogy szeretné-e a labor számára elérhetővé tenni. 
3. Válassza ki **létrehozás** a megadott virtuális gép hozzáadása a tesztkörnyezetben.

   A lab lap állapotát jeleníti meg a virtuális gép létrehozása – először mint **létrehozása**, majd, mint **futó** a virtuális gép elindítása után.

> [!NOTE]
> – A labor virtuális gépek telepítésekor [Azure Resource Manager-sablonok](devtest-lab-create-environment-from-arm.md), igényelhető virtuális gépek úgy hozhat létre a **allowClaim** tulajdonság igaz értékre a Tulajdonságok szakaszában.


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

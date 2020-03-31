---
title: Követelésre hivatkozható virtuális gépek létrehozása és kezelése az Azure DevTest Labsben | Microsoft dokumentumok
description: Megtudhatja, hogyan adhat hozzá egy igényelhető virtuális gépet egy laborhoz az Azure DevTest Labs ben
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
ms.openlocfilehash: 13d642597fdf5d0eae6c6fd4f0cab16181f033c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270796"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Követelésre hivatkozható virtuális gépek létrehozása és kezelése az Azure DevTest Labsben
Egy igény szerint cserélhető virtuális gép hozzáadása a laborhoz hasonlóan, hogy hogyan [adja hozzá a szabványos virtuális gép](devtest-lab-add-vm.md) – egy *alap,* amely egy [egyéni lemezkép,](devtest-lab-create-template.md) [képlet](devtest-lab-manage-formulas.md), vagy [marketplace-lemezkép.](devtest-lab-configure-marketplace-images.md) Ez az oktatóanyag bemutatja az Azure Portal használatával egy igény szerint elérhető virtuális gép hozzáadása a DevTest Labs laborjában, és bemutatja a folyamatokat a felhasználó követi a követelés, és a virtuális gép kikövetelése.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>A követelhető virtuális gép hozzáadása az Azure DevTest Labs laborjában
1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
1. Válassza az **Összes szolgáltatás**lehetőséget, majd a **DEVOPS** szakasz **DevTest Labs** lehetőséget. Ha a DEVOPS szakasz **DevTest Labs** mellett a * (csillag) lehetőséget **választja.** Ez a művelet hozzáadja a **DevTest Labs-t** a bal oldali navigációs menühöz, így a következő alkalommal könnyen elérheti azt. Ezután kiválaszthatja **a DevTest Labs** a bal oldali navigációs menüben.

    ![Minden szolgáltatás - válassza a DevTest Labs lehetőséget](./media/devtest-lab-create-lab/all-services-select.png)
1. A laborok listájából válassza ki azt a tesztkörnyezetet, amelyben létre szeretné hozni a virtuális gép.
2. A tesztkörnyezet **áttekintése** lapon válassza a **+ Hozzáadás**lehetőséget.

    ![Virtuális gép hozzáadása gomb](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. A **Válasszon ki egy alaplapot,** válasszon ki egy piactéri lemezképet a virtuális géphez.
1. A **Virtuálisgép** lap **Alapbeállítások** lapján hajtsa végre a következő műveleteket:
    1. Adja meg a virtuális gép nevét a **virtuális gép neve** mezőbe. A szövegdoboz előre ki van töltve egy egyedi, automatikusan generált névvel. A név megfelel az e-mail címben lévő felhasználónévnek, amelyet egy egyedi 3 jegyű szám követ. Ez a funkció időt takarít meg, hogy gondoljon egy gép nevére, és írja be minden alkalommal, amikor létrehoz egy gépet. Ezt az automatikusan kitöltött mezőt felülírhatja egy ön által választott névvel, ha szeretné. A virtuális gép automatikuskitöltésű nevének felülbírálásához írjon be egy nevet a **Virtuálisgép neve** mezőbe.
    2. Adjon meg egy rendszergazdai jogosultságokkal rendelkező **felhasználónevet** a virtuális gépen. A számítógép **felhasználóneve** előre ki van töltve egy egyedi, automatikusan generált névvel. A név az e-mail címben szereplő felhasználónévnek felel meg. Ezzel a funkcióval időt takaríthat meg, hogy minden alkalommal döntsön a felhasználónévről, amikor új gépet hoz létre. Ismét felülbírálhatja ezt az automatikusan kitöltött mezőt egy ön által választott felhasználónévvel, ha szeretné. A felhasználónév automatikus kitöltési értékének felülbírálásához írjon be egy értéket a **Felhasználónév** mezőbe. Ez a felhasználó **rendszergazdai** jogosultságokat kap a virtuális gépen.
    3. Ha az első virtuális gép létrehozása a laborban, adja meg a **jelszót** a felhasználó számára. Ha ezt a jelszót alapértelmezett jelszóként szeretné menteni a laborhoz társított Azure-kulcstartóba, válassza a **Mentés alapértelmezett jelszóként**lehetőséget. Az alapértelmezett jelszót a program a következő névvel menti a key vaultba: **VmPassword**. Amikor megpróbálja létrehozni a következő virtuális gépeket a tesztkörnyezetben, a **VmPassword** automatikusan kiválasztódik a **jelszóhoz.** Az érték felülbírálásához törölje a jelet a **Mentett titkos titok használata** jelölőnégyzetből, és adjon meg egy jelszót.

        A titkos kulcsokat is mentheti a key vault először, majd használja, miközben egy virtuális gép a laborban létrehozása közben. További információ: [Titkos kulcsok tárolása a kulcstartóban](devtest-lab-store-secrets-in-key-vault.md)című témakörben talál. A kulcstartóban tárolt jelszó használatához jelölje be a **Mentett titkos kulcs használata**lehetőséget, és adjon meg egy, a titkos kulcsnak (jelszónak) megfelelő kulcsértéket.
    4. A **További beállítások** csoportban válassza a **Méret módosítása**lehetőséget. Válassza ki az egyik előre definiált elemeket, amelyek meghatározzák a processzormagok, RAM-méret, és a merevlemez mérete a virtuális gép létrehozásához.
    5. Válassza **az Összetevők hozzáadása vagy eltávolítása**lehetőséget. Jelölje ki és konfigurálja az alaplemezképhez hozzáadni kívánt összetevőket.
    **Megjegyzés:** Ha most ismerkedik a DevTest Labs vagy az összetevők konfigurálása, olvassa el a [meglévő műtermék hozzáadása egy virtuális géphez](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) szakaszban, majd térjen vissza ide, ha kész.
2. Váltson a felső **speciális beállítások** lapra, és hajtsa végre a következő műveleteket:
    1. A virtuális hálózat módosításához, amelyben a virtuális gép található, válassza a **Virtuális hálózat módosítása**lehetőséget.
    2. Az alhálózat módosításához válassza az **Alhálózat módosítása**lehetőséget.
    3. Adja meg, hogy a virtuális gép IP-címe **nyilvános, privát vagy megosztott.**
    4. A virtuális gép automatikus törléséhez adja meg a **lejárati dátumot és az időt.**
    5. Ha azt szeretné, hogy a virtuális gép igény szerint egy tesztkörnyezet-felhasználó, válassza az **Igen** a **gép igény alá vehető** beállítás lehetőséget.
    6. Adja meg a **virtuális gép azon példányainak** számát, amelyeket elérhetővé szeretne tenni a tesztkörnyezet felhasználói számára.
3. Válassza **a Létrehozás** lehetőséget a megadott virtuális gép hozzáadása a laborhoz.

   A laborlap megjeleníti a virtuális gép létrehozásának állapotát – először **létrehozás,** majd a virtuális gép indítása után **futó állapot.**

> [!NOTE]
> Ha az [Azure Resource Manager-sablonokon](devtest-lab-create-environment-from-arm.md)keresztül telepíti a tesztkörnyezetben lévő virtuális gépeket, a tulajdonságok szakaszban az **allowClaim** tulajdonság true értékre állításával jogcímezhető virtuális gépeket hozhat létre.


## <a name="using-a-claimable-vm"></a>Követelésre hivatkozható virtuális gép használata

A felhasználók az alábbi lépések egyikével igényelhetnek bármely virtuális gépet a "Követelhető virtuális gépek" listájából:

* A labor "Áttekintés" ablaktábla alján található "Igényelhető virtuális gépek" listájából kattintson a jobb gombbal a listában szereplő virtuális gépek egyikére, és válassza a **Jogcímgép parancsot.**

  ![Egy adott követelhető virtuális gép kérése.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Az "Áttekintés" ablaktábla tetején válassza a **Jogcím kiválasztása**lehetőséget. Egy véletlenszerű virtuális gép van rendelve a jogcímezhető virtuális gépek listájából.

  ![Kérjen követeléssel nem cserélhető virtuális gép.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Miután a felhasználó azt állítja, a virtuális gép, DevTest Labs elindítja a gépet, és helyezze fel a laborfelhasználói "A virtuális gépek" listájába. Ez azt jelenti, hogy a tesztkörnyezet-felhasználó most már rendelkezik tulajdonosi jogosultságokkal ezen a számítógépen. A lépéshez szükséges idő az indítási időktől, valamint a jogcímesemény során végrehajtott egyéb egyéni műveletektől függően változhat. Miután igényelte, a gép már nem érhető el a követelhető készletben.  

## <a name="unclaim-a-vm"></a>Virtuális gép visszaigénylése

Ha egy felhasználó befejezte egy igényelt virtuális gép használatát, és azt valaki más számára is elérhetővé szeretné tenni, az alábbi lépések egyikével visszaküldheti az igényelt virtuális gépet a követelhető virtuális gépek listájára:

- A "Virtuális gépeim" listájában kattintson a jobb gombbal a listában szereplő virtuális gépek egyikére – vagy válassza ki a három pontot (...) – és válassza a **Jogcímjog parancsot.**

  ![Virtuális gép kikövetelése a virtuális gép listájában.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- A "Virtuális gépeim" listában válasszon egy virtuális gépet a felügyeleti ablaktábla megnyitásához, majd válassza a legfelső menüsor **Kimondottak** listáját.

  ![Virtuális gép kikövetelése a virtuális gép felügyeleti ablaktábláján.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Amikor egy felhasználó kiállít egy virtuális gép, már nem rendelkeznek tulajdonosi engedélyekkel az adott labor virtuális gép, és elérhető, hogy azt állította bármely más tesztkörnyezet-felhasználó az állapot, hogy a készletre lett retúrult. 

### <a name="transferring-the-data-disk"></a>Az adatlemez átvitele
Ha egy jogcímzésre jogosult virtuális géphez egy adatlemez van csatolva, és a felhasználó nem állítja, az adatlemez a virtuális gépnél marad. Amikor egy másik felhasználó majd azt állítja, hogy a virtuális gép, hogy az új felhasználó azt állítja, az adatlemez, valamint a virtuális gép.

Ez az úgynevezett "átvitele az adatlemez". Az adatlemez ekkor elérhetővé válik az új felhasználó **Saját adatlemezek** listájában, amelyeket kezelniük kell.

![Adatlemezek visszaszerzésének kijoglazon állítása.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>További lépések
* Létrehozása után csatlakozhat a virtuális géphez a Felügyeleti ablaktáblán a **Csatlakozás** lehetőség kiválasztásával.
* Fedezze fel a [DevTest Labs Azure Resource Manager gyorsstartsablon-gyűjteményét.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)

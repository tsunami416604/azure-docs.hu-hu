---
title: Virtuális gép hozzáadása egy tesztkörnyezethez az Azure DevTest Labs ben | Microsoft dokumentumok
description: Megtudhatja, hogyan adhat hozzá virtuális gépet egy laborhoz az Azure DevTest Labsben
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: 22060cc6dd5eb15e81a0c397a7b0255f16780d74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284251"
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Virtuális gép hozzáadása egy laborhoz az Azure DevTest Labsben
Ha már [létrehozta az első virtuális gépét,](tutorial-create-custom-lab.md#add-a-vm-to-the-lab)valószínűleg egy előre betöltött [piactéri lemezképből](devtest-lab-configure-marketplace-images.md)hozta létre. Most, ha további virtuális gépeket szeretne hozzáadni a tesztkörnyezethez, választhat egy *olyan bázist* is, amely [egyéni lemezkép](devtest-lab-create-template.md) vagy [képlet.](devtest-lab-manage-formulas.md) Ez az oktatóanyag végigvezeti az Azure Portal használatával egy virtuális gép hozzáadása a DevTest Labs egy laborban.

Ez a cikk azt is bemutatja, hogyan kezelheti a virtuális gép összetevőit a laborban.

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>Virtuális gép hozzáadása az Azure DevTest Labs laborjába
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

        ![Alap kiválasztása](./media/tutorial-create-custom-lab/new-virtual-machine.png)

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

        ![Alap kiválasztása](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
1. Válassza **a Létrehozás** lehetőséget a megadott virtuális gép hozzáadása a laborhoz.

   A laborlap megjeleníti a virtuális gép létrehozásának állapotát – először **létrehozás,** majd a virtuális gép indítása után **futó állapot.**

    ![Virtuális gép létrehozásának állapota](./media/tutorial-create-custom-lab/vm-creation-status.png)

## <a name="add-an-existing-artifact-to-a-vm"></a>Meglévő műtermék hozzáadása virtuális géphez
Virtuális gép létrehozása közben meglévő összetevők et adhat hozzá. Minden egyes tesztkörnyezet tartalmazza a nyilvános devtest labs műtermék-tárházból származó összetevőket, valamint a saját műtermék-tárházhoz hozzáadott és hozzáadott összetevőket.

* Az Azure DevTest Labs összetevői lehetővé *teszik* a virtuális gép kiépítésekor végrehajtott *műveletek,* például a Windows PowerShell-parancsfájlok futtatása, a Bash-parancsok futtatása és a szoftverek telepítése.
* A *műtermék-paraméterek* segítségével testreszabhatja az összetevőt az adott forgatókönyvhöz

Az összetevők létrehozásáról a Következő témakörben [olvashat: Ismerje meg, hogyan készíthet saját műtermékeket a DevTest Labs alkalmazáshoz.](devtest-lab-artifact-author.md)

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
1. Válassza az **Összes szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
1. A laborok listájából válassza ki azt a tesztkörnyezetet, amely a kívánt virtuális gép kívánt dolgozni.
1. Válassza **a Saját virtuális gépek**lehetőséget .
1. Válassza ki a kívánt virtuális gép.
1. Válassza **az Összetevők kezelése**lehetőséget.
1. Válassza **az Összetevők alkalmazása**lehetőséget.
1. Az **Összetevők alkalmazása** ablaktáblán jelölje ki a virtuális géphez hozzáadni kívánt összetevőt.
1. A **Műtermék hozzáadása** ablaktáblán adja meg a szükséges paraméterértékeket és a szükséges választható paramétereket.
1. Válassza a **Hozzáadás** lehetőséget a műtermék hozzáadásához és a **Műtermékek alkalmazása** ablaktáblához való visszatéréshez.
1. Folytassa az összetevők hozzáadását a virtuális géphez szükség szerint.
1. Miután hozzáadta az összetevőket, [módosíthatja az összetevők futtatásának sorrendjét.](#change-the-order-in-which-artifacts-are-run) Visszatérhet az összetevő [megtekintéséhez vagy módosításához](#view-or-modify-an-artifact)is.
1. Ha végzett az összetevők hozzáadásával, válassza **az Alkalmaz lehetőséget.**

## <a name="change-the-order-in-which-artifacts-are-run"></a>Az összetevők futtatásának sorrendjének módosítása
Alapértelmezés szerint az összetevők műveletek végrehajtása abban a sorrendben, amelyben hozzáadódnak a virtuális géphez.
A következő lépések bemutatják, hogyan módosíthatja az összetevők futtatásának sorrendjét.

1. Az **Összetevők alkalmazása** ablaktábla tetején jelölje ki a virtuális géphez hozzáadott összetevők számát jelző hivatkozást.

    ![A virtuális géphez hozzáadott összetevők száma](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. A **Kijelölt összetevők** ablaktáblán húzza az összetevőket a kívánt sorrendbe. **Megjegyzés:** Ha nem tudja áthúzni a műtárgyat, győződjön meg arról, hogy a műtermék bal oldaláról húzza.
1. Kattintson az **OK** gombra, amikor végzett.

## <a name="view-or-modify-an-artifact"></a>Műtermék megtekintése vagy módosítása
A következő lépések bemutatják, hogyan tekintheti meg vagy módosíthatja egy műtermék paramétereit:

1. Az **Összetevők alkalmazása** ablaktábla tetején jelölje ki a virtuális géphez hozzáadott összetevők számát jelző hivatkozást.

    ![A virtuális géphez hozzáadott összetevők száma](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. A **Kijelölt összetevők** ablaktáblán jelölje ki a megtekinteni vagy szerkesztni kívánt műterméket.
1. A **Műtermék hozzáadása** ablaktáblán hajtsa végre a szükséges módosításokat, és az **OK** gombra a **Műtermék hozzáadása** ablaktábla bezárásához.
1. A Kijelölt összetevők ablaktábla bezárásához válassza az **OK** **gombot.**

## <a name="save-azure-resource-manager-template"></a>Az Azure Resource Manager sablon mentése
Az Azure Resource Manager-sablon deklaratív módon határozza meg az ismételhető központi telepítést.
Az alábbi lépések bemutatják, hogyan mentheti az Azure Resource Manager sablont a létrehozott virtuális géphez.
Mentés után az Azure Resource Manager sablon használatával új virtuális gépeket helyezhet üzembe az [Azure PowerShell használatával.](../azure-resource-manager/templates/overview.md)

1. A **Virtuálisgép** ablaktáblán válassza az **Azure Resource Manager-sablon megtekintése**lehetőséget.
2. Az **Azure Resource Manager megtekintése sablon** ablaktáblán jelölje ki a sablon szövegét.
3. A kijelölt szöveg másolása a vágólapra
4. Az **AZURE Resource Manager-sablon ablaktábla**bezárásához válassza az **OK gombot.**
5. Nyisson meg egy szövegszerkesztőt.
6. Illessze be a sablon szövegét a vágólapról.
7. Mentse a fájlt későbbi használatra.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
* A virtuális gép létrehozása után csatlakozhat a virtuális géphez a **Virtuálisgép** ablaktábláján a Csatlakozás lehetőség kiválasztásával.
* Ismerje meg, hogyan [hozhat létre egyéni összetevőket a DevTest Labs virtuális géphez.](devtest-lab-artifact-author.md)
* Fedezze fel a [DevTest Labs Azure Resource Manager QuickStart sablongyűjteményét.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)

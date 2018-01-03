---
title: "Csatolása vagy leválasztása a Azure DevTest Labs szolgáltatásban virtuális géphez adatlemezt |} Microsoft Docs"
description: "Megtudhatja, hogyan csatolása vagy leválasztása adatlemezt a Azure DevTest Labs szolgáltatásban virtuális géphez"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 9616bf38-7db8-4915-a32a-e4f40a7a56ad
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: v-craic
ms.openlocfilehash: c1f83097fe4e5da3a46f693d37b001dbb6831527
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Csatolása vagy leválasztása adatlemezt a Azure DevTest Labs szolgáltatásban virtuális géphez
[Azure-lemezeket felügyelt](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) kezeli a virtuális gép adatlemezek társított tárfiókokat. Egy felhasználó nevű új lemezre egy virtuális géphez, típusa és szükséges lemez méretét adja meg, és az Azure hoz létre, és automatikusan kezeli a lemezt. Az adatlemez majd a virtuális gép választható le, és vagy később az azonos virtuális géphez, vagy egy másik virtuális géphez, amely ugyanahhoz a felhasználóhoz tartozik csatolt objektumkörnyezetben.

Ez a funkció elérhető tárhely vagy szoftver kívül minden különálló virtuális gép. Ha a tároló- és szoftver már létezik adatlemezt belül, azt is könnyen csatolt, leválasztott, és minden virtuális géphez adott adatlemez birtokló felhasználó tulajdonában lévő objektumkörnyezetben.

## <a name="attach-a-data-disk"></a>Adatlemez csatolása
Mielőtt adatlemezt csatol egy virtuális Gépre, tekintse át a ezek a tippek:

- A virtuális gép mérete csatolhat hány adatlemezek szabályozza. További információkért lásd: [virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- Adatlemez csak csatlakoztatása egy virtuális Gépet futtató. Győződjön meg arról, hogy a virtuális gép fusson, mielőtt újból adatlemezt csatolni.

### <a name="attach-a-new-disk"></a>Új lemez csatolása
Kövesse az alábbi lépéseket létrehozásához, és új felügyelt adatlemezt csatolni egy virtuális Gépet az Azure DevTest Labs szolgáltatásban.

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **több szolgáltatások**, majd válassza ki **DevTest Labs** a listából.
1. Válassza ki a kívánt labor labs listájának megtekintéséhez. 
1. A listájáról **a virtuális gépek**, jelöljön ki egy futó virtuális Gépet.
1. A bal oldali menüben válassza ki a **lemezek**.

    ![Válassza ki a virtuális gép adatlemezek](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png)
1. Válasszon **új Attach** hozzon létre egy új adatlemez, és csatlakoztassa a virtuális Gépet.

    ![Új adatlemez csatlakoztatása egy virtuális géphez](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Fejezze be a **új lemez csatolása** panelen írja be az adatok lemez nevét, típusát és méretét.

    ![Fejezze be az "új lemez csatolása" képernyő](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Kattintson az **OK** gombra.

Néhány másodpercen belül az új adatok lemez létrehozása, és a virtuális Géphez csatlakozik, és megjelenik a listán a **ADATLEMEZEK** ezt a virtuális gépet.

### <a name="attach-an-existing-disk"></a>Meglévő lemez csatlakoztatása
A lépések végrehajtásával csatlakoztassa újra a rendelkezésre álló adatok meglévő lemez egy futó virtuális géphez. 

1. Válassza ki a adatlemezt újracsatolni kívánt virtuális gép.
1. A bal oldali menüben válassza ki a **lemezek**.
1. Válassza ki **csatolni a meglévő** a rendelkezésre álló adatok lemez csatolása a virtuális géphez.

    ![Meglévő adatlemez csatlakoztatása egy virtuális géphez](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing2.png)

1. Az a **meglévő lemez csatolása** ablaktáblában válassza OK gombra.

    ![Meglévő adatlemez csatlakoztatása egy virtuális géphez](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Néhány másodpercen belül a adatlemez a virtuális Géphez van csatolva, és megjelenik a listán a **ADATLEMEZEK** ezt a virtuális gépet.

## <a name="detach-a-data-disk"></a>Adatlemez leválasztása
Ha már nincs szüksége egy virtuális géphez csatolt adatlemezt, könnyen leválasztás. Leválasztás a lemez eltávolítása a virtuális gépről, de tárolása a tárolási későbbi használatra.

Ha szeretné újra használni a meglévő adatok a lemezen, is csatlakoztassa újra az azonos virtuális gépen, vagy egy másikra.

### <a name="detach-from-the-vms-management-pane"></a>Válassza le a virtuális gép felügyeleti ablaktábla
1. A virtuális gépek listájából válassza ki, amely adatok lemez tartozik.
1. A bal oldali menüben válassza ki a **lemezek**.

    ![Válassza ki a virtuális gép adatlemezek](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png) 
1. A listájáról **ADATLEMEZEK**, válassza ki a leválasztani kívánt adatlemeze.
1. Válassza ki **leválasztási** teteje és a lemez részleteket tartalmazó ablaktáblán.

    ![Adatlemez leválasztása](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Válassza ki **Igen** annak ellenőrzéséhez, hogy szeretné-e a adatlemez leválasztása.

A lemez leválasztott és érhető el egy másik virtuális géphez csatolása. 
### <a name="detach-from-the-labs-main-pane"></a>Válassza le a labor főpanelen
1. A tesztkörnyezet főpanelen válassza **az adatlemezek**.

    ![A labor adatlemezek eléréséhez](./media/devtest-lab-attach-detach-data-disk/devtest-lab-my-data-disks.png)
1. Kattintson a jobb gombbal a adatlemez leválasztása –, vagy válassza ki a három ponttal (…) –, és válassza a kívánt **leválasztási**.

    ![Adatlemez leválasztása](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Válassza ki **Igen** annak ellenőrzéséhez, hogy szeretné-e válassza le azt.

   > [!NOTE]
   > Ha adatlemezt már le van választva, választhat, távolítsa el a rendelkezésre álló adatlemezek lista kiválasztásával **törlése**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Egy nem felügyelt adatok lemez frissítése
Ha egy meglévő virtuális gép nem felügyelt adatok lemezeket használó, egyszerűen válthat a virtuális Gépet felügyelt lemezeket használni. Ez a folyamat az operációsrendszer-lemez és a mellékelt adatok lemezzel alakítja át.

Egy nem felügyelt adatlemez frissítéséhez kövesse az ebben a cikkben leírt lépéseket [a adatlemez leválasztása](#detach-a-data-disk) egy nem felügyelt virtuális gépről. Ezt követően [csatlakoztassa újra a lemezt](#attach-an-existing-disk) egy felügyelt virtuális Gépen, hogy automatikusan frissítse az adatokat a lemezt a felügyelt kódba.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
Az adatlemezek kezelése [claimable virtuális gépek](devtest-lab-add-claimable-vm.md#unclaim-a-vm).


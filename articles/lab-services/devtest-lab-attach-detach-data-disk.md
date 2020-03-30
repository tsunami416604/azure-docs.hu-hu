---
title: Adatlemez csatolása vagy leválasztása egy virtuális géphez az Azure DevTest Labs ben
description: Ismerje meg, hogyan csatolhat vagy választhat le adatlemezt egy virtuális géphez az Azure DevTest Labs szolgáltatásban
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 9616bf38-7db8-4915-a32a-e4f40a7a56ad
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2020
ms.author: spelluru
ms.openlocfilehash: 3f18425408e6526904db85eae1c3a4db41d11a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78198802"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Adatlemez csatolása vagy leválasztása egy virtuális géphez az Azure DevTest Labs ben
[Az Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) kezeli a virtuális gép adatlemezeihez társított tárfiókokat. A felhasználó új adatlemezt csatol a virtuális géphez, meghatározza a szükséges lemez típusát és méretét, és az Azure automatikusan létrehozza és kezeli a lemezt. Az adatlemez ezután leválasztható a virtuális gépről, és később visszacsatolható ugyanahhoz a virtuális géphez, vagy csatolható egy másik virtuális géphez, amely ugyanahhoz a felhasználóhoz tartozik.

Ez a funkció hasznos az egyes virtuális gépeken kívüli tárolás vagy szoftverek kezeléséhez. Ha a tároló vagy szoftver már létezik egy adatlemezen belül, könnyen csatolható, leválasztható és visszacsatolható bármely olyan virtuális géphez, amely az adatlemez tanusítja.

## <a name="attach-a-data-disk"></a>Adatlemez csatolása
Mielőtt adatlemezt csatolna egy virtuális géphez, tekintse át az alábbi tippeket:

- A virtuális gép mérete határozza meg, hogy hány adatlemez csatlakoztatható. További információt a [Virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- Csak akkor csatolhat egy adatlemezt egy futó virtuális géphez. Győződjön meg arról, hogy a virtuális gép fut, mielőtt megpróbálna csatolni egy adatlemezt.

### <a name="attach-a-new-disk"></a>Új lemez csatolása
Az alábbi lépésekkel hozzon létre és csatoljon egy új felügyelt adatlemezt egy virtuális géphez az Azure DevTest Labs-ben.

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
1. Válassza az **Összes szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
1. A laborok listájából válassza ki a kívánt labort. 
1. A Saját **virtuális gépek**listájából válasszon egy futó virtuális gépet.
1. A bal oldali menüben válassza a **Lemezek**lehetőséget.
1. Válassza **az Új csatolása** lehetőséget egy új adatlemez létrehozásához és a virtuális géphez való csatolásához.

    ![Új adatlemez csatolása virtuális géphez](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Töltse ki az **Új lemez csatolása** ablaktáblát az adatlemez nevének, típusának és méretének megadásával.

    ![Töltse ki az "új lemez csatolása" űrlapot](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Válassza **az OK gombot.**

Néhány pillanat múlva létrejön az új adatlemez, és csatlakozik a virtuális géphez, és megjelenik az adott virtuális gép **adatlemezeinek** listájában.

### <a name="attach-an-existing-disk"></a>Meglévő lemez csatlakoztatása
Az alábbi lépésekkel újra csatolhat egy meglévő elérhető adatlemezt egy futó virtuális géphez. 

1. Jelöljön ki egy futó virtuális gép, amelyhez újra csatolni szeretné az adatlemezt.
1. A bal oldali menüben válassza a **Lemezek**lehetőséget.
1. Válassza **a Meglévő csatolása lehetőséget,** ha egy rendelkezésre álló adatlemezt szeretne csatolni a virtuális géphez.

    ![Meglévő adatlemez csatolása virtuális géphez](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png)

1. A **Meglévő lemez csatolása** ablaktáblán válassza az OK gombot.

    ![Meglévő adatlemez csatolása virtuális géphez](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Néhány pillanat múlva az adatlemez csatlakozik a virtuális géphez, és megjelenik az adott virtuális gép **adatlemezeinek** listájában.

## <a name="detach-a-data-disk"></a>Adatlemez leválasztása
Ha már nincs szüksége egy virtuális géphez csatlakoztatott adatlemezre, egyszerűen leválaszthatja azt. A leválasztás eltávolítja a lemezt a virtuális gépről, de később a tárolóban tartja.

Ha ismét fel szeretné használni a lemezen lévő adatokat, újra csatlakoztathatja őket ugyanahhoz a virtuális géphez vagy egy másikhoz.

### <a name="detach-from-the-vms-management-pane"></a>Leválasztás a virtuális gép felügyeleti ablaktáblájáról
1. A virtuális gépek listájából válasszon ki egy virtuális gépet, amelyhez adatlemez is tartozik.
1. A bal oldali menüben válassza a **Lemezek**lehetőséget.
1. Az **Adatlemezek**listájából jelölje ki a leválasztani kívánt adatlemezt.

    ![Adatlemezek kiválasztása virtuális géphez](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png) 
1. Válassza a **Leválasztás** lehetőséget a lemez részleteinek tetején.

    ![Adatlemez leválasztása](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Válassza az **Igen** lehetőséget az adatlemez leválasztásának megerősítéséhez.

A lemez levan választva, és egy másik virtuális géphez csatlakoztatva is elérhető. 
### <a name="detach-from-the-labs-main-pane"></a>Leválasztás a labor fő ablaktáblájáról
1. A tesztkörnyezet fő ablaktábláján válassza a **Saját adatlemezek**lehetőséget.
1. Kattintson a jobb gombbal a leválasztani kívánt adatlemezre – vagy jelölje ki a három pontot (**...**) –, és válassza a **Leválasztás parancsot.**

    ![Adatlemez leválasztása](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Válassza az **Igen** lehetőséget a leválasztás megerősítéséhez.

   > [!NOTE]
   > Ha egy adatlemez már le van választva, a **Törlés**gombra kattintva eltávolíthatja azt a rendelkezésre álló adatlemezek listájáról.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Nem felügyelt adatlemez frissítése
Ha egy meglévő virtuális gép, amely nem felügyelt adatlemezeket használ, könnyen konvertálhatja a virtuális gép felügyelt lemezek használatára. Ez a folyamat átalakítja az operációs rendszer lemezét és a csatlakoztatott adatlemezeket is.

Nem felügyelt adatlemez frissítéséhez kövesse a cikkben ismertetett lépéseket [az adatlemez nem](#detach-a-data-disk) felügyelt virtuális gépről való leválasztásához. Ezután [csatlakoztassa újra a lemezt](#attach-an-existing-disk) egy felügyelt virtuális géphez, hogy automatikusan frissítse az adatlemezt a nem felügyeltről a felügyeltre.

## <a name="next-steps"></a>További lépések
További információ a [követelhető virtuális gépek](devtest-lab-add-claimable-vm.md#unclaim-a-vm)adatlemezeikezeléséről.


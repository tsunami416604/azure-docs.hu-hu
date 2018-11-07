---
title: Csatolása vagy leválasztása adatlemez egy virtuális géphez az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg, hogyan csatolása vagy leválasztása adatlemez egy virtuális géphez az Azure DevTest Labs szolgáltatásban
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
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 2e168867ed342fb0b0545b5fdc330ba790f78de0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243555"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Csatolása vagy leválasztása adatlemez egy virtuális géphez az Azure DevTest Labs szolgáltatásban
[Az Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) kezeli a virtuális gépek társított storage-fiókok. Egy felhasználó rendeli egy új adatlemez virtuális géphez, típusa és szükséges lemez méretét adja meg, és az Azure létrehozza és felügyeli a lemezt automatikusan. Az adatlemez majd sikerült leválasztani a virtuális gépről, és akár objektumkörnyezetben később ugyanarra a virtuális gépre vagy ugyanahhoz a felhasználóhoz tartozó másik virtuális Géphez csatlakozik.

Ez a funkció praktikus storage vagy a szoftver kívül minden különálló virtuális gép felügyeletére. A storage vagy a szoftver már létezik egy adatlemez belül, ha azt is könnyen csatolt, leválasztott, és bármely, adatlemez birtokló felhasználó tulajdonában lévő virtuális géphez csatolni.

## <a name="attach-a-data-disk"></a>Adatlemez csatolása
Adatlemez csatolása a virtuális géphez, mielőtt tekintse át a következő tippeket:

- A virtuális gép méretét szabályozza, hány adatlemez csatolható. További információkért lásd: [virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- Adatlemez csak csatlakoztathat egy futó virtuális Gépre. Ellenőrizze, hogy a virtuális gép fut, adatlemez csatolása előtt.

### <a name="attach-a-new-disk"></a>Új lemez csatolása
Kövesse az alábbi lépéseket hozhat létre, és a egy új felügyelt adatlemez csatolása az Azure DevTest Labs szolgáltatásban virtuális géphez.

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
1. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben. 
1. A listából **a virtual machines**, válasszon egy futó virtuális Gépet.
1. A bal oldali menüben válassza ki a **lemezek**.

    ![Válassza ki a virtuális gép adatlemezek](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png)
1. Válasszon **Attach új** hozzon létre egy új adatlemezt, és mellékelje a virtuális Gépet.

    ![Új adatlemez csatolása a virtuális géphez](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Végezze el a **új lemez csatolása** panelen írja be egy adat-lemez nevét, típusát és méretét.

    ![Végezze el az "új lemez csatolása" képernyő](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Kattintson az **OK** gombra.

Néhány pillanat múlva az új adatlemez létrehozása és csatlakoztatása a virtuális géphez, és megjelenik a listán, **ADATLEMEZEK** virtuális gép.

### <a name="attach-an-existing-disk"></a>Meglévő lemez csatlakoztatása
Kövesse az alábbi lépéseket, adatlemez újracsatolása egy meglévő elérhető egy futó virtuális géphez. 

1. Válassza ki, amelynek meg szeretné egy adatlemez újracsatolása futó virtuális gépek.
1. A bal oldali menüben válassza ki a **lemezek**.
1. Válassza ki **meglévő lemez csatolása** , egy rendelkezésre álló adatlemez csatolása a virtuális géphez.

    ![Meglévő adatlemez csatolása a virtuális géphez](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing2.png)

1. Az a **meglévő lemez csatolása** ablaktáblán, az OK gombra.

    ![Meglévő adatlemez csatolása a virtuális géphez](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Néhány pillanat múlva az adatlemezt a virtuális Géphez van csatlakoztatva, és megjelenik a listán, **ADATLEMEZEK** virtuális gép.

## <a name="detach-a-data-disk"></a>Adatlemez leválasztása
Ha már nincs szüksége egy virtuális Géphez csatolt adatlemezre, könnyedén leválaszthatja azt. Leválasztása lemezt távolítja el a virtuális gépről, de tárolása a storage későbbi használatra.

Ha szeretné újra használni a meglévő adatokat a lemezen, újból csatolhatja ugyanahhoz a virtuális géphez, vagy egy másik.

### <a name="detach-from-the-vms-management-pane"></a>Válassza le a virtuális gép kezelése panelről
1. A virtuális gépek listájából válassza ki, amely rendelkezik csatlakoztatott adatlemez virtuális gép.
1. A bal oldali menüben válassza ki a **lemezek**.

    ![Válassza ki a virtuális gép adatlemezek](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png) 
1. A listából **ADATLEMEZEK**, válassza ki az adatlemezt, amely a leválasztani kívánt.
1. Válassza ki **leválasztási** a a lemez részletei ablaktábla felső részén.

    ![Adatlemez leválasztása](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Válassza ki **Igen** annak ellenőrzéséhez, hogy szeretné-e az adatlemez leválasztása.

A lemez le lett választva, egy másik virtuális géphez csatlakoztatható. 
### <a name="detach-from-the-labs-main-pane"></a>Válassza le a fő panelen a labor létrehozása
1. Válassza ki a fő panelen a labor **Moje datové disky**.

    ![A labor adatlemezek eléréséhez](./media/devtest-lab-attach-detach-data-disk/devtest-lab-my-data-disks.png)
1. Kattintson a jobb gombbal az adatlemez leválasztása – vagy melletti három pontra (...) – válassza ki, és válassza a kívánt **leválasztási**.

    ![Adatlemez leválasztása](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Válassza ki **Igen** , győződjön meg arról, hogy szeretné-e válassza le azt.

   > [!NOTE]
   > Ha adatlemezt már le van választva, ha szeretné, kiválasztásával a rendelkezésre álló adatok lemezt a listáról való eltávolításához **törlése**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Nem felügyelt adatlemez frissítése
Ha egy meglévő virtuális gép által használt nem felügyelt adatlemezek rendelkezik, könnyedén átalakíthatja a virtuális Gépet a felügyelt lemezek használata. Ez a folyamat az operációsrendszer-lemez és a bármely más csatolt lemez alakítja.

A nem felügyelt adatlemez frissítéshez kövesse az ebben a cikkben ismertetett lépéseket [az adatlemez leválasztása](#detach-a-data-disk) nem felügyelt virtuális gépről. Ezt követően [csatlakoztassa újból a lemezt](#attach-an-existing-disk) automatikus frissítése az adatok felügyelt virtuális lemezt a felügyelt kódba.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan kezelheti az adatlemezek [igényelhető virtuális gépek](devtest-lab-add-claimable-vm.md#unclaim-a-vm).


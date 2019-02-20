---
title: Felügyelheti és figyelheti az Azure Backup szolgáltatás Azure virtuális gépek biztonsági másolataiból
description: Ismerje meg, hogyan kezelhetők és figyelhetők Azure VM backup az Azure Backup szolgáltatással.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: sogup
ms.openlocfilehash: f5c0373e2ef094a7fc5be64f4aeb8c0bb132e683
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430063"
---
# <a name="manage-azure-vm-backups"></a>Azure virtuális gépek biztonsági másolatainak kezelése

Ez a cikk bemutatja, hogyan kezelheti az Azure-beli virtuális gépek biztonsági mentése a [Azure Backup szolgáltatás](backup-overview.md) biztonsági mentéseket, és a portál irányítópultján elérhető biztonsági riasztások tudnivalókat foglalja össze.


Az Azure Portalon a Recovery Services-tároló irányítópultján információit, beleértve a tároló hozzáférést biztosít:

* A legutóbbi biztonsági másolatra, amely egyben a legújabb visszaállítási pont.
* a biztonsági mentési szabályzat
* Az összes biztonságimásolat-pillanatképek teljes mérete
* Virtuális gépek biztonsági mentésének engedélyezése

Az irányítópult használatával készített biztonsági kezelheti és összevonással lefelé az egyes virtuális gépekhez. Nyissa meg a tároló irányítópultján achine backup kezdődik. 

![A csúszka teljes áttekintése](./media/backup-azure-manage-vms/bottom-slider.png)

## <a name="view-vms-in-the-dashboard"></a>Virtuális gépek megtekintése az irányítópulton

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A központi menüben kattintson a **Tallózás** elemre, majd az erőforrások listájába írja be a következőt: **Recovery Services**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Kattintson a **Recovery Services-tároló** elemre. 
    ![1. lépés Recovery Services-tároló létrehozása](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

3. Egyszerűen használható, kattintson a jobb gombbal a tároló a tárolók listájában > **rögzítés az irányítópulton**.
3. Nyissa meg a tároló irányítópultján. 
    ![Nyissa meg a tároló irányítópultját és a beállítások panel](./media/backup-azure-manage-vms/full-view-rs-vault.png)

4. Az a **biztonsági másolati elemek** csempére, **Azure Virtual Machines**.

    ![Nyissa meg a biztonsági másolati elemek csempe](./media/backup-azure-manage-vms/contoso-vault-1606.png)

5. A **biztonsági másolati elemek** panelen megtekintheti az egyes elemekhez az utolsó biztonsági mentési feladat. Ebben a példában egy virtuális gép működik, demovm-markgal, ez a tár által védett.  

    ![Biztonsági másolati elemek csempe](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

 
6. A tároló elem irányítópultjáról is hozzon létre vagy módosítsa a biztonsági mentési szabályzatok, visszaállítási pont megtekintése, futtatásakor egy igény szerinti biztonsági mentés leállítása és a virtuális gépek védelmének folytatása, törölni a helyreállítási pontok, és futtathat visszaállítást.

    ![Biztonsági másolati elemek az irányítópultot a beállítások panel](./media/backup-azure-manage-vms/item-dashboard-settings.png)



## <a name="manage-backup-policies"></a>Biztonsági mentési irányelvek kezelése
1. Az a [tároló elem irányítópulton](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kattintson a **minden beállítás** .

    ![Biztonsági mentési házirend panel](./media/backup-azure-manage-vms/all-settings-button.png)
2. A **beállítások**, kattintson a**biztonsági mentési szabályzat**e.
3. Az a **biztonsági mentési házirend kiválasztása** menüben:

   * Szabályzatok módosításához válasszon ki egy másik szabályzatot, és kattintson a **mentése**. Ekkor a rendszer automatikusan alkalmazza az új házirendet a tárolón.
   * Hozzon létre egy házirendet, válassza ki a **hozzon létre új**. [További információ](backup-azure-arm-vms-prepare.md#configure-a-backup-policy)

     ![Virtuális gép biztonsági mentése](./media/backup-azure-manage-vms/backup-policy-create-new.png)


## <a name="run-an-on-demand-backup"></a>Egy igény szerinti biztonsági mentés futtatása
Egy igény szerinti virtuális gépek biztonsági mentési eltarthat, ha konfigurálva van a védelem.
- Ha a kezdeti biztonsági mentés függőben, igény szerinti biztonsági mentést a Recovery Services-tárolóban hoz létre a virtuális gép teljes másolata.
- Ha a kezdeti biztonsági mentés befejeződött, egy igény szerinti biztonsági mentést fogja csak elküldi a módosításokat a korábbi pillanatképből a Recovery Services-tároló. Azt jelenti az azt követő biztonsági mentéseket állandóan növekményes.
- egy igény szerinti biztonsági mentés megőrzési He a napi biztonsági mentési pontok a szabályzatban megadott megőrzési értéket. Ha nincs napi biztonsági mentési pont ki van jelölve, a heti biztonsági mentési pontok szolgál.


Egy igény szerinti biztonsági mentés elindítása:

1. Az a [tároló elem irányítópulton](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kattintson a **biztonsági mentés**.

    ![Biztonsági mentés most gombra.](./media/backup-azure-manage-vms/backup-now-button.png)

 2. Kattintson a **Igen** a biztonsági mentési feladat elindításához.

    ![Biztonsági mentés most gombra.](./media/backup-azure-manage-vms/backup-now-check.png)

 
 A biztonsági mentési feladat létrehoz egy helyreállítási pontot. A helyreállítási pont megőrzési megegyezik a virtuális géphez társított a szabályzatban megadott megőrzési. A folyamat előrehaladását a feladathoz, a tároló irányítópultján kattintson a **biztonsági mentési feladatok** csempére.  

## <a name="stop-protecting-a-vm"></a>Virtuális gép védelmének megszüntetése

Virtuális gépek védelmének leállítani két módja van:

- Állítsa le az összes jövőbeli biztonsági mentési feladatot, és törli az összes helyreállítási pontot. A virtuális gép visszaállítására ebben az esetben nem.
- Állítsa le az összes jövőbeli biztonsági mentési feladatot, de hagyja meg a helyreállítási pontokat. Nincs társított storage-ban a helyreállítási pontok meghagyásának költsége. Azonban a helyreállítási pontok meghagyásának előnye, szükség esetén visszaállíthatja a virtuális Gépet. [További](https://azure.microsoft.com/pricing/details/backup/) kapcsolatos díjszabás.

Virtuális gép védelmének megszüntetése:

1. Az a [tároló elem irányítópulton](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kattintson a **biztonsági mentés leállítása**.

    ! [Leállítása a biztonsági mentés gomb] (./media/backup-azure-manage-vms/stop-backup-button.png
2. Döntse el, hogy megtartja vagy a biztonsági mentési adatok törlése, és győződjön meg arról, igény szerint. Szükség szerint erősítse meg, és szükség esetén adja meg a megjegyzést. Ha nem tudja biztosan az elem nevét, a kurzort a felkiáltójel nevének megjelenítéséhez.

    ![Védelem leállítása](./media/backup-azure-manage-vms/retain-or-delete-option.png)

 Egy értesítési üzenet jelzi, a biztonsági mentési feladatok le lett állítva.


## <a name="resume-protection-of-a-vm"></a>A virtuális gépek védelmének folytatása

Ha a biztonsági mentési adatok őrződnek meg, ha a virtuális gép le lett állítva, folytathatja a védelmet. Ha a törölt biztonsági mentési adatok ezután nem folytatható.

Te

1. Az a [tároló elem irányítópulton](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kattintson a **biztonsági mentés folytatása**.

2. Kövesse a [biztonsági mentési házirendek kezelése](backup-azure-manage-vms.md#manage-backup-policies) , hozzárendeli a szabályzatot a virtuális gép. választhat, mint a szabályzatot, amellyel a virtuális gép védett először egy másik szabályzatot.
3. Miután a virtuális gép biztonsági mentési házirendet alkalmazza, a következő üzenet jelenik meg.

    ![Sikeresen megtörtént a védett virtuális gépek](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Biztonsági másolatok adatainak törlése

Során egy virtuális Géphez társított biztonsági mentési adatokat törölheti a **biztonsági mentés leállítása** feladatot, vagy azt követően bármikor a biztonsági mentési feladat befejeződött.

- Érdemes lehet akár napokat vagy heteket is várni a helyreállítási pontok törlése előtt.
- A helyreállítási pontok visszaállításával ellentétben a biztonsági másolatok adatainak törlésekor nem törölhet meghatározott helyreállítási pontokat. Ha úgy dönt, hogy törli a biztonsági másolatok adatait, azzal törli az elemhez tartozó összes helyreállítási pontot is.

Ez az eljárás feltételezi, hogy a biztonsági mentési feladatot a virtuális gép leállt vagy le van tiltva.


1. Az a [tároló elem irányítópulton](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kattintson a **Delete backup**.

    ![Virtuális gép típusa](./media/backup-azure-manage-vms/delete-backup-buttom.png)

2. Írja be annak ellenőrzéséhez, hogy törli a helyreállítási pontok az elem nevét.

    ![Ellenőrzési leállítása](./media/backup-azure-manage-vms/item-verification-box.png)

4. A biztonsági mentési adatok az aktuális elem törléséhez kattintson ![leállítási biztonsági mentés gomb](./media/backup-azure-manage-vms/delete-button.png)

    Egy értesítési üzenet jelzi, a biztonsági mentési adatok törölve lett.

## <a name="next-steps"></a>További lépések
- [Ismerje meg](backup-azure-vms-first-look-arm.md) biztonsági mentése Azure virtuális gépek a virtuális gép beállításait.
- [Ismerje meg](backup-azure-arm-restore-vms.md) virtuális gépek visszaállítása. 
- [Ismerje meg](backup-azure-monitor-vms.md) Azure virtuális gép biztonsági mentéseinek figyelése.
 

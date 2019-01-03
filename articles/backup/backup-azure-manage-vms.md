---
title: Resource Manager által telepített virtuális gépek biztonsági mentéseinek kezelése
description: Ismerje meg, hogyan felügyelheti és figyelheti a Resource Managerrel üzembe helyezett virtuális gépek biztonsági másolataiból
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 11/28/2016
ms.author: trinadhk
ms.openlocfilehash: 0e599c95f40e62a4bb01a7ab698fe0ce1f738e49
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53635765"
---
# <a name="manage-azure-virtual-machine-backups"></a>Azure-beli virtuális gépek biztonsági másolatainak kezelése

Ez a cikk útmutatást nyújt a virtuális gép biztonsági mentéseinek kezelése, és a biztonsági mentési riasztások adatait a portál irányítópultján érhető el. Ebben a cikkben leírtakat virtuális gépek használata a Recovery Services-tárolókra vonatkozik. Ez a cikk nem tárgyalja a virtuális gépek létrehozása, és nem, azt ismertetik, hogyan védheti meg virtuális gépek. Alapozó védelméről az Azure Resource Manager által telepített virtuális gépek az Azure Recovery Services-tárolóval, lásd: [első lépések: Recovery Services-tároló virtuális gépek biztonsági mentése](backup-azure-vms-first-look-arm.md).

## <a name="manage-vaults-and-protected-virtual-machines"></a>Tárolók és a védett virtuális gépek kezelése
Az Azure Portalon a Recovery Services-tároló irányítópultján információit, beleértve a tároló hozzáférést biztosít:

* a legutóbbi biztonsági mentési pillanatkép, amely egyben a legújabb visszaállítási pont
* a biztonsági mentési szabályzat
* az összes biztonságimásolat-pillanatképek teljes mérete
* a tároló által védett virtuális gépek száma

Virtuális gép biztonsági másolat számos felügyeleti feladatot kezdődik, nyissa meg a tároló irányítópultján. Azonban tárolók segítségével több elemet (vagy több virtuális gép), információhoz egy adott virtuális gép védelmét, mert nyissa meg az elemet tároló irányítópultján. Az alábbi eljárás bemutatja, hogyan lehet megnyitni a *tároló irányítópultján* majd folytassa a *tároló elem irányítópulton*. Nincsenek "tippek" mindkét eljárásnál, amelyek meg, hogyan adja hozzá a tárolóhoz, és az elemet tároló az Azure-irányítópultra a PIN-kód irányítópult parancs használatával. Rögzítés az Irányítópulton egy parancsikont a tár vagy az elem létrehozásának módja. Gyakori parancsok a helyi is futtathatja.

> [!TIP]
> Ha több irányítópultjai és panel nyílik meg, a sötétkék csúszka segítségével az ablak alján húzza oda-vissza az Azure irányítópultján.
>
>

![A csúszka teljes áttekintése](./media/backup-azure-manage-vms/bottom-slider.png)

### <a name="open-a-recovery-services-vault-in-the-dashboard"></a>Nyissa meg a Recovery Services-tárolót az irányítópult:
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A központi menüben kattintson a **Tallózás** elemre, majd az erőforrások listájába írja be a következőt: **Recovery Services**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Kattintson a **Recovery Services-tároló** elemre.

    ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

    A Recovery Services-tárolók listája megjelenik.

    ![Listája a Recovery Services-tárolók ](./media/backup-azure-manage-vms/list-o-vaults.png)

   > [!TIP]
   > Ha rögzít egy tárolót az Azure irányítópulton, a tároló érhető el közvetlenül az Azure Portalon megnyitásakor. Egy tárolót az irányítópult rögzítése a tárolót a listában, kattintson a jobb gombbal a tárolóra, és válassza **rögzítés az irányítópulton**.
   >
   >
3. Tárolók listájából válassza ki a tárolót az irányítópult megnyitásához. Amikor kiválasztja a tárolót, a tároló irányítópultjának és a **beállítások** panel nyílik meg. Az alábbi ábrán a **Contoso-tároló** irányítópult van kijelölve.

    ![Nyissa meg a tároló irányítópultját és a beállítások panel](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### <a name="open-a-vault-item-dashboard"></a>Nyisson meg egy tárban elem irányítópultot
Az előző eljárás során a tároló irányítópultjának megnyitására. A tároló elem irányítópultjának megnyitásához:

1. A tároló irányítópultján található a **biztonsági másolati elemek** csempére, **Azure Virtual Machines**.

    ![Nyissa meg a biztonsági másolati elemek csempe](./media/backup-azure-manage-vms/contoso-vault-1606.png)

    A **biztonsági másolati elemek** panel felsorolja az egyes elemekhez az utolsó biztonsági mentési feladat. Ebben a példában egy virtuális gép működik, demovm-markgal, ez a tár által védett.  

    ![Biztonsági másolati elemek csempe](./media/backup-azure-manage-vms/backup-items-blade.png)

   > [!TIP]
   > A könnyű hozzáférést a tároló elemet az Azure-irányítópulton is rögzíthet. Egy tároló elemet rögzít az tároló elem listán, kattintson a jobb gombbal a cikket, és válassza **rögzítés az irányítópulton**.
   >
   >
2. Az a **biztonsági másolati elemek** panelen kattintson az elemre a tár elem irányítópultjának megnyitásához.

    ![Biztonsági másolati elemek csempe](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    A tároló elem irányítópulton és a hozzá tartozó **beállítások** panel nyílik meg.

    ![Biztonsági másolati elemek az irányítópultot a beállítások panel](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    A tároló elem irányítópultjáról végezheti el számos kulcsfontosságú feladatokat, például:

   * szabályzatok módosítása, vagy hozzon létre egy új biztonsági mentési szabályzat
   * megtekintheti a helyreállítási pontokat, és állapotuk konzisztencia
   * Igény szerinti biztonsági mentést a virtuális gép
   * Virtuális gépek védelmének megszüntetése
   * a virtuális gép védelmének folytatása
   * Törölje a biztonsági mentési adatok (vagy a helyreállítási pont)
   * [biztonsági mentési lemezek visszaállítása](backup-azure-arm-restore-vms.md#create-new-restore-disks)

A következő eljárások a kiindulási pont elem tároló irányítópultján.

## <a name="manage-backup-policies"></a>Biztonsági mentési irányelvek kezelése
1. Az a [tároló elem irányítópulton](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kattintson a **minden beállítás** megnyitásához a **beállítások** panelen.

    ![Biztonsági mentési házirend panel](./media/backup-azure-manage-vms/all-settings-button.png)
2. Az a **beállítások** panelen kattintson a **biztonsági mentési szabályzat** , hogy a panel megnyitásához.

    A panelen a biztonsági mentési gyakoriság és megőrzési tartomány részletei jelennek meg.

    ![Biztonsági mentési házirend panel](./media/backup-azure-manage-vms/backup-policy-blade.png)
3. Az a **biztonsági mentési házirend kiválasztása** menüben:

   * Szabályzatok módosításához válasszon ki egy másik szabályzatot, és kattintson a **mentése**. Ekkor a rendszer automatikusan alkalmazza az új házirendet a tárolón.
   * Hozzon létre egy házirendet, válassza ki a **hozzon létre új**.

     ![Virtuális gép biztonsági mentése](./media/backup-azure-manage-vms/backup-policy-create-new.png)

     Biztonsági mentési szabályzat létrehozásával kapcsolatos útmutatóért lásd: [biztonsági mentési házirend meghatározása](backup-azure-manage-vms.md#defining-a-backup-policy).

[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

> [!NOTE]
> Miközben a biztonsági mentési házirendek kezelése, ügyeljen arra, hogy kövesse a [ajánlott eljárások](backup-azure-vms-introduction.md#best-practices) biztonsági mentés optimális teljesítményéhez
>
>

## <a name="on-demand-backup-of-a-virtual-machine"></a>Igény szerinti biztonsági mentést a virtuális gép
Egy igény szerinti biztonsági mentés a virtuális gép eltarthat, ha konfigurálva van a védelem. Ha a kezdeti biztonsági mentés függőben, igény szerinti biztonsági mentést a Recovery Services-tárolóban hoz létre a virtuális gép teljes másolata. Ha a kezdeti biztonsági mentés befejeződött, egy igény szerinti biztonsági mentést fogja csak elküldi a módosításokat a korábbi pillanatképből a Recovery Services-tároló. Azt jelenti az azt követő biztonsági mentéseket állandóan növekményes.

> [!NOTE]
> Egy igény szerinti biztonsági mentés megőrzési tartománya a napi biztonsági mentési pontok a szabályzatban megadott megőrzési értéket. Ha nincs napi biztonsági mentési pont ki van jelölve, a heti biztonsági mentési pontok szolgál.
>
>

Egy igény szerinti biztonsági mentés a virtuális gép elindítása:

* Az a [tároló elem irányítópulton](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kattintson a **biztonsági mentés**.

    ![Biztonsági mentés most gombra.](./media/backup-azure-manage-vms/backup-now-button.png)

    A portál gondoskodik arról, hogy szeretné-e egy igény szerinti biztonsági mentési feladat elindításához. Kattintson a **Igen** a biztonsági mentési feladat elindításához.

    ![Biztonsági mentés most gombra.](./media/backup-azure-manage-vms/backup-now-check.png)

    A biztonsági mentési feladat létrehoz egy helyreállítási pontot. A helyreállítási pont megőrzési megegyezik a virtuális géphez társított a szabályzatban megadott megőrzési. A folyamat előrehaladását a feladathoz, a tároló irányítópultján kattintson a **biztonsági mentési feladatok** csempére.  

## <a name="stop-protecting-virtual-machines"></a>Virtuális gépek védelmének megszüntetése
Ha egy virtuális gép leállítása, megkérdezi, hogy szeretné-e őrizni a helyreállítási pontokat. Virtuális gépek védelmének leállítani két módja van:

* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és törölje az összes helyreállítási pontot, vagy
* Állítsa le az összes jövőbeli biztonsági mentési feladatot, de hagyja meg a helyreállítási pontok

Nincs társított storage-ban a helyreállítási pontok meghagyásának költsége. A helyreállítási pontok meghagyásának előnye az, viszont visszaállíthatja a virtuális gép később, ha szükséges. A helyreállítási pontok megőrzésének költségével kapcsolatos információkat, tekintse meg a [díjszabás](https://azure.microsoft.com/pricing/details/backup/). Ha törli az összes helyreállítási pontot választja, a virtuális gép nem állítható vissza.

Virtuális gép védelmének megszüntetése:

1. Az a [tároló elem irányítópulton](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kattintson a **biztonsági mentés leállítása**.

    ![Állítsa le a biztonsági mentés gomb](./media/backup-azure-manage-vms/stop-backup-button.png)

    Ekkor megnyílik a biztonsági mentés leállítása panel.

    ![Állítsa le a biztonsági mentés panel](./media/backup-azure-manage-vms/stop-backup-blade.png)
2. Az a **biztonsági mentés leállítása** panelen válassza ki, hogy megtartja vagy törli a biztonsági mentési adatokat. Az információs dobozt az Ön által választott részletesen ismerteti.

    ![Védelem leállítása](./media/backup-azure-manage-vms/retain-or-delete-option.png)
3. Ha a biztonsági másolatok adatainak megőrzése lehetőséget választotta, ugorjon a 4. Ha úgy döntött, hogy biztonsági mentési adatok törlése, győződjön meg arról, hogy szeretné-e a biztonsági mentési feladatok leállítása és törlése a helyreállítási pontok – írja be az elem nevét.

    ![Ellenőrzési leállítása](./media/backup-azure-manage-vms/item-verification-box.png)

    Ha nem tudja biztosan az elem nevét, a kurzort a felkiáltójel nevének megjelenítéséhez. Az elem nevét is alatt **biztonsági mentés leállítása** a panel tetején.
4. Megadhat egy **OK** vagy **Megjegyzés**.
5. Az aktuális elem a biztonsági mentési feladat leállításához kattintson ![leállítási biztonsági mentés gomb](./media/backup-azure-manage-vms/stop-backup-button-blue.png)

    Egy értesítési üzenet jelzi, a biztonsági mentési feladatok le lett állítva.

    ![Erősítse meg a védelem leállítása](./media/backup-azure-manage-vms/stop-message.png)

## <a name="resume-protection-of-a-virtual-machine"></a>a virtuális gép védelmének folytatása
Ha a **biztonsági másolatok adatainak megőrzése** lehetőséget választotta a virtuális gép védelmét, le lett állítva, akkor lehetséges, a védelem folytatásához. Ha a **biztonsági másolatok adatainak törlése** lehetőséget választotta, akkor a virtuális gép védelmét nem lehet folytatni.

A virtuális gép védelmének folytatása

1. Az a [tároló elem irányítópulton](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kattintson a **biztonsági mentés folytatása**.

    ![Védelem folytatása](./media/backup-azure-manage-vms/resume-backup-button.png)

    A biztonsági mentési házirend panel pedig megnyílik.

   > [!NOTE]
   > A virtuális gép ismételt védelme, választhat, mint a szabályzatot, amellyel a virtuális gép védett először egy másik szabályzatot.
   >
   >
2. Kövesse a [biztonsági mentési házirendek kezelése](backup-azure-manage-vms.md#manage-backup-policies) , hozzárendeli a szabályzatot a virtuális gép.

    A virtuális gépre a biztonsági mentési szabályzat alkalmazása után a következő üzenet jelenik meg.

    ![Sikeresen megtörtént a védett virtuális gépek](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Biztonsági másolatok adatainak törlése
Törölheti a során a virtuális géphez társított biztonsági mentési adatok a **biztonsági mentés leállítása** feladatot, vagy azt követően bármikor a biztonsági mentési feladat befejeződött. Érdemes lehet akár napokat vagy heteket is várni a helyreállítási pontok törlése előtt. A helyreállítási pontok visszaállításával ellentétben a biztonsági másolatok adatainak törlésekor nem törölhet meghatározott helyreállítási pontokat. Ha úgy dönt, hogy törli a biztonsági másolatok adatait, azzal törli az elemhez tartozó összes helyreállítási pontot is.

A következő eljárás feltételezi, hogy a biztonsági mentési feladatot a virtuális gép leállt vagy le van tiltva. A biztonsági mentési feladat le van tiltva, miután a **biztonsági mentés folytatása** és **Delete backup** lehetőségek elérhetők az elemet tároló irányítópultján.

![Folytatása és a Törlés gomb](./media/backup-azure-manage-vms/resume-delete-buttons.png)

Egy virtuális gépen, a biztonsági mentési adatok törlése a *biztonsági mentés letiltva*:

1. Az a [tároló elem irányítópulton](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kattintson a **Delete backup**.

    ![Virtuális gép típusa](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    A **biztonsági másolatok adatainak törlése** panel nyílik meg.

    ![Virtuális gép típusa](./media/backup-azure-manage-vms/delete-backup-blade.png)
2. Írja be annak ellenőrzéséhez, hogy törli a helyreállítási pontok az elem nevét.

    ![Ellenőrzési leállítása](./media/backup-azure-manage-vms/item-verification-box.png)

    Ha nem tudja biztosan az elem nevét, a kurzort a felkiáltójel nevének megjelenítéséhez. Az elem nevét is alatt **biztonsági másolatok adatainak törlése** a panel tetején.
3. Megadhat egy **OK** vagy **Megjegyzés**.
4. A biztonsági mentési adatok az aktuális elem törléséhez kattintson ![leállítási biztonsági mentés gomb](./media/backup-azure-manage-vms/delete-button.png)

    Egy értesítési üzenet jelzi, a biztonsági mentési adatok törölve lett.

## <a name="next-steps"></a>További lépések
Hozza létre újra a virtuális gép helyreállítási pontból információkért tekintse meg [visszaállítása az Azure virtuális gépek](backup-azure-arm-restore-vms.md). Ha a virtuális gépek védelme információra van szüksége, tekintse meg [első lépések: Recovery Services-tároló virtuális gépek biztonsági mentése](backup-azure-vms-first-look-arm.md). Események figyelésével kapcsolatos további információkért lásd: [Azure virtuális gépek biztonsági másolataival kapcsolatos riasztások figyelése](backup-azure-monitor-vms.md).

---
title: Erőforrás-kezelő telepített virtuális gépek biztonsági mentéseinek kezelése
description: Ismerje meg, hogyan kezelheti és figyelheti az erőforrás-kezelő telepített virtuális gépek biztonsági mentéseinek
services: backup
author: trinadhk
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 11/28/2016
ms.author: trinadhk
ms.openlocfilehash: 4d45db6ba6354f85c3ed67561751720b6f6f4b77
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606341"
---
# <a name="manage-azure-virtual-machine-backups"></a>Azure-beli virtuális gépek biztonsági másolatainak kezelése

Ez a cikk nyújt útmutatást a virtuális gép biztonsági másolatok kezelésére, és a biztonsági mentési riasztás adatait a portál irányítópultján érhető el. Az útmutató a virtuális gépek használata a Recovery Services-tárolók vonatkozik. Ez a cikk nem foglalkozik a virtuális gépek létrehozását, és nem az azt ismertetik, hogyan védi a virtuális gépek nem. A Recovery Services-tároló virtuális gépek Azure Resource Manager üzembe az Azure-ban ellátásáról ismertetése, lásd: [először: készítsen biztonsági mentést a Recovery Services-tároló virtuális gépek](backup-azure-vms-first-look-arm.md).

## <a name="manage-vaults-and-protected-virtual-machines"></a>Tárolók és a védett virtuális gépek kezelése
Az Azure portálon a Recovery Services-tároló irányítópult információit, a tároló, beleértve a hozzáférést biztosítja:

* a legutóbbi biztonsági mentési pillanatképet, amely egyben a legutóbbi visszaállítási pont
* a biztonsági mentési házirend
* az összes biztonsági mentési pillanatképek teljes mérete
* a tárolóval védett virtuális gépek száma

Egy virtuális gép biztonsági másolatából számos felügyeleti feladatot a kezdő ellenőrzéséhez nyissa meg a tároló az irányítópulton. Azonban az tárolók használható arra, hogy több elem (vagy több virtuális gép), egy adott virtuális gép adatainak megtekintése, mert az a tároló elem irányítópult megnyitásához. Az alábbi eljárás bemutatja, hogyan nyissa meg a *tároló irányítópult* és folytassa a *tároló elem irányítópult*. Nincsenek "tippek" mindkét eljárásnál, amelyek arra, hogyan adja hozzá a tárolóhoz, és a tároló cikk az Azure-irányítópulton a PIN-kód irányítópult parancs használatával. Rögzítés az irányítópulton a tárolóhoz vagy a cikk parancsikon létrehozásának módja. Általános jellegű parancsok a parancsikonnal is végrehajthat.

> [!TIP]
> Ha több irányítópultok és panel nyílik meg, a csúszkával sötét-kék az ablak alján csúsztassa be a Azure irányítópult oda-vissza.
>
>

![A csúszka teljes áttekintése](./media/backup-azure-manage-vms/bottom-slider.png)

### <a name="open-a-recovery-services-vault-in-the-dashboard"></a>Az irányítópult nyissa meg a Recovery Services-tároló:
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A központi menüben kattintson a **Tallózás** elemre, majd az erőforrások listájába írja be a következőt: **Recovery Services**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Kattintson a **Recovery Services-tároló** elemre.

    ![Recovery Services-tároló létrehozása – 1. lépés](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

    A Recovery Services-tárolók listája megjelenik.

    ![Helyreállítási szolgáltatások listája tárolók ](./media/backup-azure-manage-vms/list-o-vaults.png)

   > [!TIP]
   > Ha rögzíti egy tárolót az Azure-irányítópultot, a tároló érhető el közvetlenül az Azure-portál megnyitásakor. PIN-kód egy tárolót az irányítópulton, a tároló listában, kattintson a jobb gombbal a tárolóra, és válassza ki **rögzítés az irányítópulton**.
   >
   >
3. A tárolók listában jelölje ki a kívánt tárolóra az irányítópult megnyitásához. Ha bejelöli a tárolóban, a tároló irányítópult és a **beállítások** panel megnyitása. Az alábbi ábrán a **Contoso-tároló** irányítópult ki van jelölve.

    ![Nyissa meg a tároló irányítópult és a beállítások panel](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### <a name="open-a-vault-item-dashboard"></a>Nyissa meg a tároló elem irányítópult
Az előző eljárásban megnyitott a tároló irányítópult. A tároló elem irányítópult megnyitása:

1. A tároló irányítópultjának a a **biztonsági mentés elemek** csempére, kattintson a **Azure virtuális gépek**.

    ![Nyissa meg a biztonsági másolati elemei csempe](./media/backup-azure-manage-vms/contoso-vault-1606.png)

    A **biztonsági másolati elemei** panel megjeleníti az utolsó biztonsági mentési feladat minden elemhez. Ebben a példában a rendszer egy védett virtuális gépek, demovm-markgal, amelyet ehhez a tárolóhoz.  

    ![Biztonsági másolati elemei csempe](./media/backup-azure-manage-vms/backup-items-blade.png)

   > [!TIP]
   > Könnyű elérés, a PIN-kód egy tároló elemet, az Azure-irányítópulton. PIN-kód egy tároló elem a tároló elem listában, kattintson a jobb gombbal az elemet, és válassza **rögzítés az irányítópulton**.
   >
   >
2. Az a **biztonsági mentés elemek** panelen a elemre kattintva nyissa meg a tároló elem irányítópultot.

    ![Biztonsági másolati elemei csempe](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    A tároló elem irányítópult és a **beállítások** panel megnyitása.

    ![Biztonsági másolati elemei irányítópultot, amelynek beállítások panel](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    A tároló elem irányítópultról például több kulcskezelési feladatok, érhető el:

   * Módosítsa a házirendek, vagy hozzon létre egy új biztonsági mentési házirend
   * megtekintheti a visszaállítási pontok, és konzisztencia állapotukra
   * Igény szerinti biztonsági mentést a virtuális gépek
   * Virtuális gépek védelmének megszüntetése
   * A virtuális gépek a védelem folytatásához
   * Törölje a biztonsági mentési adatok (vagy a helyreállítási pont)
   * [Állítsa vissza a biztonsági mentési lemezek](backup-azure-arm-restore-vms.md#restore-backed-up-disks)

A következő eljárásokat a kiindulási pontja egy tároló elem irányítópultot.

## <a name="manage-backup-policies"></a>Biztonsági mentési irányelvek kezelése
1. Az a [tároló elem irányítópult](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kattintson a **összes beállítás** megnyitásához a **beállítások** panelen.

    ![A biztonsági mentési házirend panel](./media/backup-azure-manage-vms/all-settings-button.png)
2. Az a **beállítások** panelen kattintson a **biztonsági mentési házirend** , hogy a panel megnyitásához.

    A panelen a biztonsági mentési gyakoriság és megőrzési tartomány részletei láthatók.

    ![A biztonsági mentési házirend panel](./media/backup-azure-manage-vms/backup-policy-blade.png)
3. Az a **válassza ki a biztonsági mentési házirend** menüben:

   * Házirendek módosításához jelöljön ki egy másik házirendet, és kattintson a **mentése**. Ekkor a rendszer automatikusan alkalmazza az új házirendet a tárolón.
   * Egy házirend létrehozásához válassza **hozzon létre új**.

     ![Virtuális gép biztonsági mentése](./media/backup-azure-manage-vms/backup-policy-create-new.png)

     A biztonsági mentési házirend létrehozásával kapcsolatos utasításokért lásd: [biztonsági mentési házirend meghatározása](backup-azure-manage-vms.md#defining-a-backup-policy).

[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

> [!NOTE]
> Biztonsági házirendek kezelése során ügyeljen arra, hogy kövesse a [ajánlott eljárások](backup-azure-vms-introduction.md#best-practices) optimális teljesítményének eléréséhez biztonsági mentés
>
>

## <a name="on-demand-backup-of-a-virtual-machine"></a>Igény szerinti biztonsági mentést a virtuális gépek
Igény szerinti biztonsági mentését egy virtuális gép eltarthat, ha konfigurálva van a védelem. Ha a kezdeti biztonsági mentés folyamatban, igény szerinti biztonsági mentést a virtuális gép teljes másolatot készít a Recovery Services-tároló. Ha a kezdeti biztonsági mentés befejeződött, egy igény szerinti biztonsági mentést fogja csak elküldi a módosításokat a korábbi pillanatképből, a Recovery Services-tároló. Ez azt jelenti, hogy azt követő biztonsági mentéseket a rendszer mindig növekményes.

> [!NOTE]
> Egy igény szerinti biztonsági mentés megőrzési tartománya a napi biztonsági mentési pont a házirendben megadott megőrzési értéknek. Ha nincs napi biztonsági mentési pont van kijelölve, a heti biztonsági mentési pont szerepel.
>
>

Egy igény szerinti biztonsági mentést a virtuális gép indításához:

* Az a [tároló elem irányítópult](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kattintson a **biztonsági mentés most**.

    ![Biztonsági mentés most gombra.](./media/backup-azure-manage-vms/backup-now-button.png)

    A portál gondoskodik arról, hogy szeretné-e az igény szerinti biztonsági mentési feladat elindítása. Kattintson a **Igen** elindítja a biztonsági mentési feladatot.

    ![Biztonsági mentés most gombra.](./media/backup-azure-manage-vms/backup-now-check.png)

    A biztonsági mentési feladat létrehoz egy helyreállítási pontot. A helyreállítási pont megőrzési időtartamának megegyezik a virtuális géphez társított házirendben megadott megőrzési ideig. A folyamat előrehaladását a feladathoz, a tároló irányítópulton kattintson a **biztonsági mentési feladatok** csempére.  

## <a name="stop-protecting-virtual-machines"></a>Virtuális gépek védelmének megszüntetése
Ha kikapcsolja a virtuális gép védelmét, megkérdezi, hogy kívánja-e a helyreállítási pontok megőrzése. Virtuális gépek védelmének kikapcsolását két módja van:

* Állítsa le az összes jövőbeli biztonsági mentési feladat, és törölje az összes helyreállítási pont, vagy
* Állítsa le az összes jövőbeli biztonsági mentési feladat, de hagyja meg a helyreállítási pontok

Nincs társított áthelyezni a helyreállítási pontok tárolási költség. Azonban az, hogy a helyreállítási pontok előnye, később visszaállíthatja a virtuális gép igény. A költség, hogy a helyreállítási pontok információ: a [díjszabása](https://azure.microsoft.com/pricing/details/backup/). Ha törli az összes helyreállítási pontot választja, a virtuális gép nem állítható vissza.

Szüntesse meg a virtuális gép védelmét:

1. Az a [tároló elem irányítópult](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kattintson a **Stop biztonsági mentés**.

    ![Állítsa le a biztonsági mentési gomb](./media/backup-azure-manage-vms/stop-backup-button.png)

    A biztonsági mentés leállítása panel nyílik meg.

    ![Állítsa le a biztonsági mentési panel](./media/backup-azure-manage-vms/stop-backup-blade.png)
2. A a **biztonsági mentés leállítása** panelen válassza ki, hogy megtartja vagy a biztonsági mentési adatok törlése. Az információ írja be a választott ismerteti.

    ![Állítsa le a védelmet](./media/backup-azure-manage-vms/retain-or-delete-option.png)
3. Ha úgy döntött, hogy a biztonsági mentési adatok megőrzése mellett, ugorjon a 4. Ha úgy döntött, hogy törölje a biztonsági mentési adatokat, győződjön meg arról, hogy szeretné-e állítani a biztonsági mentési feladatokat, és törölje a helyreállítási pontok – írja be az elem nevét.

    ![Állítsa le a ellenőrzése](./media/backup-azure-manage-vms/item-verification-box.png)

    Ha nem biztos a cikk-neve, mutasson a felkiáltójel a nevének megtekintéséhez. Az elem nevét is a **állítsa le a biztonsági mentés** a panel tetején.
4. Megadhat egy **OK** vagy **Megjegyzés**.
5. A biztonsági mentési feladat az aktuális elem leállításához kattintson ![leállítás biztonsági mentés gombra.](./media/backup-azure-manage-vms/stop-backup-button-blue.png)

    Egy értesítési üzenetet lehetővé teszi a biztonsági mentési feladatok leállították.

    ![Állítsa le a védelmet megerősítése](./media/backup-azure-manage-vms/stop-message.png)

## <a name="resume-protection-of-a-virtual-machine"></a>A virtuális gépek a védelem folytatásához
Ha a **biztonsági mentési adatok megőrzése** beállítás választása a virtuális gép védelmét le lett állítva, ha lehetséges, a védelem folytatásához. Ha a **biztonságimásolat-adatok törlése** lehetőséget választotta, akkor a virtuális gép védelmét nem folytatható.

A virtuális gép védelmének folytatása

1. Az a [tároló elem irányítópult](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kattintson a **biztonsági mentés folytatása**.

    ![A védelem folytatásához](./media/backup-azure-manage-vms/resume-backup-button.png)

    A biztonsági mentési házirend panel nyílik meg.

   > [!NOTE]
   > Ha a virtuális gép ismételt védelmével, dönthet úgy, hogy egy másik házirend érvényesíthető, mint a házirendet, amellyel a virtuális gép védett kezdetben ki.
   >
   >
2. Kövesse a [biztonsági mentési házirendek kezelése](backup-azure-manage-vms.md#manage-backup-policies) a virtuális gép házirend hozzárendelése.

    Miután a virtuális gép alkalmazza a biztonsági mentési házirend, a következő üzenet jelenik meg.

    ![Sikeresen védett virtuális gép](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Biztonsági másolatok adatainak törlése
A során a virtuális géphez tartozó biztonsági mentési adatok törlése a **Stop biztonsági mentés** feladat, vagy a biztonsági mentés azt követően bármikor feladat befejeződött. Érdemes lehet akár napokat vagy heteket is várni a helyreállítási pontok törlése előtt. A helyreállítási pontok visszaállításával ellentétben a biztonsági másolatok adatainak törlésekor nem törölhet meghatározott helyreállítási pontokat. Ha úgy dönt, hogy törli a biztonsági másolatok adatait, azzal törli az elemhez tartozó összes helyreállítási pontot is.

A következő eljárás azt feltételezi, hogy a biztonsági mentési feladatot a virtuális gép leállítása vagy letiltása. A biztonsági mentési feladat le van tiltva, ha a **biztonsági mentés folytatása** és **Delete biztonsági mentés** beállítások érhetők el a tároló elem irányítópulton.

![Folytatás és a Törlés gombbal](./media/backup-azure-manage-vms/resume-delete-buttons.png)

A virtuális gépen, a biztonsági mentési adatok törléséhez a *biztonsági mentése le van tiltva*:

1. Az a [tároló elem irányítópult](backup-azure-manage-vms.md#open-a-vault-item-dashboard), kattintson a **Delete biztonsági mentés**.

    ![Virtuális gép típusa](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    A **biztonságimásolat-adatok törlése** panel nyílik meg.

    ![Virtuális gép típusa](./media/backup-azure-manage-vms/delete-backup-blade.png)
2. Írja be annak ellenőrzéséhez, hogy törli a helyreállítási pontok az elem nevét.

    ![Állítsa le a ellenőrzése](./media/backup-azure-manage-vms/item-verification-box.png)

    Ha nem biztos a cikk-neve, mutasson a felkiáltójel a nevének megtekintéséhez. Az elem nevét is a **biztonságimásolat-adatok törlése** a panel tetején.
3. Megadhat egy **OK** vagy **Megjegyzés**.
4. A biztonsági mentési adatok az aktuális elem törléséhez kattintson ![leállítás biztonsági mentés gombra.](./media/backup-azure-manage-vms/delete-button.png)

    Egy értesítési üzenetet lehetővé teszi a biztonsági mentési adatok törölve lett.

## <a name="next-steps"></a>További lépések
Hozza létre újra a virtuális gép helyreállítási pontból információkért tekintse meg [visszaállítása az Azure virtuális gépek](backup-azure-arm-restore-vms.md). Ha a virtuális gépek védelméről tájékoztatásra van szüksége, tekintse meg [először: készítsen biztonsági mentést a Recovery Services-tároló virtuális gépek](backup-azure-vms-first-look-arm.md). Események figyelésével kapcsolatos további információkért lásd: [figyelése az Azure virtuális gépek biztonsági mentéseinek riasztások](backup-azure-monitor-vms.md).

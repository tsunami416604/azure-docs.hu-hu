---
title: "Kezelni és megfigyelni az Azure virtuális gépek biztonsági mentéseinek |} Microsoft Docs"
description: "Ismerje meg, hogyan kezelheti és figyelheti az Azure virtuális gépek biztonsági mentéseinek"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 4372944e-d33a-4f6a-bd5f-d04af2842713
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cwatson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8dcefacd700f3b475eb477f80201659a2bea802
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="manage-common-azure-backup-jobs-and-trigger-alerts-in-the-classic-portal"></a>Közös Azure biztonsági mentési feladatok és a klasszikus portálon eseményindító-riasztások kezelése
> [!div class="op_single_selector"]
> * [Azure virtuális gép biztonsági mentések kezelése](backup-azure-manage-vms.md)
> * [Klasszikus virtuális gép biztonsági mentések kezelése](backup-azure-manage-vms-classic.md)
>
>

Ez a cikk tájékoztatást ad azokról a közös felügyeleti és megfigyelési feladatok Klasszikus-modell virtuális gépek Azure-ral védett.  

> [!NOTE]
> Az Azure két üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Lásd: [készítse elő a környezetet a biztonsági mentése Azure virtuális gépek](backup-azure-vms-prepare.md) talál részletes információt használata a klasszikus telepítési modell virtuális gépek.
>
> [!IMPORTANT]
>2017 márciusától már nem hozhat létre Backup-tárolókat a klasszikus portálon.
>
> A biztonsági mentési tárakról mostantól lehetőség van helyreállítási tárakra váltani. A részletekről bővebben az [Váltás biztonsági mentési tárolóról Recovery Services-tárolóra](backup-azure-upgrade-backup-to-recovery-services.md) című cikkben olvashat. A Microsoft azt javasolja, hogy a biztonsági mentési tárolóról váltson Recovery Services-tárolóra.<br/> Utáni novemberi 30, 2017 csak akkor tud biztonsági mentési tárolók létrehozása a PowerShell használatával. **2017. November 30 által**:
>- Minden fennmaradó Backup-tároló automatikusan Recovery Services-tárolóra frissül.
>- A klasszikus portálon nem lehet majd hozzáférni a biztonsági másolati adatokhoz. Helyette az Azure Portal segítségével férhet hozzá a Recovery Services-tárolókban található biztonsági mentési adatokhoz.

## <a name="manage-protected-virtual-machines"></a>Védett virtuális gépek kezelése
Védett virtuális gépek kezeléséhez:

1. Megtekintéséhez és kezeléséhez kattintson a virtuális gép biztonsági mentési beállítások a **védett elemek** fülre.
2. Kattintson a nevére, a védett elem megtekintéséhez a **biztonsági mentési részletek** lap, amely jelzi, hogy a legutóbbi biztonsági mentés kapcsolatos információkat.

    ![Virtuális gép biztonsági mentése](./media/backup-azure-manage-vms/backup-vmdetails.png)
3. Megtekintéséhez és kezeléséhez kattintson a virtuális gép biztonsági mentési házirend beállításai a **házirendek** fülre.

    ![Virtuális gép házirend](./media/backup-azure-manage-vms/manage-policy-settings.png)

    A **biztonsági mentési házirendek** lapon láthatók a meglévő házirendet. Igény szerint módosíthatók. Ha szeretne létrehozni egy új házirend kattintson **létrehozása** a a **házirendek** lap. Ne feledje, hogy ha el szeretné távolítani egy házirendet, nem a vele társított virtuális gépek.

    ![Virtuális gép házirend](./media/backup-azure-manage-vms/backup-vmpolicy.png)
4. Kaphat műveletek vagy állapottal kapcsolatos további információk a virtuális gépek a **feladatok** lap. Kattintson egy feladatra a további részletek a listában, vagy egy adott virtuális gép szűrve.

    ![Feladatok](./media/backup-azure-manage-vms/backup-job.png)

## <a name="on-demand-backup-of-a-virtual-machine"></a>Igény szerinti biztonsági mentést a virtuális gépek
Igény szerinti biztonsági mentését egy virtuális gép eltarthat, ha konfigurálva van a védelem. Ha a kezdeti biztonsági másolatot függőben a virtuális gép igény szerinti biztonsági mentést a virtuális gép teljes másolata létrehoz az Azure mentési tárolóval. Ha első biztonsági mentés befejeződött, igény szerinti biztonsági mentése nem fog csak küldési módosítások korábbi biztonsági mentés az Azure biztonsági mentésre tároló azaz azt nem mindig növekményes.

> [!NOTE]
> Egy igény szerinti biztonsági mentés a megőrzési tartomány megadott biztonsági mentési házirendben, a virtuális gép megfelelő napi megőrzési megőrzési értékre van beállítva.  
>
>

Igény szerinti biztonsági mentését egy virtuális gép érvénybe:

1. Keresse meg a **védett elemek** lapon, és válassza **Azure virtuális gép** , **típus** (Ha még nincs kiválasztva), majd kattintson a **válasszon** jelölt gombra.

    ![Virtuálisgép-típussá](./media/backup-azure-manage-vms/vm-type.png)
2. Válassza ki a virtuális gépet, amelyen igény szerinti biztonsági másolatok készítéséhez, majd kattintson a kívánt **a biztonsági mentés gombra** gombra az oldal alján.

    ![Biztonsági másolat készítése](./media/backup-azure-manage-vms/backup-now.png)

    Ezzel létrehoz egy biztonsági mentési feladat a kijelölt virtuális gépen. Ez a feladat segítségével létrehozott helyreállítási pont megőrzési időtartamának ugyanaz, mint a virtuális géphez társított házirendben megadott lesz.

    ![Biztonsági mentési feladat létrehozása](./media/backup-azure-manage-vms/creating-job.png)

   > [!NOTE]
   > A virtuális gépekhez társított szabályzat megtekintéséhez részletekbe menően tárhatják fel a virtuális gép a **védett elemek** lapon, majd kattintson a biztonsági mentési házirend fülre.
   >
   >
3. A feladat létrehozása után kattintson a **feladat megtekintése** a feladatok lapon a megfelelő feladat megtekintéséhez bejelentési található gombra.

    ![Biztonsági mentési feladat létrehozása](./media/backup-azure-manage-vms/created-job.png)
4. A feladat sikeres befejezése után egy helyreállítási pontot létrehozza, amelyek segítségével visszaállítani a virtuális gépet. Ez a helyreállítási pont oszlop értékét is növeli a 1 **védett elemek** lap.

## <a name="stop-protecting-virtual-machines"></a>Virtuális gépek védelmének megszüntetése
Ha szeretné, állítsa le a jövőbeli biztonsági mentések a virtuális gépek a következő beállításokkal:

* Azure biztonságimásolat-kezelő tárolójában a virtuális géphez tartozó biztonsági mentési adatok megőrzése mellett
* A virtuális géphez tartozó biztonsági mentési adatok törlése

Ha be van jelölve a virtuális géphez tartozó biztonsági mentési adatok megőrzése mellett, a biztonsági mentési adatok segítségével visszaállítani a virtuális gépet. Ilyen virtuális gépek díjszabása, kattintson a [Itt](https://azure.microsoft.com/pricing/details/backup/).

Szüntesse meg a virtuális gép védelmét:

1. Navigáljon a **védett elemek** lapon, és válassza **Azure virtuális gép** , a szűrő típusa (Ha még nincs kiválasztva), majd kattintson a **válasszon** gombra.

    ![Virtuálisgép-típussá](./media/backup-azure-manage-vms/vm-type.png)
2. Válassza ki a virtuális gépet, majd kattintson a **védelem kikapcsolása** az oldal alján.

    ![Állítsa le a védelmet](./media/backup-azure-manage-vms/stop-protection.png)
3. Alapértelmezés szerint Azure Backup szolgáltatással nem törli a virtuális géphez tartozó biztonsági mentési adatokat.

    ![Állítsa le a védelmet megerősítése](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    Ha a törlendő biztonsági mentési adatokat, jelölje be a jelölőnégyzetet.

    ![Jelölőnégyzet](./media/backup-azure-manage-vms/checkbox.png)

    Válasszon ki egy a biztonsági mentés leállításának oka. Ez nem kötelező, ok megadása segít Azure biztonsági mentés a visszajelzés működik, és priorizálhatja azokat a forgatókönyvet.
4. Kattintson a **Submit** gombra kattintva küldje el a **állítsa le a védelmet** feladat. Kattintson a **feladat megtekintése** a megfelelő a feladat megtekintéséhez **feladatok** lap.

    ![Állítsa le a védelmet](./media/backup-azure-manage-vms/stop-protect-success.png)

    Ha nem választott **Delete tartozó biztonsági mentési adatok** során lehetőséget **védelem kikapcsolása** varázsló, majd a feladás egy vagy több feladat befejeződése védelmi állapot módosításai **állítvaavédelem**. Az adatok továbbra is az Azure Backup szolgáltatással, amíg le nem explicit módon. Az adatokat a virtuális gép kiválasztásával bármikor törölheti a **védett elemek** lap, és kattintson a **törlése**.

    ![Leállított védelme](./media/backup-azure-manage-vms/protection-stopped-status.png)

    Ha be van jelölve a **Delete tartozó biztonsági mentési adatok** beállítást, a virtuális gép nem része a **védett elemek** lap.

## <a name="re-protect-virtual-machine"></a>Virtuális gép védelmének újbóli beállításához
Ha nincs kiválasztva a **társítása biztonsági mentési adatok** beállítást **védelem kikapcsolása**, ismételt védelemmel láthatná el a virtuális gép lépések hasonlóak regisztrált virtuális gépek biztonsági mentését. Ha védett, a virtuális gép lesz biztonsági mentési adatok őrződnek meg előtt állítsa le a védelmet, és a helyreállítási pontok létrehozása után védelmének újbóli beállítását.

Ismételt védelemmel való ellátása után a virtuális gép védelmi állapotát változik **védett** ha vannak a helyreállítási pontok előtt **védelem kikapcsolása**.

  ![A feladatátvételen VM](./media/backup-azure-manage-vms/reprotected-status.png)

> [!NOTE]
> Ha a virtuális gép ismételt védelmével, dönthet úgy, hogy egy másik házirend érvényesíthető, mint a házirendet, amellyel a virtuális gép védett kezdetben ki.
>
>

## <a name="unregister-virtual-machines"></a>Virtuális gépek regisztrációjának törlése
Ha azt szeretné, hogy távolítsa el a virtuális gépet a mentési tároló:

1. Kattintson a **UNREGISTER** gombra az oldal alján.

    ![Tiltsa le a védelmet](./media/backup-azure-manage-vms/unregister-button.png)

    Egy bejelentési értesítést megerősítést kér a képernyő alján jelenik meg. Kattintson a **Igen** folytatja.

    ![Tiltsa le a védelmet](./media/backup-azure-manage-vms/confirm-unregister.png)

## <a name="delete-backup-data"></a>Biztonságimásolat-adatok törlése
A biztonsági mentési adatokat, vagy egy virtuális géphez tartozó törölheti:

* Védelemleállítási feladat során
* Után állítsa le a védelmet feladat befejezése után a virtuális gépen

A virtuális gépen, amely a biztonsági mentési adatok törléséhez a *állítva a védelem* állapota sikeres befejezése utáni egy **biztonsági mentés leállítása** feladat:

1. Keresse meg a **védett elemek** lapon, és válassza **Azure virtuális gép** , *típus* , és kattintson a **kiválasztása** gombra.

    ![Virtuálisgép-típussá](./media/backup-azure-manage-vms/vm-type.png)
2. Válassza ki a virtuális gépet. A virtuális gép lesz a **állítva a védelem** állapotát.

    ![Védelem leállítása](./media/backup-azure-manage-vms/protection-stopped-b.png)
3. Kattintson a **törlése** gombra az oldal alján.

    ![Törölje a biztonsági mentés](./media/backup-azure-manage-vms/delete-backup.png)
4. Az a **biztonsági mentési adatok törlése** varázsló, jelöljön ki egy (ajánlott) biztonsági mentési adatok törlésének okát, majd **Submit**.

    ![biztonsági mentési adatok törlése](./media/backup-azure-manage-vms/delete-backup-data.png)
5. Ezzel létrehoz egy feladatot, amely törli a kijelölt virtuális gép biztonsági mentési adatokat. Kattintson a **feladat megtekintése** feladatok lapján a megfelelő feladat megtekintéséhez.

    ![Adatok törlése sikeresen megtörtént](./media/backup-azure-manage-vms/delete-data-success.png)

    Ha a feladat befejeződött, a virtuális gép megfelelő bejegyzés törlődik **védett elemek** lap.

## <a name="dashboard"></a>Irányítópult
Az a **irányítópult** lapon áttekintheti az Azure virtual machines, a tároló és a feladatok az elmúlt 24 órában a hozzájuk kapcsolódó információkat. Megtekintheti a biztonsági mentés állapotának és a kapcsolódó biztonsági mentési hibák.

![Irányítópult](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

> [!NOTE]
> Az irányítópult értékeket 24 óránként egyszer frissülnek.
>
>

## <a name="auditing-operations"></a>Naplózási műveletek
Azure biztonsági mentés át kell tekinteni a "művelet logs" biztonsági mentési műveleteket, így könnyen, hogy pontosan milyen felügyeleti műveleteket a mentési tároló végrehajtott az ügyfél által indított biztosít. Műveleti naplók kiváló levágást engedélyezése, és a biztonsági mentési műveletek támogatása naplózására.

A műveletnaplók jelentkezett a következő műveleteket:

* Regisztráljon
* Regisztrálás törlése
* A védelem konfigurálása
* Biztonsági mentés (mindkettő ütemezett és igény szerinti biztonsági mentést BackupNow keresztül)
* Visszaállítás
* Állítsa le a védelmet
* biztonsági mentési adatok törlése
* Házirend hozzáadása
* Házirend törlése
* Házirend frissítése
* Megszakítása

A mentési tárolóban megfelelő műveletnaplók megtekintése:

1. Navigáljon a **szolgáltatások** az Azure-portálon, és kattintson a **műveletnaplók** fülre.

    ![Műveletnaplók](./media/backup-azure-manage-vms/ops-logs.png)
2. Válassza ki a szűrők **biztonsági mentési** , *típus* , és adja meg a mentési tároló nevére a *szolgáltatásnév* , majd kattintson a **küldje el a következőt**.

    ![A művelet naplók szűrő](./media/backup-azure-manage-vms/ops-logs-filter.png)
3. A műveletek naplófájlokban, válassza ki az összes műveletet, majd kattintson **részletek** művelet megfelelő részletek megtekintéséhez.

    ![Művelet naplók beolvasása részletei](./media/backup-azure-manage-vms/ops-logs-details.png)

    A **részletek varázsló** feladat azonosítóját, amelyen ez a művelet akkor váltódik ki, és kezdő időpont a művelet erőforrás működésbe működésével kapcsolatos adatokat tartalmazza.

    ![Művelet részletei](./media/backup-azure-manage-vms/ops-logs-details-window.png)

## <a name="alert-notifications"></a>Riasztási értesítések
A feladatok egyéni riasztási értesítéseket kaphat a portálon. PowerShell-alapú riasztási szabályok definiálása a műveleti naplókat események ehhez. Azt javasoljuk, *PowerShell 1.3.0 verzió vagy újabb*.

Egyéni biztonsági mentési hibák riasztási értesítést megadásához egy minta parancs alábbihoz hasonlóan jelenik meg:

```
PS C:\> $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail contoso@microsoft.com
PS C:\> Add-AzureRmLogAlertRule -Name backupFailedAlert -Location "East US" -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -OperationName Microsoft.Backup/backupVault/Backup -Status Failed -TargetResourceId /subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/BackupVault/trinadhVault -Actions $actionEmail
```

**ResourceId**: letölthető ez Operations Logs előugró fenti szakaszban leírtak szerint. Egy művelet részletei előugró ablakban ResourceUri adni, a parancsmag az erőforrás-azonosítója.

**OperationName**: Ez a formátum lesz "Microsoft.Backup/backupvault/<EventName>" EventName az egyik regisztrálása, Unregister, ConfigureProtection, biztonsági mentése, visszaállítása, StopProtection, DeleteBackupData, CreateProtectionPolicy, DeleteProtectionPolicy, UpdateProtectionPolicy

**Állapot**: támogatott értékek vannak-elindítva, sikeres és sikertelen volt.

**Erőforráscsoport**: az erőforrás, amelyeken a művelet akkor váltódik ki, ResourceGroup. Ezt úgy szerezheti be ResourceId értékből. Érték mező között */resourceGroups/* és */providers/* a ResourceId érték a a ResourceGroup.

**Név**: a riasztási szabály nevét.

**CustomEmail**: Adja meg a kívánt riasztási értesítés küldése egyéni e-mail cím

**SendToServiceOwners**: ezt a beállítást a rendszergazdák és a társadminisztrátorok az előfizetés riasztási értesítést küld. A használat **New-AzureRmAlertRuleEmail** parancsmag

### <a name="limitations-on-alerts"></a>Riasztások korlátozásai
Eseményalapú riasztások végzik a következő korlátozások vonatkoznak:

1. A figyelmeztetéseket az összes virtuális gépet a mentési tároló. Nem szabhatja testre, és máris beolvashatja a riasztásokat az adott virtuális gépek csoportja tartalmazza a biztonságimásolat-tárolóban.
2. A funkció jelenleg előzetes verzió. [További információ](../monitoring-and-diagnostics/insights-powershell-samples.md#create-metric-alerts)
3. Értesítéseket küld a "alerts-noreply@mail.windowsazure.com". Az e-mailt olyasvalaki jelenleg nem módosíthatók.

## <a name="next-steps"></a>Következő lépések
* [Állítsa vissza az Azure virtuális gépek](backup-azure-restore-vms.md)

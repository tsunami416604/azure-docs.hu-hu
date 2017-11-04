---
title: "Telepítéséhez és kezeléséhez biztonsági mentések erőforrás-kezelő telepített virtuális gépek PowerShell használatával |} Microsoft Docs"
description: "Használja a PowerShell telepítése és kezelése az Azure biztonsági mentések erőforrás-kezelő telepített virtuális gépekhez"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 68606e4f-536d-4eac-9f80-8a198ea94d52
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/13/2017
ms.author: markgal;trinadhk
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: db04f8c6ab61d33df80cd442abc5636867e5809a
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2017
---
# <a name="use-azurermrecoveryservicesbackup-cmdlets-to-back-up-virtual-machines"></a>Készítsen biztonsági másolatot a virtuális gépek AzureRM.RecoveryServices.Backup-parancsmagok használatával
> [!div class="op_single_selector"]
> * [Resource Manager](backup-azure-vms-automation.md)
> * [Klasszikus](backup-azure-vms-classic-automation.md)
>
>

Ez a cikk bemutatja, hogyan Azure PowerShell-parancsmagok segítségével biztonsági mentése és helyreállítása Azure virtuális gép (VM) a Recovery Services-tároló. Recovery Services-tároló egy Azure Resource Manager-erőforrás és védelmét az adatok és eszközök is az Azure Backup, és az Azure Site Recovery szolgáltatásban. Recovery Services-tároló segítségével Azure Service Manager telepített virtuális gépek és az Azure Resource Manager telepített virtuális gépek védelme.

> [!NOTE]
> Az Azure két üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a Resource Manager-modell használatával létrehozott virtuális gépek történő használatra szolgál.
>
>

Ez a cikk bemutatja, hogyan PowerShell segítségével a virtuális gép védelme, és az adatok helyreállítását a helyreállítási pontból.

## <a name="concepts"></a>Alapelvek
Ha nem ismeri az Azure Backup szolgáltatással, a szolgáltatás áttekintését kivétele [Mi az Azure Backup?](backup-introduction-to-azure-backup.md) Mielőtt elkezdené, győződjön meg arról, hogy vonatkozik-e az Azure biztonsági mentési és a virtuális gép aktuális biztonsági mentési megoldásra vonatkozó korlátozások használatához szükséges előfeltételek kapcsolatos essentials.

PowerShell hatékony használatához fontos tudni, hogy a hierarchiában, az objektumok és hol kell elkezdeni az.

![Helyreállítási szolgáltatások eltér az objektumhierarchia](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

A AzureRm.RecoveryServices.Backup PowerShell parancsmag-referencia megtekintéséhez lásd: a [Azure Backup - helyreállítási szolgáltatások parancsmagjai](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) az Azure-könyvtárban.

## <a name="setup-and-registration"></a>Telepítését és regisztrálását
Megkezdéséhez:

1. [A PowerShell legújabb verziójának letöltése](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (a szükséges minimális verziója: 1.4.0)
2. Keresse meg az Azure biztonsági mentés PowerShell-parancsmagok érhető el a következő parancs beírásával:

```
PS C:\> Get-Command *azurermrecoveryservices*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmRecoveryServicesBackupItem           1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Disable-AzureRmRecoveryServicesBackupProtection    1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Enable-AzureRmRecoveryServicesBackupProtection     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupContainer         1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupItem              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJob               1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupJobDetails        1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupManagementServer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRMRecoveryServicesBackupRecoveryPoint     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupRetentionPolic... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesBackupSchedulePolicy... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Get-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Get-AzureRmRecoveryServicesVaultSettingsFile       1.4.0      AzureRM.RecoveryServices
Cmdlet          New-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          New-AzureRmRecoveryServicesVault                   1.4.0      AzureRM.RecoveryServices
Cmdlet          Remove-AzureRmRecoveryServicesProtectionPolicy     1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Remove-AzureRmRecoveryServicesVault                1.4.0      AzureRM.RecoveryServices
Cmdlet          Restore-AzureRMRecoveryServicesBackupItem          1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesBackupProperties        1.4.0      AzureRM.RecoveryServices
Cmdlet          Set-AzureRmRecoveryServicesBackupProtectionPolicy  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Set-AzureRmRecoveryServicesVaultContext            1.4.0      AzureRM.RecoveryServices
Cmdlet          Stop-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupContainer  1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Unregister-AzureRmRecoveryServicesBackupManagem... 1.4.0      AzureRM.RecoveryServices.Backup
Cmdlet          Wait-AzureRmRecoveryServicesBackupJob              1.4.0      AzureRM.RecoveryServices.Backup
```


A PowerShell segítségével automatizálhatók a következő feladatokat:

* Recovery Services-tároló létrehozása
* Azure-beli virtuális gépek biztonsági mentése
* A biztonsági mentési feladatot indít
* A figyelő egy biztonsági mentési feladat
* Állítsa vissza az Azure virtuális gép

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
A következő lépések alapján a Recovery Services-tároló létrehozása. Recovery Services-tároló nem egyezik egy biztonsági mentési tárolót.

1. Ha az Azure biztonsági mentés először használ, kell használnia a  **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)**  parancsmag futtatásával regisztrálja az Azure Recovery szolgáltató az előfizetéshez.

    ```
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
2. A Recovery Services-tároló egy Resource Manager szerinti erőforrás,, ezért el kell helyezni az erőforráscsoporton belül. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy erőforráscsoportot a a  **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)**  parancsmag. Erőforráscsoport létrehozásakor meg nevét és helyét, ahhoz az erőforráscsoporthoz.  

    ```
    PS C:\> New-AzureRmResourceGroup –Name "test-rg" –Location "West US"
    ```
3. Használja a  **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)**  parancsmaggal hozhat létre a Recovery Services-tároló. Ne felejtse el ugyanazon a helyen, a tároló adja meg, mint az erőforráscsoport használt.

    ```
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "West US"
    ```
4. Megadhatja a használandó; adattároló redundanciája, amely használhat [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) vagy [földrajzi redundáns tárolás (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage). A következő példa bemutatja a - BackupStorageRedundancy beállítás a testvault GeoRedundant értékre van állítva.

    ```
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault –Name "testvault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Sok Azure biztonsági mentést készítő parancsmagok bemeneti adatokként a Recovery Services-tároló objektum szükséges. Emiatt célszerű a Recovery Services biztonsági másolat tároló objektum tárolható egy változóban.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>A tárolók előfizetés megtekintése
Használjon  **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)**  megtekintéséhez az összes tárolók listája az aktuális előfizetésben. Ezt a parancsot használhatja, ellenőrizze, hogy létrejött-e egy új tárolót, vagy az előfizetést az elérhető tárolók megtekintéséhez.

Futtassa a parancsot, Get-AzureRmRecoveryServicesVault, az előfizetés összes tárolók megtekintéséhez. A következő példa bemutatja a minden egyes tároló megjelenő adatokat.

```
PS C:\> Get-AzureRmRecoveryServicesVault
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


## <a name="back-up-azure-vms"></a>Azure-beli virtuális gépek biztonsági mentése
Recovery Services-tároló segítségével a virtuális gépek védelmére. Alkalmazza a védelmet, mielőtt a tárolóban (a tárolóban lévő védett adatok típusától) környezetben, és a védelmi házirend ellenőrzése. A védelmi házirend az ütemezés a biztonsági mentési feladatok futtatásakor, és mennyi ideig őrzi meg minden egyes biztonsági mentési pillanatképet.

### <a name="set-vault-context"></a>Tároló környezet beállítása
Ahhoz, hogy a virtuális gép védelme, használjon  **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)**  beállítani a tároló a környezetben. A tároló-környezet van beállítva, ha az összes későbbi parancsmag vonatkozik. Az alábbi példa megállapítja a tárolóban, a tároló *testvault*.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Védelmi házirend létrehozása
Recovery Services-tároló létrehozásakor az alapértelmezett védelem és adatmegőrzési ismét. Az alapértelmezett védelmi házirendet a biztonsági mentési feladatot, a megadott időpontban naponta váltja ki. Az alapértelmezett megőrzési házirend 30 napig őrzi meg a napi helyreállítási pont. Az alapértelmezett házirend segítségével gyorsan védelme a virtuális Gépet, és később különböző adatokkal házirend szerkesztése.

Használjon  **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)**  az adatvédelmi szabályzatok megtekintéséhez a tárolóban lévő állapottal. Ez a parancsmag is használhatja, egy adott házirend segítségével, vagy egy munkaterhelés-típushoz tartozó házirendek megtekintéséhez. Az alábbi példa-házirendet munkaterhelés, AzureVM lekérdezi.

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> Az időzóna PowerShell BackupTime mező UTC. Azonban ha az Azure-portálon a biztonságimásolat-készítési időpont látható, az idő módosul az a helyi időzónára.
>
>

A biztonsági mentési házirenddel legalább egy megőrzési házirend társítva. Adatmegőrzési házirend határozza meg, hány helyreállítási pont tartják után törli a rendszer. Használjon  **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)**  megtekintéséhez az alapértelmezett megőrzési házirend.  Hasonló módon használhatja  **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)**  az alapértelmezett ütemezés házirend beszerzéséhez. A  **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)**  parancsmag létrehoz egy PowerShell-objektum, amely tartalmazza a biztonsági mentési házirend. Az ütemezés és a megőrzési csoportházirend-objektumok bemeneteként szolgálnak a  **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)**  parancsmag. A következő példa az ütemezési házirend és az adatmegőrzési változók tárolja. A példában ezeket a változókat paraméterek megadásához a védelmi házirend létrehozásakor *NewPolicy*.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```


### <a name="enable-protection"></a>Védelem engedélyezése
Miután meghatározta a biztonsági mentési házirenddel, továbbra is engedélyeznie kell egy elem a házirendet. Használjon  **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)**  kívánja engedélyezni a védelmet. Két objektum – az elemet, és a házirend-alapú védelem engedélyezését igényli. Ha a házirend már társítva van a tárolóban, a biztonsági mentési munkafolyamat kiváltásakor a házirend-ütemezést meghatározott időpontban.

A következő példa engedélyezi a védelmet a cikkhez V2VM, a házirend, NewPolicy. Engedélyezze a védelmet a nem titkosított erőforrás-kezelő virtuális gépeken

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

Titkosított virtuális gépeken (titkosítja BEK és KEK) a védelem engedélyezéséhez szükséges engedélyt az Azure Backup szolgáltatás kulcsok és titkos olvasni kulcstároló.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

A védelem engedélyezése az titkosítja a virtuális gépek (titkosítja BEK csak), hozzá kell rendelnie az Azure Backup szolgáltatás engedély megnyithassa a kulcstároló.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Ha az Azure Government felhő használja, használja a érték ff281ffe-705c-4f53-9f37-a40e6f2c68f3 paraméter **- ServicePrincipalName** a [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) parancsmag.
>
>

Klasszikus virtuális gépekhez

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>A védelmi házirend módosítása
A védelmi házirend módosításához használható [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) a SchedulePolicy vagy RetentionPolicy objektumok módosítására.

A következő példa a helyreállítási pontok megőrzésének ideje 365 nap módosításait.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>A biztonsági mentés
Használhat  **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)**  a biztonsági mentési feladatot indít. Ha a kezdeti biztonsági másolatot, akkor egy teljes biztonsági mentés. Azt követő biztonsági mentéseket egy növekményes másolatot igénybe vehet. Használjon  **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)**  időt. a biztonsági mentési feladat előtt állítsa be a tároló környezetében. Az alábbi példa azt feltételezi, hogy a tároló környezet beállítása történt.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> A StartTime és az EndTime megadása mezők PowerShell időzóna UTC. Azonban az idő az Azure portálon megjelenítésekor a idő módosul az a helyi időzónára.
>
>

## <a name="monitoring-a-backup-job"></a>A biztonsági mentési feladatot figyelése
Hosszú ideig futó műveletek, például a biztonsági mentési feladatok segítségével figyelheti az Azure portál használata nélkül. Ahhoz, hogy az egy folyamatban lévő feladat állapotát, használja a  **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)**  parancsmag. Ez a parancsmag lekérdezi a biztonsági mentési feladatok számára egy adott tárolóban, és adott tárolóhoz van megadva a tároló a környezetben. A következő példa egy folyamatban lévő feladat tömbként állapotát olvassa be, és a $joblist változó állapotát tárolja.

```
PS C:\> $joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
PS C:\> $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Ezek a feladatok befejezésére – ami szükségtelen kód - lekérdezési helyett használja a  **[várakozási-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)**  parancsmag. Ez a parancsmag végrehajtása felfüggesztése, addig, amíg a feladat befejeződik, vagy a megadott időtúllépési érték elérésekor.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Állítsa vissza az Azure virtuális gép
A visszaállítását egy virtuális Gépet az Azure portál használatával és visszaállítása a PowerShell virtuális gépek közötti fő különbség van. A PowerShell használatával a visszaállítás befejeződött a lemezek és a konfigurációs adatokat a helyreállítási pont létrehozása után.

> [!NOTE]
> A visszaállítási művelet nem hoz létre egy virtuális gépet.
>
>

A virtuális gép létrehozása a lemezről, című szakaszban [a virtuális gép létrehozása tárolt lemezekből](backup-azure-vms-automation.md#create-a-vm-from-stored-disks). Az alapvető lépéseken, egy Azure virtuális gép visszaállítására a következők:

* Válassza ki a virtuális gép
* A helyreállítási pont kiválasztása
* A lemezek visszaállítása
* A virtuális gép létrehozása tárolt lemezekből

A következő ábra a eltér az objektumhierarchia le a BackupRecoveryPoint RecoveryServicesVault a jeleníti meg.

![Helyreállítási szolgáltatások eltér az objektumhierarchia BackupContainer megjelenítése](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Biztonsági mentési adatok helyreállítását, a biztonsági másolat elem és a helyreállítási pont a időpontban adatokat tartalmazó azonosítása. Használja a  **[visszaállítási-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)**  parancsmagot, hogy a tároló vissza adatokat a felhasználói fiókhoz.

### <a name="select-the-vm"></a>Válassza ki a virtuális gép
Ahhoz, hogy a PowerShell-objektum, amely a helyes biztonságimásolat-elem azonosítja, indítsa el a tárolóban lévő-tárolójából, és az objektum egy hierarchiában lejjebb lévő módon működnek. Válassza ki a tárolóhoz, amelybe a virtuális Gépet jelöl, használja a  **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)**  parancsmag és a csövön keresztüli, hogy a  **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)**  parancsmag.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" –Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem –Container $namedContainer  –WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>A helyreállítási pont kiválasztása
Használja a  **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)**  parancsmagot, hogy az összes helyreállítási pontról biztonsági mentési elem listán. Válassza ki a visszaállítani kívánt helyreállítási pontot. Ha biztos abban, hogy melyik helyreállítási pontot szeretné használni, ajánlott válassza ki a legutóbbi RecoveryPointType = AppConsistent pontot a listában.

A következő parancsfájlt, a változó a **$rp**, helyreállítási pontot készíteni a kijelölt biztonsági mentési elemet, az elmúlt hét napban tömbje. A tömb fordított sorrendben rendezve idő 0 indexnél a legújabb helyreállítási pontot. Standard PowerShell tömb indexelő segítségével válassza ki a helyreállítási pont. A példában $rp [0] választja ki a legutóbbi helyreállítási pontot.

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
PS C:\> $rp[0]
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```



### <a name="restore-the-disks"></a>A lemezek visszaállítása
Használja a  **[visszaállítási-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)**  parancsmag történő visszaállításához, egy biztonsági mentési elem adatot és konfigurációs egy helyreállítási pontot. Ha azonosított egy helyreállítási pontot, az legyen értékét a **- RecoveryPoint** paraméter. Előző példakód **$rp [0]** volt a helyreállítási pontot szeretné használni. Az alábbi példakód a **$rp [0]** van a lemez helyreállításához használni kívánt helyreállítási pontot.

A lemezek és a konfigurációs adatok visszaállítása:

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Használja a  **[várakozási-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)**  parancsmag a visszaállítási feladat befejeződésére vár.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

A visszaállítási feladat befejezése után használja a  **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)**  parancsmagot, hogy megkapja a visszaállítás részleteit. A JobDetails tulajdonsághoz nem tartozik az információk szükségesek ahhoz, hogy a virtuális Gépet.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

Miután helyreállította a lemezeket, nyissa meg a virtuális gép létrehozásához a következő szakasszal.

## <a name="create-a-vm-from-restored-disks"></a>Hozzon létre egy virtuális Gépet visszaállított lemezekből
Miután visszaállította a lemezeket, ezek lépések segítségével hozza létre és konfigurálja a virtuális gép lemezéről.

> [!NOTE]
> Titkosított virtuális gépek létrehozásához visszaállított lemezekről, az Azure szerepkör a művelet végrehajtásához szükséges engedéllyel kell rendelkeznie **Microsoft.KeyVault/vaults/deploy/action**. Ha a szerepkör nem rendelkezik ezzel az engedéllyel, hozzon létre egy egyéni biztonsági szerepkört ezt a műveletet. További információkért lásd: [egyéni szerepkörök az Azure RBAC](../active-directory/role-based-access-control-custom-roles.md).
>
>

1. A feladat részleteit a visszaállított lemez tulajdonságainak lekérdezése.

  ```
  PS C:\> $properties = $details.properties
  PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
  PS C:\> $containerName = $properties["Config Blob Container Name"]
  PS C:\> $blobName = $properties["Config Blob Name"]
  ```

2. Állítsa be az Azure storage-környezetben, és állítsa vissza a JSON-konfigurációs fájlt.

    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```

3. A JSON-konfigurációs fájlt használja a Virtuálisgép-konfiguráció létrehozásához.

    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
    ```

4. Az operációsrendszer-lemez és adatlemezek csatolni. Attól függően, hogy a virtuális gépek konfigurációját tekintse át a megfelelő részt megfelelő parancsmagok megtekintéséhez:

    #### <a name="non-managed-non-encrypted-vms"></a>A nem felügyelt, nem titkosított virtuális gépek

    Használja az alábbi minta nem kezelt, nem titkosított virtuális gépekhez.

    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
    PS C:\> foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
    {
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="non-managed-encrypted-vms-bek-only"></a>A nem felügyelt, titkosított virtuális gépek (csak BEK)

    Nem kezelt, titkosított virtuális gépek (titkosítja BEK csak) a titkos kulcsot a key vault visszaállításához, mielőtt csatolható lemezek kell. További információkért lásd: a cikk [visszaállítani egy titkosított virtuális gépet az Azure biztonsági mentési helyreállítási pontokról](backup-azure-restore-key-secret.md). A következő példa bemutatja, hogyan operációsrendszer- és adatlemezek titkosított virtuális géphez csatolása.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="non-managed-encrypted-vms-bek-and-kek"></a>A nem felügyelt, titkosított virtuális gépek (BEK és KEK)

    Nem kezelt, titkosított virtuális gépen (titkosítja BEK és KEK) kell visszaállítani a kulcsot és titkos kulcs a key vault előtt lemezek. További információkért lásd: a cikk [visszaállítani egy titkosított virtuális gépet az Azure biztonsági mentési helyreállítási pontokról](backup-azure-restore-key-secret.md). A következő példa bemutatja, hogyan operációsrendszer- és adatlemezek titkosított virtuális géphez csatolása.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="managed-non-encrypted-vms"></a>Felügyelt, nem titkosított virtuális gépek

    Felügyelt nem titkosított virtuális gépekhez szüksége lesz felügyelt lemezek létrehozásához az blob-tárolóból, és majd csatlakoztassa a lemezeket. Részletes információkért lásd: a cikk [adatlemezt csatolni egy Windows-VM PowerShell-lel](../virtual-machines/windows/attach-disk-ps.md). Az alábbi mintakód bemutatja, hogyan felügyelt nem titkosított virtuális gépek a adatlemezt csatolni.

    ```
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="managed-encrypted-vms-bek-only"></a>Felügyelt, titkosított virtuális gépek (csak BEK)

    Felügyelt titkosított virtuális gépek (titkosítja BEK csak) szüksége lesz felügyelt lemezek létrehozásához az blob-tárolóból, és majd csatlakoztassa a lemezeket. Részletes információkért lásd: a cikk [adatlemezt csatolni egy Windows-VM PowerShell-lel](../virtual-machines/windows/attach-disk-ps.md). Az alábbi mintakód bemutatja, hogyan felügyelt titkosított virtuális gépek a adatlemezt csatolni.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

    #### <a name="managed-encrypted-vms-bek-and-kek"></a>Felügyelt, titkosított virtuális gépek (BEK és KEK)

    Felügyelt titkosított virtuális gépek (titkosítja BEK és KEK) szüksége lesz felügyelt lemezek létrehozásához az blob-tárolóból, és majd csatlakoztassa a lemezeket. Részletes információkért lásd: a cikk [adatlemezt csatolni egy Windows-VM PowerShell-lel](../virtual-machines/windows/attach-disk-ps.md). Az alábbi mintakód bemutatja, hogyan felügyelt titkosított virtuális gépek a adatlemezt csatolni.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "StandardLRS"
    PS C:\> $osDiskName = $vm.Name + "_osdisk"
    PS C:\> $osVhdUri = $obj.'properties.storageProfile'.osDisk.vhd.uri
    PS C:\> $diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $osVhdUri
    PS C:\> $osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk $diskConfig -ResourceGroupName "test"
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $dataDiskName = $vm.Name + $dd.name ;
     $dataVhdUri = $dd.vhd.uri ;
     $dataDiskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location "West US" -CreateOption Import -SourceUri $dataVhdUri ;
     $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $dataDiskConfig -ResourceGroupName "test" ;
     Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -ManagedDiskId $dataDisk2.Id -Lun $dd.Lun -CreateOption "Attach"
     }
    ```

5. A hálózati beállításainak megadása.

    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. Hozza létre a virtuális gépet.

    ```    
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="next-steps"></a>Következő lépések
Ha jobban szeret PowerShell használata az Azure-erőforrások bevonásához, olvassa el a PowerShell, [telepítés és a Windows Server biztonsági másolat kezelése](backup-client-automation.md). DPM biztonsági mentések kezelése, tekintse meg a cikket, [telepítés és a DPM a biztonsági mentés kezelése](backup-dpm-automation.md). Ezek a cikkek mindegyikét verziónál Resource Manager üzembe helyezések vagy a klasszikus telepítések esetén.  

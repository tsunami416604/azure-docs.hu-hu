---
title: A biztonsági mentés üzembe helyezése és kezelése a Resource Managerrel üzembe helyezett virtuális gépeken a PowerShell-lel
description: Üzembe helyezése és biztonsági másolatainak kezelése az Azure Resource Manager által telepített virtuális gépek PowerShell használatával
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 8/06/2018
ms.author: markgal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: faa0908f9317c10713c41d06a22e9251998fe3c7
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378448"
---
# <a name="use-azurermrecoveryservicesbackup-cmdlets-to-back-up-virtual-machines"></a>Virtuális gépek biztonsági mentése az AzureRM.RecoveryServices.Backup parancsmagok segítségével

Ez a cikk bemutatja, hogyan biztonsági mentése és helyreállítása Azure virtuális gépként (VM) egy Recovery Services-tárolót az Azure PowerShell-parancsmagok használatával. Recovery Services-tárolót egy Azure Resource Manager-erőforrás, és adatokat és adategységeket az Azure Backup és az Azure Site Recovery services segítségével. Recovery Services-tároló használatával az Azure Service Manager által telepített virtuális gépek és Azure Resource Manager által telepített virtuális gépek védelmét.

> [!NOTE]
> Az Azure két üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a Resource Manager modellel létrehozott virtuális gépek használatra szolgál.
>
>

Ez a cikk végigvezeti egy virtuális gép védelmét, és az adatok visszaállítása egy helyreállítási pontból a PowerShell segítségével.

## <a name="concepts"></a>Alapelvek
Ha nem ismeri az Azure Backup szolgáltatás áttekintését, a szolgáltatást, tekintse meg [Mi az Azure Backup?](backup-introduction-to-azure-backup.md) Mielőtt elkezdené, győződjön meg arról, hogy vonatkozik-e az essentials tudnivalók az Azure Backup és az aktuális virtuális gép biztonsági mentési megoldás vonatkozó korlátozások használata szükséges előfeltételeket.

A PowerShell segítségével hatékonyan, fontos tudni, hogy a hierarchiában, és hol kell elkezdeni az objektumok.

![Recovery Services objektum hierarchia](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Az AzureRm.RecoveryServices.Backup PowerShell-parancsmagok leírása, tekintse meg a [Azure Backup – Recovery Services-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) az Azure-könyvtárban.

## <a name="setup-and-registration"></a>Beállítása és regisztrálása
A kezdéshez:

1. [Töltse le a PowerShell legújabb verzióját](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (a szükséges minimális verzió: 1.4.0-s)

2. Keresse meg a rendelkezésre álló Azure Backup PowerShell-parancsmagok a következő parancs beírásával:
    ```PS
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
3. Jelentkezzen be az Azure-fiók használatával **Connect-AzureRmAccount**. Ez a parancsmag kimenetei weblap kéri a hitelesítő adatait: 
    - Azt is megteheti, megadhatja a fiók hitelesítő adatait a paramétert a **Connect-AzureRmAccount** parancsmag használatával a **-hitelesítő adat** paraméter.
    - Ha Ön CSP-partner nevében egy bérlő dolgozik, adja meg az ügyfél egy bérlőt az elsődleges tartomány Bérlőazonosítója vagy a bérlő neve. Például: **Connect-AzureRmAccount-bérlő "fabrikam.com"**
4. Társítsa az előfizetést szeretné használni a fiókot, mert egy fiók több előfizetéssel is rendelkezik:

    ```PS
    PS C:\> Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Az Azure Backup használatakor az első alkalommal kell használnia a **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** parancsmagot, hogy regisztrálja az Azure Recovery Services-szolgáltatót az előfizetésében.

    ```PS
    PS C:\> Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Ellenőrizheti, hogy a szolgáltatók regisztrálása sikeresen befejeződött, a következő parancsokkal:
    ```PS
    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ``` 
A parancs kimenetében a **RegistrationState** be kell állítania, **regisztrált**. Ha nem, akkor egyszerűen futtassa újra a **[Register-AzureRmResourceProvider](http://docs.microsoft.com/powershell/module/azurerm.resources/register-azurermresourceprovider)** fenti parancsmag.

A PowerShell használatával automatizálható a következő feladatokat:

* [Helyreállítási tár létrehozása](backup-azure-vms-automation.md#create-a-recovery-services-vault)
* [Azure-beli virtuális gépek biztonsági mentése](backup-azure-vms-automation.md#back-up-azure-vms)
* [Biztonsági mentési feladat aktiválása](backup-azure-vms-automation.md#trigger-a-backup-job)
* [A biztonsági mentési feladat monitorozása](backup-azure-vms-automation.md#monitoring-a-backup-job)
* [Egy Azure virtuális gép visszaállítása](backup-azure-vms-automation.md#restore-an-azure-vm)

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A következő lépések végigvezetik Önt a Recovery Services-tároló létrehozása. Recovery Services-tároló nem egyezik egy biztonsági mentési tárolót.

1. A Recovery Services-tároló, a Resource Manager-erőforrással, így a elhelyezi egy erőforráscsoporton belül kell. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy erőforráscsoportot a **[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)** parancsmagot. Egy erőforráscsoport létrehozásakor adja meg a nevét és az erőforráscsoport helyét.  

    ```PS
    PS C:\> New-AzureRmResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Használja a **[New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault)** parancsmagot a Recovery Services-tároló létrehozásához. Mindenképpen adja meg a tároló ugyanazon a helyen, mint az erőforráscsoport.

    ```PS
    PS C:\> New-AzureRmRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Adja meg a használandó; tárhely-redundancia típusát használhat [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy-lrs.md) vagy [Georedundáns tárolás (GRS)](../storage/common/storage-redundancy-grs.md). Az alábbi példa bemutatja, hogy a - BackupStorageRedundancy beállítás a testvault GeoRedundant értékre van állítva.

    ```PS
    PS C:\> $vault1 = Get-AzureRmRecoveryServicesVault -Name "testvault"
    PS C:\> Set-AzureRmRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Számos Azure Backup-parancsmaghoz szükséges bemenetként a helyreállítási tár objektum. Ebből az okból célszerű egy változóban tárolni a helyreállítási tár objektumot.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>A tárolók megtekintheti az előfizetéshez
Használat **[Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault)** összes tárolók listájának megtekintéséhez az aktuális előfizetésben. Ezt a parancsot használhatja, ellenőrizze, hogy az új tároló létrejött, vagy tekintse meg a rendelkezésre álló tárolók az előfizetésben.

Futtassa a parancsot, Get-AzureRmRecoveryServicesVault, az előfizetésben található összes tárolók megtekintéséhez. Az alábbi példa bemutatja az egyes tárolókhoz megjelenő információk.

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
Recovery Services-tároló használatával megvédheti virtuális gépeit. Alkalmazza a védelmet, mielőtt állítani a tárolási környezetet (a tár által védett adatok típusától), és ellenőrizze az alkalmazásvédelmi szabályzatot. Az alkalmazásvédelmi szabályzat el, az ütemezés a biztonsági mentési feladatok futtatásakor, és mennyi ideig maradnak minden egyes biztonsági mentési pillanatképet.

### <a name="set-vault-context"></a>Tárolási környezet beállítása
Mielőtt engedélyezné a virtuális gép védelmét, használjon **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** a tárolási környezet beállításához. A tárolási környezet beállítását követően az minden további parancsmagra érvényes lesz. Az alábbi példa beállítja a tárolóhoz, a tárolási környezet *testvault*.

```
PS C:\> Get-AzureRmRecoveryServicesVault -Name "testvault" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Egy védelmi szabályzat létrehozása
Helyreállítási tár létrehozásakor a tár alapértelmezett védelmi és megőrzési szabályzatokkal rendelkezik. Az alapértelmezett védelmi szabályzat naponta egyszer, adott időben aktivál egy biztonsági mentési feladatot. Az alapértelmezett megőrzési szabályzat 30 napig őrzi meg a napi helyreállítási pontokat. Az alapértelmezett házirend segítségével gyorsan biztosíthatja virtuális GÉPE védelmét, és később eltérő adatokkal a szabályzat szerkesztéséhez.

Használat **[Get-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupprotectionpolicy)** az alkalmazásvédelmi szabályzatok megtekintéséhez a tárolóban. Használhatja ezt a parancsmagot egy adott szabályzatot, vagy egy számításifeladat-típust rendelt házirendek megtekintéséhez. Az alábbi példa lekéri a számításifeladat-típust, AzureVM szabályzatokat.

```
PS C:\> Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> A PowerShellben a BackupTime mező az időzóna UTC. Azonban amikor biztonsági mentésének ideje jelenik meg az Azure Portalon, az idő igazodik a helyi időzónában.
>
>

A biztonsági mentési alkalmazásvédelmi szabályzat legalább egy adatmegőrzési házirend társítva. Adatmegőrzési szabályzat határozza meg, mennyi ideig egy helyreállítási pontot a megtartani, mielőtt törölné a rendszer. Használat **[Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject)** az alapértelmezett megőrzési szabályzat megtekintéséhez.  Ehhez hasonlóan használhatja **[Get-AzureRmRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupschedulepolicyobject)** az alapértelmezett ütemezés házirend beszerzéséhez. A **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** parancsmag létrehoz egy PowerShell-objektumot, amely tartalmazza a biztonsági mentési szabályzat. A ütemezése és megőrzése csoportházirend-objektumok használhatók bemeneteként a **[New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy)** parancsmagot. A következő példában változókat az ütemezési házirend és a megőrzési házirend tárolja. A példában használja ezeket a változókat paraméterek megadásához egy védelmi szabályzat létrehozásakor *NewPolicy*.

```
PS C:\> $schPol = Get-AzureRmRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> New-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```


### <a name="enable-protection"></a>Védelem engedélyezése
Miután meghatározta a biztonsági mentési alkalmazásvédelmi szabályzatot, továbbra is engedélyeznie kell a házirendet egy elem esetében. Használat **[Enable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/enable-azurermrecoveryservicesbackupprotection)** kívánja engedélyezni a védelmet. Két objektum – az elemet, és a szabályzat védelem engedélyezéséhez szükséges. Ha a házirend társítva a tárolóhoz, a biztonsági mentési munkafolyamat akkor aktiválódik, a házirend-ütemezés meghatározott időben.

Az alábbi példa lehetővé teszi, hogy a szabályzattal NewPolicy V2VM, az elem védelmét. A nem titkosított Resource Manager virtuális gépeken a védelem engedélyezése

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

A titkosított virtuális gépek (rendelkeznek BEk-KEL és KEK titkosítása) a védelem engedélyezéséhez, engedélyeznie kell az Azure Backup szolgáltatás olvasható kulcsok és titkos kulcsok a key vault.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

A védelem engedélyezése a titkosított virtuális gépek (rendelkeznek BEk-KEL csak használatával titkosított) kell, hogy engedélyezze a az Azure Backup szolgáltatás key vault titkos kódok olvasását.

```
PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Ha az Azure Government cloud használ, használja az értéket ff281ffe-705c-4f53-9f37-a40e6f2c68f3 paraméter **- ServicePrincipalName** a [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) parancsmagot.
>
>

A klasszikus virtuális gépek

```
PS C:\> $pol=Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Enable-AzureRmRecoveryServicesBackupProtection -Policy $pol -Name "V1VM" -ServiceName "ServiceName1"
```

### <a name="modify-a-protection-policy"></a>A védelmi házirend módosítása
Az alkalmazásvédelmi szabályzat módosításához használjon [Set-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/set-azurermrecoveryservicesbackupprotectionpolicy) módosíthatja a SchedulePolicy vagy RetentionPolicy objektumokat.

Az alábbi példa módosítja a helyreállítási pont megőrzése – 365 nap.

```
PS C:\> $retPol = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
PS C:\> $retPol.DailySchedule.DurationCountInDays = 365
PS C:\> $pol= Get-AzureRmRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
PS C:\> Set-AzureRmRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Indítson egy biztonsági mentést
Használhat **[Backup-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/backup-azurermrecoveryservicesbackupitem)** egy biztonsági mentési feladat aktiválásához. Ha a kezdeti biztonsági mentés, egy teljes biztonsági mentést. További biztonsági mentés növekményes igénybe vehet. Ügyeljen arra, hogy **[Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)** a tárolási környezet beállításához a biztonsági mentési feladat aktiválása előtt. Az alábbi példa azt feltételezi, tárolási környezet lett beállítva.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $item = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
PS C:\> $job = Backup-AzureRmRecoveryServicesBackupItem -Item $item
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup               InProgress            4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> A StartTime és EndTime mezők a PowerShellben az időzóna UTC. Azonban az Azure Portalon látható az idő, amikor az idő igazodik a helyi időzónában.
>
>

## <a name="monitoring-a-backup-job"></a>Biztonsági mentési feladat figyelése
Hosszú ideig futó műveletek, például a biztonsági mentési feladatok, anélkül, hogy az Azure portal használatával követheti nyomon. Egy folyamatban lévő feladat állapotának lekérdezéséhez használja a **[Get-AzureRmRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjob)** parancsmagot. Ez a parancsmag beolvassa a biztonsági mentési feladatok egy adott tárolóhoz, és a tároló van megadva a tárolási környezet. Az alábbi példa egy folyamatban lévő feladat tömbként állapotát olvassa be, és $joblist változó tárolja az állapotát.

```
PS C:\> $joblist = Get-AzureRmRecoveryservicesBackupJob –Status "InProgress"
PS C:\> $joblist[0]
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016 5:00:30 PM           cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Ezek a feladatok befejezésére – ami szükségtelen további programkódokat kellene megtervezni - lekérdezés helyett használja a **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** parancsmagot. Ez a parancsmag felfüggeszti a végrehajtási, mindaddig, amíg a feladat befejeződik, vagy a megadott időtúllépési érték elérésekor.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Egy Azure virtuális gép visszaállítása
Nincs a fő különbség a visszaállítása egy virtuális Gépet az Azure portal használatával és a PowerShell-lel virtuális gép visszaállítása. A PowerShell használatával, a visszaállítási művelet befejeződik a lemezek és a konfigurációs adatait a helyreállítási pont létrehozása után. Ha szeretné visszaállítása vagy helyreállítás egy Azure virtuális gépek biztonsági mentésének néhány fájlt, tekintse meg a [helyreállítási szakasz fájl](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)

> [!NOTE]
> A visszaállítási művelet nem hoz létre egy virtuális gépet.
>
>

Hozhat létre egy virtuális gépet a lemezről, tekintse meg a [hozzon létre a virtuális gép helyreállított lemezekből](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Az Azure virtuális gép visszaállítása alapvető lépések a következők:

* Válassza ki a virtuális Gépet
* Válassza ki a helyreállítási pont létrehozása
* A lemezek visszaállítása
* A virtuális gép tárolt lemezek létrehozása

A következő ábra az objektumhierarchia a RecoveryServicesVault le a BackupRecoveryPoint a jeleníti meg.

![Recovery Services objektumhierarchia BackupContainer megjelenítése](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Biztonsági másolat adatainak visszaállítása, állapítsa meg a biztonsági másolat elemet, és a helyreállítási pont a időponthoz adatokat tartalmazó tárban. Használja a **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** parancsmag használatával visszaállítja az adatokat a tárolóból a felhasználói fiókhoz.

### <a name="select-the-vm"></a>Válassza ki a virtuális Gépet
A PowerShell-objektumot, amely a megfelelő biztonságimásolat-elem azonosítja, indítsa el a tárolóból a tárolóban, és a hierarchiában lejjebb lévő objektum módon működnek. Válassza ki a tárolót, hogy a virtuális gépet, használja a **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** parancsmag és a szolgáltatása, amely a **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** parancsmagot.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Válassza ki a helyreállítási pont létrehozása
Használja a **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** parancsmag használatával listázhatja a biztonságimásolat-elem tartozó összes helyreállítási pontot. Ezután válassza ki a helyreállítási pontot a visszaállításhoz. Ha biztos abban, hogy melyik helyreállítási pontot, tanácsos válassza ki a legutóbbi RecoveryPointType = AppConsistent pontot a listában.

A következő parancsfájlt, a változó a **$rp**, helyreállítási pontot a kiválasztott biztonsági mentési elemet, az elmúlt hét napban tömbje. A tömb fordított sorrendben idő 0. indexnél a legújabb helyreállítási ponttal van rendezve. Standard PowerShell tömb indexelő segítségével válassza ki a helyreállítási pont. $Rp [0] a példában a legutóbbi helyreállítási pontot választja.

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
Használja a **[Restore-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/restore-azurermrecoveryservicesbackupitem)** parancsmagot, hogy a biztonsági másolati elem adatot és konfigurációs visszaállítása egy helyreállítási pontot. Ha azonosított egy helyreállítási pontot, ezzel értékeként a **- RecoveryPoint** paraméter. Az előző példakódban **$rp [0]** volt a helyreállítási pontot. A következő mintakód **$rp [0]** van a lemez visszaállításához használni kívánt helyreállítási pontot.

A lemezek és a konfigurációs adatok visszaállításához:

```
PS C:\> $restorejob = Restore-AzureRmRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
PS C:\> $restorejob
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Használja a **[Wait-AzureRmRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/wait-azurermrecoveryservicesbackupjob)** parancsmagot, hogy a visszaállítási feladat végrehajtásához.

```
PS C:\> Wait-AzureRmRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

A visszaállítási feladat befejezése után használja a **[Get-AzureRmRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupjobdetails)** parancsmagot, hogy a visszaállítási művelet részleteit. A JobDetails tulajdonságnak építse újra a virtuális gép szükséges információkat.

```
PS C:\> $restorejob = Get-AzureRmRecoveryServicesBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmRecoveryServicesBackupJobDetails -Job $restorejob
```

Miután a lemezek visszaállítását, nyissa meg a következő szakaszban a virtuális gép létrehozásához.

## <a name="create-a-vm-from-restored-disks"></a>Hozzon létre egy virtuális gép a helyreállított lemezek alapján
A lemezek visszaállítását követően használja ezeket a lépéseket kell létrehozni és konfigurálni a virtuális gépet a lemezről.

> [!NOTE]
> Titkosított virtuális gépek létrehozásához a helyreállított lemezek alapján, az Azure-szerepkörhöz a művelet végrehajtásához szükséges engedéllyel kell rendelkeznie **Microsoft.KeyVault/vaults/deploy/action**. Ha a szerepkör nem rendelkezik ezzel az engedéllyel, hozzon létre egy egyéni szerepkör ezt a műveletet. További információkért lásd: [egyéni szerepkörök az Azure RBAC](../role-based-access-control/custom-roles.md).
>
>

1. A lekérdezés a visszaállított lemez tulajdonságait a feladat részleteit.

  ```
  PS C:\> $properties = $details.properties
  PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
  PS C:\> $containerName = $properties["Config Blob Container Name"]
  PS C:\> $configBlobName = $properties["Config Blob Name"]
  ```

2. Állítsa be az Azure storage-környezetet, és állítsa vissza a JSON konfigurációs fájl.

    ```
    PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
    PS C:\> $destination_path = "C:\vmconfig.json"
    PS C:\> Get-AzureStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
    PS C:\> $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
    ```

3. A JSON konfigurációs fájl használatával hozza létre a virtuális gép konfigurációját.

    ```
   PS C:\> $vm = New-AzureRmVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
    ```

4. Operációsrendszer-lemez és adatlemezek csatolása. A virtuális gépeket a konfigurációtól függően tekintse meg a megfelelő szakaszban megfelelő parancsmagok megtekintéséhez:

    #### <a name="non-managed-non-encrypted-vms"></a>A nem felügyelt, nem titkosított virtuális gépek

    Nem felügyelt, nem titkosított virtuális gépek a következő mintát használja.

    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
    PS C:\> foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
    {
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
    }
    ```

    #### <a name="non-managed-encrypted-vms-bek-only"></a>A nem felügyelt, titkosított virtuális gépek (csak a blokktitkosítási kulcsot)

    Nem felügyelt, titkosított virtuális gépek (rendelkeznek BEk-KEL csak használatával titkosított) a titkos kulcs visszaállítása előtt lemez is csatlakoztatható a key vault kell. További információkért lásd: a cikk [egy titkosított virtuális gépek visszaállítása az Azure Backup helyreállítási pontokról](backup-azure-restore-key-secret.md). A következő minta bemutatja a titkosított virtuális gépekhez tartozó operációsrendszer- és adatlemezek csatolása.

    ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    ```
    
 Az operációsrendszer-lemez beállításakor, ellenőrizze, hogy említett, a megfelelő operációs rendszer típusa   
    ```
    PS C:\> Set-AzureRmVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
    PS C:\> $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
    PS C:\> foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzureRmVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
    ```
    
Az adattitkosítás lemezeket manuálisan engedélyezni kell a következő paranccsal.

    ```
    Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -VolumeType Data
    ```
    
   #### <a name="non-managed-encrypted-vms-bek-and-kek"></a>A nem felügyelt, titkosított virtuális gépek (rendelkeznek BEk-KEL és KEK)

   A nem felügyelt, titkosított (használatával titkosított virtuális gépeket rendelkeznek BEk-KEL és kek-KEL) a kulcs és titkos kulcs visszaállítása a key vaulthoz lemez is csatlakoztatható előtt kell. További információkért lásd: a cikk [egy titkosított virtuális gépek visszaállítása az Azure Backup helyreállítási pontokról](backup-azure-restore-key-secret.md). A következő minta bemutatja a titkosított virtuális gépekhez tartozó operációsrendszer- és adatlemezek csatolása.

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

Az adattitkosítás lemezeket manuálisan engedélyezni kell a következő paranccsal.

    ```
    Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
    ```
    
   #### <a name="managed-non-encrypted-vms"></a>Felügyelt, nem titkosított virtuális gépek

   Felügyelt nem titkosított virtuális gépek esetén kell felügyelt lemez gyors létrehozásához a blob storage-ból, majd csatlakoztassa a lemezeket. Részletes információkért lásd: a cikk [adatlemez csatolása a PowerShell használatával Windows virtuális gép](../virtual-machines/windows/attach-disk-ps.md). Az alábbi mintakód bemutatja, hogyan az adatlemezek felügyelt nem titkosított virtuális gépek számára.

    ```
    PS C:\> $storageType = "Standard_LRS"
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

   #### <a name="managed-encrypted-vms-bek-only"></a>Felügyelt, titkosított virtuális gépek (csak a blokktitkosítási kulcsot)

   Felügyelt titkosított (használatával titkosított virtuális gépeket rendelkeznek BEk-KEL csak), a következőket kell tennie felügyelt lemez gyors létrehozásához a blob storage-ból, majd csatlakoztassa a lemezeket. Részletes információkért lásd: a cikk [adatlemez csatolása a PowerShell használatával Windows virtuális gép](../virtual-machines/windows/attach-disk-ps.md). Az alábbi mintakód bemutatja, hogyan az adatlemezek felügyelt, titkosított virtuális gépek számára.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "Standard_LRS"
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

Az adattitkosítás lemezeket manuálisan engedélyezni kell a következő paranccsal.

    ```
    Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
    ```
    
   #### <a name="managed-encrypted-vms-bek-and-kek"></a>Felügyelt, titkosított virtuális gépek (rendelkeznek BEk-KEL és KEK)

   A felügyelt titkosított (használatával titkosított virtuális gépeket rendelkeznek BEk-KEL és kek-KEL) szüksége lesz a felügyelt lemez gyors létrehozásához a blob storage-ból, majd csatlakoztassa a lemezeket. Részletes információkért lásd: a cikk [adatlemez csatolása a PowerShell használatával Windows virtuális gép](../virtual-machines/windows/attach-disk-ps.md). Az alábbi mintakód bemutatja, hogyan az adatlemezek felügyelt, titkosított virtuális gépek számára.

     ```
    PS C:\> $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    PS C:\> $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    PS C:\> $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    PS C:\> $storageType = "Standard_LRS"
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
Az adattitkosítás lemezeket manuálisan engedélyezni kell a következő paranccsal.

    ```
    Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $dekUrl -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
    ```
    
5. Állítsa be a hálózati beállításokat.

    ```
    PS C:\> $nicName="p1234"
    PS C:\> $pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    PS C:\> $virtualNetwork = New-AzureRmVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    PS C:\> $virtualNetwork | Set-AzureRmVirtualNetwork
    PS C:\> $vnet = Get-AzureRmVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    PS C:\> $subnetindex=0
    PS C:\> $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    PS C:\> $vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    ```
6. Hozza létre a virtuális gépet.

    ```    
    PS C:\> New-AzureRmVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Fájlok visszaállítása egy Azure virtuális gép biztonsági mentése

A lemezek visszaállítását, valamint egy Azure virtuális gépek biztonsági mentését egyes fájlokat is visszaállíthatja. A visszaállítási fájlok funkciók hozzáférést biztosít a helyreállítási pont található összes fájl, és kezelheti azokat a fájlkezelő segítségével, mint a normál fájlok.

A fájl biztonsági másolatból történő visszaállítását Azure virtuális gép alapvető lépések a következők:

* Válassza ki a virtuális Gépet
* Válassza ki a helyreállítási pont létrehozása
* Helyreállítási pont található lemezeket
* Másolja a szükséges fájlokat
* A lemez leválasztása


### <a name="select-the-vm"></a>Válassza ki a virtuális Gépet
A PowerShell-objektumot, amely a megfelelő biztonságimásolat-elem azonosítja, indítsa el a tárolóból a tárolóban, és a hierarchiában lejjebb lévő objektum módon működnek. Válassza ki a tárolót, hogy a virtuális gépet, használja a **[Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)** parancsmag és a szolgáltatása, amely a **[ Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)** parancsmagot.

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Válassza ki a helyreállítási pont létrehozása
Használja a **[Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)** parancsmag használatával listázhatja a biztonságimásolat-elem tartozó összes helyreállítási pontot. Ezután válassza ki a helyreállítási pontot a visszaállításhoz. Ha biztos abban, hogy melyik helyreállítási pontot, tanácsos válassza ki a legutóbbi RecoveryPointType = AppConsistent pontot a listában.

A következő parancsfájlt, a változó a **$rp**, helyreállítási pontot a kiválasztott biztonsági mentési elemet, az elmúlt hét napban tömbje. A tömb fordított sorrendben idő 0. indexnél a legújabb helyreállítási ponttal van rendezve. Standard PowerShell tömb indexelő segítségével válassza ki a helyreállítási pont. $Rp [0] a példában a legutóbbi helyreállítási pontot választja.

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

### <a name="mount-the-disks-of-recovery-point"></a>Helyreállítási pont található lemezeket

Használja a **[Get-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprpmountscript)** -parancsmaggal beolvasható a szkriptet a helyreállítási pont az összes lemez csatlakoztatásához.

> [!NOTE]
> A lemezek, a gép, ahol a szkript futása iSCSI csatolt lemeznek csatlakoztatva vannak. Ezért van közel azonnali, és nem jár semmilyen díj
>
>

```
PS C:\> Get-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]

OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```
Futtassa a szkriptet a számítógépen, ahol szeretné helyreállítani a fájlokat. Adja meg a jelszót, a szkript a fent látható kell. Miután a lemezekkel rendelkezik, a Windows Fájlkezelőben használatával az új kötetek és a fájlok között tallózva. További részletekért tekintse meg a [fájl recovery dokumentációja](backup-azure-restore-files-from-vm.md)

### <a name="unmount-the-disks"></a>A lemezek leválasztása
Másolja a szükséges fájlokat, miután a lemezek leválasztása a használatával a **[Disable-AzureRmRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/disable-azurermrecoveryservicesbackuprpmountscript?view=azurermps-5.0.0)** parancsmagot. Az erősen ajánlott, ellenőrzi, hogy, hogy a fájlokat a helyreállítási pont hozzáférés eltávolítása

```
PS C:\> Disable-AzureRmRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```


## <a name="next-steps"></a>További lépések
Ha inkább a érhet el az Azure-erőforrások PowerShell-lel, tekintse meg a PowerShell a cikkben [üzembe helyezés és a Windows Server biztonsági mentés kezelése](backup-client-automation.md). Ha Ön kezeli a DPM biztonsági mentések, ismertető cikkben [üzembe helyezés és a DPM a biztonsági mentés kezelése](backup-dpm-automation.md). Az alábbi cikkek is szükséges a Resource Manager-telepítésekhez és a klasszikus üzembe helyezéssel.  

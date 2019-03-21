---
title: Biztonsági mentése és helyreállítása Azure virtuális gépeken az Azure Backup segítségével a PowerShell-lel
description: Ismerteti, hogyan lehet biztonsági mentése és helyreállítása Azure virtuális gépeken az Azure Backup segítségével a PowerShell-lel
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: a3fd89ee67b495d3ca1173faa9381ceba117ef63
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259312"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Biztonsági mentése és helyreállítása Azure virtuális gépeken a PowerShell-lel

Ez a cikk azt ismerteti, hogyan biztonsági mentése és visszaállítása az Azure virtuális gép egy [Azure Backup](backup-overview.md) Recovery Services-tároló PowerShell-parancsmagok használatával. 

Ebben a cikkben az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy Recovery Services-tárolót, és állítsa be a tárolási környezet.
> * Biztonsági mentési szabályzat meghatározása
> * A biztonsági mentési szabályzat alkalmazása több virtuális gép védelme érdekében
> * Az eseményindító egy igény szerinti biztonsági mentési feladat előtt a védett virtuális gépek is biztonsági mentése (vagy védelme) egy virtuális gépet, el kell végeznie a [Előfeltételek](backup-azure-arm-vms-prepare.md) készítse elő a környezetét a virtuális gépek védelmére. 




## <a name="before-you-start"></a>Előkészületek

- [További](backup-azure-recovery-services-vault-overview.md) Recovery Services-tárolók kapcsolatban.
- [Tekintse át](backup-architecture.md#architecture-direct-backup-of-azure-vms) az architektúra az Azure VM backup esetében [ismertetése](backup-azure-vms-introduction.md) a biztonsági mentési folyamat, és [tekintse át](backup-support-matrix-iaas.md) támogatási, korlátozások és az előfeltételek.
- Tekintse át a Recovery Services PowerShell objektum hierarchiában.


## <a name="recovery-services-object-hierarchy"></a>Recovery Services objektum hierarchia

Az alábbi ábrán az objektumhierarchia foglalja össze.

![Recovery Services objektum hierarchia](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Tekintse át a **Az.RecoveryServices** [parancsmag-referencia](https://docs.microsoft.com/powershell/module/Az.RecoveryServices/?view=azps-1.4.0) referencia az Azure-könyvtárban.



## <a name="set-up-and-register"></a>Beállítása és regisztrálása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A kezdéshez:

1. [A PowerShell legújabb verziójának letöltése](https://docs.microsoft.com/powershell/azure/install-az-ps)

2. Keresse meg a rendelkezésre álló Azure Backup PowerShell-parancsmagok a következő parancs beírásával:

    ```powershell
    Get-Command *azrecoveryservices*
    ```   
 
    Az aliasok és az Azure Backup az Azure Site Recovery és a Recovery Services-tároló-parancsmagok jelennek meg. Az alábbi képen látható egy példa látni. Akkor sem a parancsmagok teljes listája.

    ![a Recovery Services listája](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Jelentkezzen be az Azure-fiók használatával **Connect-AzAccount**. Ez a parancsmag kimenetei weblap kéri a hitelesítő adatait:

    * Azt is megteheti, megadhatja a fiók hitelesítő adatait a paramétert a **Connect-AzAccount** parancsmag használatával a **-hitelesítő adat** paraméter.
    * Ha Ön CSP-partner nevében egy bérlő dolgozik, adja meg az ügyfél egy bérlőt az elsődleges tartomány Bérlőazonosítója vagy a bérlő neve. Példa: **Connect-AzAccount -Tenant "fabrikam.com"**

4. Társítsa az előfizetést szeretné használni a fiókot, mert egy fiók több előfizetéssel is rendelkezik:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Az Azure Backup használatakor az első alkalommal kell használnia a **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** parancsmagot, hogy regisztrálja az Azure Recovery Services-szolgáltatót az előfizetésében.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Ellenőrizheti, hogy a szolgáltatók regisztrálása sikeresen befejeződött, a következő parancsokkal:
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    A parancs kimenetében a **RegistrationState** kell módosítani a **regisztrált**. Ha nem, egyszerűen csak futtatnia kell a **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** újra a parancsmagot.


## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A következő lépések végigvezetik Önt a Recovery Services-tároló létrehozása. Recovery Services-tároló nem egyezik egy biztonsági mentési tárolót.

1. A Recovery Services-tároló, a Resource Manager-erőforrással, így a elhelyezi egy erőforráscsoporton belül kell. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy erőforráscsoportot a **[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** parancsmagot. Egy erőforráscsoport létrehozásakor adja meg a nevét és az erőforráscsoport helyét.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Használja a [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault?view=azps-1.4.0) parancsmagot a Recovery Services-tároló létrehozásához. Mindenképpen adja meg a tároló ugyanazon a helyen, mint az erőforráscsoport.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Adja meg a használandó; tárhely-redundancia típusát használhat [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy-lrs.md) vagy [Georedundáns tárolás (GRS)](../storage/common/storage-redundancy-grs.md). Az alábbi példa bemutatja, hogy a - BackupStorageRedundancy beállítás a testvault GeoRedundant értékre van állítva.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Számos Azure Backup-parancsmaghoz szükséges bemenetként a helyreállítási tár objektum. Ebből az okból célszerű egy változóban tárolni a helyreállítási tár objektumot.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>A tárolók megtekintheti az előfizetéshez

Az előfizetés összes tárolók megtekintéséhez használja [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0):

```powershell
Get-AzRecoveryServicesVault
```

A kimenet a következő példához hasonló, figyelje meg, hogy a társított ResourceGroupName és a helyet.

```
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

Mielőtt engedélyezné a virtuális gép védelmét, használjon [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) a tárolási környezet beállításához. A tárolási környezet beállítását követően az minden további parancsmagra érvényes lesz. Az alábbi példa beállítja a tárolóhoz, a tárolási környezet *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="create-a-protection-policy"></a>Egy védelmi szabályzat létrehozása

Helyreállítási tár létrehozásakor a tár alapértelmezett védelmi és megőrzési szabályzatokkal rendelkezik. Az alapértelmezett védelmi szabályzat naponta egyszer, adott időben aktivál egy biztonsági mentési feladatot. Az alapértelmezett megőrzési szabályzat 30 napig őrzi meg a napi helyreállítási pontokat. Az alapértelmezett házirend segítségével gyorsan biztosíthatja virtuális GÉPE védelmét, és később eltérő adatokkal a szabályzat szerkesztéséhez.

Használata **[Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) a tárolóban elérhető az alkalmazásvédelmi szabályzatok megtekintéséhez. Használhatja ezt a parancsmagot egy adott szabályzatot, vagy egy számításifeladat-típust rendelt házirendek megtekintéséhez. Az alábbi példa lekéri a számításifeladat-típust, AzureVM szabályzatokat.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
```

A kimenet a következő példához hasonló:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> A PowerShellben a BackupTime mező az időzóna UTC. Azonban amikor biztonsági mentésének ideje jelenik meg az Azure Portalon, az idő igazodik a helyi időzónában.
>
>

A biztonsági mentési alkalmazásvédelmi szabályzat legalább egy adatmegőrzési házirend társítva. A megőrzési házirend határozza meg, mennyi ideig egy helyreállítási pontot a megtartani, mielőtt törölné a rendszer.

- Használat [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) az alapértelmezett megőrzési szabályzat megtekintéséhez.
- Ehhez hasonlóan használhatja [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) az alapértelmezett ütemezés házirend beszerzéséhez.
- A [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) parancsmag létrehoz egy PowerShell-objektumot, amely tartalmazza a biztonsági mentési szabályzat.
- A ütemezése és megőrzése csoportházirend-objektumok használhatók a New-AzRecoveryServicesBackupProtectionPolicy parancsmag bemeneteként.

A következő példában változókat az ütemezési házirend és a megőrzési házirend tárolja. A példában használja ezeket a változókat paraméterek megadásához egy védelmi szabályzat létrehozásakor *NewPolicy*.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A kimenet a következő példához hasonló:

```
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Védelem engedélyezése

Miután meghatározta a védelmi házirend, továbbra is engedélyeznie kell a házirendet egy elem esetében. Használat [engedélyezése – AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) kívánja engedélyezni a védelmet. Két objektum – az elemet, és a szabályzat védelem engedélyezéséhez szükséges. Ha a házirend társítva a tárolóhoz, a biztonsági mentési munkafolyamat akkor aktiválódik, a házirend-ütemezés meghatározott időben.

Az alábbi példák engedélyezni a védelmet a cikkhez, V2VM, NewPolicy házirendet használja. A példák a virtuális gép titkosított attól függően eltérőek lehetnek, és milyen típusú titkosítás.

A védelem engedélyezése a **nem titkosított Resource Manager virtuális gépek**:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

A titkosított virtuális gépek (rendelkeznek BEk-KEL és KEK titkosítása) a védelem engedélyezéséhez meg kell adni az Azure Backup szolgáltatás olvasásához szükséges engedély kulcsokat és titkos kulcsokat a kulcstartóból.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

A védelem engedélyezése a **titkosított virtuális gépek (rendelkeznek BEk-KEL csak használatával titkosított)**, meg kell adni az Azure Backup szolgáltatás engedély a kulcstartóban található titkos kódok olvasását.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"
```

> [!NOTE]
> Ha az Azure Government cloud használ, használja az értéket ff281ffe-705c-4f53-9f37-a40e6f2c68f3 ServicePrincipalName paraméter a [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmagot.
>


### <a name="modify-a-protection-policy"></a>A védelmi házirend módosítása

Az alkalmazásvédelmi szabályzat módosításához használjon [Set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) módosíthatja a SchedulePolicy vagy RetentionPolicy objektumokat.

Az alábbi példa módosítja a helyreállítási pont megőrzése – 365 nap.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy"
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol
```

## <a name="trigger-a-backup"></a>Indítson egy biztonsági mentést

Használat [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) egy biztonsági mentési feladat aktiválásához. Ha a kezdeti biztonsági mentés, egy teljes biztonsági mentést. További biztonsági mentés növekményes igénybe vehet. Ügyeljen arra, hogy **[Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext)** a tárolási környezet beállításához a biztonsági mentési feladat aktiválása előtt. Az alábbi példa azt feltételezi, hogy a tárolási környezet már be lett állítva.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

A kimenet a következő példához hasonló:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> A StartTime és EndTime mezők a PowerShellben az időzóna UTC. Azonban az Azure Portalon látható az idő, amikor az idő igazodik a helyi időzónában.
>
>

## <a name="monitoring-a-backup-job"></a>Biztonsági mentési feladat figyelése

Hosszú ideig futó műveletek, például a biztonsági mentési feladatok, anélkül, hogy az Azure portal használatával követheti nyomon. Egy folyamatban lévő feladat állapotának lekérdezéséhez használja a [Get-AzRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) parancsmagot. Ez a parancsmag beolvassa a biztonsági mentési feladatok egy adott tárolóhoz, és a tároló van megadva a tárolási környezet. Az alábbi példa egy folyamatban lévő feladat tömbként állapotát olvassa be, és $joblist változó tárolja az állapotát.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress"
$joblist[0]
```

A kimenet a következő példához hasonló:

```
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Ezek a feladatok befejezésére – ami szükségtelen további programkódokat kellene megtervezni - lekérdezés helyett használja a [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) parancsmagot. Ez a parancsmag felfüggeszti a végrehajtási, mindaddig, amíg a feladat befejeződik, vagy a megadott időtúllépési érték elérésekor.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200
```

## <a name="restore-an-azure-vm"></a>Egy Azure virtuális gép visszaállítása

Fontos különbség a visszaállítása egy virtuális Gépet az Azure Portallal és a PowerShell-lel virtuális gép visszaállítása van. A PowerShell használatával, a visszaállítási művelet befejeződik a lemezek és a konfigurációs adatait a helyreállítási pont létrehozása után. A visszaállítási művelet nem hozza létre a virtuális gépet. Hozhat létre egy virtuális gépet a lemezről, tekintse meg a [hozzon létre a virtuális gép helyreállított lemezekből](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Ha nem szeretné a teljes virtuális gép helyreállítása, de visszaállítása vagy helyreállítás néhány fájlokat egy Azure virtuális gépek biztonsági mentését kívánja, tekintse meg a [helyreállítási szakasz fájl](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup).

> [!Tip]
> A visszaállítási művelet nem hoz létre a virtuális gépet.
>
>

A következő ábra az objektumhierarchia a RecoveryServicesVault le a BackupRecoveryPoint a jeleníti meg.

![Recovery Services objektumhierarchia BackupContainer megjelenítése](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Biztonsági másolat adatainak visszaállítása, állapítsa meg a biztonsági másolat elemet, és a helyreállítási pont a időponthoz adatokat tartalmazó tárban. Használat [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) adatok visszaállítása a tárból a fiókjához.

Az Azure virtuális gép visszaállítása alapvető lépések a következők:

* Válassza ki a virtuális gépet.
* Válassza ki egy helyreállítási pontot.
* Állítsa vissza a lemezeket.
* A virtuális gép létrehozása a tárolt lemezekről.

### <a name="select-the-vm"></a>Válassza ki a virtuális Gépet

A PowerShell-objektumot, amely a megfelelő biztonságimásolat-elem azonosítja, indítsa el a tárolóból a tárolóban, és a hierarchiában lejjebb lévő objektum módon működnek. Válassza ki a tárolót, hogy a virtuális gépet, használja a [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) parancsmag és a szolgáltatása, amely az a [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) parancsmagot.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Válasszon helyreállítási pontot

Használja a [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) parancsmag használatával listázhatja a biztonságimásolat-elem tartozó összes helyreállítási pontot. Ezután válassza ki a helyreállítási pontot a visszaállításhoz. Ha biztos abban, hogy melyik helyreállítási pontot, tanácsos válassza ki a legutóbbi RecoveryPointType = AppConsistent pontot a listában.

A következő parancsfájlt, a változó a **$rp**, helyreállítási pontot a kiválasztott biztonsági mentési elemet, az elmúlt hét napban tömbje. A tömb fordított sorrendben idő 0. indexnél a legújabb helyreállítási ponttal van rendezve. Standard PowerShell tömb indexelő segítségével válassza ki a helyreállítási pont. $Rp [0] a példában a legutóbbi helyreállítási pontot választja.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

A kimenet a következő példához hasonló:

```powershell
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

Használja a [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) parancsmagot, hogy a biztonsági másolati elem adatot és konfigurációs visszaállítása egy helyreállítási pontot. Ha azonosította a helyreállítási pont, használjon, értékeként a **- RecoveryPoint** paraméter. A fenti mintában **$rp [0]** volt a helyreállítási pontot. A következő mintakód **$rp [0]** van a lemez visszaállításához használni kívánt helyreállítási pontot.

A lemezek és a konfigurációs adatok visszaállításához:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG"
$restorejob
```

#### <a name="restore-managed-disks"></a>Felügyelt lemezek visszaállítását

> [!NOTE]
> Ha a mentett virtuális gép felügyelt lemezek vannak, és visszaállítja őket felügyelt lemezként, bevezettük a funkció az Azure PowerShell erőforrás-kezelő modul v 6.7.0. és újabb verziók
>
>

Adjon meg egy további paraméter **TargetResourceGroupName** a felügyelt lemezek visszaállítja RG megadásához. 

> [!NOTE]
> Erősen ajánlott a használata a **TargetResourceGroupName** paramétere visszaállítása felügyelt lemezek jelentős teljesítménynövekedést eredményez, mivel. Ezenkívül modulból Az Azure Powershell 1.0-s és újabb verziók, ez a paraméter megadása kötelező esetén visszaállítást a felügyelt lemezekkel rendelkező
>
>


```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks"
```

A **VMConfig.JSON** storage-fiókhoz lesz visszaállítva, és a felügyelt lemezek cél RG lesz visszaállítva.

A kimenet a következő példához hasonló:

```powershell
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Használja a [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) parancsmagot, hogy a visszaállítási feladat végrehajtásához.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

A visszaállítási feladat befejezése után használja a [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) parancsmagot, hogy a visszaállítási művelet részleteit. A JobDetails tulajdonságnak építse újra a virtuális gép szükséges információkat.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob
```

Miután a lemezek visszaállítását, nyissa meg a következő szakaszban a virtuális gép létrehozásához.

## <a name="create-a-vm-from-restored-disks"></a>Hozzon létre egy virtuális gép a helyreállított lemezek alapján

Miután visszaállította a lemezeket, a következő eljárással hozhat létre és konfigurálja a virtuális gépet a lemezről.

> [!NOTE]
> Titkosított virtuális gépek létrehozásához a helyreállított lemezek alapján, az Azure-szerepkörhöz a művelet végrehajtásához szükséges engedéllyel kell rendelkeznie **Microsoft.KeyVault/vaults/deploy/action**. Ha a szerepkör nem rendelkezik ezzel az engedéllyel, hozzon létre egy egyéni szerepkör ezt a műveletet. További információkért lásd: [egyéni szerepkörök az Azure RBAC](../role-based-access-control/custom-roles.md).
>
>

> [!NOTE]
> A lemezek visszaállítását követően most már beszerezheti közvetlenül használatával hozzon létre egy új virtuális Gépet, amely a központi telepítési sablont. Nincs több különböző PS parancsmagok hozhat létre kombinace spravovaného a nespravovaného virtuális gépeket, amelyek titkosított/titkosítás nélkül.

A létrejövő feladat részleteit a sablon URI, amely lekérdezhetők és üzembe helyezett biztosít.

```powershell
   $properties = $details.properties
   $templateBlobURI = $properties["Template Blob Uri"]
```

Egyszerűen helyezze üzembe a sablont, egy új virtuális Gépet hoz létre, leírtak [Itt](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy#deploy-a-template-from-an-external-source).

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobURI -storageAccountType Standard_GRS
```

A következő szakaszban azok "VMConfig" fájlt használó virtuális gép létrehozásához szükséges lépéseket.

> [!NOTE]
> Erősen ajánlott lásd fent egy virtuális Gépet hoz létre központi telepítési sablon használatához. Ebben a szakaszban (1 – 6. pontok) hamarosan elavulttá válik.

1. A lekérdezés a visszaállított lemez tulajdonságait a feladat részleteit.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Állítsa be az Azure storage-környezetet, és állítsa vissza a JSON konfigurációs fájl.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. A JSON konfigurációs fájl használatával hozza létre a virtuális gép konfigurációját.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Operációsrendszer-lemez és adatlemezek csatolása. Ebben a lépésben a példák a különböző felügyelt és a Virtuálisgép-konfigurációk titkosítva biztosít. A Virtuálisgép-konfiguráció megfelelő a példát követve.

   * **A nem felügyelt és nem titkosított virtuális gépek** – használja a következő mintát nem felügyelt, nem titkosított virtuális gépek számára.

       ```powershell
       Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
       ```

   * **A nem felügyelt és a titkosított virtuális gépek az Azure ad-vel (csak a blokktitkosítási kulcsot)** – nem felügyelt, titkosított virtuális gépek az Azure ad-vel (rendelkeznek BEk-KEL csak használatával titkosított), vissza kell állítania a titkos kulcsot a key vaulthoz, mielőtt lemez is csatlakoztatható. További információkért lásd: a [egy titkosított virtuális gépek visszaállítása az Azure Backup helyreállítási pontokról](backup-azure-restore-key-secret.md). A következő minta bemutatja a titkosított virtuális gépekhez tartozó operációsrendszer- és adatlemezek csatolása. Az operációsrendszer-lemez beállításakor győződjön meg arról, ha a megfelelő operációs rendszer típusa.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **A nem felügyelt és a titkosított virtuális gépek az Azure ad-vel (rendelkeznek BEk-KEL és KEK)** – nem felügyelt, titkosított virtuális gépek az Azure AD (rendelkeznek BEk-KEL és kek-KEL használatával titkosítva), a kulcs és titkos kulcs visszaállítása a key vaulthoz a lemezek csatolása előtt. További információkért lásd: [egy titkosított virtuális gépek visszaállítása az Azure Backup helyreállítási pontokról](backup-azure-restore-key-secret.md). A következő minta bemutatja a titkosított virtuális gépekhez tartozó operációsrendszer- és adatlemezek csatolása.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
      ```

   * **A nem felügyelt és a titkosított virtuális gépek anélkül, hogy az Azure AD (kizárólag a blokktitkosítási kulcsot)** – nem felügyelt, titkosított virtuális gépek anélkül, hogy az Azure AD (titkosítva rendelkeznek BEk-KEL csak), ha forrás **keyVault vagy titkos kódja nem érhető el** állítsa vissza a titkos kulcsok a key vaulttal az eljárás segítségével [egy nem titkosított virtuális gépek visszaállítása az Azure Backup helyreállítási pontokról](backup-azure-restore-key-secret.md). Ezután hajtsa végre az alábbi parancsfájlok a visszaállított operációs rendszer blob (Ez a lépés nem kötelező adatokat a BLOB) titkosítási adatok beállítása. A visszaállított kulcstartóból a $dekurl lehet beolvasni.<br>

   Az alábbi szkriptet kell végrehajtani, csak akkor, ha a forrás keyVault vagy titkos kódja nem érhető el.

      ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```

    Miután a **titkok érhetők el** és a titkosítási adatok is lehet megadni az operációs rendszer Blob, csatlakoztassa a lemezeket, az alábbi parancsfájl használatával.<br>

    Ha a forrás keyVault vagy titkos kódokkal már rendelkezésre állnak, majd a fenti szkript kell nem hajtható végre.

      ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **A nem felügyelt és a titkosított virtuális gépek (rendelkeznek BEk-KEL és KEK) az Azure AD nélkül** – nem felügyelt, titkosított virtuális gépek (titkosítva rendelkeznek BEk-KEL és KEK), az Azure AD nélkül Ha forrás **keyVault/kulcsa vagy titkos kódja nem érhetők el** kulcs helyreállítása és hajtsa végre az eljárást, a kulcstartó titkos kódok [egy nem titkosított virtuális gépek visszaállítása az Azure Backup helyreállítási pontokról](backup-azure-restore-key-secret.md). Ezután hajtsa végre az alábbi parancsfájlok a visszaállított operációs rendszer blob (Ez a lépés nem kötelező adatokat a BLOB) titkosítási adatok beállítása. A visszaállított kulcstartóból a $dekurl és $kekurl lehet beolvasni.

   Az alábbi szkriptet kell végrehajtani, csak akkor, ha a forrás keyVault/kulcsa vagy titkos kódja nem érhető el.

    ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
      ```
   Miután a **kulcs vagy titkos kódokkal érhetők el** és a titkosítási adatok vannak beállítva az operációs rendszer BLOB, csatlakoztassa a lemezeket, az alábbi parancsfájl használatával.

    Ha a forrás keyVault vagy kulcs vagy titkos kódokkal érhetők el, majd a fenti szkript kell nem hajtható végre.

    ```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
      ```

   * **Felügyelt és nem titkosított virtuális gépek** – felügyelt nem titkosított virtuális gépek esetén a visszaállított felügyelt lemezt. Részletes információkért lásd: [adatlemez csatolása a PowerShell használatával Windows virtuális gép](../virtual-machines/windows/attach-disk-ps.md).

   * **Felügyelt és a titkosított virtuális gépek az Azure ad-vel (csak a blokktitkosítási kulcsot)** – az Azure AD (rendelkeznek BEk-KEL csak használatával titkosított), felügyelt, titkosított virtuális gépek csatlakoztatása a visszaállított felügyelt lemezeket. Részletes információkért lásd: [adatlemez csatolása a PowerShell használatával Windows virtuális gép](../virtual-machines/windows/attach-disk-ps.md).

   * **Felügyelt és a titkosított virtuális gépek az Azure ad-vel (rendelkeznek BEk-KEL és KEK)** – az Azure AD (rendelkeznek BEk-KEL és kek-KEL használatával titkosítva), felügyelt, titkosított virtuális gépek csatlakoztatása a visszaállított felügyelt lemezeket. Részletes információkért lásd: [adatlemez csatolása a PowerShell használatával Windows virtuális gép](../virtual-machines/windows/attach-disk-ps.md).

   * **Felügyelt és a titkosított virtuális gépek anélkül, hogy az Azure AD (kizárólag a blokktitkosítási kulcsot)** -felügyelt, titkosított virtuális gépek (titkosítva rendelkeznek BEk-KEL csak), az Azure AD nélkül Ha a forrás **keyVault vagy titkos kódja nem érhetők el** állítsa vissza a titkos kulcsok a key vault használatával a az eljárás [egy nem titkosított virtuális gépek visszaállítása az Azure Backup helyreállítási pontokról](backup-azure-restore-key-secret.md). Ezután hajtsa végre az alábbi parancsfájlok a visszaállított operációsrendszer-lemez (Ez a lépés nem kötelező adatlemez) titkosítási adatok beállítása. A visszaállított kulcstartóból a $dekurl lehet beolvasni.

     Az alábbi szkriptet kell végrehajtani, csak akkor, ha a forrás keyVault vagy titkos kódja nem érhető el.  

     ```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
      $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
      Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
      ```

     Miután érhetők el a titkos kulcsok és a titkosítási adatok vannak megadva az operációsrendszer-lemez, csatlakoztassa a visszaállított felügyelt lemezeket, lásd: [adatlemez csatolása a PowerShell használatával Windows virtuális gép](../virtual-machines/windows/attach-disk-ps.md).

   * **Felügyelt és a titkosított virtuális gépek (rendelkeznek BEk-KEL és KEK) az Azure AD nélkül** – felügyelt, titkosított virtuális gépek nélkül (titkosítását a rendelkeznek BEk-KEL és KEK) az Azure AD, ha forrás **keyVault/kulcsa vagy titkos kódja nem érhetők el** a kulcs és titkos kulcs visszaállítása hajtsa végre az eljárást, a tároló [egy nem titkosított virtuális gépek visszaállítása az Azure Backup helyreállítási pontokról](backup-azure-restore-key-secret.md). Ezután hajtsa végre az alábbi parancsfájlok a visszaállított operációsrendszer-lemez (Ez a lépés nem kötelező adatlemez) titkosítási adatok beállítása. A visszaállított kulcstartóból a $dekurl és $kekurl lehet beolvasni.

   Az alábbi szkriptet kell végrehajtani, csak akkor, ha a forrás keyVault/kulcsa vagy titkos kódja nem érhető el.

   ```powershell
     $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
     $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
     $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
     $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
     $diskupdateconfig = Set-AzDiskUpdateDiskEncryptionKey -DiskUpdate $diskupdateconfig -SecretUrl $dekUrl -SourceVaultId $keyVaultId  
     $diskupdateconfig = Set-AzDiskUpdateKeyEncryptionKey -DiskUpdate $diskupdateconfig -KeyUrl $kekUrl -SourceVaultId $keyVaultId  
     Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Miután a kulcs vagy titkos kódokkal érhetők el, és a titkosítási adatok vannak megadva az operációsrendszer-lemez, csatlakoztassa a visszaállított felügyelt lemezeket, lásd: [adatlemez csatolása a PowerShell használatával Windows virtuális gép](../virtual-machines/windows/attach-disk-ps.md).

5. Állítsa be a hálózati beállításokat.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Hozza létre a virtuális gépet.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. A leküldéses ADE bővítmény.

   * **Virtuális gép az Azure ad-vel** – a következő parancs használatával manuálisan engedélyezze a titkosítást az adatlemezek  

     **Csak a blokktitkosítási kulcsot**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **Rendelkeznek BEk-KEL és kek-KEL**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Virtuális gép az Azure AD nélkül** – a következő parancs használatával manuálisan engedélyezze a titkosítást az adatlemezek.

     Ha a sémanév AADClientID, kéri a parancs végrehajtása közben, akkor kell frissíteni az Azure Powershellt.

     **Csak a blokktitkosítási kulcsot**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **Rendelkeznek BEk-KEL és kek-KEL**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

## <a name="restore-files-from-an-azure-vm-backup"></a>Fájlok visszaállítása egy Azure virtuális gép biztonsági mentése

A lemezek visszaállítását, valamint egy Azure virtuális gépek biztonsági mentését egyes fájlokat is visszaállíthatja. A visszaállítási fájlok funkciót egy helyreállítási pont található összes fájl hozzáférést biztosít. A fájlokat a fájlkezelő használatával felügyeli a normál fájlok esetében tenné.

A fájl biztonsági másolatból történő visszaállítását egy Azure virtuális gép alapvető lépések a következők:

* Válassza ki a virtuális Gépet
* Válasszon helyreállítási pontot
* Helyreállítási pont található lemezeket
* Másolja a szükséges fájlokat
* A lemez leválasztása

### <a name="select-the-vm"></a>Válassza ki a virtuális Gépet

A PowerShell-objektumot, amely a megfelelő biztonságimásolat-elem azonosítja, indítsa el a tárolóból a tárolóban, és a hierarchiában lejjebb lévő objektum módon működnek. Válassza ki a tárolót, hogy a virtuális gépet, használja a [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) parancsmag és a szolgáltatása, amely az a [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) parancsmagot.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM"
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM"
```

### <a name="choose-a-recovery-point"></a>Válasszon helyreállítási pontot

Használja a [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) parancsmag használatával listázhatja a biztonságimásolat-elem tartozó összes helyreállítási pontot. Ezután válassza ki a helyreállítási pontot a visszaállításhoz. Ha biztos abban, hogy melyik helyreállítási pontot, tanácsos válassza ki a legutóbbi RecoveryPointType = AppConsistent pontot a listában.

A következő parancsfájlt, a változó a **$rp**, helyreállítási pontot a kiválasztott biztonsági mentési elemet, az elmúlt hét napban tömbje. A tömb fordított sorrendben idő 0. indexnél a legújabb helyreállítási ponttal van rendezve. Standard PowerShell tömb indexelő segítségével válassza ki a helyreállítási pont. $Rp [0] a példában a legutóbbi helyreállítási pontot választja.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0]
```

A kimenet a következő példához hasonló:

```
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

Használja a [Get-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) -parancsmaggal beolvasható a szkriptet a helyreállítási pont az összes lemez csatlakoztatásához.

> [!NOTE]
> A lemezek, a gép, ahol a szkript futása iSCSI csatolt lemeznek csatlakoztatva vannak. Csatlakoztatási azonnal megtörténik, és nem kell díjat fizetniük.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

A kimenet a következő példához hasonló:

```powershell
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Futtassa a szkriptet a számítógépen, ahol szeretné helyreállítani a fájlokat. A szkript végrehajtásához a megadott jelszót kell megadnia. Miután a lemezekkel rendelkezik, a Windows Fájlkezelőben használatával keresse meg az új kötetek és a fájlok. További információkért tekintse meg a biztonsági mentés cikk [fájlok helyreállítása Azure virtuális gépek biztonsági mentésének](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>A lemezek leválasztása

Másolja a szükséges fájlokat, miután a [Disable-AzRecoveryServicesBackupRPMountScript](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) , válassza le a lemezeket. Mindenképpen válassza le a lemezeket, így a rendszer eltávolítja a helyreállítási pont a fájlok elérését.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0]
```

## <a name="next-steps"></a>További lépések

Ha inkább a érhet el az Azure-erőforrások PowerShell-lel, tekintse meg a PowerShell a cikkben [üzembe helyezés és a Windows Server biztonsági mentés kezelése](backup-client-automation.md). Ha Ön kezeli a DPM biztonsági mentések, ismertető cikkben [üzembe helyezés és a DPM a biztonsági mentés kezelése](backup-dpm-automation.md). 
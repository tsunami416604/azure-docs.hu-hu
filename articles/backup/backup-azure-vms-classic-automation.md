---
title: "Telepítéséhez és kezeléséhez biztonsági mentése Azure virtuális gépek PowerShell használatával |} Microsoft Docs"
description: "Megtudhatja, hogyan telepíthetnek és kezelhetnek az Azure Backup szolgáltatáshoz a PowerShell használatával."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 2e24b1d9-4375-4049-a28d-e3bc01152f32
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/2/2017
ms.author: markgal;trinadhk;jimpark
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f0f06adb8177ce2d17aa0b40666470279c04e22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azurermbackup-cmdlets-to-back-up-virtual-machines"></a>Készítsen biztonsági másolatot a virtuális gépek AzureRM.Backup-parancsmagok használatával
> [!div class="op_single_selector"]
> * [Resource Manager](backup-azure-vms-automation.md)
> * [Klasszikus](backup-azure-vms-classic-automation.md)
>
>

Ez a cikk bemutatja, hogyan használja a biztonsági mentése és helyreállítása Azure virtuális gépek Azure Powershellt. Az Azure két különböző üzemi modellel rendelkezik az erőforrások létrehozásához és használatához: Resource Manager-alapú és klasszikus. Ez a cikk ismerteti, klasszikus telepítési modellel adatok biztonsági mentéséhez a mentési tárolóba. Ha nem hozott létre a biztonsági másolatok tárolóját az előfizetésében szereplő, az erőforrás-kezelő verzió Ez a cikk meg [használata AzureRM.RecoveryServices.Backup parancsmagok segítségével készítsen biztonsági másolatot a virtuális gépek](backup-azure-vms-automation.md). A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

> [!IMPORTANT]
> A biztonsági mentési tárolókról mostantól lehetőség van Recovery Services-tárolókra váltani. A részletekről bővebben az [Váltás biztonsági mentési tárolóról Recovery Services-tárolóra](backup-azure-upgrade-backup-to-recovery-services.md) című cikkben olvashat. A Microsoft azt javasolja, hogy a biztonsági mentési tárolóról váltson Recovery Services-tárolóra.<br/> 2017. október 15-től a PowerShell nem használható Backup-tárolók létrehozására. **2017. november 1-től**:
>- Minden fennmaradó Backup-tároló automatikusan Recovery Services-tárolóra frissül.
>- A klasszikus portálon nem lehet majd hozzáférni a biztonsági másolati adatokhoz. Helyette az Azure Portal segítségével férhet hozzá a Recovery Services-tárolókban található biztonsági mentési adatokhoz.
>

## <a name="concepts"></a>Alapelvek
A cikkben a PowerShell-parancsmagok használatával virtuális gépek biztonsági mentésére vonatkozó információkkal. Az Azure virtuális gépek védelméről bevezető információkat talál [tervezze meg a virtuális gép biztonsági mentési infrastruktúra az Azure-ban](backup-azure-vms-introduction.md).

> [!NOTE]
> Kezdés előtt olvassa el a [Előfeltételek](backup-azure-vms-prepare.md) Azure Backup szolgáltatással működéséhez szükséges és a [korlátozások](backup-azure-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm) az aktuális virtuális gép biztonsági mentési megoldás.
>
>

PowerShell hatékony használatához szánjon egy kis időt a hierarchiában, és hol kell elkezdeni az objektumok ismertetése.

![Eltér az objektumhierarchia](./media/backup-azure-vms-classic-automation/object-hierarchy.png)

A két legfontosabb adatfolyamok engedélyezze a virtuális gép védelmét, és adatok helyreállításához a helyreállítási pontból. Ez a cikk megkönnyítik a két forgatókönyvekben engedélyezése a PowerShell-parancsmagok használata adept célja.

## <a name="setup-and-registration"></a>Telepítését és regisztrálását
Megkezdéséhez:

1. [Töltse le a legfrissebb PowerShell](https://github.com/Azure/azure-powershell/releases) (szükséges minimális verziója: 1.0.0)
2. Keresse meg az Azure biztonsági mentés PowerShell-parancsmagok érhető el a következő parancs beírásával:

```
PS C:\> Get-Command *azurermbackup*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmBackupItem                           1.0.1      AzureRM.Backup
Cmdlet          Disable-AzureRmBackupProtection                    1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupContainerReregistration        1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupProtection                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupContainer                         1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupItem                              1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJob                               1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJobDetails                        1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupRecoveryPoint                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVaultCredentials                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupRetentionPolicyObject             1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Register-AzureRmBackupContainer                    1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupProtectionPolicy               1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupVault                          1.0.1      AzureRM.Backup
Cmdlet          Restore-AzureRmBackupItem                          1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Stop-AzureRmBackupJob                              1.0.1      AzureRM.Backup
Cmdlet          Unregister-AzureRmBackupContainer                  1.0.1      AzureRM.Backup
Cmdlet          Wait-AzureRmBackupJob                              1.0.1      AzureRM.Backup
```

A következő telepítését és regisztrálását feladatok automatizálhatók a PowerShell használatával:

* Backup-tároló létrehozása
* A virtuális gépek regisztrálása az Azure Backup szolgáltatás

### <a name="create-a-backup-vault"></a>Backup-tároló létrehozása
> [!WARNING]
> Az ügyfelek először az Azure Backup segítségével az Azure Backup-szolgáltató az előfizetéshez használandó regisztrálnia kell. Ezt megteheti a következő parancs futtatásával: Register-AzureRmResourceProvider - ProviderNamespace "Microsoft.Backup"
>
>

Létrehozhat egy új mentési tárolót használ a **New-AzureRmBackupVault** parancsmag. A mentési tároló egy ARM-erőforrás, ezért el kell helyezni az erőforráscsoporton belül. Egy rendszergazda jogú Azure PowerShell-konzolban a következő parancsokat:

```
PS C:\> New-AzureRmResourceGroup –Name “test-rg” –Location “West US”
PS C:\> $backupvault = New-AzureRmBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

Kaphat be egy adott előfizetés összes mentési tárolók listája az **Get-AzureRmBackupVault** parancsmag.

> [!NOTE]
> Célszerű a mentési tároló objektum tárolására egy változóba. A tároló objektum sok Azure Backup-parancsmagokhoz tartozó bemeneti adatokként van szükség.
>
>

### <a name="registering-the-vms"></a>A virtuális gépek regisztrálása
Az első lépés a biztonsági mentés konfigurálása az Azure Backup szolgáltatás felé, hogy a számítógép vagy virtuális gép az Azure biztonsági mentési tárolóhoz regisztrált. A **Register-AzureRmBackupContainer** parancsmag az Azure IaaS virtuális gépként bemeneti adatokat fogad, és regisztrálja azt a megadott tárolóhoz. A regisztrációs műveletet az Azure virtuális géphez társít a mentési tárolóban, és nyomon követi a virtuális Gépet, a biztonsági mentési teljes életciklusukon keresztül.

A virtuális gép regisztrálása az Azure Backup szolgáltatás hoz létre a legfelső szintű tároló objektumok. A tároló általában több olyan elemeket tartalmaz, biztonsági másolat készíthető, de a virtuális gépek esetén nem lesznek a tároló csak egy biztonsági mentési elemet.

```
PS C:\> $registerjob = Register-AzureRmBackupContainer -Vault $backupvault -Name "testvm" -ServiceName "testvm"
```

## <a name="backup-azure-vms"></a>Azure-beli virtuális gépek biztonsági mentése
### <a name="create-a-protection-policy"></a>Védelmi házirend létrehozása
Nincs kötelező hozzon létre egy új védelmi házirendet elindítani a virtuális gépek biztonsági mentését. A tároló tartalmaz egy "alapértelmezett házirend", amely segítségével gyorsan engedélyezni a védelmet, és majd a jobb oldali később szerkeszthetők. A tárolóban elérhető házirendek listáját beszerzése szolgáltatással a **Get-AzureRmBackupProtectionPolicy** parancsmagot:

```
PS C:\> Get-AzureRmBackupProtectionPolicy -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DefaultPolicy             AzureVM            Daily              26-Aug-15 12:30:00 AM
```

> [!NOTE]
> Az időzóna PowerShell BackupTime mező UTC. Azonban az Azure-portálon a biztonságimásolat-készítési időpont látható, ha az időzóna igazodik a helyi rendszer UTC eltolás mellett.
>
>

A biztonsági mentési házirend legalább egy megőrzési házirend társítva. A megőrzési házirend határozza meg, hány helyreállítási pont tartják az Azure Backup szolgáltatással. A **New-AzureRmBackupRetentionPolicy** parancsmag megőrzési házirend adatokat PowerShell-objektumokat hoz létre. A megőrzési csoportházirend-objektumok használt bemeneteként a *New-AzureRmBackupProtectionPolicy* parancsmagot, vagy közvetlenül az a *Enable-AzureRmBackupProtection* parancsmag.

A biztonsági mentési házirend határozza meg, mikor és milyen gyakran történik-e egy elem biztonsági mentését. A **New-AzureRmBackupProtectionPolicy** parancsmag létrehoz egy PowerShell-objektum, amely tartalmazza a biztonsági mentési házirend. A biztonsági mentési házirendet bemenetként szolgál a *Enable-AzureRmBackupProtection* parancsmag.

```
PS C:\> $Daily = New-AzureRmBackupRetentionPolicyObject -DailyRetention -Retention 30
PS C:\> $newpolicy = New-AzureRmBackupProtectionPolicy -Name DailyBackup01 -Type AzureVM -Daily -BackupTime ([datetime]"3:30 PM") -RetentionPolicy $Daily -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DailyBackup01             AzureVM            Daily              01-Sep-15 3:30:00 PM
```

### <a name="enable-protection"></a>Védelem engedélyezése
Védelem engedélyezése magában foglalja a két objektum – az elemet, és a házirendet, és mindkét kell ugyanahhoz a tárolóhoz tartozik. Ha a házirend társítva a cikk, a biztonsági mentési munkafolyamat fog indítsa meghatározott ütemezés szerint.

```
PS C:\> Get-AzureRmBackupContainer -Type AzureVM -Status Registered -Vault $backupvault | Get-AzureRmBackupItem | Enable-AzureRmBackupProtection -Policy $newpolicy
```

### <a name="initial-backup"></a>Kezdeti biztonsági mentés
A biztonsági mentés ütemezése során a teljes kezdeti másolatot, az elem és a növekményes másolatot az azt követő biztonsági mentéseket a kezeli. Azonban ha a kezdeti biztonsági másolatot egy bizonyos időpontban hiba akkor fordulhat elő, vagy akár azonnal kényszeríteni kívánja használja a **Backup-AzureRmBackupItem** parancsmagot:

```
PS C:\> $container = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -Name "testvm"
PS C:\> $backupjob = Get-AzureRmBackupItem -Container $container | Backup-AzureRmBackupItem
PS C:\> $backupjob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

> [!NOTE]
> A PowerShell StartTime és az EndTime megadása mezőlistájában, valamint az időzóna UTC. Azonban az hasonló információ az Azure-portálon jelenik meg, amikor az időzóna igazodik a helyi rendszer órája.
>
>

### <a name="monitoring-a-backup-job"></a>A biztonsági mentési feladatot figyelése
A legtöbb hosszú ideig futó műveletek Azure Backup feladatként van modellezni. Ez megkönnyíti nyomon követési anélkül, hogy az Azure-portálon tartsa nyitva mindig.

Ahhoz, hogy az egy folyamatban lévő feladat legfrissebb állapotának, használja a **Get-AzureRmBackupJob** parancsmag.

```
PS C:\> $joblist = Get-AzureRmBackupJob -Vault $backupvault -Status InProgress
PS C:\> $joblist[0]

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

Ezek a feladatok befejezésére – ami szükségtelen, további kód - lekérdezési helyett már egyszerűbb használni a **várakozási-AzureRmBackupJob** parancsmag. Ha parancsfájlt használja, a parancsmag végrehajtása idejére felfüggeszti a feladat befejeződik, vagy a megadott időtúllépési érték elérésekor.

```
PS C:\> Wait-AzureRmBackupJob -Job $joblist[0] -Timeout 43200
```


## <a name="restore-an-azure-vm"></a>Állítsa vissza az Azure virtuális gép
Ahhoz, hogy a biztonsági mentési adatok visszaállításához kell azonosítani a biztonsági másolat elemet, és a helyreállítási pont, amely tárolja a időpontban adatokat. Ezeket az információkat a visszaállítási-AzureRmBackupItem parancsmagnak kezdeményezése visszaállítás, a tároló adatait a felhasználói fiókhoz megadott.

### <a name="select-the-vm"></a>Válassza ki a virtuális gép
Ahhoz, hogy a PowerShell-objektum, amely a helyes biztonságimásolat-elem azonosítja, meg kell elindítani a tárolóban lévő-tárolójából, és működnek az objektum hierarchia valamennyi alsóbb szintjén. Válassza ki a tárolóhoz, amelybe a virtuális Gépet jelöl, használja a **Get-AzureRmBackupContainer** parancsmag és a csövön keresztüli, hogy a **Get-AzureRmBackupItem** parancsmag.

```
PS C:\> $backupitem = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -name "testvm" | Get-AzureRmBackupItem
```

### <a name="choose-a-recovery-point"></a>A helyreállítási pont kiválasztása
Most listázhatja a biztonsági mentési elem az összes helyreállítási pont a **Get-AzureRmBackupRecoveryPoint** parancsmagot, és válassza ki a visszaállítani kívánt helyreállítási pontot. Általában a felhasználók a legutóbbi válasszon *AppConsistent* pontot a listában.

```
PS C:\> $rp =  Get-AzureRmBackupRecoveryPoint -Item $backupitem
PS C:\> $rp

RecoveryPointId    RecoveryPointType  RecoveryPointTime      ContainerName
---------------    -----------------  -----------------      -------------
15273496567119     AppConsistent      01-Sep-15 12:27:38 PM  iaasvmcontainer;testvm;testv...
```

A változó ```$rp``` a helyreállítási pontok egy tömb van a kijelölt biztonsági másolatát, fordított sorrendben idő – a legutóbbi helyreállítási pontot a(z) 0. Standard PowerShell tömb indexelő segítségével válassza ki a helyreállítási pont. Például: ```$rp[0]``` választja ki a legutóbbi helyreállítási pontot.

### <a name="restoring-disks"></a>Lemezek visszaállítása
A visszaállítási műveleteket az Azure portálon keresztül, és Azure PowerShell használatával végzett közötti fő különbség van. A PowerShell használatával a visszaállítási művelet leáll a lemezek és a konfigurációs adatainak visszaállításához a helyreállítási pontból. Nem hoz létre egy virtuális gépet.

> [!WARNING]
> A visszaállítási AzureRmBackupItem nem hoz létre egy virtuális Gépet. A megadott tárfiókhoz a lemezek csak visszaállítja. Ez történik, nem azonos a működés az Azure portálon.
>
>

```
PS C:\> $restorejob = Restore-AzureRmBackupItem -StorageAccountName "DestAccount" -RecoveryPoint $rp[0]
PS C:\> $restorejob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Restore         InProgress      01-Sep-15 1:14:01 PM   01-Jan-01 12:00:00 AM
```

A részletekért a visszaállítási művelet használatával a **Get-AzureRmBackupJobDetails** parancsmag a visszaállítási feladat befejezése után. A *hiba részletei* tulajdonságnak az információk szükségesek ahhoz, hogy a virtuális gép van.

```
PS C:\> $restorejob = Get-AzureRmBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmBackupJobDetails -Job $restorejob
```

### <a name="build-the-vm"></a>A virtuális gép létrehozása
A virtuális gép kívül a visszaállított lemezek felépítése végezhető régebbi Azure Service Management PowerShell-parancsmagok használatával, az új Azure Resource Manager-sablonok vagy akár az Azure portál használatával. Az első példában bemutatjuk, hogyan érheti el az Azure Service Management-parancsmagok használatával.

```
$properties  = $details.Properties

$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$blobName = $properties["Config Blob Name"]

$keys = Get-AzureStorageKey -StorageAccountName $storageAccountName
$storageAccountKey = $keys.Primary
$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey


$destination_path = "C:\Users\admin\Desktop\vmconfig.xml"
Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path -Context $storageContext


$obj = [xml](((Get-Content -Path $destination_path -Encoding UniCode)).TrimEnd([char]0x00))
$pvr = $obj.PersistentVMRole
$os = $pvr.OSVirtualHardDisk
$dds = $pvr.DataVirtualHardDisks
$osDisk = Add-AzureDisk -MediaLocation $os.MediaLink -OS $os.OS -DiskName "panbhaosdisk"
$vm = New-AzureVMConfig -Name $pvr.RoleName -InstanceSize $pvr.RoleSize -DiskName $osDisk.DiskName

if (!($dds -eq $null))
{
    foreach($d in $dds.DataVirtualHardDisk)
    {
        $lun = 0
        if(!($d.Lun -eq $null))
        {
            $lun = $d.Lun
        }
        $name = "panbhadataDisk" + $lun
        Add-AzureDisk -DiskName $name -MediaLocation $d.MediaLink
        $vm | Add-AzureDataDisk -Import -DiskName $name -LUN $lun
    }
}

New-AzureVM -ServiceName "panbhasample" -Location "SouthEast Asia" -VM $vm
```

További információ a hogyan hozhat létre egy virtuális Gépet a visszaállított lemezekről olvassa el a következő parancsmagokat:

* [Adja hozzá AzureDisk](https://msdn.microsoft.com/library/azure/dn495252.aspx)
* [Új AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx)
* [Új AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)

## <a name="code-samples"></a>Kódminták
### <a name="1-get-the-completion-status-of-job-sub-tasks"></a>1. Feladat alfeladatok befejezési állapotának beolvasása
Egyes alfeladatok befejezési állapotának nyomon követése, használhatja a **Get-AzureRmBackupJobDetails** parancsmagot:

```
PS C:\> $details = Get-AzureRmBackupJobDetails -JobId $backupjob.InstanceId -Vault $backupvault
PS C:\> $details.SubTasks

Name                                                        Status
----                                                        ------
Take Snapshot                                               Completed
Transfer data to Backup vault                               InProgress
```

### <a name="2-create-a-dailyweekly-report-of-backup-jobs"></a>2. A biztonsági mentési feladatok napi vagy heti jelentés létrehozása
A rendszergazdák általában érdemes tudni, hogy milyen biztonsági mentési feladatok az elmúlt 24 órában, e biztonsági mentési feladatok állapotának futott. Emellett az átvitt adatok mennyisége akadályozható rendszergazdák becsült havi használati adatok. Az alábbi parancsfájl kéri le a nyers adatokat az Azure Backup szolgáltatás, és megjeleníti a PowerShell-konzolján.

```
param(  [Parameter(Mandatory=$True,Position=1)]
        [string]$backupvaultname,

        [Parameter(Mandatory=$False,Position=2)]
        [int]$numberofdays = 7)


#Initialize variables
$DAILYBACKUPSTATS = @()
$backupvault = Get-AzureRmBackupVault -Name $backupvaultname
$enddate = ([datetime]::Today).AddDays(1)
$startdate = ([datetime]::Today)

for( $i = 1; $i -le $numberofdays; $i++ )
{
    # We query one day at a time because pulling 7 days of data might be too much
    $dailyjoblist = Get-AzureRmBackupJob -Vault $backupvault -From $startdate -To $enddate -Type AzureVM -Operation Backup
    Write-Progress -Activity "Getting job information for the last $numberofdays days" -Status "Day -$i" -PercentComplete ([int]([decimal]$i*100/$numberofdays))

    foreach( $job in $dailyjoblist )
    {
        #Extract the information for the reports
        $newstatsobj = New-Object System.Object
        $newstatsobj | Add-Member -Type NoteProperty -Name Date -Value $startdate
        $newstatsobj | Add-Member -Type NoteProperty -Name VMName -Value $job.WorkloadName
        $newstatsobj | Add-Member -Type NoteProperty -Name Duration -Value $job.Duration
        $newstatsobj | Add-Member -Type NoteProperty -Name Status -Value $job.Status

        $details = Get-AzureRmBackupJobDetails -Job $job
        $newstatsobj | Add-Member -Type NoteProperty -Name BackupSize -Value $details.Properties["Backup Size"]
        $DAILYBACKUPSTATS += $newstatsobj
    }

    $enddate = $enddate.AddDays(-1)
    $startdate = $startdate.AddDays(-1)
}

$DAILYBACKUPSTATS | Out-GridView
```

Ha szeretné diagramkészítési-képességeket adhat a jelentés kimenetében, ismerje meg, a TechNet blogbejegyzést [diagramkészítési a PowerShell használatával](http://blogs.technet.com/b/richard_macdonald/archive/2009/04/28/3231887.aspx)

## <a name="next-steps"></a>Következő lépések
Ha jobban szeret PowerShell használatával az Azure-erőforrások bevonásához, tekintse meg a PowerShell cikk a Windows Server védelmének [telepítés és a Windows Server biztonsági másolat kezelése](backup-client-automation-classic.md). Is van a DPM biztonsági mentések kezelése a PowerShell cikk [telepítés és a DPM a biztonsági mentés kezelése](backup-dpm-automation-classic.md). Ezek a cikkek mindegyikét a Resource Manager üzembe helyezések és a klasszikus központi telepítések verziója szükséges.

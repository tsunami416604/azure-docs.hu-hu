---
title: A függelék – az IaaS virtuális gépekhez az Azure Disk Encryption |} A Microsoft Docs
description: 'Ez a cikk a függelék: a Microsoft Azure Disk Encryption a Windows és Linux rendszerű IaaS virtuális gépek.'
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 01/14/2019
ms.custom: seodec18
ms.openlocfilehash: b6d0b702a334bf1127f570bff026fa4332331209
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260134"
---
# <a name="appendix-for-azure-disk-encryption"></a>Az Azure Disk Encryption for függelék 

Ez a cikk a mellékletet [IaaS virtuális gépekhez az Azure Disk Encryption](azure-security-disk-encryption-overview.md). Ellenőrizze, hogy az Azure Disk Encryption, IaaS virtuális gépek cikkek előbb a környezetet tudni elolvasni. Ez a cikk ismerteti, hogyan készíti elő az előzetes titkosítással VHD-k és egyéb feladatokhoz.

## <a name="connect-to-your-subscription"></a>Csatlakozás az előfizetéshez
A Kezdés előtt tekintse át a [Előfeltételek](azure-security-disk-encryption-prerequisites.md) cikk. Miután az összes előfeltétel teljesül-e, csatlakozzon az előfizetéséhez a következő parancsmag futtatásával:

### <a name="bkmk_ConnectPSH"></a> Csatlakozzon az előfizetéséhez, a PowerShell-lel

1. Indítsa el az Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába a következő parancsot:

     ```powershell
     Connect-AzureRmAccount 
     ```
2. Ha több előfizetéssel rendelkezik, és adjon meg egy használni kívánt, írja be a következő, a fiókhoz tartozó előfizetések megtekintéséhez:
     
     ```powershell
     Get-AzureRmSubscription
     ```
3. Válassza ki a használni kívánt előfizetést, írja be:
 
     ```powershell
      Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>
     ```
4. Győződjön meg arról, hogy helyesen szerepel-e beállítva az előfizetés, írja be:
     
     ```powershell
     Get-AzureRmSubscription
     ```
5. Szükség esetén az Azure AD-csatlakozás [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```
6. Ellenőrizze az Azure Disk Encryption parancsmagjai telepítve vannak-e, írja be:
     
     ```powershell
     Get-command *diskencryption*
     ```
                       
7. Felülvizsgálat [Ismerkedés az Azure PowerShell-lel](/powershell/azure/get-started-azureps) és [AzureAD](/powershell/module/azuread), ha szükséges.

### <a name="bkmk_ConnectCLI"></a> Csatlakozás az előfizetéshez az Azure CLI-vel

1. Jelentkezzen be Azure-ban [az bejelentkezési](/cli/azure/authenticate-azure-cli#sign-in-interactively). 
     
     ```azurecli
     az login
     ```

2. Ha szeretne, válassza ki a bejelentkezéshez, használja a bérlő:
    
     ```azurecli
     az login --tenant <tenant>
     ```

3. Ha több előfizetést és adnia azt szeretné, az előfizetések listája az első [az fióklista](/cli/azure/account#az-account-list) , és adja meg a [fiók beállítása az](/cli/azure/account#az-account-set).
     
     ```azurecli
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. A telepített verzió ellenőrzéséhez.
     
     ```azurecli
        az --version
     ``` 

5. Felülvizsgálat [Azure CLI 2.0 használatának első lépései](/cli/azure/get-started-with-azure-cli) szükség esetén. 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Az Azure Disk Encryption minta PowerShell-parancsfájlok 

- **Az előfizetés összes titkosított virtuális gépek listája**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzureRmVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Összes lemez titkosítási titkos kulcsot a kulcstartóban található virtuális gépek titkosításához használt listázása** 

     ```azurepowershell-interactive
     Get-AzureKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="bkmk_prereq-script"></a> Az Azure Disk Encryption előfeltétel PowerShell-parancsfájl használatával
Ha már ismeri az Azure Disk Encryption előfeltételeinek, használhatja a [az Azure Disk Encryption előfeltétel PowerShell-parancsprogram](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). A PowerShell-parancsfájl egy példát, tekintse meg a [titkosítani a virtuális gépek gyors üzembe helyezés](quick-encrypt-vm-powershell.md). Eltávolíthatja a megjegyzéseket a parancsfájl egy szakaszában sor 211, az összes lemez titkosítása egy meglévő erőforráscsoportot a meglévő virtuális gépek indítása. 

Az alábbi táblázat mutatja, hogy mely paraméterek is használható a PowerShell-parancsfájlt: 


|Paraméter|Leírás|Kötelező megadni|
|------|------|------|
|$resourceGroupName| Az erőforrás nevét, amelyhez a KeyVault tartozik.  Ezen a néven egy új erőforráscsoport létrejön, ha egy nem létezik.| True (Igaz)|
|$keyVaultName|A KeyVault a melyik titkosítási kulcsai elhelyezni kívánt nevét. Ezen a néven egy új tároló létrejön, ha egy nem létezik.| True (Igaz)|
|$location|A KeyVault helye. Győződjön meg arról a KeyVault és a virtuális gépek titkosítását ugyanazon a helyen. A helyek listáját a következővel érheti el: `Get-AzureRMLocation`.|True (Igaz)|
|$subscriptionId|Használható az Azure-előfizetés azonosítója.  Az előfizetés-azonosítóját a következővel érheti el: `Get-AzureRMSubscription`.|True (Igaz)|
|$aadAppName|Neve az Azure AD-alkalmazást, amely a KeyVault titkos kódok írása történik. Ha a megadott néven még nem létezik alkalmazás, a rendszer létrehoz egyet a beírt néven. Ha az alkalmazás már létezik, aadClientSecret a paramétert átadhatja a parancsfájlt.|False (Hamis)|
|$aadClientSecret|A korábban létrehozott Azure AD-alkalmazás titkos ügyfélkódja.|False (Hamis)|
|$keyEncryptionKeyName|A KeyVault választható kulcstitkosítási kulcs neve. Ezen a néven egy új kulcsot létrejön, ha egy nem létezik.|False (Hamis)|


## <a name="resource-manager-templates"></a>Resource Manager-sablonok

<!--   - [Create a key vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create) -->

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Titkosítása és visszafejtése a virtuális gépek az Azure AD-alkalmazás nélkül


- [A meglévő vagy IaaS Windows rendszerű virtuális gépek futtatása a lemeztitkosítás engedélyezve](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)
- [Letiltja a lemeztitkosítást a meglévő vagy IaaS Windows rendszerű virtuális gépek futtatása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
- [Lemeztitkosítás IaaS virtuális gépen meglévő vagy futó Linux engedélyezése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
 -  [Egy futó Linux rendszerű virtuális gép titkosításának letiltása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Letiltja a titkosítást csak engedélyezett megváltoztatását az adatköteteken Linux rendszerű virtuális gépekhez.  

### <a name="encrypt-or-decrypt-vm-scale-sets"></a>Titkosítása és visszafejtése a Virtuálisgép-méretezési csoportok

- [A futó Linux rendszerű virtuális gép méretezési lemeztitkosítás engedélyezve](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Egy futó Windows virtuális gép méretezési csoporthoz lemeztitkosítással engedélyezése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

 - [Egy virtuális gép méretezési állítsa be a Linux rendszerű virtuális gépek üzembe helyezése Linux VMSS a jumpbox, és lehetővé teszi, hogy titkosítással](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

 - [A virtuális gép méretezési állítsa be a Windows virtuális gépek üzembe helyezése Windows VMSS a jumpbox, és lehetővé teszi, hogy titkosítással](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Tiltsa le a futó Linux rendszerű virtuális gép méretezési lemeztitkosítás](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Tiltsa le a futó Windows virtuális gép méretezési csoporthoz lemeztitkosítással](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Titkosítása és visszafejtése a virtuális gépek az Azure AD-alkalmazás (előző kiadás) 
 
- [A meglévő vagy IaaS Windows rendszerű virtuális gépek futtatása a lemeztitkosítás engedélyezve](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)

- [Lemeztitkosítás IaaS virtuális gépen meglévő vagy futó Linux engedélyezése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    

- [Disk encryption szolgáltatást futtató Windows IaaS letiltása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 

-  [Egy futó Linux rendszerű virtuális gép titkosításának letiltása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Letiltja a titkosítást csak engedélyezett megváltoztatását az adatköteteken Linux rendszerű virtuális gépekhez. 

- [Új IaaS Windows virtuális Gépen a Marketplace-ről a lemeztitkosítás engedélyezve](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)
    - Ez a sablon létrehoz egy új titkosított Windows virtuális Gépet, amely a Windows Server 2012 image z galerie használja.

- [Új titkosított Windows IaaS felügyelt lemez virtuális gép létrehozása katalógus-lemezkép](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)
    - Ez a sablon a Windows Server 2012 katalóguslemezt használó felügyelt lemezeket hoz létre egy új titkosított Windows virtuális gép.

- [A teljes lemeztitkosítás felügyelt lemezekkel rendelkező RHEL 7.2 üzembe](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)
    - Ez a sablon létrehoz egy teljes mértékben titkosított RHEL 7.2 rendszerű virtuális Gépet az Azure-ban felügyelt lemezeket használ. Tartalmazza a titkosított meghajtókról 30-GB OS és a csatlakoztatásának helye /mnt/raidencrypted titkosított 200 GB-os tömb (RAID-0). Tekintse meg a [– gyakori kérdések](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) támogatott Linux-kiszolgáló disztribúciókra vonatkozó cikket. 

- [A teljes lemeztitkosítás, nem felügyelt lemezekkel rendelkező RHEL 7.2 üzembe](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel-unmanaged)
    - Ez a sablon létrehoz egy teljes mértékben titkosított RHEL 7.2 rendszerű virtuális Gépet az Azure-ban egy titkosított 30 GB-os operációs rendszer meghajtóját és a egy titkosított 200 GB-os tömb (RAID-0) /mnt/raidencrypted felcsatolva. Tekintse meg a [– gyakori kérdések](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) támogatott Linux-kiszolgáló disztribúciókra vonatkozó cikket. 

- [Egy előre titkosított virtuális merevlemez a Windows vagy Linux rendszeren a lemeztitkosítás engedélyezve](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm)

- [Hozzon létre egy új titkosított felügyelt lemezt egy előzetes titkosítással VHD/storage-blobból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Létrehoz egy új titkosított felügyelt lemezt előre titkosított virtuális merevlemez és a megfelelő titkosítási beállítások

- [Egy futó Windows virtuális gép az Azure AD-ügyfél tanúsítványának ujjlenyomata lemeztitkosítás engedélyezve](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-aad-client-cert)
    


## <a name="bkmk_preWin"></a> Előzetes titkosítással Windows virtuális merevlemez előkészítése
A következő szakaszok szükség annak előre titkosított Windows virtuális merevlemez előkészítése az Azure IaaS-titkosított merevlemezként üzembe helyezéshez. Az információk segítségével készítheti elő, és indítsa el az Azure Site Recovery vagy az Azure friss Windows virtuális gép (VHD). VHD feltöltése és készítse elő a további információkért lásd: [általános VHD feltöltése és ezzel hozzon létre új virtuális gépeket az Azure-ban](../virtual-machines/windows/upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Nem TPM-OS védelem engedélyezése csoportházirend frissítése
A BitLocker csoportházirend-beállítás konfigurálása **a BitLocker meghajtótitkosítás**, amelyek alapján megtalálhatja **helyi számítógép-házirend** > **számítógép konfigurációja**  >  **Felügyeleti sablonok** > **Windows-összetevők**. Ez a beállítás módosítása **operációsrendszer-meghajtók** > **további hitelesítés indításkor szükséges** > **kompatibilisTPMnélküliBitLockerengedélyezése**, az alábbi ábrán látható módon:

![Microsoft Antimalware szolgáltatás az Azure-ban](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>A BitLocker a szolgáltatás-összetevők telepítése
Windows Server 2012 és újabb verziók használja a következő parancsot:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

A Windows Server 2008 R2 a következő paranccsal:

    ServerManagerCmd -install BitLockers
### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Készítse elő az operációsrendszer-kötet BitLocker segítségével `bdehdcfg`
Az operációs rendszer partíció tömörítése, és a gép előkészítése a BitLocker, hajtsa végre a [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) szükség esetén:

    bdehdcfg -target c: shrink -quiet 


### <a name="protect-the-os-volume-by-using-bitlocker"></a>Az operációsrendszer-kötet védelmét a BitLocker használatával
Használja a [ `manage-bde` ](https://technet.microsoft.com/library/ff829849.aspx) paranccsal engedélyezheti a titkosítást a rendszerindító kötet egy külső kulcsvédő segítségével. A külső meghajtók vagy kötetek is elhelyezhető a külső kulcs (.bek fájl). Titkosítás után a következő újraindításkor engedélyezve van a rendszerlemez vagy rendszerindító köteten.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Készítse elő a virtuális Gépet egy külön data/erőforrás VHD-t a bitlockerrel a külső kulcs beolvasása.

## <a name="bkmk_LinuxRunning"></a> Egy Linux virtuális gépen futó operációs rendszer meghajtójának titkosításához

### <a name="prerequisites-for-os-disk-encryption"></a>Az operációs rendszer lemeztitkosítás előfeltételei

* A virtuális Gépet kell használnia egy kompatibilis az operációs rendszer lemeztitkosítás terjesztési felsorolt a [Azure Disk Encryption – gyakori kérdések](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) 
* A virtuális Gépen a Marketplace-lemezképből az Azure Resource Manager kell létrehozni.
* Az Azure virtuális gép legalább 4 GB RAM (javasolt a mérete, 7 GB).
* (Az RHEL és CentOS) Tiltsa le a SELinux. Tiltsa le a SELinux, lásd: "4.4.2. Letiltás SELinux"az a [SELinux felhasználói és rendszergazdai útmutató](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) a virtuális gépen.
* Miután letiltja az SELinux, legalább egyszer indítsa újra a virtuális gép.

### <a name="steps"></a>Lépések
1. Virtuális gép létrehozása a korábban megadott disztribúció használatával.

 7.2 CentOS az operációs rendszer lemeztitkosítás támogatott keresztül egy rendszerképet. Ez a rendszerkép használatához adja meg a "7.2n" Termékváltozat, a virtuális gép létrehozásakor:

 ```powershell
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Konfigurálja a virtuális gép igény szerint. Ha a (operációs rendszer és összes adatok) titkosítása meghajtók, az adatmeghajtók kell lennie a megadott és a csatlakoztatható /etc/fstab fog.

 > [!NOTE]
 > Használja az UUID azonosítója =... megadásához adatmeghajtók az/etc/fstab fájlban a blokk-eszköz neve (például/dev/sdb1) megadása helyett. Titkosítás során meghajtók sorrendje módosítja, a virtuális gépen. Ha a virtuális gép egy adott rendelés blokkeszközöket támaszkodik, azt fogja tudni titkosítás után csatlakoztassa őket.

3. Jelentkezzen ki az SSH-munkamenetet.

4. Titkosítása az operációs rendszer, adja meg, mint volumeType **összes** vagy **operációs rendszer** amikor engedélyezi a titkosítást.

 > [!NOTE]
 > Az összes felhasználói térben futó folyamatok, nem, `systemd` szolgáltatások le fognak állni a egy `SIGKILL`. Indítsa újra a virtuális Gépet. Ha engedélyezi az operációs rendszer lemeztitkosítás egy futó virtuális gépen, tervezze meg a virtuális gépek üzemszünete.

5. Rendszeres időközönként szakaszban foglaltak szerint a titkosítás állapotának figyelése a [következő szakasz](#monitoring-os-encryption-progress).

6. Miután a Get-AzureRmVmDiskEncryptionStatus "VMRestartPending" jeleníti meg, indítsa újra a virtuális gép jelentkezik be, vagy a portal, PowerShell vagy parancssori felület használatával.
    ```powershell
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Indítsa újra, mielőtt azt javasoljuk, hogy mentse [rendszerindítási diagnosztika](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) a virtuális gép.

## <a name="monitoring-os-encryption-progress"></a>Az operációs rendszer titkosítási folyamat figyelése
Az operációs rendszer titkosítási folyamat három módon figyelheti:

* Használja a `Get-AzureRmVmDiskEncryptionStatus` parancsmagot, és vizsgálja meg a Feladatnézetben mező:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 Miután a virtuális gép eléri a "Az operációs rendszer lemezén titkosítás lépései", körülbelül 40 – 50 percet vesz igénybe egy Premium Storage virtuális gépek biztonsági.

 Mert [#388 ki](https://github.com/Azure/WALinuxAgent/issues/388) WALinuxAgent, a `OsVolumeEncrypted` és `DataVolumesEncrypted` szabályzatként jelenik meg `Unknown` az egyes disztribúciók. A WALinuxAgent verzió 2.1.5, és később, a probléma automatikusan megoldódik. Ha látja `Unknown` a kimenetben ellenőrizheti lemeztitkosítási állapotát az Azure Resource Explorer használatával.

 Lépjen a [Azure erőforrás-kezelő](https://resources.azure.com/), majd bontsa ki a hierarchiában, a bal oldali panelen kiválasztása:

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 A az InstanceView görgessen le a titkosítási állapot meghajtó megtekintéséhez.

 ![Virtuális gép példányait tartalmazó nézet](./media/azure-security-disk-encryption/vm-instanceview.png)

* Tekintse meg [rendszerindítási diagnosztika](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). A ADE bővítményből származó üzenetek előtaggal kell `[AzureDiskEncryption]`.

* Jelentkezzen be a virtuális gép SSH-n keresztül, és a bővítmény a napló beolvasása:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 Azt javasoljuk, hogy nem jelentkezik be a virtuális gép, amíg folyamatban van az operációs rendszer titkosítási. Másolja a naplókat, csak akkor, ha a két módszer nem sikerült.

## <a name="bkmk_preLinux"></a> Előzetes titkosítással Linux rendszerű virtuális merevlemez előkészítése
Előre titkosított virtuális merevlemezek előkészítéséhez a terjesztési függően változhat. Példák a előkészítése [Ubuntu 16](#bkmk_Ubuntu), [openSUSE, 13.2](#bkmk_openSUSE), és [CentOS 7](#bkmk_CentOS) érhetők el. 

### <a name="bkmk_Ubuntu"></a> Ubuntu 16
A terjesztési telepítése közben titkosítás konfigurálása a következő lépések végrehajtásával:

1. Válassza ki **titkosított kötetek konfigurálni** mikor particionálni a lemezeket.

 ![Ubuntu 16.04 beállítása – titkosított kötetek konfigurálni](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Hozzon létre egy külön rendszerindítási meghajtót, amely nem lesznek titkosítva. A legfelső szintű meghajtójának titkosításához.

 ![Ubuntu 16.04-telepítő – titkosításához, jelölje ki az eszközöket](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Adjon meg egy jelszót. Ez az a jelszót a key vault feltöltött.

 ![Ubuntu 16.04 beállítása – adja meg a jelszót](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Fejezze be a particionálást.

 ![Ubuntu 16.04 beállítása – Befejezés particionálása](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Ha a virtuális gép, és a egy hozzáférési kódot a rendszer kéri, használja a 3. lépésében megadott jelszót.

 ![Ubuntu 16.04 beállítása – adja meg a jelszót a rendszerindító](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. A virtuális gép előkészítése Azure-ban való fel [ezek az utasítások](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Nem futnak (a virtuális gép megszüntetése) az utolsó lépés még.

Adja meg a titkosítás működéséhez az Azure-ral a következő lépések végrehajtásával:

1. Hozzon létre egy fájlt /usr/local/sbin/azure_crypt_key.sh, a következő parancsfájl tartalmát. A KeyFileName figyelmet fordítania, mert az Azure által használt a hozzáférési kódot fájlnév.

    ```
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. Módosítsa a titkosítási konfiguráció a */etc/crypttab*. A listának így kell kinéznie:
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Ha szerkeszti *azure_crypt_key.sh* Windows, és másolja a, Linux, futtassa `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Adja hozzá a parancsfájl végrehajtható engedélyek:
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. Szerkesztés */etc/initramfs-tools/modules* sorok hozzáfűzésével:
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. Futtatás `update-initramfs -u -k all` frissíteni a initramfs győződjön meg arról, hogy a `keyscript` érvénybe léptetéséhez.

7. Most már Ön is a virtuális gép megszüntetéséhez.

 ![Ubuntu 16.04-telepítő – frissítés-initramfs](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Folytassa a következő lépéssel, és töltse fel a VHD-t az Azure-bA.

### <a name="bkmk_openSUSE"></a>  openSUSE, 13.2
Konfigurálja a titkosítást a terjesztési telepítése során, tegye a következőket:
1. Amikor particionálni a lemezeket, válassza ki a **Kötetcsoport titkosítása**, majd írja be a jelszót. Ez az a jelszó a key vault feltöltött lesz.

 ![openSUSE, 13.2 beállítása – Kötetcsoport titkosítása](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Indítsa el a virtuális gép az Ön jelszavát.

 ![openSUSE, 13.2 beállítása – adja meg a jelszót a rendszerindító](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. A virtuális gép előkészítése Azure-ban utasításait követve feltöltése [SLES- vagy opensuse-alapú virtuális gép előkészítése Azure-](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Nem futnak (a virtuális gép megszüntetése) az utolsó lépés még.

A titkosítás működéséhez az Azure-ral konfigurálásához kövesse az alábbi lépéseket:
1. Szerkessze a /etc/dracut.conf, és adja hozzá a következő sort:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Tegye megjegyzésbe ezen a fájl /usr/lib/dracut/modules.d/90crypt/module-setup.sh végén:
 ```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. Fűzze hozzá a következő sort a fájl /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh elejére:
 ```
    DRACUT_SYSTEMD=0
 ```
És az összes előfordulását módosítsa:
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
erre:
```
    if [ 1 ]; then
```
4. Szerkesztés /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh, és fűzze hozzá a "# nyitott LUKS eszköz":

    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Futtatás `/usr/sbin/dracut -f -v` az initrd frissíteni.

6. Most a virtuális gép megszüntetése, és töltse fel a VHD-t az Azure-bA.

### <a name="bkmk_CentOS"></a> CentOS 7
Konfigurálja a titkosítást a terjesztési telepítése során, tegye a következőket:
1. Válassza ki **az adatok titkosítása** lemezek particionálása esetén.

 ![CentOS 7 beállítása – telepítési cél](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Győződjön meg arról, hogy **titkosítása** gyökérpartíciót van kiválasztva.

 ![CentOS 7 beállítása – válassza ki a gyökérpartíciót titkosítása](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Adjon meg egy jelszót. Ez az a feltöltött lesz a kulcstartó hozzáférési kódot.

 ![CentOS 7 beállítása – adja meg a jelszót](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Ha a virtuális gép, és a egy hozzáférési kódot a rendszer kéri, használja a 3. lépésében megadott jelszót.

 ![CentOS 7 beállítása – adja meg a jelszót a rendszerindítási](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. A virtuális gép előkészítése Azure-ba való feltöltése, a "CentOS 7.0 +" szakaszban foglaltak szerint [CentOS-alapú virtuális gép előkészítése Azure-](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Nem futnak (a virtuális gép megszüntetése) az utolsó lépés még.

6. Most a virtuális gép megszüntetése, és töltse fel a VHD-t az Azure-bA.

A titkosítás működéséhez az Azure-ral konfigurálásához kövesse az alábbi lépéseket:

1. Szerkessze a /etc/dracut.conf, és adja hozzá a következő sort:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Tegye megjegyzésbe ezen a fájl /usr/lib/dracut/modules.d/90crypt/module-setup.sh végén:
```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. Fűzze hozzá a következő sort a fájl /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh elejére:
```
    DRACUT_SYSTEMD=0
```
És az összes előfordulását módosítsa:
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
erre:
```
    if [ 1 ]; then
```
4. /Usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh szerkesztése, és fűzze hozzá a "# nyitott LUKS eszköz" után a következőket:
    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Futtassa a "/ usr/sbin/dracut - f - v" az initrd frissíteni.

![CentOS 7 telepítő – /usr/sbin/dracut -f - v rendszert futtató](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

## <a name="bkmk_UploadVHD"></a> Titkosított virtuális lemezek feltöltése az Azure storage-fiókba
BitLocker-titkosítást vagy DM-Crypt titkosítás engedélyezése után a helyi titkosított virtuális merevlemez kell feltölteni a tárfiókba.
```powershell
    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="bkmk_UploadSecret"></a> Töltse fel a titkos kulcsot a key vault előzetes titkosított virtuális gép
Titkosításához az Azure AD-alkalmazás (előző kiadás) használatával, a a korábban beszerzett-lemeztitkosítás titkos kulcsot a kulcstartóban titkos blobnévvel legyen feltöltve. A key vaultban kell rendelkeznie a lemeztitkosítás és az Azure AD-ügyfél engedélyezve.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="bkmk_SecretnoKEK"></a> Lemeztitkosítás titkos kódja egy KEK nem titkosított
Állítsa be a titkos kulcsot tárol a kulcstárolóban, használja a [Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret). Ha egy Windows virtuális gépet, a blokktitkosítási kulcsot fájl Base64 kódolású karakterláncként kódolt és majd a key vaulttal történő feltöltésekor a `Set-AzureKeyVaultSecret` parancsmagot. Linux esetén a hozzáférési kódot Base64 kódolású karakterláncként kódolt és feltölthetők a key vaulthoz. Emellett ellenőrizze, hogy a titkos kulcsot a key vaultban történő létrehozásakor a következő címkék vannak-e beállítva.

#### <a name="windows-bek-file"></a>Windows rendelkeznek BEk-KEL fájl
```powershell
# Change the VM Name, key vault name, and specify the path to the BEK file.
$VMName ="MySecureVM"
$BEKFilepath = "C:\test\BEK\12345678-90AB-CDEF-A1B2-C3D4E5F67890A.BEK"
$VeyVaultName ="MySecureVault"

# Get the name of the BEK file from the BEK file path. This will be a tag for the key vault secret.
$BEKFileName =  Split-Path $BEKFilepath -Leaf

# These tags will be added to the key vault secret so you can easily see which BEK file belongs to which VM.
$tags = @{“MachineName” = “$VMName”;"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "$BEKFileName"}

# Convert the BEK file to a Base64 string.
$FileContentEncoded = [System.convert]::ToBase64String((Get-Content -Path $BEKFilepath -Encoding Byte))

# Create a new secret in the vault from the converted BEK file. 
# The file is converted to a secure string before import into the key vault

$SecretName = [guid]::NewGuid().ToString()
$SecureSecretValue = ConvertTo-SecureString $FileContentEncoded -AsPlainText -Force
$Secret = Set-AzureKeyVaultSecret -VaultName $VeyVaultName -Name $SecretName -SecretValue $SecureSecretValue -tags $tags

# Show the secret's URL and store it as a variable. This is used as -DiskEncryptionKeyUrl in Set-AzureRmVMOSDisk when you attach your OS disk. 
$SecretUrl=$secret.Id
$SecretUrl
```

#### <a name="linux"></a>Linux
```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Használja a `$secretUrl` esetében a következő lépésben [KEK használata nélkül az operációsrendszer-lemez csatolása](#bkmk_URLnoKEK).

### <a name="bkmk_SecretKEK"></a> Lemeztitkosítás titkos kódja egy KEK titkosítva
Mielőtt feltölti a titkos kulcsot a key vaulthoz, igény szerint titkosíthatók, kulcstitkosítási kulcs használatával. Használja a sortörés [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) először mesterkulcs a kulcstitkosítási kulcs használatával. A sortörés művelet kimenete egy base64 URL-kódolású karakterláncot, amely ezt követően feltöltheti, titkos kulcs használatával a [ `Set-AzureKeyVaultSecret` ](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) parancsmagot.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Használat `$KeyEncryptionKey` és `$secretUrl` esetében a következő lépésben [KEK használatával az operációsrendszer-lemez csatolása](#BKMK_URLKEK).

##  <a name="bkmk_SecretURL"></a> Adja meg titkos URL-cím, egy operációsrendszer-lemez csatolása

###  <a name="bkmk_URLnoKEK"></a>Egy KEK használata nélkül
Amíg az operációsrendszer-lemez csatol, teljesítenie kell a `$secretUrl`. Az URL-címet az "a-lemeztitkosítás titkos kulcs egy KEK nem titkosított" szakaszban jött létre.
```powershell
    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="bkmk_URLKEK"></a>Egy KEK használatával
Ha az operációsrendszer-lemez csatolásához `$KeyEncryptionKey` és `$secretUrl`. Az URL-címet az "Lemeztitkosítás titkos kódja egy KEK titkosított" szakaszban jött létre.
```powershell
    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```

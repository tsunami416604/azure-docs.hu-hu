---
title: Az Azure Disk Encryption az Azure AD-alkalmazás Linux rendszerű IaaS virtuális gépek (előző kiadás)
description: Ez a cikk útmutatást nyújt a Microsoft Azure Disk Encryption engedélyezése a Linux rendszerű IaaS virtuális gépekhez.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 853b1308edb55257495c0aed52710cfe23008203
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828485"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Azure Disk Encryption engedélyezése az Azure AD-vel Linux rendszerű virtuális gépeken (korábbi kiadás)

**Az Azure Disk Encryption új kiadása így nem egy Azure AD-alkalmazás paramétert, amelyek biztosítják a virtuális gép lemez-titkosítás engedélyezéséhez. Az új kiadással már nem szükséges az Azure AD-beli hitelesítő adatok megadása a titkosítás engedélyezése lépés során. Valamennyi új virtuális gépeket titkosítani kell az Azure AD alkalmazás paramétereit az új kiadás használata nélkül. Ahhoz, hogy az új kiadással VM lemeztitkosítás utasítások megtekintése: [Linux rendszerű virtuális gépekhez az Azure Disk Encryption](disk-encryption-linux.md). Az Azure AD-alkalmazás paraméterekkel már titkosított virtuális gépek továbbra is támogatottak, és továbbra is fenn kell tartani az AAD-szintaxissal.**

Engedélyezheti a lemeztitkosítási számos forgatókönyv, és a lépések eltérőek lehetnek a forgatókönyv szerint. Az alábbi szakaszok részletesebben a forgatókönyvek Linux rendszerű IaaS virtuális gépek terjed ki. A lemezes titkosítást csak a [támogatott virtuálisgép-méretek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems)virtuális gépei esetében lehet alkalmazni. A következő előfeltételeknek is teljesülniük kell:

- [A virtuális gépekre vonatkozó további követelmények](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Hálózatkezelés és Csoportházirend](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Titkosítási kulcs tárolási követelményei](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Igénybe vehet egy [pillanatkép](snapshot-copy-managed-disk.md) és/vagy lemezek előtt készítsen biztonsági másolatot. Biztonsági mentések ellenőrizze, hogy a helyreállítási beállítások titkosítás során váratlan hiba esetén lehetséges. A felügyelt lemezekkel rendelkező virtuális gépek biztonsági szükséges, a titkosítás előtt. A biztonsági mentés után a set-AzVMDiskEncryptionExtension parancsmag használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. Biztonsági mentése és visszaállítása titkosított virtuális gépek kapcsolatos további információkért lásd: a [Azure Backup](../../backup/backup-azure-vms-encryption.md) cikk. 

>[!WARNING]
 > - Ha korábban már használta [Azure Disk Encryption az Azure ad-alkalmazással](disk-encryption-overview-aad.md) a virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Ezen a titkosított virtuális gépen nem használható [Azure Disk Encryption](disk-encryption-overview.md) , mivel ez nem támogatott forgatókönyv, ami azt jelenti, hogy a titkosított virtuális gép esetében a HRE alkalmazásból való átállás még nem támogatott.
 > - Annak érdekében, hogy a titkosítási titkos kulcsok nem adatbázisközi regionális határokat, az Azure Disk Encryption a Key Vault és a virtuális gépek közös elhelyezése ugyanabban a régióban van szüksége. Létrehozhat és használhat egy Key Vaultot, amely ugyanabban a régióban, mint a virtuális gép titkosítását.
 > - Linux operációs rendszer kötet titkosításakor az a folyamat néhány órát is igénybe vehet. Előfordulhat, hogy tovább tart, mint az adatkötetek titkosításához Linux operációsrendszer-kötetek.
> - A Linux operációs rendszer köteteinek titkosításakor a virtuális gépet nem szabad megfontolni. Határozottan javasoljuk, hogy az SSH-bejelentkezések elkerülése érdekében a titkosítási folyamat során ne akadályozza meg az összes olyan megnyitott fájl blokkolását, amelyet a titkosítási folyamat során el kell érnie. A folyamat ellenőrzéséhez használhatja a [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) vagy a [VM encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsait. Ez a folyamat várhatóan néhány órát is igénybe vehet egy 30 GB-OS kötetre, és további időt az adatkötetek titkosítására. Az adatmennyiség-titkosítási idő arányos lesz az adatkötetek méretétől és mennyiségétől, kivéve, ha az összes titkosítás formátuma beállítást használja. 
 > - Linux rendszerű virtuális gépek titkosításának letiltása csak az adatkötetek esetében támogatott. Nem támogatott a adatok vagy operációsrendszer-kötettel, ha az operációsrendszer-kötet titkosított.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="bkmk_RunningLinux"> </a> Engedélyezze a titkosítást egy meglévő vagy futó IaaS Linux rendszerű virtuális gépen

Ebben a forgatókönyvben a Resource Manager-sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával engedélyezheti a titkosítást. 

>[!IMPORTANT]
 >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt. A felügyelt lemez pillanatképét elvégezhet a portálról, vagy [Azure Backup](../../backup/backup-azure-vms-encryption.md) is használható. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. A biztonsági mentést követően a set-AzVMDiskEncryptionExtension parancsmag használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. A set-AzVMDiskEncryptionExtension parancs a felügyelt lemezes virtuális gépektől a biztonsági mentésig meghiúsul, és ez a paraméter meg lett adva. 
>
>Titkosítása vagy letiltja a titkosítást a virtuális gép újraindítását okozhatja. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Engedélyezze a titkosítást egy meglévő vagy futó Linux rendszerű virtuális gépen az Azure CLI használatával 
Engedélyezheti lemeztitkosítás a titkosított virtuális merevlemezen történő telepítéssel és a [Azure CLI 2.0](/cli/azure) parancssori eszköz. Használhatja a böngészőjében az [Azure Cloud Shell-lel](../../cloud-shell/overview.md), vagy telepítheti a helyi gépen, és használhatja bármely PowerShell-munkamenetben. Meglévő, vagy Azure-ban futó IaaS Linux rendszerű virtuális gépek titkosításának engedélyezéséhez használja a következő CLI-parancsokat:

Használja a [az vm encryption engedélyezése](/cli/azure/vm/encryption#az-vm-encryption-enable) parancsot az Azure-ban futó IaaS virtuális gép titkosításának engedélyezéséhez.

-  **Ügyfél titkos kulcs használatával futó virtuális gépek titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **A titkos ügyfélkulcsot burkolása KEK használatával futó virtuális gépek titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának megtekintéséhez használja az az [VM encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Titkosítás letiltása:** A titkosítás letiltásához használja az az [VM encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. Letiltja a titkosítást csak engedélyezett megváltoztatását az adatköteteken Linux rendszerű virtuális gépekhez.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a> Engedélyezheti a titkosítást egy meglévő vagy futó Linux rendszerű virtuális gép PowerShell-lel
A [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban. Készítsen [pillanatképet](snapshot-copy-managed-disk.md) és/vagy készítsen biztonsági másolatot a virtuális gépről [Azure Backup](../../backup/backup-azure-vms-encryption.md) a lemezek titkosítása előtt. A-skipVmBackup paraméter már meg van adva a PowerShell-parancsfájlokban egy futó Linux rendszerű virtuális gép titkosításához.

- **Ügyfél titkos kulcs használatával futó virtuális gépek titkosítása:** Az alábbi szkript inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, virtuális gép, a key vault, AAD-alkalmazás és titkos Ügyfélkód kell rendelkezik már létre van hozva előfeltételeket. Cserélje le a MyVirtualMachineResourceGroup, a MyKeyVaultResourceGroup, a MySecureVM, a MySecureVault, a My-HRE-Client-ID és a My-HRE-Client-Secret értékeket az értékekre. Módosítsa a-VolumeType paramétert annak megadásához, hogy mely lemezek vannak titkosítva.

    ```azurepowershell
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $vmName = 'MySecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
    ```
- **A titkos ügyfélkulcsot burkolása KEK használatával futó virtuális gépek titkosítása:** Azure Disk Encryption lehetővé teszi egy meglévő kulcs megadását a kulcstartóban a titkosítás engedélyezésekor létrehozott lemez-titkosítási titkok becsomagolásához. Amikor egy kulcsalapú titkosítás kulcsa van megadva, az Azure Disk Encryption a kulcs segítségével burkolhatja a titkosítási titkos kulcsait a Key Vault írása előtt. Módosítsa a-VolumeType paramétert annak megadásához, hogy mely lemezek vannak titkosítva. 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[KVresource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name]</br> </br>
  A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának vizsgálatához használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Lemez titkosításának letiltása:** A titkosítás letiltásához használja a [disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. Letiltja a titkosítást csak engedélyezett megváltoztatását az adatköteteken Linux rendszerű virtuális gépekhez.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Engedélyezheti a titkosítást egy meglévő vagy futó IaaS Linux rendszerű virtuális gép sablon alapján

Lemeztitkosítás IaaS virtuális gépen meglévő vagy futó Linux rendszerű Azure-beli használatával engedélyezheti a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. Kattintson a **üzembe helyezés az Azure** Azure gyors üzembe helyezés a sablonban.

2. Válassza ki az előfizetést, erőforráscsoportot, erőforráscsoport helye, paraméterek, jogi feltételek és szerződés. Kattintson a **létrehozás** engedélyezheti a titkosítást a meglévő vagy futó IaaS virtuális gépen.

Az alábbi táblázat a meglévő vagy egy Azure AD ügyfél-Azonosítót használó virtuális gépek futtatása a Resource Manager-Sablonparaméterek:

| Paraméter | Leírás |
| --- | --- |
| AADClientID | Az Azure AD-alkalmazást, amely a key vault titkos kódok írási engedélyekkel rendelkezik ügyfél-azonosítója. |
| AADClientSecret | Az Azure AD-alkalmazást, amely titkos kulcsok a key vault írási engedélyekkel rendelkezik a titkos ügyfélkódja. |
| keyVaultName | A kulcstartóhoz, amely fel kell tölteni a kulcs neve. Az Azure CLI-parancs használatával beszerezheti azt `az keyvault show --name "MySecureVault" --query KVresourceGroup`. |
|  keyEncryptionKeyURL | A kulcsalapú titkosítás kulcsa, a létrehozott kulcs titkosításához használt URL-címe. Ez a paraméter nem kötelező, ha kiválasztja **nokek** a UseExistingKek legördülő listában. Ha **kek** a UseExistingKek legördülő listában, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| VolumeType | A titkosítási műveletet végzi el a kötet típusa. Érvényes támogatott értékei a következők _operációs rendszer_ vagy _összes_ (lásd a támogatott Linux-disztribúciók és azok verzióinak az Előfeltételek szakaszban korábban operációsrendszer- és adatlemezek). |
| SequenceVersion | A BitLocker művelet verziója. Növelje a verziószámot, minden alkalommal, amikor a lemeztitkosítási műveletet végzi el az azonos virtuális gépen. |
| vmName | Neve a virtuális gép, amely a titkosítási műveletet kell végrehajtani. |
| Hozzáférési kód | Írjon be egy erős jelszót a szolgáltatásadat-titkosítási kulcs. |



## <a name="bkmk_EFA"> </a>A Linux rendszerű IaaS virtuális gépek adatlemezét EncryptFormatAll szolgáltatással
A **EncryptFormatAll** paraméter lerövidíti a Linux data lemezek titkosítását. Partíciók megfelelnek bizonyos feltételeknek lesz formázva (az aktuális fájlrendszer). Majd azok lesz újra kell csatlakoztatni vissza a hol történt a parancs végrehajtása előtt. Ha kizár egy olyan adatok lemezt, amely megfelel a feltételeknek, válassza le azt a parancs futtatása előtt.

 Ez a parancs futtatása után, amely csatlakoztatva voltak meghajtóknak korábban lesz formázva. Ezután a titkosítási réteget a már üres meghajtó felett indul. Ha ezt a lehetőséget választja, a rövid élettartamú erőforrás lemezt a virtuális Géphez csatolt is titkosítva lesznek. A rövid élettartamú meghajtó alaphelyzetbe áll, ha újraformázta és újra titkosítja a virtuális gép az Azure Disk Encryption megoldás a következő alkalommal.

>[!WARNING]
> EncryptFormatAll nem használható, ha egy virtuális Gépet az adatkötetek szükséges adatokat. Lemezek kizárhatják titkosítás, ha shellről őket. Meg kell megismerheti a szolgáltatás paraméter és a utalás előtt próbálja ki az éles virtuális gép a, próbálja ki az először a tesztelési virtuális gép EncryptFormatAll először. A EncryptFormatAll beállítás formázza az adatlemezt, és a rajta lévő adatok elvesznek. A folytatás előtt győződjön meg arról, hogy ki szeretne lemezek megfelelően le-e. </br></br>
 >Ez a paraméter beállítás folyamatban van a titkosítási beállítások frissítése közben, ha azt a számítógép újraindítása előtt a tényleges titkosítási vezethet. Ebben az esetben is érdemes távolítsa el a lemezt nem szeretné, hogy formázva az fstab fájl. Hasonlóképpen adja hozzá azt a partíciót az fstab fájl titkosítása-formátumú, mielőtt elindítaná a titkosítási műveletet. 

### <a name="bkmk_EFACriteria"> </a> EncryptFormatAll feltételek
A paraméter kerül, ha az összes partíció, és titkosítja azokat, amennyiben azok megfelelnek **összes** az alábbi feltételek: 
- Nem legfelső szintű vagy az operációs rendszer és rendszerindító partíció
- Már nem titkosított
- Nem rendelkeznek BEk-KEL kötet
- Nem RAID-kötet
- LVM kötet nem
- Csatlakoztatva van

A RAID vagy LVM kötetet, hanem a RAID vagy LVM kötetet alkotó lemezek titkosítása.

### <a name="bkmk_EFATemplate"> </a> Használja a EncryptFormatAll paramétert a sablon alapján
Az EncryptFormatAll beállítást használja, minden olyan már meglévő Azure Resource Manager-sablon, amely titkosítja a Linux rendszerű virtuális gép használja, és módosítsa a **EncryptionOperation** AzureDiskEncryption erőforrás mezőjét.

1. Tegyük fel, használja a [futó Linux rendszerű IaaS virtuális gépek titkosításához a Resource Manager-sablon](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Kattintson a **üzembe helyezés az Azure** Azure gyors üzembe helyezés a sablonban.
3. Módosítsa a **EncryptionOperation** a **EnableEncryption** való **EnableEncryptionFormatAl**
4. Válassza ki az előfizetést, az erőforráscsoport, erőforráscsoport helye, más paramétereket, a jogi feltételeket és az szerződés. Kattintson a **létrehozás** engedélyezheti a titkosítást a meglévő vagy futó IaaS virtuális gépen.


### <a name="bkmk_EFAPSH"> </a> Használja a EncryptFormatAll paraméterrel együtt egy PowerShell-parancsmag
Használja a [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmagot a `EncryptFormatAll` paraméterrel.

**Futó virtuális gép titkosítása az ügyfél titkos és EncryptFormatAll használatával:** Az alábbi szkript például inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot a EncryptFormatAll paraméterrel. Az erőforráscsoport, virtuális gép, a key vault, AAD-alkalmazás és titkos Ügyfélkód kell rendelkezik már létre van hozva előfeltételeket. Cserélje le a MyKeyVaultResourceGroup, a MyVirtualMachineResourceGroup, a MySecureVM, a MySecureVault, a My-HRE-Client-ID és a My-HRE-Client-Secret értékeket az értékekre.
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="bkmk_EFALVM"> </a> Használja a EncryptFormatAll paraméterrel rendelkező logikai kötet kezelő (LVM) 
Azt javasoljuk, hogy az LVM-a-crypt beállítása. A következő példák az összes cserélje le az eszköz-elérési útját, és akkor csatlakozási bármilyen megfelelő-e a használati eset. A telepítő a következő elvégezhető:

- Adja hozzá az adatlemezeket a virtuális Gépet alkotó lesz.
- Formázhatja, csatlakoztathatja és ezeket a lemezeket ad hozzá az fstab fájlt.

    1. Formázza az újonnan hozzáadott lemezt. Itt az Azure létrehoz symlinks használjuk. Symlinks használatával elkerülheti a problémákat, az eszköz nevének módosítása. További információkért lásd: a [problémák elhárítása eszköznevek](troubleshoot-device-names-problems.md) cikk.
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Csatlakoztassa a lemezeket.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Adja hozzá az fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Futtassa a set-AzVMDiskEncryptionExtension PowerShell-parancsmagot a-EncryptFormatAll használatával a lemezek titkosításához.
         ```azurepowershell-interactive
         Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Állítsa be LVM felett ezeket a lemezeket. Vegye figyelembe a titkosított meghajtó zárolását a virtuális gép befejezése után indul el. Az LVM csatlakoztatási így is megkapják ezt követően kell-e késleltetni.




## <a name="bkmk_VHDpre"> </a> Ügyfél-titkosítású VHD-t és a titkosítási kulcsok alapján létrehozott új IaaS virtuális gépek
Ebben a forgatókönyvben engedélyezheti a Resource Manager-sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával titkosítja. A Resource Manager-sablon és a CLI-parancsokat az alábbi szakaszok részletesebben ismertetik. 

Kövesse az utasításokat a függelékben használható az Azure-ban előzetes titkosítással rendszerképek előkészítéséről. A lemezkép létrehozását követően használhatja a lépéseket a következő szakaszban titkosított Azure virtuális gép létrehozásához.

* [Előzetes titkosítással Linux rendszerű virtuális merevlemez előkészítése](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt. A felügyelt lemez pillanatképét elvégezhet a portálról, vagy [Azure Backup](../../backup/backup-azure-vms-encryption.md) is használható. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. A biztonsági mentést követően a set-AzVMDiskEncryptionExtension parancsmag használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. A set-AzVMDiskEncryptionExtension parancs a felügyelt lemezes virtuális gépektől a biztonsági mentésig meghiúsul, és ez a paraméter meg lett adva. 
>
>Titkosítása vagy letiltja a titkosítást a virtuális gép újraindítását okozhatja. 



### <a name="bkmk_VHDprePSH"> </a> Az Azure PowerShell használatával előre titkosított virtuális merevlemezzel rendelkező IaaS virtuális gépek titkosítása 
A titkosított virtuális merevlemez titkosítását a [set-AzVMOSDisk PowerShell-](/powershell/module/az.compute/set-azvmosdisk#examples)parancsmag használatával engedélyezheti. Az alábbi példa néhány gyakori paramétereket biztosít. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott adatlemez
Hozzáadhat egy új lemez használatával [az vm disk attach](add-disk.md), vagy [az Azure Portalon keresztül](attach-disk-portal.md). Mielőtt titkosíthatók, először csatlakoztassa az újonnan csatolt adatlemezre kell. Az adatmeghajtó titkosításához kell kérnie, mert a meghajtó használhatatlan lesz, bár a titkosítás folyamatban van. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott lemezt az Azure CLI-vel
 Ha a virtuális gép korábban titkosítva van az "All", majd a--kötettípus paramétert kell hagyni minden. Mind az operációs rendszer és az adatlemezek összes tartalmazza. Ha a virtuális gép korábban titkosított kötetet típusú "OS", majd a--kötettípus paraméter az összes lehet módosítani, hogy az operációs rendszer és az új adatlemezt is tartalmazza. Ha a virtuális gép titkosított "Adatok" csak a kötet típusát, majd azt is marad, "Data" ahogyan alább is látható. Hozzáadásával és a egy új adatlemez csatolása a virtuális gép nem elegendő előkészítése a titkosításhoz. Az újonnan csatolt lemez is kell formázva, és megfelelően csatlakoztatva a virtuális gép titkosítás engedélyezése előtt. Linux rendszeren a lemezt az/etc/fstab-csatlakoztatni kell egy [állandó blokk eszköznév](troubleshoot-device-names-problems.md).  

Powershell-szintaxis szakembereket a CLI nem igényel egy egyedi verziója szolgáltatni a titkosítás engedélyezése a felhasználó. A CLI automatikusan hoz létre, és használja a saját egyedi feladatütemezési version értéke.

-  **Ügyfél titkos kulcs használatával futó virtuális gépek titkosítása:** 

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **A titkos ügyfélkulcsot burkolása KEK használatával futó virtuális gépek titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott lemezt az Azure PowerShell használatával
 Új lemez titkosítása Linux rendszeren a Powershell segítségével, amikor egy új verziója kell adni. A feladatütemezési verziójában található egyedinek kell lennie. Az alábbi parancsfájlt előállít egy GUID Azonosítót a verziója. 
 

-  **Ügyfél titkos kulcs használatával futó virtuális gépek titkosítása:** Az alábbi szkript inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, virtuális gép, a key vault, AAD-alkalmazás és titkos Ügyfélkód kell rendelkezik már létre van hozva előfeltételeket. Cserélje le a MyVirtualMachineResourceGroup, a MyKeyVaultResourceGroup, a MySecureVM, a MySecureVault, a My-HRE-Client-ID és a My-HRE-Client-Secret értékeket az értékekre. A - VolumeType paraméter értéke az adatlemezeket, és nem az operációsrendszer-lemez. Ha a virtuális gép korábban titkosított "OS" vagy "All" típusú kötet, majd a - VolumeType paraméter kell módosítani az összes, hogy az operációs rendszer és az új adatlemezt is tartalmazza.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $vmName = 'MySecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **A titkos ügyfélkulcsot burkolása KEK használatával futó virtuális gépek titkosítása:** Azure Disk Encryption lehetővé teszi egy meglévő kulcs megadását a kulcstartóban a titkosítás engedélyezésekor létrehozott lemez-titkosítási titkok becsomagolásához. Amikor egy kulcsalapú titkosítás kulcsa van megadva, az Azure Disk Encryption a kulcs segítségével burkolhatja a titkosítási titkos kulcsait a Key Vault írása előtt. A - VolumeType paraméter értéke az adatlemezeket, és nem az operációsrendszer-lemez. Ha a virtuális gép korábban titkosított "OS" vagy "All" típusú kötet, majd a - VolumeType paraméter kell módosítani az összes, hogy az operációs rendszer és az új adatlemezt is tartalmazza.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> </br>
A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

## <a name="disable-encryption-for-linux-vms"></a>Linux rendszerű virtuális gépek titkosításának letiltása
Letilthatja a titkosítás az Azure PowerShell, az Azure CLI használatával vagy a Resource Manager-sablonnal. 

>[!IMPORTANT]
>Letiltja a titkosítást a Linuxos virtuális gépeken az Azure Disk Encryption csak a támogatott adatköteteket. Nem támogatott a adatok vagy operációsrendszer-kötettel, ha az operációsrendszer-kötet titkosított.  

- **Lemez titkosításának letiltása a Azure PowerShell:** A titkosítás letiltásához használja a [disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Titkosítás letiltása az Azure CLI-vel:** A titkosítás letiltásához használja az az [VM encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Tiltsa le a Resource Manager-sablonnal titkosítást:** A titkosítás letiltásához használja a [Linux rendszerű virtuális gép titkosításának letiltására](https://aka.ms/decrypt-linuxvm) szolgáló sablont.
     1. Kattintson a **Deploy to Azure** (Üzembe helyezés az Azure-ban) elemre.
     2. Válassza ki az előfizetés, erőforráscsoport, hely, virtuális gép, jogi feltételeket és szerződés.
     3.  Kattintson a **beszerzési** letiltja a lemeztitkosítást a futó Windows virtuális gépek. 


## <a name="next-steps"></a>További lépések

- [A Linux Azure Disk Encryption áttekintése](disk-encryption-overview-aad.md)
- [Kulcstartó létrehozása és konfigurálása az Azure AD-vel való Azure Disk Encryptionhoz (előző kiadás)](disk-encryption-key-vault-aad.md)

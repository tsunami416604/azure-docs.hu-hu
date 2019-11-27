---
title: Az Azure Disk Encryption az Azure AD-alkalmazás Linux rendszerű IaaS virtuális gépek (előző kiadás)
description: Ez a cikk útmutatást nyújt a Microsoft Azure Disk Encryption engedélyezése a Linux rendszerű IaaS virtuális gépekhez.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: d41f2138a453e4a34354c10bbebad41724a18d1d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457484"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Azure Disk Encryption engedélyezése az Azure AD-vel Linux rendszerű virtuális gépeken (korábbi kiadás)

A Azure Disk Encryption új kiadása szükségtelenné teszi a Azure Active Directory (Azure AD) alkalmazás paraméterének megadását a VM-lemezek titkosításának engedélyezéséhez. Az új kiadással már nem szükséges az Azure AD-beli hitelesítő adatok megadása a titkosítás engedélyezése lépés során. Az új kiadás használatával az új virtuális gépeket az Azure AD-alkalmazás paramétereinek használata nélkül kell titkosítani. A VM Disk Encryption új kiadással való engedélyezésével kapcsolatos útmutatásért lásd: [Azure Disk Encryption Linux rendszerű virtuális gépekhez](disk-encryption-linux.md). Azok a virtuális gépek, amelyek már az Azure AD-alkalmazás paramétereivel lettek titkosítva, továbbra is támogatottak, és továbbra is fenn kell tartaniuk a HRE szintaxisát

Számos lemezes titkosítási forgatókönyvet engedélyezhet, és a lépések a forgatókönyvtől függően változhatnak. A következő részek a Linux-alapú infrastruktúra (IaaS) virtuális gépekkel kapcsolatos részletesebb forgatókönyveket ismertetik. A lemezes titkosítást csak a [támogatott virtuálisgép-méretek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems)virtuális gépei esetében lehet alkalmazni. A következő előfeltételeknek is teljesülniük kell:

- [A virtuális gépekre vonatkozó további követelmények](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Hálózatkezelés és Csoportházirend](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Titkosítási kulcs tárolási követelményei](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Készítsen [pillanatképet](snapshot-copy-managed-disk.md), készítsen biztonsági másolatot, vagy mindkettőt a lemezek titkosítása előtt. Biztonsági mentések ellenőrizze, hogy a helyreállítási beállítások titkosítás során váratlan hiba esetén lehetséges. A felügyelt lemezekkel rendelkező virtuális gépek biztonsági szükséges, a titkosítás előtt. A biztonsági mentést követően a set-AzVMDiskEncryptionExtension parancsmag használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. További információ a titkosított virtuális gépek biztonsági mentéséről és visszaállításáról: [Azure Backup](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Ha korábban [Az Azure ad-alkalmazással Azure Disk Encryption](disk-encryption-overview-aad.md) használta a virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Nem használhatja [Azure Disk Encryption](disk-encryption-overview.md) ezen a titkosított virtuális gépen, mert ez nem támogatott forgatókönyv, ami azt jelenti, hogy a titkosított virtuális géphez való áttérés az Azure ad-alkalmazásból még nem támogatott.
 > - Annak érdekében, hogy a titkosítási titkok ne legyenek több regionális határon, Azure Disk Encryption kell a kulcstartót, és a virtuális gépeket ugyanabban a régióban kell elhelyezni. Hozzon létre és használjon egy olyan kulcstartót, amely ugyanabban a régióban található, mint a titkosítani kívánt virtuális gép.
 > - A Linux operációs rendszer köteteinek titkosításakor a folyamat néhány órát is igénybe vehet. A Linux operációsrendszer-kötetek esetében a titkosításhoz használt adatmennyiség hosszabb időt is igénybe veheti.
> - A Linux operációs rendszer köteteinek titkosítása esetén a virtuális gépet elérhetetlenné kell tekinteni. Határozottan javasoljuk, hogy az SSH-bejelentkezések elkerülése mellett a titkosítás folyamatban van, hogy elkerülje a titkosítási folyamat során elérni kívánt megnyitott fájlok blokkolását. A folyamat ellenőrzéséhez használja a [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) vagy a [VM encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsait. A folyamat várhatóan több órát is igénybe vehet egy 30 GB-OS operációsrendszer-kötetre, valamint további időt az adatkötetek titkosítására. Az adatmennyiség-titkosítási idő arányos az adatkötetek méretétől és mennyiségétől, kivéve, ha az **összes titkosítás formátuma** beállítást használja. 
 > - Linux rendszerű virtuális gépek titkosításának letiltása csak az adatkötetek esetében támogatott. Ha az operációsrendszer-kötet titkosítva van, nem támogatott az adatvagy operációsrendszer-köteteken. 

 

## <a name="bkmk_RunningLinux"></a> Titkosítás engedélyezése meglévő vagy futó IaaS Linux rendszerű virtuális gépen

Ebben az esetben engedélyezheti a titkosítást a Azure Resource Manager sablon, a PowerShell-parancsmagok vagy az Azure CLI-parancsok használatával. 

>[!IMPORTANT]
 >A Azure Disk Encryption engedélyezése előtt el kell végeznie egy pillanatkép készítését vagy a felügyelt lemezes virtuálisgép-példány biztonsági mentését. A felügyelt lemezről pillanatképet készíthet a Azure Portalről, vagy használhatja a [Azure Backupt](../../backup/backup-azure-vms-encryption.md)is. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. A biztonsági mentés után a set-AzVMDiskEncryptionExtension parancsmag használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. A set-AzVMDiskEncryptionExtension parancs a felügyelt lemezes virtuális gépekről a biztonsági mentésig meghiúsul, és ez a paraméter meg van adva. 
>
>A titkosítás titkosítása vagy letiltása a virtuális gép újraindítását okozhatja. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen az Azure CLI használatával 
Az [Azure CLI 2,0](/cli/azure) parancssori eszköz telepítésével és használatával engedélyezheti a lemez titkosítását a titkosított virtuális merevlemezen. Használhatja a böngészőjében az [Azure Cloud Shell-lel](../../cloud-shell/overview.md), vagy telepítheti a helyi gépen, és használhatja bármely PowerShell-munkamenetben. Meglévő, vagy Azure-ban futó IaaS Linux rendszerű virtuális gépek titkosításának engedélyezéséhez használja a következő CLI-parancsokat:

Az az [VM encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) paranccsal engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban.

-  **Futó virtuális gép titkosítása az ügyfél titkos kódjának használatával:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Futó virtuális gép titkosítása a KEK használatával az ügyfél titkos kódjának becsomagolásához:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name].</br> </br> A kulcs-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-név]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id].

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának megtekintéséhez használja az az [VM encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Titkosítás letiltása:** A titkosítás letiltásához használja az az [VM encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. Letiltja a titkosítást csak engedélyezett megváltoztatását az adatköteteken Linux rendszerű virtuális gépekhez.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"></a> Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen a PowerShell használatával
A [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban. Készítsen [pillanatképet](snapshot-copy-managed-disk.md) , vagy készítsen biztonsági másolatot a virtuális gépről [Azure Backup](../../backup/backup-azure-vms-encryption.md) a lemezek titkosítása előtt. A-skipVmBackup paraméter már meg van adva a PowerShell-parancsfájlokban egy futó Linux rendszerű virtuális gép titkosításához.

- **Futó virtuális gép titkosítása az ügyfél titkos kódjának használatával:** A következő parancsfájl inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép, a Key Vault, az Azure AD-alkalmazás és az ügyfél titkos kulcsa már előfeltételként lett létrehozva. Cserélje le a MyVirtualMachineResourceGroup, a MyKeyVaultResourceGroup, a MySecureVM, a MySecureVault, a My-HRE-Client-ID és a My-HRE-Client-Secret értékeket az értékekre. Módosítsa a-VolumeType paramétert annak megadásához, hogy mely lemezek vannak titkosítva.

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
- **Futó virtuális gép titkosítása a KEK használatával az ügyfél titkos kódjának becsomagolásához:** Azure Disk Encryption lehetővé teszi egy meglévő kulcs megadását a kulcstartóban a titkosítás engedélyezésekor létrehozott lemez-titkosítási titkok becsomagolásához. Ha a kulcs titkosítási kulcsa meg van adva, a Azure Disk Encryption ezt a kulcsot használja a titkosítási titkok becsomagolásához a kulcstartóba való írás előtt. Módosítsa a-VolumeType paramétert annak megadásához, hogy mely lemezek vannak titkosítva. 

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
  > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[KVresource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name].</br> </br>
  A kulcs-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-név]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id]. 
    
- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának vizsgálatához használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot. 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Lemez titkosításának letiltása:** A titkosítás letiltásához használja a [disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. Letiltja a titkosítást csak engedélyezett megváltoztatását az adatköteteken Linux rendszerű virtuális gépekhez.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"></a> Titkosítás engedélyezése meglévő vagy futó IaaS Linux rendszerű virtuális gépen sablon alapján

Az Azure-ban a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)használatával engedélyezheti a lemez titkosítását egy meglévő vagy futó IaaS Linux rendszerű virtuális gépen.

1. Az Azure Gyorsindítás sablonjában válassza az **üzembe helyezés az Azure-** ban lehetőséget.

2. Válassza ki az előfizetést, erőforráscsoportot, erőforráscsoport helye, paraméterek, jogi feltételek és szerződés. A **Létrehozás** gombra kattintva engedélyezheti a titkosítást a meglévő vagy futó IaaS virtuális gépen.

Az alábbi táblázat a meglévő vagy egy Azure AD ügyfél-Azonosítót használó virtuális gépek futtatása a Resource Manager-Sablonparaméterek:

| Paraméter | Leírás |
| --- | --- |
| AADClientID | Az Azure AD-alkalmazást, amely a key vault titkos kódok írási engedélyekkel rendelkezik ügyfél-azonosítója. |
| AADClientSecret | Az Azure AD-alkalmazást, amely titkos kulcsok a key vault írási engedélyekkel rendelkezik a titkos ügyfélkódja. |
| keyVaultName | A kulcstartóhoz, amely fel kell tölteni a kulcs neve. Az Azure CLI-parancs `az keyvault show --name "MySecureVault" --query KVresourceGroup`használatával kérheti le. |
|  keyEncryptionKeyURL | A kulcsalapú titkosítás kulcsa, a létrehozott kulcs titkosításához használt URL-címe. Ez a paraméter nem kötelező, ha a **UseExistingKek** legördülő listában a **nokek** lehetőséget választja. Ha a **UseExistingKek** legördülő listában a **KEK** elemet választja, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| VolumeType | A titkosítási műveletet végzi el a kötet típusa. Az érvényes támogatott értékek az _operációs rendszer_ vagy _az összes_. (Lásd a támogatott Linux-disztribúciókat és az operációs rendszer és az adatlemezek verzióját a korábbi előfeltételek szakaszban.) |
| SequenceVersion | A BitLocker művelet verziója. Növelje a verziószámot, minden alkalommal, amikor a lemeztitkosítási műveletet végzi el az azonos virtuális gépen. |
| vmName | Neve a virtuális gép, amely a titkosítási műveletet kell végrehajtani. |
| Hozzáférési kód | Írjon be egy erős jelszót a szolgáltatásadat-titkosítási kulcs. |



## <a name="bkmk_EFA"> </a>A EncryptFormatAll funkció használata a Linux IaaS virtuális gépeken található adatlemezekhez
A EncryptFormatAll paraméter csökkenti a Linux-adatlemezek titkosításának idejét. Az egyes feltételeknek megfelelő partíciók formátuma (a jelenlegi fájlrendszerrel együtt). Ezután újracsatlakoztatják őket a parancs végrehajtása előtt. Ha ki szeretne zárni egy olyan adatlemezt, amely megfelel a feltételeknek, leválaszthatja azt a parancs futtatása előtt.

 A parancs futtatása után a korábban csatlakoztatott meghajtók formázása megtörtént. Ezután a titkosítási réteg a most üres meghajtón indul el. Ha ez a beállítás be van jelölve, a virtuális géphez csatolt ideiglenes erőforrás lemez is titkosítva lesz. Ha az ideiglenes meghajtót alaphelyzetbe állítja, a rendszer a következő lehetőségnél újraformázza és újra titkosítja a virtuális gépet a Azure Disk Encryption megoldással.

>[!WARNING]
> A EncryptFormatAll nem szabad használni, ha a virtuális gép adatkötetein szükség van a szükséges értékekre. A lemezek a titkosításból való leválasztásával zárhatók ki. Először próbálja ki a EncryptFormatAll paramétert egy teszt virtuális gépen, hogy megértse a funkció paraméterét és annak következményeit az éles virtuális gépen való kipróbálás előtt. A EncryptFormatAll beállítás az adatlemezt formázza, így a rajta lévő összes adattal elvész. A folytatás előtt ellenőrizze, hogy a kizárni kívánt lemezek megfelelően le vannak-e választva. </br></br>
 >Ha ezt a paramétert a titkosítási beállítások frissítésekor állítja be, akkor a tényleges titkosítás előtt újraindítást eredményezhet. Ebben az esetben azt is el szeretné távolítani, hogy a lemez ne legyen formázva az fstab-fájlból. Hasonlóképpen, a titkosítási művelet elindítása előtt adja hozzá a titkosítani kívánt partíciót az fstab-fájlhoz. 

### <a name="bkmk_EFACriteria"></a> EncryptFormatAll feltételek
A paraméter az összes partíción halad át, és titkosítja őket, ha az alábbi feltételek *mindegyike* teljesül: 
- Nem legfelső szintű vagy az operációs rendszer és rendszerindító partíció
- Már nem titkosított
- Nem rendelkeznek BEk-KEL kötet
- Nem RAID-kötet
- Az LVM-kötet nem
- Csatlakoztatva van

A RAID vagy LVM kötetet, hanem a RAID vagy LVM kötetet alkotó lemezek titkosítása.

### <a name="bkmk_EFATemplate"></a> A EncryptFormatAll paraméter használata sablonnal
A EncryptFormatAll beállítás használatához használja az összes olyan meglévő Azure Resource Manager sablont, amely titkosítja a linuxos virtuális gépet, és módosítja a AzureDiskEncryption erőforrás **EncryptionOperation** mezőjét.

1. Példaként használja a [Resource Manager-sablont a futó linuxos IAAS virtuális gépek titkosításához](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Az Azure Gyorsindítás sablonjában válassza az **üzembe helyezés az Azure-** ban lehetőséget.
3. Módosítsa a **EncryptionOperation** mezőt a **EnableEncryption** értékről a **EnableEncryptionFormatAl**értékre.
4. Válassza ki az előfizetést, az erőforráscsoport, erőforráscsoport helye, más paramétereket, a jogi feltételeket és az szerződés. A **Létrehozás** gombra kattintva engedélyezheti a titkosítást a meglévő vagy futó IaaS virtuális gépen.


### <a name="bkmk_EFAPSH"></a> A EncryptFormatAll paraméter használata PowerShell-parancsmaggal
Használja a [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmagot a EncryptFormatAll paraméterrel.

**Futó virtuális gép titkosítása az ügyfél titkos és EncryptFormatAll használatával:** Az alábbi szkript például inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot a EncryptFormatAll paraméterrel. Az erőforráscsoport, a virtuális gép, a Key Vault, az Azure AD-alkalmazás és az ügyfél titkos kulcsa már előfeltételként lett létrehozva. Cserélje le a MyKeyVaultResourceGroup, a MyVirtualMachineResourceGroup, a MySecureVM, a MySecureVault, a My-HRE-Client-ID és a My-HRE-Client-Secret értékeket az értékekre.
  
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


### <a name="bkmk_EFALVM"></a> A EncryptFormatAll paraméter használata a logikai kötet-kezelővel (LVM) 
Azt javasoljuk, hogy az LVM-a-crypt beállítása. Az alábbi példákban cserélje le az eszköz-elérési utat és a csatolási az Ön használati esetére. A telepítő a következő elvégezhető:

- Adja hozzá az adatlemezeket a virtuális Gépet alkotó lesz.
- Formázhatja, csatlakoztathatja és ezeket a lemezeket ad hozzá az fstab fájlt.

    1. Formázza az újonnan hozzáadott lemezt. Itt az Azure létrehoz symlinks használjuk. Symlinks használatával elkerülheti a problémákat, az eszköz nevének módosítása. További információ: az [eszközök neveivel kapcsolatos problémák elhárítása](troubleshoot-device-names-problems.md).
    
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




## <a name="bkmk_VHDpre"></a> Az ügyfél által titkosított VHD-és titkosítási kulcsokból létrehozott új IaaS virtuális gépek
Ebben a forgatókönyvben engedélyezheti a Resource Manager-sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával titkosítja. A Resource Manager-sablon és a CLI-parancsokat az alábbi szakaszok részletesebben ismertetik. 

Kövesse az utasításokat a függelékben használható az Azure-ban előzetes titkosítással rendszerképek előkészítéséről. A lemezkép létrehozását követően használhatja a lépéseket a következő szakaszban titkosított Azure virtuális gép létrehozásához.

* [Előre titkosított linuxos virtuális merevlemez előkészítése](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >A Azure Disk Encryption engedélyezése előtt el kell végeznie egy pillanatkép készítését vagy a felügyelt lemezes virtuálisgép-példány biztonsági mentését. Pillanatképet készíthet a felügyelt lemezről a portálról, vagy használhatja a [Azure Backupt](../../backup/backup-azure-vms-encryption.md)is. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. A biztonsági mentés után a set-AzVMDiskEncryptionExtension parancsmag használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. A set-AzVMDiskEncryptionExtension parancs a felügyelt lemezes virtuális gépekről a biztonsági mentésig meghiúsul, és ez a paraméter meg van adva. 
>
>A titkosítás titkosítása vagy letiltása a virtuális gép újraindítását okozhatja.



### <a name="bkmk_VHDprePSH"></a> A IaaS-alapú virtuális gépek titkosítása előre titkosított VHD-k használatával Azure PowerShell 
A titkosított virtuális merevlemez titkosítását a [set-AzVMOSDisk PowerShell-](/powershell/module/az.compute/set-azvmosdisk#examples)parancsmag használatával engedélyezheti. A következő példa néhány gyakori paramétert biztosít. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott adatlemez
Új adatlemezt az [az VM Disk Attach](add-disk.md) vagy [a Azure Portal](attach-disk-portal.md)használatával adhat hozzá. Mielőtt titkosíthatók, először csatlakoztassa az újonnan csatolt adatlemezre kell. Meg kell kérnie az adatmeghajtó titkosítását, mert a meghajtó használhatatlanná válik, amíg a titkosítás folyamatban van. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>A titkosítás engedélyezése az újonnan hozzáadott lemezeken az Azure CLI-vel
 Ha a virtuális gép korábban "all" titkosítással lett titkosítva, akkor a--Volume-Type paraméternek meg kell maradnia. Mind az operációs rendszer és az adatlemezek összes tartalmazza. Ha a virtuális gépet korábban az "operációs rendszer" mennyiségi típusával titkosítták, akkor a--Volume-type paramétert úgy kell módosítani, hogy az operációs rendszer és az új adatlemez is szerepeljen. Ha a virtuális gépet csak az "adatmennyiség" értékkel titkosították, akkor az itt látható módon maradhat az adatforgalomban. Egy új adatlemez egy virtuális géphez való hozzáadása és csatolása nem elegendő a titkosítás előkészítéséhez. A titkosítás engedélyezése előtt az újonnan csatlakoztatott lemeznek is formázva kell lennie, és megfelelően csatlakoztatni kell a virtuális gépen. Linux rendszeren a lemeznek egy [állandó blokk-eszköz nevével](troubleshoot-device-names-problems.md)kell csatlakoztatnia az/etc/fstab-ben. 

A PowerShell-szintaxissal ellentétben a CLI-nek nem kell egyedi sorozatot megadnia a titkosítás engedélyezésekor. A CLI automatikusan hoz létre, és használja a saját egyedi feladatütemezési version értéke.

-  **Futó virtuális gép titkosítása az ügyfél titkos kódjának használatával:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Futó virtuális gép titkosítása a KEK használatával az ügyfél titkos kódjának becsomagolásához:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott lemezt az Azure PowerShell használatával
 Ha a PowerShell használatával titkosít egy új lemezt a Linux rendszerben, meg kell adni egy új sorozatot. A feladatütemezési verziójában található egyedinek kell lennie. A következő szkript létrehoz egy GUID azonosítót a Sequence verzióhoz. 
 

-  **Futó virtuális gép titkosítása az ügyfél titkos kódjának használatával:** A következő parancsfájl inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép, a Key Vault, az Azure AD-alkalmazás és az ügyfél titkos kulcsa már előfeltételként lett létrehozva. Cserélje le a MyVirtualMachineResourceGroup, a MyKeyVaultResourceGroup, a MySecureVM, a MySecureVault, a My-HRE-Client-ID és a My-HRE-Client-Secret értékeket az értékekre. A - VolumeType paraméter értéke az adatlemezeket, és nem az operációsrendszer-lemez. Ha a virtuális gépet korábban "operációs rendszer" vagy "all" típusú kötettel titkosították, akkor a-VolumeType paramétert úgy kell módosítani, hogy az operációs rendszer és az új adatlemez is szerepeljen.

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
- **Futó virtuális gép titkosítása a KEK használatával az ügyfél titkos kódjának becsomagolásához:** Azure Disk Encryption lehetővé teszi egy meglévő kulcs megadását a kulcstartóban a titkosítás engedélyezésekor létrehozott lemez-titkosítási titkok becsomagolásához. Ha a kulcs titkosítási kulcsa meg van adva, a Azure Disk Encryption ezt a kulcsot használja a titkosítási titkok becsomagolásához a kulcstartóba való írás előtt. A - VolumeType paraméter értéke az adatlemezeket, és nem az operációsrendszer-lemez. Ha a virtuális gépet korábban "operációs rendszer" vagy "all" típusú kötettel titkosították, akkor a-VolumeType paramétert úgy kell módosítani, hogy az operációs rendszer és az új adatlemez is szerepeljen.

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
> A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name]. </br> </br>
A kulcs-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-név]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id].

## <a name="disable-encryption-for-linux-vms"></a>Linux rendszerű virtuális gépek titkosításának letiltása
A titkosítást letilthatja Azure PowerShell, az Azure CLI vagy egy Resource Manager-sablon használatával. 

>[!IMPORTANT]
>Letiltja a titkosítást a Linuxos virtuális gépeken az Azure Disk Encryption csak a támogatott adatköteteket. Ha az operációsrendszer-kötet titkosítva van, nem támogatott az adatvagy operációsrendszer-köteteken. 

- **Lemez titkosításának letiltása a Azure PowerShell:** A titkosítás letiltásához használja a [disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Titkosítás letiltása az Azure CLI-vel:** A titkosítás letiltásához használja az az [VM encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **A titkosítás letiltása Resource Manager-sablonnal:** A titkosítás letiltásához használja a [titkosítás letiltása egy futó linuxos](https://aka.ms/decrypt-linuxvm) virtuálisgép-sablonon.
     1. Válassza **az üzembe helyezés az Azure-** ban lehetőséget.
     2. Válassza ki az előfizetés, erőforráscsoport, hely, virtuális gép, jogi feltételeket és szerződés.
     3. Válassza a **vásárlás** lehetőséget a lemez titkosításának letiltásához egy futó WINDOWSOS virtuális gépen. 


## <a name="next-steps"></a>Következő lépések

- [A Linux Azure Disk Encryption áttekintése](disk-encryption-overview-aad.md)
- [Kulcstartó létrehozása és konfigurálása az Azure AD-vel való Azure Disk Encryptionhoz (előző kiadás)](disk-encryption-key-vault-aad.md)

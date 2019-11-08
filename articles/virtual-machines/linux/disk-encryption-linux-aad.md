---
title: Azure Disk Encryption Azure AD alkalmazás Linux IaaS virtuális gépekkel (korábbi kiadás)
description: Ez a cikk a Linux IaaS virtuális gépek Microsoft Azure lemezes titkosításának engedélyezéséhez nyújt útmutatást.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 35d14599ca9a7abdad88603deeb8431f77e92a92
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748944"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Azure Disk Encryption engedélyezése az Azure AD-vel Linux rendszerű virtuális gépeken (korábbi kiadás)

**A Azure Disk Encryption új kiadása szükségtelenné teszi az Azure AD-alkalmazás paramétereinek biztosítását a virtuális gépek lemezes titkosításának engedélyezéséhez. Az új kiadással már nem szükséges az Azure AD-beli hitelesítő adatok megadása a titkosítás engedélyezése lépés során. Az új kiadás használatával az új virtuális gépeket az Azure AD-alkalmazás paramétereinek használata nélkül kell titkosítani. A VM Disk Encryption új kiadással való engedélyezésével kapcsolatos utasítások megtekintéséhez lásd: [Azure Disk Encryption Linux rendszerű virtuális gépekhez](disk-encryption-linux.md). Azok a virtuális gépek, amelyek már az Azure AD-alkalmazás paramétereivel lettek titkosítva, továbbra is támogatottak, és továbbra is fenn kell tartaniuk a HRE szintaxisát**

Számos lemezes titkosítási forgatókönyvet engedélyezhet, és a lépések a forgatókönyvtől függően változhatnak. A következő részek a Linux IaaS virtuális gépekkel kapcsolatos részletesebb forgatókönyveket ismertetik. A lemezes titkosítást csak a [támogatott virtuálisgép-méretek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems)virtuális gépei esetében lehet alkalmazni. A következő előfeltételeknek is teljesülniük kell:

- [A virtuális gépekre vonatkozó további követelmények](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Hálózatkezelés és Csoportházirend](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Titkosítási kulcs tárolási követelményei](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Készítsen [pillanatképet](snapshot-copy-managed-disk.md) és/vagy készítsen biztonsági mentést a lemezek titkosítása előtt. A biztonsági másolatok gondoskodnak arról, hogy a rendszer egy helyreállítási lehetőséget akkor lehessen, ha nem várt hiba történik a titkosítás során. A felügyelt lemezekkel rendelkező virtuális gépek biztonsági mentést igényelnek a titkosítás megkezdése előtt. A biztonsági mentés után a set-AzVMDiskEncryptionExtension parancsmag használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. További információ a titkosított virtuális gépek biztonsági mentéséről és visszaállításáról: [Azure Backup](../../backup/backup-azure-vms-encryption.md) . 

>[!WARNING]
 > - Ha korábban már használta [Azure Disk Encryption az Azure ad-alkalmazással](disk-encryption-overview-aad.md) a virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Ezen a titkosított virtuális gépen nem használható [Azure Disk Encryption](disk-encryption-overview.md) , mivel ez nem támogatott forgatókönyv, ami azt jelenti, hogy a titkosított virtuális gép esetében a HRE alkalmazásból való átállás még nem támogatott.
 > - Annak érdekében, hogy a titkosítási titkok ne haladják meg a regionális határokat, Azure Disk Encryption szüksége van a Key Vaultra, és a virtuális gépeket ugyanabban a régióban kell elhelyezni. Hozzon létre és használjon olyan Key Vault, amely ugyanabban a régióban található, mint a titkosítani kívánt virtuális gép.
 > - A Linux operációs rendszer köteteinek titkosításakor a folyamat néhány órát is igénybe vehet. A Linux RENDSZERű kötetek esetében normális, hogy a titkosításhoz használt adatmennyiségek hosszabb ideig tartanak.
> - A Linux operációs rendszer köteteinek titkosításakor a virtuális gépet nem szabad megfontolni. Határozottan javasoljuk, hogy az SSH-bejelentkezések elkerülése érdekében a titkosítási folyamat során ne akadályozza meg az összes olyan megnyitott fájl blokkolását, amelyet a titkosítási folyamat során el kell érnie. A folyamat ellenőrzéséhez használhatja a [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) vagy a [VM encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsait. Ez a folyamat várhatóan néhány órát is igénybe vehet egy 30 GB-OS kötetre, és további időt az adatkötetek titkosítására. Az adatmennyiség-titkosítási idő arányos lesz az adatkötetek méretétől és mennyiségétől, kivéve, ha az összes titkosítás formátuma beállítást használja. 
 > - A Linux rendszerű virtuális gépek titkosításának letiltása csak az adatkötetek esetében támogatott. Ha az operációsrendszer-kötet titkosítva van, a rendszer nem támogatja az adatvagy operációsrendszer-köteteken.  

 

## <a name="bkmk_RunningLinux"></a> Titkosítás engedélyezése meglévő vagy futó IaaS Linux rendszerű virtuális gépen

Ebben az esetben a titkosítást a Resource Manager-sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával engedélyezheti. 

>[!IMPORTANT]
 >A felügyelt lemezes virtuálisgép-példányok pillanatképe és/vagy biztonsági mentése kötelező, a Azure Disk Encryption engedélyezése előtt. A felügyelt lemezről pillanatképet készíthet a portálról, vagy [Azure Backup](../../backup/backup-azure-vms-encryption.md) használható. A biztonsági mentések biztosítják, hogy a titkosítás során fellépő váratlan hibák esetén a helyreállítási lehetőség is lehetséges legyen. A biztonsági mentést követően a set-AzVMDiskEncryptionExtension parancsmag használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. A set-AzVMDiskEncryptionExtension parancs a felügyelt lemezes virtuális gépektől a biztonsági mentésig meghiúsul, és ez a paraméter meg lett adva. 
>
>A titkosítás titkosítása vagy letiltása miatt előfordulhat, hogy a virtuális gép újraindul. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen az Azure CLI használatával 
Az [Azure CLI 2,0](/cli/azure) parancssori eszköz telepítésével és használatával engedélyezheti a lemez titkosítását a titkosított virtuális merevlemezen. Használhatja a böngészőjében az [Azure Cloud Shell-lel](../../cloud-shell/overview.md), vagy telepítheti a helyi gépen, és használhatja bármely PowerShell-munkamenetben. Az Azure-ban meglévő vagy futó IaaS Linux rendszerű virtuális gépek titkosításának engedélyezéséhez használja a következő CLI-parancsokat:

Az az [VM encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) paranccsal engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban.

-  **Egy futó virtuális gép titkosítása az ügyfél titkos kódjával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Egy futó virtuális gép titkosítása a KEK használatával az ügyfél titkos kódjának becsomagolásához:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name]</br> </br>
A Key-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-name]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának megtekintéséhez használja az az [VM encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Titkosítás letiltása:** A titkosítás letiltásához használja az az [VM encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. A titkosítás letiltása csak a Linux rendszerű virtuális gépek adatkötetein engedélyezett.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"></a> Titkosítás engedélyezése meglévő vagy futó Linux rendszerű virtuális gépen a PowerShell használatával
A [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban. Készítsen [pillanatképet](snapshot-copy-managed-disk.md) és/vagy készítsen biztonsági másolatot a virtuális gépről [Azure Backup](../../backup/backup-azure-vms-encryption.md) a lemezek titkosítása előtt. A-skipVmBackup paraméter már meg van adva a PowerShell-parancsfájlokban egy futó Linux rendszerű virtuális gép titkosításához.

- **Egy futó virtuális gép titkosítása az ügyfél titkos** kódjával: Az alábbi szkript inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép, a Key Vault, a HRE alkalmazás és az ügyfél titkos kulcsa már előfeltételként lett létrehozva. Cserélje le a MyVirtualMachineResourceGroup, a MyKeyVaultResourceGroup, a MySecureVM, a MySecureVault, a My-HRE-Client-ID és a My-HRE-Client-Secret értékeket az értékekre. Módosítsa a-VolumeType paramétert annak megadásához, hogy mely lemezek vannak titkosítva.

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
- **Egy futó virtuális gép titkosítása a KEK használatával az ügyfél titkos kódjának becsomagolásához:** Azure Disk Encryption lehetővé teszi egy meglévő kulcs megadását a kulcstartóban a titkosítás engedélyezésekor létrehozott lemez-titkosítási titkok becsomagolásához. Ha a kulcs titkosítási kulcsa meg van adva, Azure Disk Encryption ezt a kulcsot használja a titkosítási titok becsomagolásához a Key Vaultba való írás előtt. Módosítsa a-VolumeType paramétert annak megadásához, hogy mely lemezek vannak titkosítva. 

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
  A Key-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-name]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id] 
    
- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának vizsgálatához használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Lemez titkosításának letiltása:** A titkosítás letiltásához használja a [disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. A titkosítás letiltása csak a Linux rendszerű virtuális gépek adatkötetein engedélyezett.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"></a> Titkosítás engedélyezése meglévő vagy futó IaaS Linux rendszerű virtuális gépen sablon alapján

Az Azure-ban a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)használatával engedélyezheti a lemez titkosítását egy meglévő vagy futó IaaS Linux rendszerű virtuális gépen.

1. Az Azure rövid útmutató sablonjában kattintson az **üzembe helyezés az Azure-** ba lehetőségre.

2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, a paramétereket, a jogi feltételeket és a szerződést. A **Létrehozás** gombra kattintva engedélyezheti a titkosítást a meglévő vagy futó IaaS virtuális gépen.

Az alábbi táblázat az Azure AD ügyfél-azonosítót használó meglévő vagy futó virtuális gépek Resource Manager-sablonjának paramétereit sorolja fel:

| Paraméter | Leírás |
| --- | --- |
| AADClientID | Az Azure AD-alkalmazás ügyfél-azonosítója, amely jogosult a Key vaultba írni a titkos kulcsokat. |
| AADClientSecret | Az Azure AD-alkalmazás azon titkos kulcsa, amely a Key vaultba való titkos kódok írásához szükséges engedélyekkel rendelkezik. |
| keyVaultName | Annak a kulcstárolónak a neve, amelyre a kulcsot fel kell tölteni. Az Azure CLI-parancs `az keyvault show --name "MySecureVault" --query KVresourceGroup`használatával kérheti le. |
|  keyEncryptionKeyURL | A generált kulcs titkosításához használt kulcs titkosítási kulcsának URL-címe. Ez a paraméter nem kötelező, ha a UseExistingKek legördülő listában a **nokek** lehetőséget választja. Ha a UseExistingKek legördülő listában a **KEK** elemet választja, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| volumeType | A titkosítási művelet végrehajtásához használt kötet típusa. Az érvényes támogatott értékek az _operációs rendszer_ vagy _az összes_ (lásd a támogatott Linux-DISZTRIBÚCIÓKAT és az operációs rendszer és az adatlemezek verzióit a korábbi előfeltételek szakaszban). |
| sequenceVersion | A BitLocker-művelet szekvenciális verziója. Minden alkalommal növelje ezt a verziószámot, amikor egy lemezes titkosítási műveletet hajt végre ugyanazon a virtuális gépen. |
| vmName | Annak a virtuális gépnek a neve, amelyen a titkosítási műveletet végre szeretné állítani. |
| Hozzáférési kód | Adjon meg egy erős jelszót az adattitkosítási kulcsként. |



## <a name="bkmk_EFA"> </a>A EncryptFormatAll funkció használata a Linux IaaS virtuális gépeken található adatlemezekhez
A **EncryptFormatAll** paraméter csökkenti a Linux-adatlemezek titkosításának idejét. A megadott feltételeknek megfelelő partíciók (az aktuális fájlrendszerrel) lesznek formázva. Ezután újra kell csatlakoztatni őket a parancs végrehajtása előtt. Ha ki szeretne zárni egy olyan adatlemezt, amely megfelel a feltételeknek, leválaszthatja azt a parancs futtatása előtt.

 A parancs futtatása után a korábban csatlakoztatott meghajtók formázása megtörténik. Ezután a titkosítási réteg a most üres meghajtón lesz elindítva. Ha ez a beállítás be van jelölve, a virtuális géphez csatolt ideiglenes erőforrás lemez is titkosítva lesz. Ha az ideiglenes meghajtót alaphelyzetbe állítja, a rendszer a következő lehetőségnél újraformázza és újra titkosítja a virtuális gépet a Azure Disk Encryption megoldással.

>[!WARNING]
> A EncryptFormatAll nem használható, ha a virtuális gép adatkötetein szükség van a szükséges értékekre. A lemezek a titkosításból való leválasztásával zárhatók ki. Először próbálja ki először a EncryptFormatAll a tesztelési virtuális gépen, és tekintse át a funkció paraméterét és annak következményeit az éles virtuális gépen való kipróbálás előtt. A EncryptFormatAll beállítás az adatlemezt formázza, és a rajta lévő összes adattal elvész. A továbblépés előtt ellenőrizze, hogy a kizárni kívánt lemezek megfelelően le vannak-e választva. </br></br>
 >Ha ezt a paramétert a titkosítási beállítások frissítésekor állítja be, akkor a tényleges titkosítás előtt újraindítást eredményezhet. Ebben az esetben el kell távolítania azt a lemezt is, amelyet nem kíván formázni az fstab-fájlból. Hasonlóképpen, a titkosítási művelet megkezdése előtt adja hozzá a titkosítani kívánt partíciót az fstab-fájlhoz. 

### <a name="bkmk_EFACriteria"></a> EncryptFormatAll feltételek
A (z) paraméter minden partíciót megnyit, és titkosítja őket, ha az alábbi feltételek **mindegyike** teljesül: 
- Nem root/OS/rendszerindító partíció
- Még nincs titkosítva
- Nem egy BEK-kötet
- Nem RAID-kötet
- Nem LVM kötet
- Csatlakoztatva van

Titkosítsa a RAID vagy az LVM kötetet alkotó lemezeket a RAID vagy az LVM kötet helyett.

### <a name="bkmk_EFATemplate"></a> A EncryptFormatAll paraméter használata sablonnal
A EncryptFormatAll beállítás használatához használja a Linux rendszerű virtuális gépet titkosító, korábban létező Azure Resource Manager-sablont, és módosítsa a AzureDiskEncryption erőforrás **EncryptionOperation** mezőjét.

1. Példaként használja a [Resource Manager-sablont a futó linuxos IAAS virtuális gépek titkosításához](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Az Azure rövid útmutató sablonjában kattintson az **üzembe helyezés az Azure-** ba lehetőségre.
3. A **EncryptionOperation** módosítása a **EnableEncryption** és a **EnableEncryptionFormatAl** között
4. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, az egyéb paramétereket, a jogi feltételeket és a szerződést. A **Létrehozás** gombra kattintva engedélyezheti a titkosítást a meglévő vagy futó IaaS virtuális gépen.


### <a name="bkmk_EFAPSH"></a> A EncryptFormatAll paraméter használata PowerShell-parancsmaggal
Használja a [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmagot a `EncryptFormatAll` paraméterrel.

**Futó virtuális gép titkosítása az ügyfél titkos és EncryptFormatAll használatával:** Az alábbi szkript például inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot a EncryptFormatAll paraméterrel. Az erőforráscsoport, a virtuális gép, a Key Vault, a HRE alkalmazás és az ügyfél titkos kulcsa már előfeltételként lett létrehozva. Cserélje le a MyKeyVaultResourceGroup, a MyVirtualMachineResourceGroup, a MySecureVM, a MySecureVault, a My-HRE-Client-ID és a My-HRE-Client-Secret értékeket az értékekre.
  
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
Javasoljuk, hogy legyen egy LVM-on-Crypt beállítás. Az alábbi példákban cserélje le az eszköz-elérési utat és a csatolási a használati esetre. Ezt a telepítést a következőképpen teheti meg:

- Adja hozzá a virtuális gépet alkotó adatlemezeket.
- Formázza, csatlakoztassa és adja hozzá ezeket a lemezeket az fstab-fájlhoz.

    1. Formázza az újonnan hozzáadott lemezt. Az Azure által generált symlink-ket itt fogjuk használni. A symlink-EK használata elkerüli az eszközök nevének változásával kapcsolatos problémákat. További információt az [eszközök neveivel kapcsolatos problémák elhárítása](troubleshoot-device-names-problems.md) című cikkben talál.
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Csatlakoztassa a lemezeket.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Hozzáadás az fstab-hoz.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Futtassa a set-AzVMDiskEncryptionExtension PowerShell-parancsmagot a-EncryptFormatAll használatával a lemezek titkosításához.
         ```azurepowershell-interactive
         Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Az LVM beállítása az új lemezek felett. Figyelje meg, hogy a titkosított meghajtók zárolása a virtuális gép elindítása után megtörtént. Ezért az LVM csatlakoztatását is el kell halasztani.




## <a name="bkmk_VHDpre"></a> Az ügyfél által titkosított VHD-és titkosítási kulcsokból létrehozott új IaaS virtuális gépek
Ebben a forgatókönyvben engedélyezheti a titkosítást a Resource Manager-sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával. A következő részek részletesebben ismertetik a Resource Manager-sablon és a CLI-parancsok részleteit. 

Az Azure-ban használható, előre titkosított rendszerképek előkészítéséhez használja a függelék utasításait. A rendszerkép létrehozása után a következő szakasz lépéseit követve hozzon létre egy titkosított Azure-beli virtuális gépet.

* [Előre titkosított linuxos virtuális merevlemez előkészítése](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >A felügyelt lemezes virtuálisgép-példányok pillanatképe és/vagy biztonsági mentése kötelező, a Azure Disk Encryption engedélyezése előtt. A felügyelt lemezről pillanatképet készíthet a portálról, vagy [Azure Backup](../../backup/backup-azure-vms-encryption.md) használható. A biztonsági mentések biztosítják, hogy a titkosítás során fellépő váratlan hibák esetén a helyreállítási lehetőség is lehetséges legyen. A biztonsági mentést követően a set-AzVMDiskEncryptionExtension parancsmag használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. A set-AzVMDiskEncryptionExtension parancs a felügyelt lemezes virtuális gépektől a biztonsági mentésig meghiúsul, és ez a paraméter meg lett adva. 
>
>A titkosítás titkosítása vagy letiltása miatt előfordulhat, hogy a virtuális gép újraindul. 



### <a name="bkmk_VHDprePSH"></a> A IaaS-alapú virtuális gépek titkosítása előre titkosított VHD-k használatával Azure PowerShell 
A titkosított virtuális merevlemez titkosítását a [set-AzVMOSDisk PowerShell-](/powershell/module/az.compute/set-azvmosdisk#examples)parancsmag használatával engedélyezheti. Az alábbi példa néhány gyakori paramétert biztosít. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Titkosítás engedélyezése újonnan hozzáadott adatlemezen
Új adatlemezt az [az VM Disk Attach](add-disk.md)vagy [a Azure Portal](attach-disk-portal.md)használatával adhat hozzá. A titkosítás előtt először csatlakoztatnia kell az újonnan csatlakoztatott adatlemezt. Meg kell kérnie az adatmeghajtó titkosítását, mivel a meghajtó használhatatlanná válik, amíg a titkosítás folyamatban van. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>A titkosítás engedélyezése az újonnan hozzáadott lemezeken az Azure CLI-vel
 Ha a virtuális gép korábban az "all" titkosítással lett titkosítva, akkor a--Volume-Type paraméternek meg kell maradnia. Mind az operációs rendszer és az adatlemezek is szerepelnek. Ha a virtuális gépet korábban "operációs rendszer" típusú kötettel titkosítták, akkor a--Volume-type paramétert úgy kell módosítani, hogy az operációs rendszer és az új adatlemez is szerepeljen. Ha a virtuális gépet csak az "adatmennyiség" mennyiségi típussal titkosították, akkor az alább látható módon "az" adatforgalom maradhat. Az új adatlemezek virtuális géphez való hozzáadása és csatolása nem elegendő a titkosítás előkészítéséhez. Az újonnan csatlakoztatott lemeznek a titkosítás engedélyezése előtt a virtuális gépen is formázottnak és megfelelően kell csatlakoztatnia. Linux rendszeren a lemezt egy [állandó blokk-eszköz nevével](troubleshoot-device-names-problems.md)kell csatlakoztatni az/etc/fstab-ben.  

A PowerShell-szintaxissal ellentétben a parancssori felület nem igényli, hogy a felhasználó egyedi sorozatot adjon meg a titkosítás engedélyezésekor. A CLI automatikusan generálja és felhasználja a saját egyedi sorszámának értékét.

-  **Egy futó virtuális gép titkosítása az ügyfél titkos kódjával:** 

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Egy futó virtuális gép titkosítása a KEK használatával az ügyfél titkos kódjának becsomagolásához:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>A titkosítás engedélyezése egy újonnan hozzáadott lemezen Azure PowerShell
 Ha a PowerShell használatával titkosít egy új lemezt a Linux rendszerhez, meg kell adni egy új sorozatot. A Sequence verziójának egyedinek kell lennie. Az alábbi szkript létrehoz egy GUID azonosítót a Sequence verzióhoz. 
 

-  **Egy futó virtuális gép titkosítása az ügyfél titkos** kódjával: Az alábbi szkript inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép, a Key Vault, a HRE alkalmazás és az ügyfél titkos kulcsa már előfeltételként lett létrehozva. Cserélje le a MyVirtualMachineResourceGroup, a MyKeyVaultResourceGroup, a MySecureVM, a MySecureVault, a My-HRE-Client-ID és a My-HRE-Client-Secret értékeket az értékekre. A-VolumeType paraméter adatlemezekre van beállítva, nem az operációsrendszer-lemezre. Ha a virtuális gépet korábban "operációs rendszer" vagy "all" típusú kötettel titkosítták, akkor a-VolumeType paramétert úgy kell módosítani, hogy az operációs rendszer és az új adatlemez is szerepeljen.

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
- **Egy futó virtuális gép titkosítása a KEK használatával az ügyfél titkos kódjának becsomagolásához:** Azure Disk Encryption lehetővé teszi egy meglévő kulcs megadását a kulcstartóban a titkosítás engedélyezésekor létrehozott lemez-titkosítási titkok becsomagolásához. Ha a kulcs titkosítási kulcsa meg van adva, Azure Disk Encryption ezt a kulcsot használja a titkosítási titok becsomagolásához a Key Vaultba való írás előtt. A-VolumeType paraméter adatlemezekre van beállítva, nem az operációsrendszer-lemezre. Ha a virtuális gépet korábban "operációs rendszer" vagy "all" típusú kötettel titkosítták, akkor a-VolumeType paramétert úgy kell módosítani, hogy az operációs rendszer és az új adatlemez is szerepeljen.

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
> A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name] </br> </br>
A Key-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-name]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id] 

## <a name="disable-encryption-for-linux-vms"></a>Linux rendszerű virtuális gépek titkosításának letiltása
A titkosítást a Azure PowerShell, az Azure CLI vagy egy Resource Manager-sablonnal is letilthatja. 

>[!IMPORTANT]
>A Azure Disk Encryption Linux rendszerű virtuális gépeken való titkosításának letiltása csak az adatkötetek esetében támogatott. Ha az operációsrendszer-kötet titkosítva van, a rendszer nem támogatja az adatvagy operációsrendszer-köteteken.  

- **Lemez titkosításának letiltása a Azure PowerShell:** A titkosítás letiltásához használja a [disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Titkosítás letiltása az Azure CLI-vel:** A titkosítás letiltásához használja az az [VM encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **A titkosítás letiltása Resource Manager-sablonnal:** A titkosítás letiltásához használja a [Linux rendszerű virtuális gép titkosításának letiltására](https://aka.ms/decrypt-linuxvm) szolgáló sablont.
     1. Kattintson a **Deploy to Azure** (Üzembe helyezés az Azure-ban) elemre.
     2. Válassza ki az előfizetést, az erőforráscsoportot, a helyet, a virtuális gépet, a jogi feltételeket és a szerződést.
     3.  Kattintson a **vásárlás** gombra a lemez titkosításának letiltásához egy futó WINDOWSOS virtuális gépen. 


## <a name="next-steps"></a>További lépések

- [A Linux Azure Disk Encryption áttekintése](disk-encryption-overview-aad.md)
- [Kulcstartó létrehozása és konfigurálása az Azure AD-vel való Azure Disk Encryptionhoz (előző kiadás)](disk-encryption-key-vault-aad.md)

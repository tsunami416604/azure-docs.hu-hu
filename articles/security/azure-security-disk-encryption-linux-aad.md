---
title: Az Azure AD-alkalmazás Linux rendszerű IaaS virtuális gépek (előző kiadás) az Azure Disk Encryption |} A Microsoft Docs
description: Ez a cikk útmutatást nyújt a Microsoft Azure Disk Encryption engedélyezése a Linux rendszerű IaaS virtuális gépekhez.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 09/19/2018
ms.openlocfilehash: d544d131aed8dc6560f09b7a293e950c38380fb8
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623418"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms-previous-release"></a>Az Azure Disk Encryption engedélyezése a Linux rendszerű IaaS virtuális gépekhez (előző kiadás)

**Az Azure Disk Encryption új kiadása így nem egy Azure AD-alkalmazás paramétert, amelyek biztosítják a virtuális gép lemez-titkosítás engedélyezéséhez. Az új kiadással, már nem kell az Azure AD hitelesítő adatok titkosítás engedélyezése lépés során. Valamennyi új virtuális gépeket titkosítani kell az Azure AD alkalmazás paramétereit az új kiadás használata nélkül. Ahhoz, hogy az új kiadással VM lemeztitkosítás utasítások megtekintése: [Linux rendszerű virtuális gépekhez az Azure Disk Encryption](azure-security-disk-encryption-linux.md). Az Azure AD-alkalmazás paraméterekkel már titkosított virtuális gépek továbbra is támogatottak, és továbbra is fenn kell tartani az AAD-szintaxissal.**

Engedélyezheti a lemeztitkosítási számos forgatókönyv, és a lépések eltérőek lehetnek a forgatókönyv szerint. Az alábbi szakaszok részletesebben a forgatókönyvek Linux rendszerű IaaS virtuális gépek terjed ki. Lemeztitkosítás, használata előtt a [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites-aad.md) kell elvégezni, és a [Linux rendszerű IaaS virtuális gépek további Előfeltételek](azure-security-disk-encryption-prerequisites-aad.md#bkmk_LinuxPrereq) szakasz át kell tekinteni.

Igénybe vehet egy [pillanatkép](../virtual-machines/windows/snapshot-copy-managed-disk.md) és/vagy lemezek előtt készítsen biztonsági másolatot. Biztonsági mentések ellenőrizze, hogy a helyreállítási beállítások titkosítás során váratlan hiba esetén lehetséges. A felügyelt lemezekkel rendelkező virtuális gépek biztonsági szükséges, a titkosítás előtt. Miután biztonsági másolatból történik, a Set-azurermvmdiskencryptionextension parancs parancsmag segítségével felügyelt lemezek titkosítása a - skipVmBackup paraméter megadásával. Biztonsági mentése és visszaállítása titkosított virtuális gépek kapcsolatos további információkért lásd: a [Azure Backup](../backup/backup-azure-vms-encryption.md) cikk. 

>[!WARNING]
 >Annak érdekében, hogy a titkosítási titkos kulcsok nem adatbázisközi regionális határokat, az Azure Disk Encryption a Key Vault és a virtuális gépek közös elhelyezése ugyanabban a régióban van szüksége. Létrehozhat és használhat egy Key Vaultot, amely ugyanabban a régióban, mint a virtuális gép titkosítását.</br></br>

> Linux operációs rendszer kötet titkosításakor az a folyamat néhány órát is igénybe vehet. Előfordulhat, hogy tovább tart, mint az adatkötetek titkosításához Linux operációsrendszer-kötetek. 

>Linux rendszerű virtuális gépek titkosításának letiltása csak az adatkötetek esetében támogatott. Nem támogatott a adatok vagy operációsrendszer-kötettel, ha az operációsrendszer-kötet titkosított.  


## <a name="bkmk_NewLinux"></a> Új Linux IaaS virtuális gép üzembe helyezése a lemeztitkosítás engedélyezve 

1. Használja a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel) hozzon létre egy új Linux rendszerű IaaS virtuális gép titkosított. A sablon létrehoz egy új RedHat Linux 7.2 rendszerű virtuális Gépet és a egy 200 GB-os RAID-0 tömb és a teljes lemeztitkosítás, felügyelt lemezeket használ. Az a [– gyakori kérdések](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) a cikkben azt láthatja, hogy egyes Linux-disztribúciókon csak titkosítás támogatása az adatlemezeket. Azonban ez a sablon lehetővé teszi a sablonok üzembe helyezése és a titkosítás állapotának ellenőrzése a különböző módszerek megismerése. 
 
1. Kattintson a **üzembe helyezés az Azure** az Azure Resource Manager-sablon.

2. Válassza ki az előfizetést, erőforráscsoportot, erőforráscsoport helye, paraméterek, jogi feltételek és szerződés. Kattintson a **létrehozás** engedélyezheti a titkosítást a meglévő vagy futó IaaS virtuális gépen.

3. Miután telepíti a sablont, ellenőrizze a virtuális gép titkosítási állapotát a kívánt módszer használatával:
     - Ellenőrizze az Azure CLI-vel a [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
         ```

     - Az Azure PowerShell használatával ellenőrizze a [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) parancsmagot. 

         ```azurepowershell-interactive
         Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
         ```

     - Válassza ki a virtuális Gépet, majd kattintson a **lemezek** alatt a **beállítások** fejléc encryption státusz a portálon. A diagram alatt **titkosítási**, látni fogja, hogy annak engedélyezve van-e. 

| Paraméter | Leírás |
| --- | --- |
| AAD ügyfél-azonosító | Az Azure AD-alkalmazást, amely a key vault titkos kódok írási engedélyekkel rendelkezik ügyfél-azonosítója. |
| AAD-Ügyfélkulcs | Az Azure AD-alkalmazást, amely titkos kulcsok a key vault írási engedélyekkel rendelkezik a titkos ügyfélkódja. |
| Kulcstartó neve | A key vaultban kell elhelyezni a kulcs neve. |
| Key Vault-erőforráscsoport | A kulcstároló erőforráscsoport. |


## <a name="bkmk_RunningLinux"> </a> Engedélyezze a titkosítást egy meglévő vagy futó IaaS Linux rendszerű virtuális gépen
Ebben a forgatókönyvben a Resource Manager-sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával engedélyezheti a titkosítást. 

>[!IMPORTANT]
 >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt. A felügyelt lemez pillanatképét elvégezhet a portálról, vagy [Azure Backup](../backup/backup-azure-vms-encryption.md) is használható. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. Miután biztonsági másolatból történik, a Set-azurermvmdiskencryptionextension parancs parancsmag segítségével felügyelt lemezek titkosítása a - skipVmBackup paraméter megadásával. A Set-azurermvmdiskencryptionextension parancs parancs felügyelt lemezes virtuális gépek elleni sikertelen lesz, amíg a biztonsági másolat lett végrehajtva, és ez a paraméter lett megadva. 
>
>Titkosítása vagy letiltja a titkosítást a virtuális gép újraindítását okozhatja. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Engedélyezze a titkosítást egy meglévő vagy futó Linux rendszerű virtuális gépen az Azure CLI használatával 
Engedélyezheti lemeztitkosítás a titkosított virtuális merevlemezen történő telepítéssel és a [Azure CLI 2.0](/cli/azure) parancssori eszköz. Használhatja a böngészőjében az [Azure Cloud Shell-lel](../cloud-shell/overview.md), vagy telepítheti a helyi gépen, és használhatja bármely PowerShell-munkamenetben. Meglévő, vagy Azure-ban futó IaaS Linux rendszerű virtuális gépek titkosításának engedélyezéséhez használja a következő CLI-parancsokat:

Használja a [az vm encryption engedélyezése](/cli/azure/vm/encryption#az-vm-encryption-enable) parancsot az Azure-ban futó IaaS virtuális gép titkosításának engedélyezéséhez.

-  **Ügyfél titkos kulcs használatával futó virtuális gépek titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **A titkos ügyfélkulcsot burkolása KEK használatával futó virtuális gépek titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek:** IaaS virtuális gép titkosítási állapotának ellenőrzéséhez használja a [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Tiltsa le a titkosítást:** titkosítás letiltásához használja a [letiltása az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. Letiltja a titkosítást csak engedélyezett megváltoztatását az adatköteteken Linux rendszerű virtuális gépekhez.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a> Engedélyezheti a titkosítást egy meglévő vagy futó Linux rendszerű virtuális gép PowerShell-lel
Használja a [Set-azurermvmdiskencryptionextension parancs](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) parancsmag, amellyel engedélyezheti a titkosítást az Azure-ban futó IaaS virtuális gép. 

-  **Ügyfél titkos kulcs használatával futó virtuális gép titkosítása:** az alábbi parancsfájlt inicializálja a változók és a Set-azurermvmdiskencryptionextension parancs parancsmagot futtatja. Az erőforráscsoport, virtuális gép, a key vault, AAD-alkalmazás és titkos Ügyfélkód kell rendelkezik már létre van hozva előfeltételeket. MySecureRg, MySecureVM, MySecureVault My-AAD-client-ID és a saját – AAD-client-secret cserélje le az értékeket. Szükség lehet a – VolumeType paraméter hozzáadása, ha épp titkosít, adatlemez, és nem az operációsrendszer-lemez. 

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **A titkos ügyfélkulcsot burkolása KEK használatával futó virtuális gép titkosítása:** az Azure Disk Encryption lehetővé teszi, hogy Ön adja meg a meglévő kulcsot tárol a kulcstárolóban burkolása lemez titkosítási titkos kódok titkosításának engedélyezése során létrehozott. Amikor egy kulcsalapú titkosítás kulcsa van megadva, az Azure Disk Encryption a kulcs segítségével burkolhatja a titkosítási titkos kulcsait a Key Vault írása előtt. Szükség lehet a – VolumeType paraméter hozzáadása, ha épp titkosít, adatlemez, és nem az operációsrendszer-lemez. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

 >[!NOTE]
 > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Ellenőrizze, hogy a lemezek:** IaaS virtuális gép titkosítási állapotának ellenőrzéséhez használja a [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) parancsmagot. 
    
     ```azurepowershell-interactive 
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName MySecureRg -VMName MySecureVM
     ```
    
- **Tiltsa le a lemeztitkosítás:** letiltja a titkosítást, használja a [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) parancsmagot. Letiltja a titkosítást csak engedélyezett megváltoztatását az adatköteteken Linux rendszerű virtuális gépekhez.
     
     ```azurepowershell-interactive 
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
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
| keyVaultName | A kulcstartóhoz, amely fel kell tölteni a kulcs neve. Az Azure CLI-parancs használatával beszerezheti azt `az keyvault show --name "MySecureVault" --query resourceGroup`. |
|  keyEncryptionKeyURL | A kulcsalapú titkosítás kulcsa, a létrehozott kulcs titkosításához használt URL-címe. Ez a paraméter nem kötelező, ha kiválasztja **nokek** a UseExistingKek legördülő listában. Ha **kek** a UseExistingKek legördülő listában, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| VolumeType | A titkosítási műveletet végzi el a kötet típusa. Érvényes támogatott értékei a következők _operációs rendszer_ vagy _összes_ (lásd a támogatott Linux-disztribúciók és azok verzióinak az Előfeltételek szakaszban korábban operációsrendszer- és adatlemezek). |
| SequenceVersion | A BitLocker művelet verziója. Növelje a verziószámot, minden alkalommal, amikor a lemeztitkosítási műveletet végzi el az azonos virtuális gépen. |
| vmName | Neve a virtuális gép, amely a titkosítási műveletet kell végrehajtani. |
| Hozzáférési kód | Írjon be egy erős jelszót a szolgáltatásadat-titkosítási kulcs. |



## <a name="bkmk_EFA"> </a>A Linux rendszerű IaaS virtuális gépek adatlemezét EncryptFormatAll szolgáltatással
A **EncryptFormatAll** paraméter lerövidíti a Linux data lemezek titkosítását. Partíciók megfelelnek bizonyos feltételeknek lesz formázva (az aktuális fájlrendszer). Majd azok lesz újra kell csatlakoztatni vissza a hol történt a parancs végrehajtása előtt. Ha kizár egy olyan adatok lemezt, amely megfelel a feltételeknek, válassza le azt a parancs futtatása előtt.

 Ez a parancs futtatása után, amely csatlakoztatva voltak meghajtóknak korábban lesz formázva. Ezután a titkosítási réteget a már üres meghajtó felett indul. Ha ezt a lehetőséget választja, a rövid élettartamú erőforrás lemezt a virtuális Géphez csatolt is titkosítva lesznek. A rövid élettartamú meghajtó alaphelyzetbe áll, ha újraformázta és újra titkosítja a virtuális gép az Azure Disk Encryption megoldás a következő alkalommal.

>[!WARNING]
> EncryptFormatAll nem használható, ha egy virtuális Gépet az adatkötetek szükséges adatokat. Lemezek kizárhatják titkosítás, ha shellről őket. Meg kell megismerheti a szolgáltatás paraméter és a utalás előtt próbálja ki az éles virtuális gép a, próbálja ki az először a tesztelési virtuális gép EncryptFormatAll először. A EncryptFormatAll beállítás formázza az adatlemezt, és a rajta lévő adatok elvesznek. A folytatás előtt ellenőrizze, hogy ki szeretne lemezek megfelelően unmouted. </br></br>
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
Használja a [Set-azurermvmdiskencryptionextension parancs](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) parancsmagot a [EncryptFormatAll paraméter](https://www.powershellgallery.com/packages/AzureRM/5.0.0). 

**Titkos Ügyfélkód és EncryptFormatAll futó virtuális gép titkosítása:** tegyük fel, az alábbi parancsfájlt inicializálja a változók és a Set-azurermvmdiskencryptionextension parancs parancsmagot futtatja a EncryptFormatAll paraméterrel. Az erőforráscsoport, virtuális gép, a key vault, AAD-alkalmazás és titkos Ügyfélkód kell rendelkezik már létre van hozva előfeltételeket. MySecureRg, MySecureVM, MySecureVault My-AAD-client-ID és a saját – AAD-client-secret cserélje le az értékeket.
  
   ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MySecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="bkmk_EFALVM"> </a> Használja a EncryptFormatAll paraméterrel rendelkező logikai kötet kezelő (LVM) 
Azt javasoljuk, hogy az LVM-a-crypt beállítása. A következő példák az összes cserélje le az eszköz-elérési útját, és akkor csatlakozási bármilyen megfelelő-e a használati eset. A telepítő a következő elvégezhető:

- Adja hozzá az adatlemezeket a virtuális Gépet alkotó lesz.
- Formázhatja, csatlakoztathatja és ezeket a lemezeket ad hozzá az fstab fájlt.

    1. Formázza az újonnan hozzáadott lemezt. Itt az Azure létrehoz symlinks használjuk. Symlinks használatával elkerülheti a problémákat, az eszköz nevének módosítása. További információkért lásd: a [problémák elhárítása eszköznevek](../virtual-machines/linux/troubleshoot-device-names-problems.md) cikk.
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Csatlakoztassa a lemezeket.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Adja hozzá az fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Futtassa a Set-azurermvmdiskencryptionextension parancs PowerShell-parancsmag - EncryptFormatAll ezek a lemezek titkosítása.
         ```azurepowershell-interactive
         Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Állítsa be LVM felett ezeket a lemezeket. Vegye figyelembe a titkosított meghajtó zárolását a virtuális gép befejezése után indul el. Az LVM csatlakoztatási így is megkapják ezt követően kell-e késleltetni.




## <a name="bkmk_VHDpre"> </a> Ügyfél-titkosítású VHD-t és a titkosítási kulcsok alapján létrehozott új IaaS virtuális gépek
Ebben a forgatókönyvben engedélyezheti a Resource Manager-sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával titkosítja. A Resource Manager-sablon és a CLI-parancsokat az alábbi szakaszok részletesebben ismertetik. 

Kövesse az utasításokat a függelékben használható az Azure-ban előzetes titkosítással rendszerképek előkészítéséről. A lemezkép létrehozását követően használhatja a lépéseket a következő szakaszban titkosított Azure virtuális gép létrehozásához.

* [Előzetes titkosítással Windows virtuális merevlemez előkészítése](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Előzetes titkosítással Linux rendszerű virtuális merevlemez előkészítése](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt. A felügyelt lemez pillanatképét elvégezhet a portálról, vagy [Azure Backup](../backup/backup-azure-vms-encryption.md) is használható. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. Miután biztonsági másolatból történik, a Set-azurermvmdiskencryptionextension parancs parancsmag segítségével felügyelt lemezek titkosítása a - skipVmBackup paraméter megadásával. A Set-azurermvmdiskencryptionextension parancs parancs felügyelt lemezes virtuális gépek elleni sikertelen lesz, amíg a biztonsági másolat lett végrehajtva, és ez a paraméter lett megadva. 
>
>Titkosítása vagy letiltja a titkosítást a virtuális gép újraindítását okozhatja. 



### <a name="bkmk_VHDprePSH"> </a> Az Azure PowerShell használatával előre titkosított virtuális merevlemezzel rendelkező IaaS virtuális gépek titkosítása 
A PowerShell-parancsmag használatával a titkosított virtuális merevlemezen lemeztitkosítás is engedélyeznie [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples). Az alábbi példa néhány gyakori paramétereket biztosít. 

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName "MySecureRG"
```

### <a name="bkmk_VHDpreRM"> </a> A Resource Manager-sablon használatával előre titkosított virtuális merevlemezzel rendelkező IaaS virtuális gépek titkosítása 
Ön lemeztitkosítás a titkosított virtuális merevlemezen használatával engedélyezheti a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. Kattintson az Azure gyorsindítási sablon **üzembe helyezés az Azure**.

2. Válassza ki az előfizetést, erőforráscsoportot, erőforráscsoport helye, paraméterek, jogi feltételek és szerződés. Kattintson a **létrehozás** engedélyezheti a titkosítást az új IaaS virtuális gépen.

A következő táblázat felsorolja a Resource Manager-sablon paramétereit a titkosított virtuális merevlemez:

| Paraméter | Leírás |
| --- | --- |
| newStorageAccountName | A tárfiók tárolja a titkosított virtuális Rendszerlemez neve. Ezt a tárfiókot kell már létrejöttek az ugyanabban az erőforráscsoportban és ugyanazon a helyen a virtuális géppel. |
| osVhdUri | URI-ját a rendszert tartalmazó virtuális Merevlemezt a tárfiókból. |
| osType | Az operációs rendszer terméktípusa (Windows/Linux). |
| virtualNetworkName | Neve a virtuális hálózattal, a virtuális gép hálózati Adaptert kell tartoznia. A név kell már létrejöttek az ugyanabban az erőforráscsoportban és ugyanazon a helyen a virtuális gép. |
| subnetName | A-alhálózatot a virtuális hálózattal, a virtuális gép hálózati Adaptert kell tartoznia a neve. |
| vmSize | A virtuális gép méretét. Jelenleg csak a Standard A, D és G sorozat támogatottak. |
| keyVaultResourceID | Az erőforrás-azonosító, amely azonosítja a kulcstároló erőforrása az Azure Resource Manager. A PowerShell-parancsmag segítségével beszerezheti azt `(Get-AzureRmKeyVault -VaultName &lt;MyKeyVaultName&gt; -ResourceGroupName &lt;MyResourceGroupName&gt;).ResourceId` vagy az Azure CLI-parancs használatával `az keyvault show --name "MySecureVault" --query id`|
| keyVaultSecretUrl | URL-címe a lemez-titkosítási kulcs, amely a key vaultban van beállítva. |
| keyVaultKekUrl | A kulcsalapú titkosítás kulcsa a generált lemez-titkosítási kulcs titkosításához URL-címe. |
| vmName | Az IaaS virtuális gép nevét. |

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott adatlemez
Hozzáadhat egy új lemez használatával [az vm disk attach](../virtual-machines/linux/add-disk.md), vagy [az Azure Portalon keresztül](../virtual-machines/linux/attach-disk-portal.md). Mielőtt titkosíthatók, először csatlakoztassa az újonnan csatolt adatlemezre kell. Az adatmeghajtó titkosításához kell kérnie, mert a meghajtó használhatatlan lesz, bár a titkosítás folyamatban van. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott lemezt az Azure CLI-vel
 Ha a virtuális gép korábban titkosítva van az "All", majd a--kötettípus paramétert kell hagyni minden. Mind az operációs rendszer és az adatlemezek összes tartalmazza. Ha a virtuális gép korábban titkosított kötetet típusú "OS", majd a--kötettípus paraméter az összes lehet módosítani, hogy az operációs rendszer és az új adatlemezt is tartalmazza. Ha a virtuális gép titkosított "Adatok" csak a kötet típusát, majd azt is marad, "Data" ahogyan alább is látható. Hozzáadásával és a egy új adatlemez csatolása a virtuális gép nem elegendő előkészítése a titkosításhoz. Az újonnan csatolt lemez is kell formázva, és megfelelően csatlakoztatva a virtuális gép titkosítás engedélyezése előtt. Linux rendszeren a lemezt az/etc/fstab-csatlakoztatni kell egy [állandó blokk eszköznév](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems).  

Powershell-szintaxis szakembereket a CLI nem igényel egy egyedi verziója szolgáltatni a titkosítás engedélyezése a felhasználó. A CLI automatikusan hoz létre, és használja a saját egyedi feladatütemezési version értéke.

-  **Ügyfél titkos kulcs használatával futó virtuális gépek titkosítása:** 

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **A titkos ügyfélkulcsot burkolása KEK használatával futó virtuális gépek titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott lemezt az Azure PowerShell használatával
 Új lemez titkosítása Linux rendszeren a Powershell segítségével, amikor egy új verziója kell adni. A feladatütemezési verziójában található egyedinek kell lennie. Az alábbi parancsfájlt előállít egy GUID Azonosítót a verziója. 
 

-  **Ügyfél titkos kulcs használatával futó virtuális gép titkosítása:** az alábbi parancsfájlt inicializálja a változók és a Set-azurermvmdiskencryptionextension parancs parancsmagot futtatja. Az erőforráscsoport, virtuális gép, a key vault, AAD-alkalmazás és titkos Ügyfélkód kell rendelkezik már létre van hozva előfeltételeket. MySecureRg, MySecureVM, MySecureVault My-AAD-client-ID és a saját – AAD-client-secret cserélje le az értékeket. A - VolumeType paraméter értéke az adatlemezeket, és nem az operációsrendszer-lemez. Ha a virtuális gép korábban titkosított "OS" vagy "All" típusú kötet, majd a - VolumeType paraméter kell módosítani az összes, hogy az operációs rendszer és az új adatlemezt is tartalmazza.

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
    ```
- **A titkos ügyfélkulcsot burkolása KEK használatával futó virtuális gép titkosítása:** az Azure Disk Encryption lehetővé teszi, hogy Ön adja meg a meglévő kulcsot tárol a kulcstárolóban burkolása lemez titkosítási titkos kódok titkosításának engedélyezése során létrehozott. Amikor egy kulcsalapú titkosítás kulcsa van megadva, az Azure Disk Encryption a kulcs segítségével burkolhatja a titkosítási titkos kulcsait a Key Vault írása előtt. A - VolumeType paraméter értéke az adatlemezeket, és nem az operációsrendszer-lemez. Ha a virtuális gép korábban titkosított "OS" vagy "All" típusú kötet, majd a - VolumeType paraméter kell módosítani az összes, hogy az operációs rendszer és az új adatlemezt is tartalmazza.

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data';
     ```


>[!NOTE]
> A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> </br>
A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

## <a name="disable-encryption-for-linux-vms"></a>Linux rendszerű virtuális gépek titkosításának letiltása
Letilthatja a titkosítás az Azure PowerShell, az Azure CLI használatával vagy a Resource Manager-sablonnal. 

>[!IMPORTANT]
>Letiltja a titkosítást a Linuxos virtuális gépeken az Azure Disk Encryption csak a támogatott adatköteteket. Nem támogatott a adatok vagy operációsrendszer-kötettel, ha az operációsrendszer-kötet titkosított.  

- **Letiltja a lemeztitkosítást, az Azure PowerShell használatával:** letiltja a titkosítást, használja a [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Tiltsa le a titkosítást az Azure CLI-vel:** titkosítás letiltásához használja a [letiltása az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **Tiltsa le a Resource Manager-sablonnal titkosítást:** használja a [futó Linux rendszerű virtuális gépek titkosításának letiltása](https://aka.ms/decrypt-linuxvm) sablon titkosítás letiltásához.
     1. Kattintson a **Deploy to Azure** (Üzembe helyezés az Azure-ban) elemre.
     2. Válassza ki az előfizetés, erőforráscsoport, hely, virtuális gép, jogi feltételeket és szerződés.
     3.  Kattintson a **beszerzési** letiltja a lemeztitkosítást a futó Windows virtuális gépek. 


## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A Windows Azure Disk Encryption engedélyezése](azure-security-disk-encryption-windows-aad.md)

---
title: Az Azure Disk Encryption az Azure AD-alkalmazás Windows IaaS virtuális gépek (előző kiadás)
description: Ez a cikk útmutatást nyújt a Microsoft Azure lemez titkosítása a Windows IaaS virtuális gépek engedélyezése.
author: mestew
ms.service: security
ms.topic: article
ms.author: mstewart
ms.date: 03/04/2019
ms.custom: seodec18
ms.openlocfilehash: 47310efc32f1fa8d691da21ba30eaccd379cf812
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57764370"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms-previous-release"></a>Engedélyezze az Azure Disk titkosítás a Windows IaaS virtuális gépek (előző kiadás)

**Az Azure Disk Encryption új kiadása így nem egy Azure AD-alkalmazás paramétert, amelyek biztosítják a virtuális gép lemez-titkosítás engedélyezéséhez. Az új kiadással, már nem kell az Azure AD hitelesítő adatok titkosítás engedélyezése lépés során. Valamennyi új virtuális gépeket titkosítani kell az Azure AD alkalmazás paramétereit az új kiadás használata nélkül. Ahhoz, hogy az új kiadással VM lemeztitkosítás utasítások megtekintése: [Azure Disk Encryption Windows virtuális gépek](azure-security-disk-encryption-windows.md). Az Azure AD-alkalmazás paraméterekkel már titkosított virtuális gépek továbbra is támogatottak, és továbbra is fenn kell tartani az AAD-szintaxissal.**


Engedélyezheti a lemeztitkosítási számos forgatókönyv, és a lépések eltérőek lehetnek a forgatókönyv szerint. Az alábbi szakaszok részletesebben a forgatókönyvek Windows IaaS virtuális gépekhez terjed ki. Mielőtt használhatná a lemeztitkosítást, a [az Azure Disk Encryption előfeltétel](../security/azure-security-disk-encryption-prerequisites-aad.md) kell elvégezni. 

Igénybe vehet egy [pillanatkép](../virtual-machines/windows/snapshot-copy-managed-disk.md) és/vagy lemezek előtt készítsen biztonsági másolatot. Biztonsági mentések ellenőrizze, hogy a helyreállítási beállítások titkosítás során váratlan hiba esetén lehetséges. A felügyelt lemezekkel rendelkező virtuális gépek biztonsági szükséges, a titkosítás előtt. Miután biztonsági másolatból történik, a Set-AzVMDiskEncryptionExtension parancsmag segítségével felügyelt lemezek titkosítása a - skipVmBackup paraméter megadásával. Biztonsági mentése és visszaállítása titkosított virtuális gépek kapcsolatos további információkért lásd: a [Azure Backup](../backup/backup-azure-vms-encryption.md) cikk. 

>[!WARNING]
> - Ha korábban már használt [az Azure Disk Encryption az Azure AD-alkalmazás](azure-security-disk-encryption-prerequisites-aad.md) Ez a virtuális gép titkosítására, kell továbbra is használja ezt a beállítást a virtuális gép titkosítására. Nem használhat [az Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) a titkosított virtuális gépen, ez nem támogatott forgatókönyv, azaz a átváltani AAD-alkalmazás számára a titkosított virtuális gép nem támogatott még.
> - Annak érdekében, hogy a titkosítási titkos kulcsok nem adatbázisközi regionális határokat, az Azure Disk Encryption a Key Vault és a virtuális gépek közös elhelyezése ugyanabban a régióban van szüksége. Létrehozhat és használhat egy Key Vaultot, amely ugyanabban a régióban, mint a virtuális gép titkosítását. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>A Marketplace-ről létrehozott új IaaS virtuális gépek titkosításának engedélyezése
Disk encryption szolgáltatást új IaaS Windows virtuális gép a piactérről, az Azure Resource Manager-sablon használatával engedélyezheti. A sablon létrehoz egy új titkosított Windows virtuális gépet a Windows Server 2012 image z galerie.

1. Az a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image), kattintson a **üzembe helyezés az Azure**.

2. Válassza ki az előfizetést, erőforráscsoportot, erőforráscsoport helye, paraméterek, jogi feltételek és szerződés. Kattintson a **beszerzési** új IaaS virtuális gép üzembe helyezése, a titkosítás engedélyezve van.

3. Miután telepíti a sablont, ellenőrizze a virtuális gép titkosítási állapotát a kívánt módszer használatával:
     - Ellenőrizze az Azure CLI-vel a [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Az Azure PowerShell használatával ellenőrizze a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot. 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Válassza ki a virtuális Gépet, majd kattintson a **lemezek** alatt a **beállítások** fejléc encryption státusz a portálon. A diagram alatt **titkosítási**, látni fogja, hogy annak engedélyezve van-e. 
           ![Az Azure portal - lemez titkosítás engedélyezve](./media/azure-security-disk-encryption/disk-encryption-fig2.png) az alábbi táblázat a Resource Manager-Sablonparaméterek az új virtuális gépek a Marketplace-en forgatókönyvet használja az Azure AD ügyfél-azonosító:

| Paraméter | Leírás | 
| --- | --- |
| adminUserName | A virtuális gép rendszergazdai felhasználóneve. |
| adminPassword | A virtuális gép rendszergazdai felhasználójának jelszava. |
| newStorageAccountName | Neve a tárfiók operációsrendszer- és virtuális merevlemezek tárolásához. |
| vmSize | A virtuális gép méretét. Jelenleg csak a Standard A, D és G sorozat támogatottak. |
| virtualNetworkName | Neve a virtuális hálózattal, a virtuális gép hálózati Adaptert kell tartoznia. |
| subnetName | A virtuális hálózat, amely a virtuális gép hálózati Adaptert kell tartoznia az alhálózat neve. |
| AADClientID | Ügyfél-Azonosítóját az Azure AD-alkalmazást, amely jogosult a kulcstartó titkos kódok írni. |
| AADClientSecret | Az Azure AD-alkalmazást, amely titkos kulcsok a key vault írási engedélyekkel rendelkezik a titkos ügyfélkódja. |
| keyVaultURL | A kulcstartóhoz, amely fel kell tölteni a BitLocker-kulcs URL-címe. A parancsmag segítségével beszerezheti azt `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` vagy az Azure CLI használatával `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | A kulcsalapú titkosítás kulcsa, a generált (nem kötelező) a BitLocker-kulcs titkosításához használt URL-címe. </br> </br>KeyEncryptionKeyURL egy nem kötelező paraméter. Is tenné a saját KEK számára további védelmi a szolgáltatásadat-titkosítási kulcs (a jelszó titkos kódot) tárol a kulcstárolóban. |
| keyVaultResourceGroup | A kulcstároló erőforráscsoport. |
| vmName | Neve a virtuális gép, amely a titkosítási műveletet kell végrehajtani. |


## <a name="bkmk_RunningWinVM"></a> Engedélyezze a titkosítást a meglévő vagy IaaS Windows rendszerű virtuális gépek futtatása
Ebben a forgatókönyvben egy sablont, a PowerShell-parancsmagokkal vagy a CLI-parancsok használatával engedélyezheti a titkosítást. Az alábbi szakaszok részletesebben ismertetik az Azure Disk Encryption engedélyezése. 

>[!IMPORTANT]
 >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt. A felügyelt lemez pillanatképét elvégezhet a portálról, vagy [Azure Backup](../backup/backup-azure-vms-encryption.md) is használható. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. Miután biztonsági másolatból történik, a Set-AzVMDiskEncryptionExtension parancsmag segítségével felügyelt lemezek titkosítása a - skipVmBackup paraméter megadásával. A Set-AzVMDiskEncryptionExtension parancs felügyelt lemezes virtuális gépek elleni sikertelen lesz, amíg a biztonsági másolat lett végrehajtva, és ez a paraméter lett megadva. 
>
>Titkosítása vagy letiltja a titkosítást a virtuális gép újraindítását okozhatja. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Engedélyezze a titkosítást a meglévő vagy a virtuális gép futtatása az Azure PowerShell használatával 
Használja a [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag, amellyel engedélyezheti a titkosítást az Azure-ban futó IaaS virtuális gép. Az Azure Disk Encryption titkosítás engedélyezése a PowerShell-parancsmagok használatával kapcsolatos információkért tekintse meg a blogbejegyzések [az Azure Disk Encryption megismerése az Azure PowerShell használatával – 1. rész](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) és [az Azure Disk Encryption megismerése az Azure PowerShell használatával – 2. rész](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

-  **Ügyfél titkos kulcs használatával futó virtuális gépek titkosítása:** Az alábbi parancsfájlt inicializálja a változók és a Set-AzVMDiskEncryptionExtension parancsmagot futtatja. Az erőforráscsoport, virtuális gép, a key vault, AAD-alkalmazás és titkos Ügyfélkód kell rendelkezik már létre van hozva előfeltételeket. MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID és a saját – AAD-client-secret cserélje le az értékeket.
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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **A titkos ügyfélkulcsot burkolása KEK használatával futó virtuális gépek titkosítása:** Az Azure Disk Encryption lehetővé teszi egy meglévő kulcs megadását burkolása közben a titkosítás engedélyezése, a lemez titkosítási titkos kulcsok a key vaultban lévő. Amikor egy kulcsalapú titkosítás kulcsa van megadva, az Azure Disk Encryption a kulcs segítségével burkolhatja a titkosítási titkos kulcsait a Key Vault írása előtt. 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek:** IaaS virtuális gép titkosítási állapotának ellenőrzéséhez használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Lemeztitkosítás letiltása:** A titkosítás letiltásához használja a [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Engedélyezze a titkosítást a meglévő vagy a virtuális gép futtatása az Azure CLI-vel
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
     > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek:** IaaS virtuális gép titkosítási állapotának ellenőrzéséhez használja a [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Tiltsa le a titkosítást:** Titkosítás letiltásához használja a [letiltása az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
 
 > [!NOTE]
 >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt. A felügyelt lemez pillanatképét elvégezhet a portálról, vagy [Azure Backup](../backup/backup-azure-vms-encryption.md) is használható. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. Miután biztonsági másolatból történik, a Set-AzVMDiskEncryptionExtension parancsmag segítségével felügyelt lemezek titkosítása a - skipVmBackup paraméter megadásával. Ez a parancs-alapú felügyelt lemezes virtuális gépek elleni sikertelen lesz, amíg a biztonsági másolat lett végrehajtva, és ez a paraméter lett megadva. 
>
>Titkosítása vagy letiltja a titkosítást a virtuális gép újraindítását okozhatja. 

### <a name="bkmk_RunningWinVMwRM"> </a>A Resource Manager-sablon használatával
Disk encryption szolgáltatást meglévő vagy IaaS Windows virtuális gépek futtatása az Azure-beli használatával engedélyezheti a [futó Windows virtuális gépek titkosításához a Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. Kattintson az Azure gyorsindítási sablon **üzembe helyezés az Azure**.

2. Válassza ki az előfizetést, erőforráscsoportot, erőforráscsoport helye, paraméterek, jogi feltételek és szerződés. Kattintson a **beszerzési** engedélyezheti a titkosítást a meglévő vagy futó IaaS virtuális gépen.

Az alábbi táblázat a meglévő vagy egy Azure AD ügyfél-Azonosítót használó virtuális gépek a Resource Manager sablon paramétereit:

| Paraméter | Leírás |
| --- | --- |
| AADClientID | Az Azure AD-alkalmazást, amely a key vault titkos kódok írási engedélyekkel rendelkezik ügyfél-azonosítója. |
| AADClientSecret | Az Azure AD-alkalmazást, amely a key vault titkos kódok írási engedélyekkel rendelkezik a titkos ügyfélkódja. |
| keyVaultName | A kulcstartóhoz, amely fel kell tölteni a BitLocker-kulcs neve. A parancsmag segítségével beszerezheti azt `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` vagy az Azure CLI-paranccsal "az keyvault list--resource-group"MySecureGroup" |Convertfrom-JSON "|
|  keyEncryptionKeyURL | A kulcsalapú titkosítás kulcsa, a létrehozott BitLocker-kulcs titkosítására használt URL-címe. Ez a paraméter nem kötelező, ha kiválasztja **nokek** a UseExistingKek legördülő listában. Ha **kek** a UseExistingKek legördülő listában, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| VolumeType | A titkosítási műveletet végzi el a kötet típusa. Érvényes értékek a következők _operációs rendszer_, _adatok_, és _összes_. |
| SequenceVersion | A BitLocker művelet verziója. Növelje a verziószámot, minden alkalommal, amikor a lemeztitkosítási műveletet végzi el az azonos virtuális gépen. |
| vmName | Neve a virtuális gép, amely a titkosítási műveletet kell végrehajtani. |


## <a name="bkmk_VHDpre"> </a>Ügyfél-titkosítású VHD-t és a titkosítási kulcsok alapján létrehozott új IaaS virtuális gépek
Ebben a forgatókönyvben engedélyezheti a Resource Manager-sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával titkosítja. A Resource Manager-sablon és a CLI-parancsokat az alábbi szakaszok részletesebben ismertetik. 

Kövesse az utasításokat a függelékben használható az Azure-ban előzetes titkosítással rendszerképek előkészítéséről. A lemezkép létrehozását követően használhatja a lépéseket a következő szakaszban titkosított Azure virtuális gép létrehozásához.

* [Előzetes titkosítással Windows virtuális merevlemez előkészítése](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Előzetes titkosítással Linux rendszerű virtuális merevlemez előkészítése](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt. A felügyelt lemez pillanatképét elvégezhet a portálról, vagy [Azure Backup](../backup/backup-azure-vms-encryption.md) is használható. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. Miután biztonsági másolatból történik, a Set-AzVMDiskEncryptionExtension parancsmag segítségével felügyelt lemezek titkosítása a - skipVmBackup paraméter megadásával. A Set-AzVMDiskEncryptionExtension parancs felügyelt lemezes virtuális gépek elleni sikertelen lesz, amíg a biztonsági másolat lett végrehajtva, és ez a paraméter lett megadva. 
>
>Titkosítása vagy letiltja a titkosítást a virtuális gép újraindítását okozhatja. 



### <a name="bkmk_VHDprePSH"> </a> Az Azure PowerShell használatával előre titkosított virtuális merevlemezzel rendelkező virtuális gépek titkosítása
A PowerShell-parancsmag használatával a titkosított virtuális merevlemezen lemeztitkosítás is engedélyeznie [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). Az alábbi példa néhány gyakori paramétereket biztosít. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```


### <a name="bkmk_VHDpreRM"> </a>Resource Manager-sablonnal előre titkosított virtuális merevlemezzel rendelkező IaaS virtuális gépek titkosítása 
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
| keyVaultResourceID | Az erőforrás-azonosító, amely azonosítja a kulcstároló erőforrása az Azure Resource Manager. A PowerShell-parancsmag segítségével beszerezheti azt `(Get-AzKeyVault -VaultName "MyKeyVaultName"; -ResourceGroupName "MyKeyVaultResourceGroupName").ResourceId` vagy az Azure CLI-parancs használatával `az keyvault show --name "MySecureVault" --query id`|
| keyVaultSecretUrl | URL-címe a lemez-titkosítási kulcs, amely a key vaultban van beállítva. |
| keyVaultKekUrl | A kulcsalapú titkosítás kulcsa a generált lemez-titkosítási kulcs titkosításához URL-címe. |
| vmName | Az IaaS virtuális gép nevét. |

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott adatlemez
Is [adjon hozzá egy új lemezt egy Windows virtuális Gépet PowerShell-lel](../virtual-machines/windows/attach-disk-ps.md), vagy [az Azure Portalon keresztül](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott lemezt az Azure PowerShell használatával
 Amikor új lemez titkosítása Windows virtuális gépekhez a Powershell segítségével, meg kell adni egy új verziója. A feladatütemezési verziójában található egyedinek kell lennie. Az alábbi parancsfájlt előállít egy GUID Azonosítót a verziója. Bizonyos esetekben egy újonnan hozzáadott adatlemez előfordulhat, hogy automatikusan titkosítja az Azure Disk Encryption bővítmény által. Automatikus titkosítás általában akkor fordul elő, amikor a virtuális gép újraindul, miután az új lemezt online állapotba kerül. Ezt általában a hibát, mert az "Összes" van megadva a kötet típusa, ha korábban már futott lemeztitkosítás a virtuális gépen. Automatikus titkosítás az újonnan hozzáadott adatlemez esetén javasoljuk a Set-AzVmDiskEncryptionExtension parancsmag újra futtatni az új verziója. Az új adatlemez automatikusan titkosítva, és nem szeretne titkosítását, ha az összes meghajtó először visszafejtéséhez, majd újra titkosítja a az új feladatütemezés az operációs rendszer a kötet típusának megadása. 
 

-  **Ügyfél titkos kulcs használatával futó virtuális gépek titkosítása:** Az alábbi parancsfájlt inicializálja a változók és a Set-AzVMDiskEncryptionExtension parancsmagot futtatja. Az erőforráscsoport, virtuális gép, a key vault, AAD-alkalmazás és titkos Ügyfélkód kell rendelkezik már létre van hozva előfeltételeket. MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM, MySecureVault, My-AAD-client-ID és a saját – AAD-client-secret cserélje le az értékeket. Ebben a példában "Összes", amely tartalmazza az operációs rendszer és a Data - VolumeType paraméterhez. Ha csak szeretné az operációs rendszer kötetének titkosításához, használja a "OS" - VolumeType paraméterhez. 

     ```azurepowershell
      $sequenceVersion = [Guid]::NewGuid();
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;
    ```
- **A titkos ügyfélkulcsot burkolása KEK használatával futó virtuális gépek titkosítása:** Az Azure Disk Encryption lehetővé teszi egy meglévő kulcs megadását burkolása közben a titkosítás engedélyezése, a lemez titkosítási titkos kulcsok a key vaultban lévő. Amikor egy kulcsalapú titkosítás kulcsa van megadva, az Azure Disk Encryption a kulcs segítségével burkolhatja a titkosítási titkos kulcsait a Key Vault írása előtt. Ebben a példában "Összes", amely tartalmazza az operációs rendszer és a Data - VolumeType paraméterhez. Ha csak szeretné az operációs rendszer kötetének titkosításához, használja a "OS" - VolumeType paraméterhez. 

     ```azurepowershell
     $sequenceVersion = [Guid]::NewGuid();
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
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott lemezt az Azure CLI-vel
  Az Azure CLI-paranccsal automatikusan megad egy új verziója, engedélyezze a titkosítást a parancs futtatásakor. A kötet-yype paraméter elfogadható értékek: All, az operációs rendszer és az adatok. Szükség lehet a kötet-típus paraméter módosítani, a rendszer- és, ha a virtuális gép csak épp titkosít lemez egy típusa. A példákban "Összes" a kötet-típus paraméter. 

-  **Ügyfél titkos kulcs használatával futó virtuális gépek titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **A titkos ügyfélkulcsot burkolása KEK használatával futó virtuális gépek titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>A titkosítást az Azure AD ügyfél Tanúsítványalapú hitelesítés használatával.
Ügyféltanúsítvány-alapú hitelesítés is használhatja, vagy a KEK nélkül. A parancsfájlok megköveteli, hogy [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites-aad.md) befejeződött. Mielőtt a PowerShell-parancsfájlokat használ, már a tanúsítványt a key vault feltöltött és a virtuális Gépre telepített. Ha túl KEK használ, a KEK már léteznie kell. További információkért lásd: a [az Azure ad-alapú hitelesítés](azure-security-disk-encryption-prerequisites-aad.md#bkmk_Cert) az Előfeltételek című szakaszban.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Engedélyezze a titkosítást a Tanúsítványalapú hitelesítés használata az Azure PowerShell használatával

```powershell
## Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$VMRGName = 'MyVirtualMachineResourceGroup'
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Engedélyezze a titkosítást a Tanúsítványalapú hitelesítés és a egy kek-KEL használatával az Azure PowerShell használatával

```powershell
# Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$VMRGName = 'MyVirtualMachineResourceGroup';
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable.

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Tiltsa le a titkosítást
Letilthatja a titkosítás az Azure PowerShell, az Azure CLI használatával vagy a Resource Manager-sablonnal. 

- **Tiltsa le a lemeztitkosítást, az Azure PowerShell használatával:** A titkosítás letiltásához használja a [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Tiltsa le a titkosítást az Azure CLI-vel:** Titkosítás letiltásához használja a [letiltása az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Tiltsa le a Resource Manager-sablonnal titkosítást:** 

    1. Kattintson a **üzembe helyezés az Azure** származó a [letiltja a lemeztitkosítást a Windows rendszerű virtuális gépek futtatásához](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) sablont.
    2. Válassza ki az előfizetés, erőforráscsoport, hely, virtuális gép, jogi feltételeket és szerződés.
    3.  Kattintson a **beszerzési** letiltja a lemeztitkosítást a futó Windows virtuális gépek. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Linuxhoz készült Azure Disk Encryption engedélyezése](azure-security-disk-encryption-linux-aad.md)

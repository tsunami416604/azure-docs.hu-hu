---
title: Engedélyezze az Azure Disk Encryption Windows IaaS virtuális gépekhez |} A Microsoft Docs
description: Ez a cikk útmutatást nyújt a Microsoft Azure lemez titkosítása a Windows IaaS virtuális gépek engedélyezése.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 8b3905c8-844f-4ec7-ad95-b386e9843053
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: 772ab272603d0f8e0badf899c439592b20b7c8a3
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39391745"
---
#  <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>A Windows Iaas virtuális gépek az Azure Disk Encryption engedélyezése 

Engedélyezheti a lemeztitkosítási számos forgatókönyv, és a lépések eltérőek lehetnek a forgatókönyv szerint. Az alábbi szakaszok részletesebben a forgatókönyvek Windows IaaS virtuális gépekhez terjed ki. Mielőtt használhatná a lemeztitkosítást, a [az Azure Disk Encryption előfeltétel](/articles/security/azure-security-disk-encryption-prerequisites.md) kell elvégezni. 

Igénybe vehet egy [pillanatkép](../virtual-machines/windows/snapshot-copy-managed-disk.md) és/vagy lemezek előtt készítsen biztonsági másolatot. Biztonsági mentések győződjön meg arról, hogy egy helyreállítási lehetőséget nem várt hiba esetén titkosítás közben. A felügyelt lemezekkel rendelkező virtuális gépek biztonsági szükséges, a titkosítás előtt. Miután biztonsági másolatból történik, a Set-azurermvmdiskencryptionextension parancs parancsmag segítségével felügyelt lemezek titkosítása a - skipVmBackup paraméter megadásával. Biztonsági mentése és visszaállítása titkosított virtuális gépek kapcsolatos további információkért lásd: a [Azure Backup](../backup/backup-azure-vms-encryption.md) cikk. 

>[!WARNING]
>Annak érdekében, hogy a titkosítási titkos kulcsok nem adatbázisközi regionális határokat, az Azure Disk Encryption a Key Vault és a virtuális gépek közös elhelyezése ugyanabban a régióban van szüksége. Létrehozhat és használhat egy Key Vaultot, amely ugyanabban a régióban, mint a virtuális gép titkosítását. 


## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>A Marketplace-ről létrehozott új IaaS virtuális gépek titkosításának engedélyezése
Disk encryption szolgáltatást új IaaS Windows virtuális gép a piactérről, az Azure Resource Manager-sablon használatával engedélyezheti. A sablon létrehoz egy új titkosított Windows virtuális gépet a Windows Server 2012 image z galerie.

1. Az a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image), kattintson a **üzembe helyezés az Azure**.

2. Válassza ki az előfizetést, erőforráscsoportot, erőforráscsoport helye, paraméterek, jogi feltételek és szerződés. Kattintson a **beszerzési** új IaaS virtuális gép üzembe helyezése, a titkosítás engedélyezve van.

3. Miután telepíti a sablont, ellenőrizze a virtuális gép titkosítási állapotát a kívánt módszer használatával:
     - Ellenőrizze az Azure CLI-vel a [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
         ```

     - Az Azure PowerShell használatával ellenőrizze a [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) parancsmagot. 

         ```azurepowershell-interactive
         Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
         ```

     -  Válassza ki a virtuális Gépet, majd kattintson a **lemezek** alatt a **beállítások** fejléc encryption státusz a portálon. A diagram alatt **titkosítási**, látni fogja, hogy annak engedélyezve van-e. 
           ![Az Azure Portal - lemez titkosítás engedélyezve](./media/azure-security-disk-encryption/disk-encryption-fig2.png) az alábbi táblázat a Resource Manager-Sablonparaméterek az új virtuális gépek a Marketplace-en forgatókönyvet használja az Azure AD ügyfél-azonosító:

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
| keyVaultURL | A kulcstartóhoz, amely fel kell tölteni a BitLocker-kulcs URL-címe. A parancsmag segítségével beszerezheti azt `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName).VaultURI` vagy az Azure CLI használatával `az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | A kulcsalapú titkosítás kulcsa, a generált (nem kötelező) a BitLocker-kulcs titkosításához használt URL-címe. </br> </br>KeyEncryptionKeyURL egy nem kötelező paraméter. Is tenné a saját KEK számára további védelmi a szolgáltatásadat-titkosítási kulcs (a jelszó titkos kódot) tárol a kulcstárolóban. |
| keyVaultResourceGroup | A kulcstároló erőforráscsoport. |
| vmName | Neve a virtuális gép, amely a titkosítási műveletet kell végrehajtani. |


## <a name="bkmk_RunningWinVM"></a> Engedélyezze a titkosítást a meglévő vagy IaaS Windows rendszerű virtuális gépek futtatása
Ebben a forgatókönyvben egy sablont, a PowerShell-parancsmagokkal vagy a CLI-parancsok használatával engedélyezheti a titkosítást. Az alábbi szakaszok részletesebben ismertetik az Azure Disk Encryption engedélyezése. 

>[!IMPORTANT]
 >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt. A felügyelt lemez pillanatképét elvégezhet a portálról, vagy [Azure Backup](../backup/backup-azure-vms-encryption.md) is használható. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. Miután biztonsági másolatból történik, a Set-azurermvmdiskencryptionextension parancs parancsmag segítségével felügyelt lemezek titkosítása a - skipVmBackup paraméter megadásával. A Set-azurermvmdiskencryptionextension parancs parancs felügyelt lemezes virtuális gépek elleni sikertelen lesz, amíg a biztonsági másolat lett végrehajtva, és ez a paraméter lett megadva. 
>
>Titkosítása vagy letiltja a titkosítást a virtuális gép újraindítását okozhatja. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Engedélyezze a titkosítást a meglévő vagy a virtuális gép futtatása az Azure PowerShell használatával 
Használja a [Set-azurermvmdiskencryptionextension parancs](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) parancsmag, amellyel engedélyezheti a titkosítást az Azure-ban futó IaaS virtuális gép. Az Azure Disk Encryption titkosítás engedélyezése a PowerShell-parancsmagok használatával kapcsolatos információkért tekintse meg a blogbejegyzések [az Azure Disk Encryption megismerése az Azure PowerShell használatával – 1. rész](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) és [az Azure Disk Encryption megismerése az Azure PowerShell használatával – 2. rész](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

-  **Ügyfél titkos kulcs használatával futó virtuális gép titkosítása:** az alábbi parancsfájlt inicializálja a változók és a Set-azurermvmdiskencryptionextension parancs parancsmagot futtatja. Az erőforráscsoport, virtuális gép, a key vault, AAD-alkalmazás és titkos Ügyfélkód kell rendelkezik már létre van hozva előfeltételeket. MySecureRg, MySecureVM, MySecureVault My-AAD-client-ID és a saját – AAD-client-secret cserélje le az értékeket.
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
- **A titkos ügyfélkulcsot burkolása KEK használatával futó virtuális gép titkosítása:** az Azure Disk Encryption lehetővé teszi, hogy Ön adja meg a meglévő kulcsot tárol a kulcstárolóban burkolása lemez titkosítási titkos kódok titkosításának engedélyezése során létrehozott. Amikor egy kulcsalapú titkosítás kulcsa van megadva, az Azure Disk Encryption a kulcs segítségével burkolhatja a titkosítási titkos kulcsait a Key Vault írása előtt. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = ‘MyExtraSecureVM’;
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
   > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek:** IaaS virtuális gép titkosítási állapotának ellenőrzéséhez használja a [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) parancsmagot. 
     ```azurepowershell-interactive
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
     ```
    
- **Tiltsa le a lemeztitkosítás:** letiltja a titkosítást, használja a [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Engedélyezze a titkosítást a meglévő vagy a virtuális gép futtatása az Azure CLI-vel
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
     > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek:** IaaS virtuális gép titkosítási állapotának ellenőrzéséhez használja a [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Tiltsa le a titkosítást:** titkosítás letiltásához használja a [letiltása az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
 
 > [!NOTE]
 >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt. A felügyelt lemez pillanatképét elvégezhet a portálról, vagy [Azure Backup](../backup/backup-azure-vms-encryption.md) is használható. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. Miután biztonsági másolatból történik, a Set-azurermvmdiskencryptionextension parancs parancsmag segítségével felügyelt lemezek titkosítása a - skipVmBackup paraméter megadásával. Ez a parancs-alapú felügyelt lemezes virtuális gépek elleni sikertelen lesz, amíg a biztonsági másolat lett végrehajtva, és ez a paraméter lett megadva. 
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
| keyVaultName | A kulcstartóhoz, amely fel kell tölteni a BitLocker-kulcs neve. A parancsmag segítségével beszerezheti azt `(Get-AzureRmKeyVault -ResourceGroupName <MyResourceGroupName>). Vaultname` vagy az Azure CLI-paranccsal "az keyvault list--resource-group"MySecureGroup" |Convertfrom-JSON "|
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
 >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt. A felügyelt lemez pillanatképét elvégezhet a portálról, vagy [Azure Backup](../backup/backup-azure-vms-encryption.md) is használható. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. Miután biztonsági másolatból történik, a Set-azurermvmdiskencryptionextension parancs parancsmag segítségével felügyelt lemezek titkosítása a - skipVmBackup paraméter megadásával. A Set-azurermvmdiskencryptionextension parancs parancs felügyelt lemezes virtuális gépek elleni sikertelen lesz, amíg a biztonsági másolat lett végrehajtva, és ez a paraméter lett megadva. 
>
>Titkosítása vagy letiltja a titkosítást a virtuális gép újraindítását okozhatja. 



### <a name="bkmk_VHDprePSH"> </a> Az Azure PowerShell használatával előre titkosított virtuális merevlemezzel rendelkező virtuális gépek titkosítása
A PowerShell-parancsmag használatával a titkosított virtuális merevlemezen lemeztitkosítás is engedélyeznie [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples). Az alábbi példa néhány gyakori paramétereket biztosít. 

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResouceGroupName "MySecureRG"
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
| keyVaultResourceID | Az erőforrás-azonosító, amely azonosítja a kulcstároló erőforrása az Azure Resource Manager. A PowerShell-parancsmag segítségével beszerezheti azt `(Get-AzureRmKeyVault -VaultName &lt;MyKeyVaultName&gt; -ResourceGroupName &lt;MyResourceGroupName&gt;).ResourceId` vagy az Azure CLI-parancs használatával `az keyvault show --name "MySecureVault" --query id`|
| keyVaultSecretUrl | URL-címe a lemez-titkosítási kulcs, amely a key vaultban van beállítva. |
| keyVaultKekUrl | A kulcsalapú titkosítás kulcsa a generált lemez-titkosítási kulcs titkosításához URL-címe. |
| vmName | Az IaaS virtuális gép nevét. |

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott adatlemez
Is [adjon hozzá egy új lemezt egy Windows virtuális Gépet PowerShell-lel](../virtual-machines/windows/attach-disk-ps.md), vagy [az Azure Portalon keresztül](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott lemezt az Azure PowerShell használatával
 Amikor új lemez titkosítása Windows virtuális gépekhez a Powershell segítségével, meg kell adni egy új verziója. A feladatütemezési verziójában található egyedinek kell lennie. Az alábbi parancsfájlt előállít egy GUID Azonosítót a verziója. Bizonyos esetekben egy újonnan hozzáadott adatlemez előfordulhat, hogy automatikusan titkosítja az Azure Disk Encryption bővítmény által. Ha ez történik, azt javasoljuk, a Set-azurermvmdiskencryptionextension parancs parancsmag újra futtatni az új verziója.
 

-  **Ügyfél titkos kulcs használatával futó virtuális gép titkosítása:** az alábbi parancsfájlt inicializálja a változók és a Set-azurermvmdiskencryptionextension parancs parancsmagot futtatja. Az erőforráscsoport, virtuális gép, a key vault, AAD-alkalmazás és titkos Ügyfélkód kell rendelkezik már létre van hozva előfeltételeket. MySecureRg, MySecureVM, MySecureVault My-AAD-client-ID és a saját – AAD-client-secret cserélje le az értékeket. A - VolumeType paraméter értéke az adatlemezeket, és nem az operációsrendszer-lemez. 

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

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId  –SequenceVersion $sequenceVersion;
    ```
- **A titkos ügyfélkulcsot burkolása KEK használatával futó virtuális gép titkosítása:** az Azure Disk Encryption lehetővé teszi, hogy Ön adja meg a meglévő kulcsot tárol a kulcstárolóban burkolása lemez titkosítási titkos kódok titkosításának engedélyezése során létrehozott. Amikor egy kulcsalapú titkosítás kulcsa van megadva, az Azure Disk Encryption a kulcs segítségével burkolhatja a titkosítási titkos kulcsait a Key Vault írása előtt. Szükség lehet adja hozzá a - VolumeType paramétert, ha az adatlemezeket, és nem az operációsrendszer-lemez titkosít. 

     ```azurepowershell-interactive
     $sequenceVersion = [Guid]::NewGuid();
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

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott lemezt az Azure CLI-vel
 Az Azure CLI-paranccsal automatikusan megad egy új verziója, engedélyezze a titkosítást a parancs futtatásakor. 
-  **Ügyfél titkos kulcs használatával futó virtuális gépek titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **A titkos ügyfélkulcsot burkolása KEK használatával futó virtuális gépek titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault"--key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>A titkosítást az Azure AD ügyféltanúsítvány-alapú hitelesítés használatával.
Ügyféltanúsítvány-alapú hitelesítés is használhatja, vagy a KEK nélkül. A parancsfájlok megköveteli, hogy [az Azure Disk Encryption előfeltétel](azure-security-disk-encryption-prerequisites.md) befejeződött. Mielőtt a PowerShell-parancsfájlokat használ, már a tanúsítványt a key vault feltöltött és a virtuális Gépre telepített. Ha túl KEK használ, a KEK már léteznie kell. További információkért lásd: a [az Azure ad-alapú hitelesítés](azure-security-disk-encryption-prerequisites.md#bkmk_Cert) az Előfeltételek című szakaszban.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Engedélyezze a titkosítást a Tanúsítványalapú hitelesítés használata az Azure PowerShell használatával

```powershell
## Fill in 'MySecureRg', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$rgName = 'MySecureRg';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = ‘MySecureVM’;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Engedélyezze a titkosítást, tanúsítványalapú hitelesítéssel és a egy kek-KEL használatával az Azure PowerShell használatával

```powershell
# Fill in 'MySecureRg', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$rgName = 'MySecureRg';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Tiltsa le a titkosítást
Letilthatja a titkosítás az Azure PowerShell, az Azure CLI használatával vagy a Resource Manager-sablonnal. 

- **Letiltja a lemeztitkosítást, az Azure PowerShell használatával:** letiltja a titkosítást, használja a [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

- **Tiltsa le a titkosítást az Azure CLI-vel:** titkosítás letiltásához használja a [letiltása az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **Tiltsa le a Resource Manager-sablonnal titkosítást:** 

    1. Kattintson a **üzembe helyezés az Azure** származó a [letiltja a lemeztitkosítást a Windows rendszerű virtuális gépek futtatásához](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) sablont.
    2. Válassza ki az előfizetés, erőforráscsoport, hely, virtuális gép, jogi feltételeket és szerződés.
    3.  Kattintson a **beszerzési** letiltja a lemeztitkosítást a futó Windows virtuális gépek. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Linuxhoz készült Azure Disk Encryption engedélyezése](azure-security-disk-encryption-linux.md)
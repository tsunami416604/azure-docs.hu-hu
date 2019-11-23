---
title: Azure Disk Encryption az Azure AD-vel a Windows rendszerű virtuális gépekhez (előző kiadás)
description: Ez a cikk útmutatást nyújt a Microsoft Azure lemez titkosítása a Windows IaaS virtuális gépek engedélyezése.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: d06be85e4d18bc0867835a307b44ec8813c79d7d
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245005"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Azure Disk Encryption az Azure AD-vel a Windows rendszerű virtuális gépekhez (előző kiadás)

**A Azure Disk Encryption új kiadása szükségtelenné teszi az Azure AD-alkalmazás paramétereinek biztosítását a virtuális gépek lemezes titkosításának engedélyezéséhez. Az új kiadással már nem szükséges az Azure AD-beli hitelesítő adatok megadása a titkosítás engedélyezése lépés során. Az új kiadás használatával az új virtuális gépeket az Azure AD-alkalmazás paramétereinek használata nélkül kell titkosítani. A VM Disk Encryption új kiadással való engedélyezésével kapcsolatos utasítások megtekintéséhez lásd: [Azure Disk Encryption Windows rendszerű virtuális gépekhez](disk-encryption-windows.md). Azok a virtuális gépek, amelyek már az Azure AD-alkalmazás paramétereivel lettek titkosítva, továbbra is támogatottak, és továbbra is fenn kell tartaniuk a HRE szintaxisát**


Engedélyezheti a lemeztitkosítási számos forgatókönyv, és a lépések eltérőek lehetnek a forgatókönyv szerint. Az alábbi szakaszok részletesebben a forgatókönyvek Windows IaaS virtuális gépekhez terjed ki. Mielőtt használhatná a lemeztitkosítást, a [az Azure Disk Encryption előfeltétel](disk-encryption-overview-aad.md) kell elvégezni. 


>[!IMPORTANT]
> - Készítsen [pillanatképet](snapshot-copy-managed-disk.md) , és/vagy készítsen biztonsági másolatot a lemezek titkosítása előtt. Biztonsági mentések ellenőrizze, hogy a helyreállítási beállítások titkosítás során váratlan hiba esetén lehetséges. A felügyelt lemezekkel rendelkező virtuális gépek biztonsági szükséges, a titkosítás előtt. A biztonsági mentés után a [set-AzVMDiskEncryptionExtension parancsmag](/powershell/module/az.compute/set-azvmdiskencryptionextension) használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. További információ a titkosított virtuális gépek biztonsági mentéséről és visszaállításáról: [titkosított Azure-beli virtuális gép biztonsági mentése és visszaállítása](../../backup/backup-azure-vms-encryption.md). 
>
> - A titkosítás titkosítása vagy letiltása miatt előfordulhat, hogy a virtuális gép újraindul.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>A Marketplace-ről létrehozott új IaaS virtuális gépek titkosításának engedélyezése
Disk encryption szolgáltatást új IaaS Windows virtuális gép a piactérről, az Azure Resource Manager-sablon használatával engedélyezheti. A sablon létrehoz egy új titkosított Windows virtuális gépet a Windows Server 2012 image z galerie.

1. Az a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image), kattintson a **üzembe helyezés az Azure**.

2. Válassza ki az előfizetést, erőforráscsoportot, erőforráscsoport helye, paraméterek, jogi feltételek és szerződés. Kattintson a **beszerzési** új IaaS virtuális gép üzembe helyezése, a titkosítás engedélyezve van.

3. Miután telepíti a sablont, ellenőrizze a virtuális gép titkosítási állapotát a kívánt módszer használatával:
     - Ellenőrizze az Azure CLI-vel a [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - A [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmag használatával ellenőrizze, hogy Azure PowerShell-e. 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Válassza ki a virtuális Gépet, majd kattintson a **lemezek** alatt a **beállítások** fejléc encryption státusz a portálon. A diagram alatt **titkosítási**, látni fogja, hogy annak engedélyezve van-e. 
           ![Azure Portal – a lemez titkosítása engedélyezve](../media/disk-encryption/disk-encryption-fig2.png)

A következő táblázat felsorolja az új virtuális gépekhez tartozó Resource Manager-sablon paramétereit az Azure AD ügyfél-azonosító használatával:

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


### <a name="bkmk_RunningWinVMPSH"></a> Engedélyezze a titkosítást a meglévő vagy a virtuális gép futtatása az Azure PowerShell használatával 
A [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban. Az Azure Disk Encryption titkosítás engedélyezése a PowerShell-parancsmagok használatával kapcsolatos információkért tekintse meg a blogbejegyzések [az Azure Disk Encryption megismerése az Azure PowerShell használatával – 1. rész](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) és [az Azure Disk Encryption megismerése az Azure PowerShell használatával – 2. rész](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx).

-  **Egy futó virtuális gép titkosítása az ügyfél titkos** kódjával: Az alábbi szkript inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, virtuális gép, a key vault, AAD-alkalmazás és titkos Ügyfélkód kell rendelkezik már létre van hozva előfeltételeket. Cserélje le a MyKeyVaultResourceGroup, a MyVirtualMachineResourceGroup, a MySecureVM, a MySecureVault, a My-HRE-Client-ID és a My-HRE-Client-Secret értékeket az értékekre.
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
- **A titkos ügyfélkulcsot burkolása KEK használatával futó virtuális gép titkosítása:** az Azure Disk Encryption lehetővé teszi, hogy Ön adja meg a meglévő kulcsot tárol a kulcstárolóban burkolása lemez titkosítási titkos kódok titkosításának engedélyezése során létrehozott. Amikor egy kulcsalapú titkosítás kulcsa van megadva, az Azure Disk Encryption a kulcs segítségével burkolhatja a titkosítási titkos kulcsait a Key Vault írása előtt. 

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
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának vizsgálatához használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Tiltsa le a lemeztitkosítás:** letiltja a titkosítást, használja a [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Engedélyezze a titkosítást a meglévő vagy a virtuális gép futtatása az Azure CLI-vel
Használja a [az vm encryption engedélyezése](/cli/azure/vm/encryption#az-vm-encryption-enable) parancsot az Azure-ban futó IaaS virtuális gép titkosításának engedélyezéséhez.

- **Ügyfél titkos kulcs használatával futó virtuális gépek titkosítása:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **A titkos ügyfélkulcsot burkolása KEK használatával futó virtuális gépek titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának megtekintéséhez használja az az [VM encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Tiltsa le a titkosítást:** titkosítás letiltásához használja a [letiltása az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="bkmk_RunningWinVMwRM"> </a>A Resource Manager-sablon használatával
Disk encryption szolgáltatást meglévő vagy IaaS Windows virtuális gépek futtatása az Azure-beli használatával engedélyezheti a [futó Windows virtuális gépek titkosításához a Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. Kattintson az Azure gyorsindítási sablon **üzembe helyezés az Azure**.

2. Válassza ki az előfizetést, erőforráscsoportot, erőforráscsoport helye, paraméterek, jogi feltételek és szerződés. Kattintson a **beszerzési** engedélyezheti a titkosítást a meglévő vagy futó IaaS virtuális gépen.

Az alábbi táblázat a meglévő vagy egy Azure AD ügyfél-Azonosítót használó virtuális gépek a Resource Manager sablon paramétereit:

| Paraméter | Leírás |
| --- | --- |
| AADClientID | Az Azure AD-alkalmazást, amely a key vault titkos kódok írási engedélyekkel rendelkezik ügyfél-azonosítója. |
| AADClientSecret | Az Azure AD-alkalmazást, amely a key vault titkos kódok írási engedélyekkel rendelkezik a titkos ügyfélkódja. |
| keyVaultName | A kulcstartóhoz, amely fel kell tölteni a BitLocker-kulcs neve. A parancsmag `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` vagy az Azure CLI-parancs használatával kérhető le `az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | A kulcsalapú titkosítás kulcsa, a létrehozott BitLocker-kulcs titkosítására használt URL-címe. Ez a paraméter nem kötelező, ha kiválasztja **nokek** a UseExistingKek legördülő listában. Ha **kek** a UseExistingKek legördülő listában, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| VolumeType | A titkosítási műveletet végzi el a kötet típusa. Érvényes értékek a következők _operációs rendszer_, _adatok_, és _összes_. |
| SequenceVersion | A BitLocker művelet verziója. Növelje a verziószámot, minden alkalommal, amikor a lemeztitkosítási műveletet végzi el az azonos virtuális gépen. |
| vmName | Neve a virtuális gép, amely a titkosítási műveletet kell végrehajtani. |


## <a name="bkmk_VHDpre"> </a>Ügyfél-titkosítású VHD-t és a titkosítási kulcsok alapján létrehozott új IaaS virtuális gépek
Ebben a forgatókönyvben engedélyezheti a Resource Manager-sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával titkosítja. A Resource Manager-sablon és a CLI-parancsokat az alábbi szakaszok részletesebben ismertetik. 

Kövesse az utasításokat a függelékben használható az Azure-ban előzetes titkosítással rendszerképek előkészítéséről. A lemezkép létrehozását követően használhatja a lépéseket a következő szakaszban titkosított Azure virtuális gép létrehozásához.

* [Előzetes titkosítással Windows virtuális merevlemez előkészítése](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="bkmk_VHDprePSH"> </a> Az Azure PowerShell használatával előre titkosított virtuális merevlemezzel rendelkező virtuális gépek titkosítása
A titkosított virtuális merevlemez titkosítását a [set-AzVMOSDisk PowerShell-](/powershell/module/az.compute/set-azvmosdisk#examples)parancsmag használatával engedélyezheti. Az alábbi példa néhány gyakori paramétereket biztosít. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott adatlemez
Is [adjon hozzá egy új lemezt egy Windows virtuális Gépet PowerShell-lel](attach-disk-ps.md), vagy [az Azure Portalon keresztül](attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott lemezt az Azure PowerShell használatával
 Amikor új lemez titkosítása Windows virtuális gépekhez a Powershell segítségével, meg kell adni egy új verziója. A feladatütemezési verziójában található egyedinek kell lennie. Az alábbi parancsfájlt előállít egy GUID Azonosítót a verziója. Bizonyos esetekben egy újonnan hozzáadott adatlemez előfordulhat, hogy automatikusan titkosítja az Azure Disk Encryption bővítmény által. Automatikus titkosítás általában akkor fordul elő, amikor a virtuális gép újraindul, miután az új lemezt online állapotba kerül. Ezt általában a hibát, mert az "Összes" van megadva a kötet típusa, ha korábban már futott lemeztitkosítás a virtuális gépen. Ha az automatikus titkosítás egy újonnan hozzáadott adatlemezen történik, javasoljuk, hogy a set-AzVmDiskEncryptionExtension parancsmagot újra futtassa új sorozat-verzióval. Az új adatlemez automatikusan titkosítva, és nem szeretne titkosítását, ha az összes meghajtó először visszafejtéséhez, majd újra titkosítja a az új feladatütemezés az operációs rendszer a kötet típusának megadása. 
 

-  **Egy futó virtuális gép titkosítása az ügyfél titkos** kódjával: Az alábbi szkript inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, virtuális gép, a key vault, AAD-alkalmazás és titkos Ügyfélkód kell rendelkezik már létre van hozva előfeltételeket. Cserélje le a MyKeyVaultResourceGroup, a MyVirtualMachineResourceGroup, a MySecureVM, a MySecureVault, a My-HRE-Client-ID és a My-HRE-Client-Secret értékeket az értékekre. Ebben a példában "Összes", amely tartalmazza az operációs rendszer és a Data - VolumeType paraméterhez. Ha csak szeretné az operációs rendszer kötetének titkosításához, használja a "OS" - VolumeType paraméterhez. 

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
- **A titkos ügyfélkulcsot burkolása KEK használatával futó virtuális gép titkosítása:** az Azure Disk Encryption lehetővé teszi, hogy Ön adja meg a meglévő kulcsot tárol a kulcstárolóban burkolása lemez titkosítási titkos kódok titkosításának engedélyezése során létrehozott. Amikor egy kulcsalapú titkosítás kulcsa van megadva, az Azure Disk Encryption a kulcs segítségével burkolhatja a titkosítási titkos kulcsait a Key Vault írása előtt. Ebben a példában "Összes", amely tartalmazza az operációs rendszer és a Data - VolumeType paraméterhez. Ha csak szeretné az operációs rendszer kötetének titkosításához, használja a "OS" - VolumeType paraméterhez. 

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
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

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
Ügyféltanúsítvány-alapú hitelesítés is használhatja, vagy a KEK nélkül. Mielőtt a PowerShell-parancsfájlokat használ, már a tanúsítványt a key vault feltöltött és a virtuális Gépre telepített. Ha túl KEK használ, a KEK már léteznie kell. További információkért lásd: a [az Azure ad-alapú hitelesítés](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) az Előfeltételek című szakaszban.


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
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

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

- **Letiltja a lemeztitkosítást, az Azure PowerShell használatával:** letiltja a titkosítást, használja a [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Tiltsa le a titkosítást az Azure CLI-vel:** titkosítás letiltásához használja a [letiltása az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Tiltsa le a Resource Manager-sablonnal titkosítást:** 

    1. Kattintson a **üzembe helyezés az Azure** származó a [letiltja a lemeztitkosítást a Windows rendszerű virtuális gépek futtatásához](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) sablont.
    2. Válassza ki az előfizetés, erőforráscsoport, hely, virtuális gép, jogi feltételeket és szerződés.
    3.  Kattintson a **beszerzési** letiltja a lemeztitkosítást a futó Windows virtuális gépek. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Disk Encryption áttekintése](disk-encryption-overview.md)

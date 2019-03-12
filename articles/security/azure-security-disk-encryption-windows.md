---
title: A Windows IaaS virtuális gépek az Azure Disk Encryption engedélyezése
description: Ez a cikk útmutatást nyújt a Microsoft Azure lemez titkosítása a Windows IaaS virtuális gépek engedélyezése.
author: mestew
ms.service: security
ms.topic: article
ms.author: mstewart
ms.date: 03/05/2019
ms.custom: seodec18
ms.openlocfilehash: ff0b8c3ec5cd259d96a0761e287e5ad95feb26b1
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57774744"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>A Windows IaaS virtuális gépek az Azure Disk Encryption engedélyezése

Engedélyezheti a lemeztitkosítási számos forgatókönyv, és a lépések eltérőek lehetnek a forgatókönyv szerint. Az alábbi szakaszok részletesebben a forgatókönyvek Windows IaaS virtuális gépekhez terjed ki. Mielőtt használhatná a lemeztitkosítást, a [az Azure Disk Encryption előfeltétel](../security/azure-security-disk-encryption-prerequisites.md) kell elvégezni. 

Igénybe vehet egy [pillanatkép](../virtual-machines/windows/snapshot-copy-managed-disk.md) és/vagy lemezek előtt készítsen biztonsági másolatot. Biztonsági mentések ellenőrizze, hogy a helyreállítási beállítások titkosítás során váratlan hiba esetén lehetséges. A felügyelt lemezekkel rendelkező virtuális gépek biztonsági szükséges, a titkosítás előtt. Miután biztonsági másolatból történik, a Set-AzVMDiskEncryptionExtension parancsmag segítségével felügyelt lemezek titkosítása a - skipVmBackup paraméter megadásával. Biztonsági mentése és visszaállítása titkosított virtuális gépek kapcsolatos további információkért lásd: a [Azure Backup](../backup/backup-azure-vms-encryption.md) cikk. 

>[!WARNING]
> - Ha korábban már használt [az Azure Disk Encryption az Azure AD-alkalmazás](azure-security-disk-encryption-prerequisites-aad.md) Ez a virtuális gép titkosítására, kell továbbra is használja ezt a beállítást a virtuális gép titkosítására. Nem használhat [az Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) a titkosított virtuális gépen, ez nem támogatott forgatókönyv, azaz a átváltani AAD-alkalmazás számára a titkosított virtuális gép nem támogatott még. 
> - Az Azure Disk Encryption van szüksége a Key Vault és a virtuális gépek közös elhelyezése ugyanabban a régióban. Létrehozhat és használhat egy Key Vaultot, amely ugyanabban a régióban, mint a virtuális gép titkosítását. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningWinVM"></a> Engedélyezze a titkosítást a meglévő vagy IaaS Windows rendszerű virtuális gépek futtatása
Ebben a forgatókönyvben egy sablont, a PowerShell-parancsmagokkal vagy a CLI-parancsok használatával engedélyezheti a titkosítást. Az alábbi szakaszok részletesebben ismertetik az Azure Disk Encryption engedélyezése. Ha a virtuálisgép-bővítmény séma információra van szüksége, tekintse meg a [Azure Disk Encryption a Windows-bővítmény](../virtual-machines/extensions/azure-disk-enc-windows.md) cikk.

>[!IMPORTANT]
 >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt. A felügyelt lemez pillanatképét elvégezhet a portálról, vagy [Azure Backup](../backup/backup-azure-vms-encryption.md) is használható. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. Miután biztonsági másolatból történik, a Set-AzVMDiskEncryptionExtension parancsmag segítségével felügyelt lemezek titkosítása a - skipVmBackup paraméter megadásával. A Set-AzVMDiskEncryptionExtension parancs felügyelt lemezes virtuális gépek elleni sikertelen lesz, amíg a biztonsági másolat lett végrehajtva, és ez a paraméter lett megadva. 
>
>Titkosítása vagy letiltja a titkosítást a virtuális gép újraindítását okozhatja. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Engedélyezze a titkosítást a meglévő vagy a virtuális gép futtatása az Azure PowerShell használatával 
Használja a [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag, amellyel engedélyezheti a titkosítást az Azure-ban futó IaaS virtuális gép. 

-  **Egy futó virtuális gép titkosítása:** Az alábbi parancsfájlt inicializálja a változók és a Set-AzVMDiskEncryptionExtension parancsmagot futtatja. Az erőforráscsoport, a virtuális gép és a key vault kell rendelkezik már létre van hozva előfeltételeket. MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM és MySecureVault cserélje le az értékeket.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Futó virtuális gépek KEK használatával titkosítása:** 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek:** IaaS virtuális gép titkosítási állapotának ellenőrzéséhez használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Lemeztitkosítás letiltása:** A titkosítás letiltásához használja a [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. Lemez adattitkosítás a Windows virtuális gép letiltása, ha mind az operációs rendszer és az adatlemezek titkosítottak nem a várt módon működik. Inkább tiltsa le a titkosítást az összes lemez.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Engedélyezze a titkosítást a meglévő vagy a virtuális gép futtatása az Azure CLI-vel
Használja a [az vm encryption engedélyezése](/cli/azure/vm/encryption#az-vm-encryption-enable) parancsot az Azure-ban futó IaaS virtuális gép titkosításának engedélyezéséhez.

-  **Egy futó virtuális gép titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Futó virtuális gépek KEK használatával titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek:** IaaS virtuális gép titkosítási állapotának ellenőrzéséhez használja a [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Tiltsa le a titkosítást:** Titkosítás letiltásához használja a [letiltása az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. Lemez adattitkosítás a Windows virtuális gép letiltása, ha mind az operációs rendszer és az adatlemezek titkosítottak nem a várt módon működik. Inkább tiltsa le a titkosítást az összes lemez.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
 
 > [!NOTE]
 >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt. A felügyelt lemez pillanatképét elvégezhet a portálról, vagy [Azure Backup](../backup/backup-azure-vms-encryption.md) is használható. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. Miután biztonsági másolatból történik, a Set-AzVMDiskEncryptionExtension parancsmag segítségével felügyelt lemezek titkosítása a - skipVmBackup paraméter megadásával. Ez a parancs-alapú felügyelt lemezes virtuális gépek elleni sikertelen lesz, amíg a biztonsági másolat lett végrehajtva, és ez a paraméter lett megadva. 
>
>Titkosítása vagy letiltja a titkosítást a virtuális gép újraindítását okozhatja. 

### <a name="bkmk_RunningWinVMwRM"> </a>A Resource Manager-sablon használatával
Disk encryption szolgáltatást meglévő vagy IaaS Windows virtuális gépek futtatása az Azure-beli használatával engedélyezheti a [futó Windows virtuális gépek titkosításához a Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. Kattintson az Azure gyorsindítási sablon **üzembe helyezés az Azure**.

2. Válassza ki az előfizetés, erőforráscsoport, hely, beállítások, jogi feltételeket és szerződés. Kattintson a **beszerzési** engedélyezheti a titkosítást a meglévő vagy futó IaaS virtuális gépen.

Az alábbi táblázat a meglévő vagy a virtuális gépek Resource Manager sablon paramétereit:

| Paraméter | Leírás |
| --- | --- |
| vmName | A titkosítási művelet végrehajtásához a virtuális gép nevét. |
| keyVaultName | A kulcstartóhoz, amely fel kell tölteni a BitLocker-kulcs neve. A parancsmag segítségével beszerezheti azt `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` vagy az Azure CLI-paranccsal "az keyvault list--resource-group"MyKeyVaultResourceGroup" |ConvertFrom-JSON "|
| keyVaultResourceGroup | Az erőforráscsoport, amely tartalmazza a key vault neve|
|  keyEncryptionKeyURL | A kulcsalapú titkosítás kulcsa, a létrehozott BitLocker-kulcs titkosítására használt URL-címe. Ez a paraméter nem kötelező, ha kiválasztja **nokek** a UseExistingKek legördülő listában. Ha **kek** a UseExistingKek legördülő listában, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| VolumeType | A titkosítási műveletet végzi el a kötet típusa. Érvényes értékek a következők _operációs rendszer_, _adatok_, és _összes_. 
| forceUpdateTag | Minden alkalommal, amikor a művelet van szüksége lehet kényszerítéséhez futtassa adja át egy egyedi értéket, például egy GUID Azonosítót. |
| resizeOSDisk | Az operációs rendszer partíció a teljes rendszer virtuális Merevlemeze foglalnak rendszerkötet felosztása előtt lehet átméretezni. |
| location | Minden erőforrás helye. |

## <a name="encrypt-virtual-machine-scale-sets"></a>A virtual machine scale sets titkosítása

[Azure-beli virtuálisgép-méretezési csoportok](../virtual-machine-scale-sets/overview.md) létrehozása és kezelése egy csoportot az azonos, elosztott terhelésű virtuális gépek segítségével. A virtuálisgép-példányok száma automatikusan növelhető vagy csökkenthető a pillanatnyi igényeknek megfelelően vagy egy meghatározott ütemezés szerint. A parancssori felület vagy az Azure PowerShell használatával a virtual machine scale sets titkosítása.


### <a name="register-for-disk-encryption-preview-using-azure-powershell"></a>Regisztráljon az Azure Powershell-lel a lemez titkosítási előzetes verzió

Az Azure disk encryption virtuálisgép-méretezési csoportokhoz előzetes kell önálló regisztrálja az előfizetését [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature). Csak akkor kell hajtsa végre a következő lépéseket a lemeztitkosítási előzetes szolgáltatás első használatakor:

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

A regisztrációs kérelem propagálása akár 10 percet is igénybe vehet. A regisztrációs állapot ellenőrzéséhez [Get-AzProviderFeature](/powershell/module/az.Resources/Get-azProviderFeature). Ha a `RegistrationState` jelentések *regisztrált*, regisztrálja újra a *Microsoft.Compute* szolgáltató [Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider):

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Virtuálisgép-méretezési csoportok az Azure PowerShell használatával titkosítása

Használja a [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/set-azvmssdiskencryptionextension) parancsmag, amellyel engedélyezheti a titkosítást egy Windows virtuálisgép-méretezési csoportot. Az erőforráscsoport, a virtuális gép és a key vault kell rendelkezik már létre van hozva előfeltételeket.

-  **A futó virtuálisgép-méretezési titkosítása**:
    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGame -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:

    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $KeyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     Set-AzRmVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGame -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $KeyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

   >[!NOTE]
   > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Adattitkosítás állapotának lekérése egy virtuálisgép-méretezési csoportot:** Használja a [Get-AzVmssDiskEncryption](/powershell/module/az.compute/get-azvmssdiskencryption) parancsmagot.
    
    ```azurepowershell-interactive
    get-AzVmssVMDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

- **Egy virtuálisgép-méretezési csoportot titkosításának letiltása**: Használja a [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/disable-azvmssdiskencryption) parancsmagot. 

    ```azurepowershell-interactive
    Disable-AzVmssDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

### <a name="register-for-disk-encryption-preview-using-azure-cli"></a>Regisztráljon az Azure CLI-vel a lemez titkosítási előzetes verzió

Az Azure disk encryption virtuálisgép-méretezési csoportokhoz előzetes kell önálló regisztrálja az előfizetését [az a funkció regisztrálása](/cli/azure/feature#az-feature-register). Csak akkor kell hajtsa végre a következő lépéseket a lemeztitkosítási előzetes szolgáltatás első használatakor:

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

A regisztrációs kérelem propagálása akár 10 percet is igénybe vehet. A regisztrációs állapot ellenőrzéséhez [az funkció show](/cli/azure/feature#az-feature-show). Ha a `State` jelentések *regisztrált*, regisztrálja újra a *Microsoft.Compute* szolgáltató [az provider register](/cli/azure/provider#az-provider-register):

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```



###  <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Virtuális gép méretezési csoportok az Azure CLI-vel titkosítása

Használja a [az vmss-titkosítás engedélyezése](/cli/azure/vmss/encryption#az-vmss-encryption-enable) titkosításának egy Windows virtuálisgép-méretezési csoportot. Ha a házirend a méretezési csoport manuális állít be, indítsa el a titkosítást, [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). Az erőforráscsoport, a virtuális gép és a key vault kell rendelkezik már létre van hozva előfeltételeket.

-  **A futó virtuálisgép-méretezési titkosítása**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **Egy futó virtuális gép méretezési használatával KEK burkolása a kulcs titkosítása**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
     
   >[!NOTE]
   > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Adattitkosítás állapotának lekérése egy virtuálisgép-méretezési csoportot:** Használat [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
     az vmss encryption show --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

- **Egy virtuálisgép-méretezési csoportot titkosításának letiltása**: Használat [az vmss titkosítás letiltása](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-windows-virtual-machine-scale-sets"></a>Az Azure Resource Manager-sablonokkal Windows virtuálisgép-méretezési csoportok

Titkosítása és visszafejtése Windows virtuálisgép-méretezési csoportok, az Azure Resource Manager-sablonokkal és az alábbi utasításokat:

- [Engedélyezheti a titkosítást egy Windows virtuálisgép-méretezési csoportot](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)
- [Virtuálisgép-méretezési készlet Windows virtuális gépek a jumpbox üzembe, és engedélyezheti a titkosítást a Windows Virtuálisgép-méretezési csoportot](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)
- [Windows Virtuálisgép-méretezési készlet titkosításának letiltása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

     1. Kattintson a **Deploy to Azure** (Üzembe helyezés az Azure-ban) elemre.
     2. Töltse ki a kötelező mezőket, majd fogadja el a feltételeket és kikötéseket.
     3. Kattintson a **beszerzési** a sablon üzembe helyezéséhez.

## <a name="bkmk_VHDpre"> </a> Ügyfél-titkosítású VHD-t és a titkosítási kulcsok alapján létrehozott új IaaS virtuális gépek

Ebben a forgatókönyvben engedélyezheti a PowerShell-parancsmagokkal vagy a CLI-parancsok használatával titkosítja. 

Kövesse az utasításokat a függelékben használható az Azure-ban előzetes titkosítással rendszerképek előkészítéséről. A lemezkép létrehozását követően használhatja a lépéseket a következő szakaszban titkosított Azure virtuális gép létrehozásához.

* [Előzetes titkosítással Windows virtuális merevlemez előkészítése](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Előzetes titkosítással Linux rendszerű virtuális merevlemez előkészítése](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt. A felügyelt lemez pillanatképét elvégezhet a portálról, vagy [Azure Backup](../backup/backup-azure-vms-encryption.md) is használható. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. Miután biztonsági másolatból történik, a Set-AzVMDiskEncryptionExtension parancsmag segítségével felügyelt lemezek titkosítása a - skipVmBackup paraméter megadásával. A Set-AzVMDiskEncryptionExtension parancs felügyelt lemezes virtuális gépek elleni sikertelen lesz, amíg a biztonsági másolat lett végrehajtva, és ez a paraméter lett megadva. 
>
>Titkosítása vagy letiltja a titkosítást a virtuális gép újraindítását okozhatja. 


### <a name="bkmk_VHDprePSH"> </a> Az Azure PowerShell használatával előre titkosított virtuális merevlemezzel rendelkező virtuális gépek titkosítása
A PowerShell-parancsmag használatával a titkosított virtuális merevlemezen lemeztitkosítás is engedélyeznie [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples). Az alábbi példa néhány gyakori paramétereket biztosít. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott adatlemez
Is [adjon hozzá egy új lemezt egy Windows virtuális Gépet PowerShell-lel](../virtual-machines/windows/attach-disk-ps.md), vagy [az Azure Portalon keresztül](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott lemezt az Azure PowerShell használatával
 Amikor új lemez titkosítása Windows virtuális gépekhez a Powershell segítségével, meg kell adni egy új verziója. A feladatütemezési verziójában található egyedinek kell lennie. Az alábbi parancsfájlt előállít egy GUID Azonosítót a verziója. Bizonyos esetekben egy újonnan hozzáadott adatlemez előfordulhat, hogy automatikusan titkosítja az Azure Disk Encryption bővítmény által. Automatikus titkosítás általában akkor fordul elő, amikor a virtuális gép újraindul, miután az új lemezt online állapotba kerül. Ezt általában a hibát, mert az "Összes" van megadva a kötet típusa, ha korábban már futott lemeztitkosítás a virtuális gépen. Automatikus titkosítás az újonnan hozzáadott adatlemez esetén javasoljuk a Set-AzVmDiskEncryptionExtension parancsmag újra futtatni az új verziója. Az új adatlemez automatikusan titkosítva, és nem szeretne titkosítását, ha az összes meghajtó először visszafejtéséhez, majd újra titkosítja a az új feladatütemezés az operációs rendszer a kötet típusának megadása. 
  
 

-  **Egy futó virtuális gép titkosítása:** Az alábbi parancsfájlt inicializálja a változók és a Set-AzVMDiskEncryptionExtension parancsmagot futtatja. Az erőforráscsoport, a virtuális gép és a key vault kell rendelkezik már létre van hozva előfeltételeket. MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM és MySecureVault cserélje le az értékeket. Ebben a példában "Összes", amely tartalmazza az operációs rendszer és a Data - VolumeType paraméterhez. Ha csak szeretné az operációs rendszer kötetének titkosításához, használja a "OS" - VolumeType paraméterhez. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Futó virtuális gépek KEK használatával titkosítása:** Ebben a példában "Összes", amely tartalmazza az operációs rendszer és a Data - VolumeType paraméterhez. Ha csak szeretné az operációs rendszer kötetének titkosításához, használja a "OS" - VolumeType paraméterhez.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott lemezt az Azure CLI-vel
 Az Azure CLI-paranccsal automatikusan megad egy új verziója, engedélyezze a titkosítást a parancs futtatásakor. A példában "Összes" a kötet-típus paraméter. Szükség lehet a kötet-típus paraméter módosítani az operációs rendszer, ha az operációsrendszer-lemez csak amelyet épp titkosít. Powershell-szintaxis szakembereket a CLI nem igényel egy egyedi verziója szolgáltatni a titkosítás engedélyezése a felhasználó. A CLI automatikusan hoz létre, és használja a saját egyedi feladatütemezési version értéke.   

-  **Egy futó virtuális gép titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Futó virtuális gépek KEK használatával titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Tiltsa le a titkosítást
Letilthatja a titkosítás az Azure PowerShell, az Azure CLI használatával vagy a Resource Manager-sablonnal. Lemez adattitkosítás a Windows virtuális gép letiltása, ha mind az operációs rendszer és az adatlemezek titkosítottak nem a várt módon működik. Inkább tiltsa le a titkosítást az összes lemez.

- **Tiltsa le a lemeztitkosítást, az Azure PowerShell használatával:** A titkosítás letiltásához használja a [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Tiltsa le a titkosítást az Azure CLI-vel:** Titkosítás letiltásához használja a [letiltása az vm encryption](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Tiltsa le a Resource Manager-sablonnal titkosítást:** 

    1. Kattintson a **üzembe helyezés az Azure** származó a [letiltja a lemeztitkosítást a Windows rendszerű virtuális gépek futtatásához](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) sablont.
    2. Válassza ki az előfizetés, erőforráscsoport, hely, virtuális gép, kötettípus, jogi feltételeket és szerződés.
    3.  Kattintson a **beszerzési** letiltja a lemeztitkosítást a futó Windows virtuális gépek. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Linuxhoz készült Azure Disk Encryption engedélyezése](azure-security-disk-encryption-linux.md)

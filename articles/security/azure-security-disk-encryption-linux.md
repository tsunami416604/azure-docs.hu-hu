---
title: Linux rendszerű IaaS virtuális gépekhez az Azure Disk Encryption engedélyezése
description: Ez a cikk útmutatást nyújt a Microsoft Azure Disk Encryption engedélyezése a Linux rendszerű IaaS virtuális gépekhez.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 09/16/2019
ms.custom: seodec18
ms.openlocfilehash: 03d50fbd9c3138f4d34dd748da50faefc3d8b24d
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067830"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms"></a>Linux rendszerű IaaS virtuális gépekhez az Azure Disk Encryption engedélyezése 

Engedélyezheti a lemeztitkosítási számos forgatókönyv, és a lépések eltérőek lehetnek a forgatókönyv szerint. Az alábbi szakaszok részletesebben a forgatókönyvek Linux rendszerű IaaS virtuális gépek terjed ki. A lemez titkosításának használata előtt meg kell adni a [Azure Disk Encryption előfeltételeket](azure-security-disk-encryption-prerequisites.md) , és a [Linux IaaS virtuális gépekre vonatkozó további előfeltételeket](azure-security-disk-encryption-prerequisites.md#additional-prerequisites-for-linux-iaas-vms) meg kell vizsgálni.

Igénybe vehet egy [pillanatkép](../virtual-machines/windows/snapshot-copy-managed-disk.md) és/vagy lemezek előtt készítsen biztonsági másolatot. Biztonsági mentések ellenőrizze, hogy a helyreállítási beállítások titkosítás során váratlan hiba esetén lehetséges. A felügyelt lemezekkel rendelkező virtuális gépek biztonsági szükséges, a titkosítás előtt. A biztonsági mentés után a set-AzVMDiskEncryptionExtension parancsmag használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. Biztonsági mentése és visszaállítása titkosított virtuális gépek kapcsolatos további információkért lásd: a [Azure Backup](../backup/backup-azure-vms-encryption.md) cikk. 

>[!WARNING]
> - Ha korábban már használta [Azure Disk Encryption az Azure ad-alkalmazással](azure-security-disk-encryption-prerequisites-aad.md) a virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Ezen a titkosított virtuális gépen nem használható [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) , mivel ez nem támogatott forgatókönyv, ami azt jelenti, hogy a titkosított virtuális gép esetében a HRE alkalmazásból való átállás még nem támogatott.
 > - Az Azure Disk Encryption van szüksége a Key Vault és a virtuális gépek közös elhelyezése ugyanabban a régióban. Létrehozhat és használhat egy Key Vaultot, amely ugyanabban a régióban, mint a virtuális gép titkosítását.
> - A Linux operációs rendszer köteteinek titkosításakor a virtuális gépet nem szabad megfontolni. Határozottan javasoljuk, hogy az SSH-bejelentkezések elkerülése érdekében a titkosítási folyamat során ne akadályozza meg az összes olyan megnyitott fájl blokkolását, amelyet a titkosítási folyamat során el kell érnie. A folyamat ellenőrzéséhez használhatja a [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) vagy a [VM encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsait. Ez a folyamat várhatóan néhány órát is igénybe vehet egy 30 GB-OS kötetre, és további időt az adatkötetek titkosítására. Az adatmennyiség-titkosítási idő arányos lesz az adatkötetek méretétől és mennyiségétől, kivéve, ha az összes titkosítás formátuma beállítást használja. 
> - Linux rendszerű virtuális gépek titkosításának letiltása csak az adatkötetek esetében támogatott. Nem támogatott a adatok vagy operációsrendszer-kötettel, ha az operációsrendszer-kötet titkosított.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningLinux"> </a> Engedélyezze a titkosítást egy meglévő vagy futó IaaS Linux rendszerű virtuális gépen
Ebben a forgatókönyvben a Resource Manager-sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával engedélyezheti a titkosítást. Ha a virtuálisgép-bővítmény séma információra van szüksége, tekintse meg a [az Azure Disk Encryption bővítmény Linux](../virtual-machines/extensions/azure-disk-enc-linux.md) cikk.

>[!IMPORTANT]
 >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt. A felügyelt lemez pillanatképét elvégezhet a portálról, vagy [Azure Backup](../backup/backup-azure-vms-encryption.md) is használható. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. A biztonsági mentést követően a set-AzVMDiskEncryptionExtension parancsmag használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. A set-AzVMDiskEncryptionExtension parancs a felügyelt lemezes virtuális gépektől a biztonsági mentésig meghiúsul, és ez a paraméter meg lett adva. 
>
>Titkosítása vagy letiltja a titkosítást a virtuális gép újraindítását okozhatja. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Engedélyezze a titkosítást egy meglévő vagy futó Linux rendszerű virtuális gépen az Azure CLI használatával 
Engedélyezheti lemeztitkosítás a titkosított virtuális merevlemezen történő telepítéssel és a [Azure CLI 2.0](/cli/azure) parancssori eszköz. Használhatja a böngészőjében az [Azure Cloud Shell-lel](../cloud-shell/overview.md), vagy telepítheti a helyi gépen, és használhatja bármely PowerShell-munkamenetben. Meglévő, vagy Azure-ban futó IaaS Linux rendszerű virtuális gépek titkosításának engedélyezéséhez használja a következő CLI-parancsokat:

Használja a [az vm encryption engedélyezése](/cli/azure/vm/encryption#az-vm-encryption-enable) parancsot az Azure-ban futó IaaS virtuális gép titkosításának engedélyezéséhez.

- **Egy futó virtuális gép titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Futó virtuális gépek KEK használatával titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
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
A [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban. Készítsen [pillanatképet](../virtual-machines/windows/snapshot-copy-managed-disk.md) és/vagy készítsen biztonsági másolatot a virtuális gépről [Azure Backup](../backup/backup-azure-vms-encryption.md) a lemezek titkosítása előtt. A-skipVmBackup paraméter már meg van adva a PowerShell-parancsfájlokban egy futó Linux rendszerű virtuális gép titkosításához.

-  **Egy futó virtuális gép titkosítása:** Az alábbi szkript inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép és a key vault kell rendelkezik már létre van hozva előfeltételeket. Cserélje le az MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értékeket az értékekre. Módosítsa a-VolumeType paramétert annak megadásához, hogy mely lemezek vannak titkosítva.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Futó virtuális gépek KEK használatával titkosítása:** Szükség lehet a – VolumeType paraméter hozzáadása, ha épp titkosít, adatlemez, és nem az operációsrendszer-lemez. 

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának vizsgálatához használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Lemez titkosításának letiltása:** A titkosítás letiltásához használja a [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. Letiltja a titkosítást csak engedélyezett megváltoztatását az adatköteteken Linux rendszerű virtuális gépekhez.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Engedélyezheti a titkosítást egy meglévő vagy futó IaaS Linux rendszerű virtuális gép sablon alapján

Lemeztitkosítás IaaS virtuális gépen meglévő vagy futó Linux rendszerű Azure-beli használatával engedélyezheti a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Kattintson a **üzembe helyezés az Azure** Azure gyors üzembe helyezés a sablonban.

2. Válassza ki az előfizetést, erőforráscsoportot, erőforráscsoport helye, paraméterek, jogi feltételek és szerződés. Kattintson a **létrehozás** engedélyezheti a titkosítást a meglévő vagy futó IaaS virtuális gépen.

Az alábbi táblázat a meglévő vagy a virtuális gépek Resource Manager sablon paraméterei:

| Paraméter | Leírás |
| --- | --- |
| vmName | A titkosítási művelet végrehajtásához a virtuális gép nevét. |
| keyVaultName | A kulcstartóhoz, amely fel kell tölteni a BitLocker-kulcs neve. A parancsmag `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` vagy az Azure CLI-parancs használatával kérheti le.`az keyvault list --resource-group "MyKeyVaultResourceGroupName"`|
| keyVaultResourceGroup | Az erőforráscsoport, amely tartalmazza a key vault neve|
|  keyEncryptionKeyURL | A kulcsalapú titkosítás kulcsa, a létrehozott BitLocker-kulcs titkosítására használt URL-címe. Ez a paraméter nem kötelező, ha kiválasztja **nokek** a UseExistingKek legördülő listában. Ha **kek** a UseExistingKek legördülő listában, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| VolumeType | A titkosítási műveletet végzi el a kötet típusa. Érvényes értékek a következők _operációs rendszer_, _adatok_, és _összes_. 
| forceUpdateTag | Minden alkalommal, amikor a művelet van szüksége lehet kényszerítéséhez futtassa adja át egy egyedi értéket, például egy GUID Azonosítót. |
| resizeOSDisk | Az operációs rendszer partíció a teljes rendszer virtuális Merevlemeze foglalnak rendszerkötet felosztása előtt lehet átméretezni. |
| location | Minden erőforrás helye. |



## <a name="encrypt-virtual-machine-scale-sets"></a>A virtual machine scale sets titkosítása
[Azure-beli virtuálisgép-méretezési csoportok](../virtual-machine-scale-sets/overview.md) létrehozása és kezelése egy csoportot az azonos, elosztott terhelésű virtuális gépek segítségével. A virtuálisgép-példányok száma automatikusan növelhető vagy csökkenthető a pillanatnyi igényeknek megfelelően vagy egy meghatározott ütemezés szerint. A parancssori felület vagy az Azure PowerShell használatával a virtual machine scale sets titkosítása. A Linux méretezési csoport virtuális gépei esetében csak az adatlemezek titkosítása támogatott.

Egy Linux rendszerű méretezési csoport adatok lemeztitkosítás batch fájl példa található [Itt](https://github.com/Azure-Samples/azure-cli-samples/tree/master/disk-encryption/vmss). Ebben a példában egy erőforráscsoportot, a Linux rendszerű méretezési csoportot hoz létre, 5 GB-os adatlemezt csatlakoztat és titkosítja a virtuálisgép-méretezési csoportot.

### <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Virtuális gép méretezési csoportok az Azure CLI-vel titkosítása

Használja a [az vmss-titkosítás engedélyezése](/cli/azure/vmss/encryption#az-vmss-encryption-enable) titkosításának egy Windows virtuálisgép-méretezési csoportot. Ha a házirend a méretezési csoport manuális állít be, indítsa el a titkosítást, [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). Az erőforráscsoport, a virtuális gép és a key vault kell rendelkezik már létre van hozva előfeltételeket. 

-  **A futó virtuálisgép-méretezési titkosítása**
    ```azurecli-interactive
    az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --volume-type DATA --disk-encryption-keyvault "MySecureVault"
    ```

-  **Egy futó virtuális gép méretezési használatával KEK burkolása a kulcs titkosítása**
     ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --volume-type DATA --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault"
     ```

    >[!NOTE]
    > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Virtuálisgép-méretezési csoport titkosítási állapotának beolvasása:** Használat [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
    az vmss encryption show --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

- **Virtuálisgép-méretezési csoport titkosításának letiltása**: Használat [az vmss encryption disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
    az vmss encryption disable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Virtuálisgép-méretezési csoportok az Azure PowerShell használatával titkosítása

A [set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/set-azvmssdiskencryptionextension) parancsmag használatával engedélyezheti a titkosítást egy Windows rendszerű virtuálisgép-méretezési csoporton. Az erőforráscsoport, a virtuális gép és a key vault kell rendelkezik már létre van hozva előfeltételeket.

-  **A futó virtuálisgép-méretezési titkosítása**:
      ```powershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
      $VmssName = "MySecureVmss";
      $KeyVaultName= "MySecureVault";
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -VolumeType Data -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
      ```

-  **Egy futó virtuálisgép-méretezési csoport titkosítása a KEK használatával a kulcs becsomagolásához**:
      ```powershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
      $VmssName = "MySecureVmss";
      $KeyVaultName= "MySecureVault";
      $keyEncryptionKeyName = "MyKeyEncryptionKey";
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $KeyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -VolumeType Data -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl  -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $KeyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
      ```

    >[!NOTE]
    > A lemez-titkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Virtuálisgép-készlet titkosítási állapotának beolvasása**: Használja a [Get-AzVmssVMDiskEncryption](/powershell/module/az.compute/get-azvmssvmdiskencryption) parancsmagot.
    
    ```powershell
    Get-AzVmssVMDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

- **Virtuálisgép-méretezési csoport titkosításának letiltása**: Használja a [disable-AzVmssDiskEncryption](/powershell/module/az.compute/disable-azvmssdiskencryption) parancsmagot. 

    ```powershell
    Disable-AzVmssDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-linux-virtual-machine-scale-sets"></a>Azure Resource Manager sablonok linuxos virtuálisgép-méretezési csoportokhoz

A Linux rendszerű virtuálisgép-méretezési csoportok titkosításához vagy visszafejtéséhez használja az alábbi Azure Resource Manager sablonokat és utasításokat:

- [Titkosítás engedélyezése linuxos virtuálisgép-méretezési csoporton](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)
- [Titkosítás letiltása Linux rendszerű virtuálisgép-méretezési csoportokon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

     1. Kattintson a **Deploy to Azure** (Üzembe helyezés az Azure-ban) elemre.
     2. Adja meg a kötelező mezőket, majd fogadja el a feltételeket és a kikötéseket.
     3. A sablon üzembe helyezéséhez kattintson a **vásárlás** elemre.

## <a name="bkmk_EFA"> </a>A Linux rendszerű IaaS virtuális gépek adatlemezét EncryptFormatAll szolgáltatással

A **EncryptFormatAll** paraméter lerövidíti a Linux data lemezek titkosítását. Partíciók megfelelnek bizonyos feltételeknek lesz formázva (az aktuális fájlrendszer). Majd azok lesz újra kell csatlakoztatni vissza a hol történt a parancs végrehajtása előtt. Ha kizár egy olyan adatok lemezt, amely megfelel a feltételeknek, válassza le azt a parancs futtatása előtt.

 Ez a parancs futtatása után, amely csatlakoztatva voltak meghajtóknak korábban lesz formázva. Ezután a titkosítási réteget a már üres meghajtó felett indul. Ha ezt a lehetőséget választja, a rövid élettartamú erőforrás lemezt a virtuális Géphez csatolt is titkosítva lesznek. A rövid élettartamú meghajtó alaphelyzetbe áll, ha újraformázta és újra titkosítja a virtuális gép az Azure Disk Encryption megoldás a következő alkalommal. Az erőforrás-lemez titkosítása után a [Microsoft Azure Linux-ügynök](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) nem fogja tudni kezelni az erőforrás lemezét, és engedélyezi a lapozófájlt, de manuálisan is konfigurálhatja a lapozófájlt.

>[!WARNING]
> EncryptFormatAll nem használható, ha egy virtuális Gépet az adatkötetek szükséges adatokat. Lemezek kizárhatják titkosítás, ha shellről őket. Meg kell megismerheti a szolgáltatás paraméter és a utalás előtt próbálja ki az éles virtuális gép a, próbálja ki az először a tesztelési virtuális gép EncryptFormatAll először. A EncryptFormatAll beállítás formázza az adatlemezt, és a rajta lévő adatok elvesznek. A folytatás előtt győződjön meg arról, hogy ki szeretne lemezek megfelelően le-e. </br></br>
 >Ez a paraméter beállítás folyamatban van a titkosítási beállítások frissítése közben, ha azt a számítógép újraindítása előtt a tényleges titkosítási vezethet. Ebben az esetben is érdemes távolítsa el a lemezt nem szeretné, hogy formázva az fstab fájl. Hasonlóképpen adja hozzá azt a partíciót az fstab fájl titkosítása-formátumú, mielőtt elindítaná a titkosítási műveletet. 

### <a name="bkmk_EFACriteria"> </a> EncryptFormatAll feltételek
A paraméter kerül, ha az összes partíció, és titkosítja azokat, amennyiben azok megfelelnek **összes** az alábbi feltételek: 
- Nem legfelső szintű vagy az operációs rendszer és rendszerindító partíció
- Már nem titkosított
- Nem rendelkeznek BEk-KEL kötet
- Nem RAID-kötet
- Az LVM-kötet nem
- Csatlakoztatva van

A RAID vagy LVM kötetet, hanem a RAID vagy LVM kötetet alkotó lemezek titkosítása.

### <a name="bkmk_EFAPSH"> </a> Használja a EncryptFormatAll paramétert az Azure CLI-vel
Használja a [az vm encryption engedélyezése](/cli/azure/vm/encryption#az-vm-encryption-enable) parancsot az Azure-ban futó IaaS virtuális gép titkosításának engedélyezéséhez.

-  **EncryptFormatAll egy futó virtuális gép titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="bkmk_EFAPSH"> </a> Használja a EncryptFormatAll paraméterrel együtt egy PowerShell-parancsmag
Használja a [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmagot a EncryptFormatAll paraméterrel. 

**EncryptFormatAll egy futó virtuális gép titkosítása:** Az alábbi szkript például inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot a EncryptFormatAll paraméterrel. Az erőforráscsoport, a virtuális gép és a key vault kell rendelkezik már létre van hozva előfeltételeket. Cserélje le az MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értékeket az értékekre.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
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
    
    4. Futtassa a set-AzVMDiskEncryptionExtension PowerShell-parancsmagot a-EncryptFormatAll használatával a lemezek titkosításához.

         ```azurepowershell-interactive
         $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
         
         Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
         ```

    5. Állítsa be LVM felett ezeket a lemezeket. Vegye figyelembe a titkosított meghajtó zárolását a virtuális gép befejezése után indul el. Az LVM csatlakoztatási így is megkapják ezt követően kell-e késleltetni.


## <a name="bkmk_VHDpre"> </a> Ügyfél-titkosítású VHD-t és a titkosítási kulcsok alapján létrehozott új IaaS virtuális gépek
Ebben a forgatókönyvben engedélyezheti a PowerShell-parancsmagokkal vagy a CLI-parancsok használatával titkosítja. 

Kövesse az utasításokat a függelékben használható az Azure-ban előzetes titkosítással rendszerképek előkészítéséről. A lemezkép létrehozását követően használhatja a lépéseket a következő szakaszban titkosított Azure virtuális gép létrehozásához.

* [Előzetes titkosítással Windows virtuális merevlemez előkészítése](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Előzetes titkosítással Linux rendszerű virtuális merevlemez előkészítése](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Pillanatkép kötelező és/vagy biztonsági mentési felügyelt lemez alapú Virtuálisgép-példány kívül, és az Azure Disk Encryption engedélyezése előtt. A felügyelt lemez pillanatképét elvégezhet a portálról, vagy [Azure Backup](../backup/backup-azure-vms-encryption.md) is használható. Biztonsági másolatok ellenőrizze, hogy a helyreállítási beállítások esetén minden váratlan hiba lehetséges titkosítás közben. A biztonsági mentést követően a set-AzVMDiskEncryptionExtension parancsmag használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. A set-AzVMDiskEncryptionExtension parancs a felügyelt lemezes virtuális gépektől a biztonsági mentésig meghiúsul, és ez a paraméter meg lett adva. 
>
>Titkosítása vagy letiltja a titkosítást a virtuális gép újraindítását okozhatja. 



### <a name="bkmk_VHDprePSH"> </a> Az Azure PowerShell használatával előre titkosított virtuális merevlemezzel rendelkező IaaS virtuális gépek titkosítása 
A titkosított virtuális merevlemez titkosítását a [set-AzVMOSDisk PowerShell-](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples)parancsmag használatával engedélyezheti. Az alábbi példa néhány gyakori paramétereket biztosít. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott adatlemez

Hozzáadhat egy új lemez használatával [az vm disk attach](../virtual-machines/linux/add-disk.md), vagy [az Azure Portalon keresztül](../virtual-machines/linux/attach-disk-portal.md). Mielőtt titkosíthatók, először csatlakoztassa az újonnan csatolt adatlemezre kell. Az adatmeghajtó titkosításához kell kérnie, mert a meghajtó használhatatlan lesz, bár a titkosítás folyamatban van. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott lemezt az Azure CLI-vel

 Ha a virtuális gép korábban titkosítva van az "All", majd a--kötettípus paramétert kell hagyni minden. Mind az operációs rendszer és az adatlemezek összes tartalmazza. Ha a virtuális gép korábban titkosított kötetet típusú "OS", majd a--kötettípus paraméter az összes lehet módosítani, hogy az operációs rendszer és az új adatlemezt is tartalmazza. Ha a virtuális gép titkosított "Adatok" csak a kötet típusát, majd azt is marad, "Data" ahogyan alább is látható. Hozzáadásával és a egy új adatlemez csatolása a virtuális gép nem elegendő előkészítése a titkosításhoz. Az újonnan csatolt lemez is kell formázva, és megfelelően csatlakoztatva a virtuális gép titkosítás engedélyezése előtt. Linux rendszeren a lemezt az/etc/fstab-csatlakoztatni kell egy [állandó blokk eszköznév](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems).  

Powershell-szintaxis szakembereket a CLI nem igényel egy egyedi verziója szolgáltatni a titkosítás engedélyezése a felhasználó. A CLI automatikusan hoz létre, és használja a saját egyedi feladatütemezési version értéke.

-  **Az adatkötetek futó virtuális gépek titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Az adatkötetek KEK használó, futó virtuális gépek titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Engedélyezheti a titkosítást egy újonnan hozzáadott lemezt az Azure PowerShell használatával
 Új lemez titkosítása Linux rendszeren a Powershell segítségével, amikor egy új verziója kell adni. A feladatütemezési verziójában található egyedinek kell lennie. Az alábbi parancsfájlt előállít egy GUID Azonosítót a verziója. Készítsen [pillanatképet](../virtual-machines/windows/snapshot-copy-managed-disk.md) és/vagy készítsen biztonsági másolatot a virtuális gépről [Azure Backup](../backup/backup-azure-vms-encryption.md) a lemezek titkosítása előtt. A-skipVmBackup paraméter már meg van adva a PowerShell-parancsfájlokban egy újonnan hozzáadott adatlemez titkosításához.
 

-  **Az adatkötetek futó virtuális gépek titkosítása:** Az alábbi szkript inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép és a key vault kell rendelkezik már létre van hozva előfeltételeket. Cserélje le az MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értékeket az értékekre. A - VolumeType paraméter elfogadható értékek: All, az operációs rendszer és az adatok. Ha a virtuális gép korábban titkosított "OS" vagy "All" típusú kötet, majd a - VolumeType paraméter kell módosítani az összes, hogy az operációs rendszer és az új adatlemezt is tartalmazza.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Az adatkötetek KEK használó, futó virtuális gépek titkosítása:** A - VolumeType paraméter elfogadható értékek: All, az operációs rendszer és az adatok. Ha a virtuális gép korábban titkosított "OS" vagy "All" típusú kötet, majd a - VolumeType paraméter kell módosítani az összes, hogy az operációs rendszer és az új adatlemezt is tartalmazza.

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[KVresource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name]</br> A kulcs-titkosítás – key paraméter értékének szintaxisa a KEK, mint a teljes URI Azonosítóját: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Linux rendszerű virtuális gépek titkosításának letiltása
Letilthatja a titkosítás az Azure PowerShell, az Azure CLI használatával vagy a Resource Manager-sablonnal. 

>[!IMPORTANT]
>Letiltja a titkosítást a Linuxos virtuális gépeken az Azure Disk Encryption csak a támogatott adatköteteket. Nem támogatott a adatok vagy operációsrendszer-kötettel, ha az operációsrendszer-kötet titkosított.  

- **Lemez titkosításának letiltása a Azure PowerShell:** A titkosítás letiltásához használja a [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Titkosítás letiltása az Azure CLI-vel:** A titkosítás letiltásához használja az az [VM encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Tiltsa le a Resource Manager-sablonnal titkosítást:** A titkosítás letiltásához használja a [Linux rendszerű virtuális gép titkosításának letiltására](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) szolgáló sablont.
     1. Kattintson a **Deploy to Azure** (Üzembe helyezés az Azure-ban) elemre.
     2. Válassza ki az előfizetés, erőforráscsoport, hely, virtuális gép, jogi feltételeket és szerződés.
     3.  Kattintson a **beszerzési** letiltja a lemeztitkosítást a futó Windows virtuális gépek. 


## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [A Windows Azure Disk Encryption engedélyezése](azure-security-disk-encryption-windows.md)

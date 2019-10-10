---
title: Azure Disk Encryption forgatókönyvek Windows rendszerű virtuális gépeken
description: Ez a cikk útmutatást nyújt a Windows rendszerű virtuális gépek Microsoft Azure lemezes titkosításának engedélyezéséhez különböző forgatókönyvek esetén
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 948712b684d1cd1b072862b7253d745f89b0cc56
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244992"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Azure Disk Encryption forgatókönyvek Windows rendszerű virtuális gépeken

A Azure Disk Encryption a BitLocker külső kulcstartóját használja az Azure Virtual Machines (VM) operációsrendszer-és adatlemezei mennyiségi titkosításának biztosításához, és integrálva van Azure Key Vault a lemezes titkosítási kulcsok és titkos kódok felügyeletéhez és kezeléséhez. A szolgáltatás áttekintését lásd: [Azure Disk Encryption Windows rendszerű virtuális gépekhez](disk-encryption-overview.md).

Sok lemezes titkosítási forgatókönyv létezik, és a lépések a forgatókönyvtől függően eltérőek lehetnek. A következő részek a Windows rendszerű virtuális gépekkel kapcsolatos részletesebb forgatókönyveket tárgyalják.

A lemezes titkosítást csak a [támogatott virtuálisgép-méretek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems)virtuális gépei esetében lehet alkalmazni. A következő előfeltételeknek is teljesülniük kell:

- [Hálózati követelmények](disk-encryption-overview.md#networking-requirements)
- [Csoportházirend követelmények](disk-encryption-overview.md#group-policy-requirements)
- [Titkosítási kulcs tárolási követelményei](disk-encryption-overview.md#encryption-key-storage-requirements)



>[!IMPORTANT]
> - Ha korábban már használta Azure Disk Encryption az Azure AD-vel egy virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Részletekért lásd: [Azure Disk Encryption az Azure ad-vel (előző kiadás)](disk-encryption-overview-aad.md) . 
>
> - Készítsen [pillanatképet](snapshot-copy-managed-disk.md) , és/vagy készítsen biztonsági másolatot a lemezek titkosítása előtt. A biztonsági másolatok gondoskodnak arról, hogy a rendszer egy helyreállítási lehetőséget akkor lehessen, ha nem várt hiba történik a titkosítás során. A felügyelt lemezekkel rendelkező virtuális gépek biztonsági mentést igényelnek a titkosítás megkezdése előtt. A biztonsági mentés után a [set-AzVMDiskEncryptionExtension parancsmag](/powershell/module/az.compute/set-azvmdiskencryptionextension) használatával titkosíthatja a felügyelt lemezeket a-skipVmBackup paraméter megadásával. További információ a titkosított virtuális gépek biztonsági mentéséről és visszaállításáról: [titkosított Azure-beli virtuális gép biztonsági mentése és visszaállítása](../../backup/backup-azure-vms-encryption.md). 
>
> - A titkosítás titkosítása vagy letiltása miatt előfordulhat, hogy a virtuális gép újraindul.


## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és az Azure-hoz való kapcsolódás

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Titkosítás engedélyezése meglévő vagy Windows rendszerű virtuális gépen
Ebben az esetben a titkosítást a Resource Manager-sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával engedélyezheti. Ha a virtuálisgép-bővítményre vonatkozó séma-információra van szüksége, olvassa el a [Azure Disk Encryption a Windows-bővítményről szóló](../extensions/azure-disk-enc-windows.md) cikket.

## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a>Titkosítás engedélyezése meglévő vagy futó IaaS Windows rendszerű virtuális gépeken
A titkosítást sablon, PowerShell-parancsmagok vagy CLI-parancsok használatával engedélyezheti. Ha a virtuálisgép-bővítményre vonatkozó séma-információra van szüksége, olvassa el a [Azure Disk Encryption a Windows-bővítményről szóló](../extensions/azure-disk-enc-windows.md) cikket.

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Titkosítás engedélyezése meglévő vagy futó virtuális gépeken Azure PowerShell 
A [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban. 

-  **Futó virtuális gép titkosítása:** Az alábbi szkript inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép és a Key Vault már előfeltételként lett létrehozva. Cserélje le az MyKeyVaultResourceGroup, a MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értéket az értékekre.

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
- **Futó virtuális gép titkosítása a KEK használatával:** 

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
   > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name]</br> A Key-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-name]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának vizsgálatához használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmagot. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Lemez titkosításának letiltása:** A titkosítás letiltásához használja a [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. Az adatlemez titkosításának letiltása a Windowsos virtuális gépen, ha az operációs rendszer és az adatlemezek titkosítva vannak, nem a várt módon működik. A titkosítás letiltása az összes lemezen.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Titkosítás engedélyezése meglévő vagy futó virtuális gépeken az Azure CLI-vel
Az az [VM encryption Enable](/cli/azure/vm/encryption#az-vm-encryption-enable) paranccsal engedélyezheti a titkosítást egy futó IaaS virtuális gépen az Azure-ban.

- **Futó virtuális gép titkosítása:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Futó virtuális gép titkosítása a KEK használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name] </br> A Key-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-name]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Egy IaaS virtuális gép titkosítási állapotának megtekintéséhez használja az az [VM encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Titkosítás letiltása:** A titkosítás letiltásához használja az az [VM encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. Az adatlemez titkosításának letiltása a Windowsos virtuális gépen, ha az operációs rendszer és az adatlemezek titkosítva vannak, nem a várt módon működik. A titkosítás letiltása az összes lemezen.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>A Resource Manager-sablon használata

Az Azure-ban meglévő vagy futó IaaS Windows rendszerű virtuális gépeken is engedélyezheti a lemez titkosítását, ha a [Resource Manager-sablonnal titkosít egy futó Windows rendszerű virtuális gépet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).


1. Az Azure Gyorsindítás sablonon kattintson az **üzembe helyezés az Azure**-ban lehetőségre.

2. Válassza ki az előfizetést, az erőforráscsoportot, a helyet, a beállításokat, a jogi feltételeket és a szerződést. Kattintson a **vásárlás** gombra a titkosítás engedélyezéséhez a meglévő vagy futó IaaS virtuális gépen.

A következő táblázat a Resource Manager-sablon paramétereit sorolja fel a meglévő vagy futó virtuális gépekhez:

| Paraméter | Leírás |
| --- | --- |
| vmName | A titkosítási műveletet futtató virtuális gép neve. |
| keyVaultName | Annak a kulcstárolónak a neve, amelyre a BitLocker-kulcsot fel kell tölteni. A `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` parancsmaggal vagy az Azure CLI-parancs `az keyvault list --resource-group "MyKeyVaultResourceGroup"` paranccsal kérheti le.|
| keyVaultResourceGroup | A kulcstárolót tartalmazó erőforráscsoport neve|
|  keyEncryptionKeyURL | A kulcs titkosítási kulcsának URL-címe: https://@no__t -0keyvault-name&gt;.vault.azure.net/Key/&lt;key-Name @ no__t-3. Ha nem szeretne KEK-t használni, hagyja üresen ezt a mezőt. |
| volumeType | A titkosítási művelet végrehajtásához használt kötet típusa. Az érvényes értékek az _operációs rendszer_, _az adatok_és _az összes_. 
| forceUpdateTag | Adjon meg egy egyedi értéket, például egy GUID-azonosítót, amikor a műveletnek kényszerített futtatást kell futtatnia. |
| resizeOSDisk | Ha az operációsrendszer-partíciót át szeretné méretezni a teljes operációsrendszer-lemez elfoglalásához a rendszerkötet felosztása előtt. |
| location | Az összes erőforrás helye. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Az ügyfél által titkosított VHD-és titkosítási kulcsokból létrehozott új IaaS virtuális gépek

Ebben az esetben létrehozhat egy új virtuális gépet egy előre titkosított virtuális merevlemezről és a hozzá tartozó titkosítási kulcsokról a PowerShell-parancsmagok vagy a CLI-parancsok használatával. 

Használja az [előre titkosított Windows virtuális merevlemez előkészítése](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)című témakör utasításait. A rendszerkép létrehozása után a következő szakasz lépéseit követve hozzon létre egy titkosított Azure-beli virtuális gépet.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Virtuális gépek titkosítása előre titkosított virtuális merevlemezekkel Azure PowerShell
A titkosított virtuális merevlemez titkosítását a [set-AzVMOSDisk PowerShell-](/powershell/module/az.compute/set-azvmosdisk#examples)parancsmag használatával engedélyezheti. Az alábbi példa néhány gyakori paramétert biztosít. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Titkosítás engedélyezése újonnan hozzáadott adatlemezen
Új lemezt a PowerShell vagy [a Azure Portal](attach-managed-disk-portal.md) [használatával adhat hozzá a Windows rendszerű virtuális](attach-disk-ps.md)gépekhez. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>A titkosítás engedélyezése egy újonnan hozzáadott lemezen Azure PowerShell
 Amikor a PowerShell használatával titkosít egy új lemezt a Windows rendszerű virtuális gépekhez, meg kell adni egy új sorozatot. A Sequence verziójának egyedinek kell lennie. Az alábbi szkript létrehoz egy GUID azonosítót a Sequence verzióhoz. Bizonyos esetekben előfordulhat, hogy az újonnan hozzáadott adatlemezek automatikusan titkosítva lesznek a Azure Disk Encryption bővítménnyel. Az automatikus titkosítás általában akkor fordul elő, amikor a virtuális gép újraindul, miután az új lemez online állapotba kerül. Ez általában az okozza, hogy az "all" érték van megadva a kötet típusához, amikor a lemez titkosítása korábban futott a virtuális gépen. Ha az automatikus titkosítás egy újonnan hozzáadott adatlemezen történik, javasoljuk, hogy a set-AzVmDiskEncryptionExtension parancsmagot újra futtassa új sorozat-verzióval. Ha az új adatlemez automatikusan titkosítva van, és nem kívánja titkosítani, az összes meghajtót visszafejti, majd újratitkosítja egy új sorozat-verzióval, amely meghatározza a kötet típusának operációs rendszerét. 
  
 

-  **Futó virtuális gép titkosítása:** Az alábbi szkript inicializálja a változókat, és futtatja a set-AzVMDiskEncryptionExtension parancsmagot. Az erőforráscsoport, a virtuális gép és a Key Vault már előfeltételként lett létrehozva. Cserélje le az MyKeyVaultResourceGroup, a MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értéket az értékekre. Ez a példa az "all" kifejezést használja a-VolumeType paraméterhez, amely az operációs rendszer és az adatköteteket is tartalmazza. Ha csak az operációs rendszer kötetét szeretné titkosítani, használja az "operációs rendszer" lehetőséget a-VolumeType paraméterhez. 

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
- **Futó virtuális gép titkosítása a KEK használatával:** Ez a példa az "all" kifejezést használja a-VolumeType paraméterhez, amely az operációs rendszer és az adatköteteket is tartalmazza. Ha csak az operációs rendszer kötetét szeretné titkosítani, használja az "operációs rendszer" lehetőséget a-VolumeType paraméterhez.

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
    > A Disk-Encryption-kulcstartó paraméter értékének szintaxisa a teljes azonosító karakterlánc:/Subscriptions/[előfizetés-azonosító-GUID]/resourceGroups/[Resource-Group-name]/providers/Microsoft.KeyVault/vaults/[kulcstartó-name]</br> A Key-encryption-Key paraméter értékének szintaxisa a KEK teljes URI-ja a következőhöz hasonlóan: https://[kulcstartó-name]. Vault. Azure. net/Keys/[kekname]/[KEK-Unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>A titkosítás engedélyezése az újonnan hozzáadott lemezeken az Azure CLI-vel
 Az Azure CLI-parancs automatikusan megadja az új sorozat verzióját, amikor a parancs futtatásával engedélyezi a titkosítást. A példa az "all" értéket használja a Volume-Type paraméterhez. Előfordulhat, hogy módosítania kell a Volume-type paramétert az operációs rendszerre, ha csak az operációsrendszer-lemezt titkosítja. A PowerShell-szintaxissal ellentétben a parancssori felület nem igényli, hogy a felhasználó egyedi sorozatot adjon meg a titkosítás engedélyezésekor. A CLI automatikusan generálja és felhasználja a saját egyedi sorszámának értékét.   

-  **Futó virtuális gép titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Futó virtuális gép titkosítása a KEK használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Titkosítás letiltása
A titkosítást a Azure PowerShell, az Azure CLI vagy egy Resource Manager-sablonnal is letilthatja. Az adatlemez titkosításának letiltása a Windowsos virtuális gépen, ha az operációs rendszer és az adatlemezek titkosítva vannak, nem a várt módon működik. A titkosítás letiltása az összes lemezen.

- **Lemez titkosításának letiltása a Azure PowerShell:** A titkosítás letiltásához használja a [disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagot. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Titkosítás letiltása az Azure CLI-vel:** A titkosítás letiltásához használja az az [VM encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **A titkosítás letiltása Resource Manager-sablonnal:** 

    1. Kattintson a **telepítés az Azure** -ba lehetőségre a [lemez titkosításának letiltása Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) virtuálisgép-sablon futtatásához.
    2. Válassza ki az előfizetést, az erőforráscsoportot, a helyet, a virtuális gépet, a kötet típusát, a jogi feltételeket és a szerződést.
    3.  Kattintson a **vásárlás** gombra a lemez titkosításának letiltásához egy futó WINDOWSOS virtuális gépen. 

## <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

A Azure Disk Encryption a következő forgatókönyvek, funkciók és technológiák esetében nem működik:

- A klasszikus virtuálisgép-létrehozási módszerrel létrehozott alapszintű virtuális gép vagy virtuális gépek titkosítása.
- A szoftveres RAID-rendszerekkel konfigurált Windows-alapú virtuális gépek titkosítása.
- Integráció egy helyszíni kulcskezelő rendszerrel.
- Azure Files (megosztott fájlrendszer).
- Hálózati fájlrendszer (NFS).
- Dinamikus kötetek.
- Windows Server-tárolók, amelyek mindegyik tárolóhoz dinamikus köteteket hoznak létre.
- Ideiglenes operációsrendszer-lemezek.
- Megosztott/elosztott fájlrendszerek titkosítása, például (de nem kizárólag a) DFS, a GFS, a DRDB és a CephFS.

## <a name="next-steps"></a>Következő lépések

- [Azure Disk Encryption áttekintése](disk-encryption-overview.md)
- [Azure Disk Encryption minta parancsfájlok](disk-encryption-sample-scripts.md)
- [Hibaelhárítás Azure Disk Encryption](disk-encryption-troubleshooting.md)

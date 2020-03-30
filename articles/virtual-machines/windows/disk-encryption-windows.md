---
title: Azure Disk Encryption-forgatókönyvek Windows rendszerű virtuális gépekhez
description: Ez a cikk a Microsoft Azure lemeztitkosítás különböző forgatókönyvekhez való engedélyezéséről nyújt tájékoztatást a Windows-szolgáltatásokhoz
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: ed64ee3d0e024c32be08ed4e010a6933033c3f87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476518"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Azure Disk Encryption-forgatókönyvek Windows rendszerű virtuális gépekhez

Az Azure Disk Encryption a BitLocker külső kulcsvédőt használja az Azure virtuális gépek operációs rendszerének és adatlemezeinek kötettitkosításához, és az Azure Key Vaultszolgáltatással integrálva van a lemeztitkosítási kulcsok és titkos kulcsok vezérléséhez és kezeléséhez. A szolgáltatás áttekintését az [Azure Lemeztitkosítás Windows-gépekhez című témakörben találja.](disk-encryption-overview.md)

Számos lemeztitkosítási forgatókönyv létezik, és a lépések a forgatókönyvtől függően változhatnak. A következő szakaszok részletesebben ismertetik a Windows virtuális gépek forgatókönyveit.

Csak a [támogatott virtuális gépméretű és operációs rendszerű](disk-encryption-overview.md#supported-vms-and-operating-systems)virtuális gépekre alkalmazhat lemeztitkosítást. Az alábbi előfeltételeknek is meg kell felelnie:

- [Hálózati követelmények](disk-encryption-overview.md#networking-requirements)
- [Csoportházirend-követelmények](disk-encryption-overview.md#group-policy-requirements)
- [Titkosítási kulcs tárolási követelményei](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Ha korábban már használta az Azure Lemeztitkosítás t az Azure AD-vel egy virtuális gép titkosításához, továbbra is ezt a lehetőséget kell használnia a virtuális gép titkosításához. A részletekért tekintse meg [az Azure Disk Encryption with Azure AD (előző kiadás)](disk-encryption-overview-aad.md) című témakört. 
>
> - A lemezek titkosítása előtt [készítsen pillanatképet](snapshot-copy-managed-disk.md) és/vagy hozzon létre biztonsági másolatot. A biztonsági mentések biztosítják, hogy a helyreállítási lehetőség lehetséges legyen, ha a titkosítás során váratlan hiba lép fel. A felügyelt lemezekkel rendelkező virtuális gépeknek biztonsági mentésre van szükségük a titkosítás előtt. A biztonsági mentés tkövetően a [Set-AzVMDiskEncryptionExtension parancsmag](/powershell/module/az.compute/set-azvmdiskencryptionextension) segítségével titkosíthatja a felügyelt lemezeket a -skipVmBackup paraméter megadásával. A titkosított virtuális gépek biztonsági és visszaállítási módjáról a [Titkosított Azure virtuális gép biztonsági és visszaállítása](../../backup/backup-azure-vms-encryption.md)című témakörben talál további információt. 
>
> - Titkosítás vagy titkosítás letiltása okozhat a virtuális gép újraindítása.

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és csatlakozás az Azure-hoz

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Titkosítás engedélyezése meglévő vagy windowsos virtuális gépen
Ebben az esetben engedélyezheti a titkosítást az Erőforrás-kezelő sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával. Ha sémainformációkra van szüksége a virtuálisgép-bővítményhez, olvassa el az [Azure Disk Encryption for Windows extension](../extensions/azure-disk-enc-windows.md) cikket.

## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a>Titkosítás engedélyezése meglévő vagy futó IaaS Windows virtuális gépeken
A titkosítást sablon, PowerShell-parancsmag vagy CLI-parancs használatával engedélyezheti. Ha sémainformációkra van szüksége a virtuálisgép-bővítményhez, olvassa el az [Azure Disk Encryption for Windows extension](../extensions/azure-disk-enc-windows.md) cikket.

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Titkosítás engedélyezése meglévő vagy futó virtuális gépeken az Azure PowerShell használatával 
A [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezze a titkosítást egy futó IaaS virtuális gépen az Azure-ban. 

-  **Futó virtuális gép titkosítása:** Az alábbi parancsfájl inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension parancsmamot. Az erőforráscsoport, a virtuális gép és a key vault már létre kell hozni az előfeltételek. Cserélje le a MyKeyVaultResourceGroup, a MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értékeket az Ön értékeire.

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
- **Futó virtuális gép titkosítása KEK használatával:** 

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
   > A lemeztitkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás-kulcs paraméter értékének szintaxisa a KEK teljes URI-ja, mint például: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítottak-e:** Az IaaS virtuális gép titkosítási állapotának ellenőrzéséhez használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmag. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Lemeztitkosítás letiltása:** A titkosítás letiltásához használja az [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagját. A windowsos virtuális gépek adatlemezeinek titkosítása nem a várt módon működik, ha az operációs rendszer és az adatlemezek egyaránt titkosítottak. Tiltsa le a titkosítást az összes lemezen.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Titkosítás engedélyezése meglévő vagy futó virtuális gépeken az Azure CLI-vel
Használja az [az vm titkosításengedélyezése](/cli/azure/vm/encryption#az-vm-encryption-enable) parancs titkosítás engedélyezéséhez egy futó IaaS virtuális gép az Azure-ban.

- **Futó virtuális gép titkosítása:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Futó virtuális gép titkosítása KEK használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A lemeztitkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A kulcs-titkosítás-kulcs paraméter értékének szintaxisa a KEK teljes URI-ja, mint például: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítottak-e:** Az IaaS virtuális gép titkosítási állapotának ellenőrzéséhez használja az [az vm titkosítási show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Titkosítás letiltása:** A titkosítás letiltásához használja az [az vm titkosítás letiltása](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. A windowsos virtuális gépek adatlemezeinek titkosítása nem a várt módon működik, ha az operációs rendszer és az adatlemezek egyaránt titkosítottak. Tiltsa le a titkosítást az összes lemezen.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>Az Erőforráskezelő sablon használata

A Lemeztitkosítást az Azure-ban meglévő vagy futó IaaS Windows-virtuális gépeken engedélyezheti az [Erőforrás-kezelő sablon használatával egy futó Windows virtuális gép titkosításához.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)


1. Az Azure gyorsindítási sablonján kattintson **a Telepítés az Azure-ba**elemre.

2. Válassza ki az előfizetést, az erőforráscsoportot, a helyet, a beállításokat, a jogi feltételeket és a megállapodást. Kattintson **a Vásárlás** gombra a meglévő vagy futó IaaS virtuális gép titkosításának engedélyezéséhez.

Az alábbi táblázat a meglévő vagy futó virtuális gépek Erőforrás-kezelő sablonparamétereit sorolja fel:

| Paraméter | Leírás |
| --- | --- |
| vmName | A titkosítási művelet futtatásához használt virtuális gép neve. |
| keyVaultName | Annak a kulcstartónak a neve, amelybe a BitLocker-kulcsot fel kell tölteni. Beszerezheti a parancsmag `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` vagy az Azure CLI parancs használatával`az keyvault list --resource-group "MyKeyVaultResourceGroup"`|
| keyVaultResourceGroup | A key vaultot tartalmazó erőforráscsoport neve|
|  keyEncryptionKeyURL | A kulcstitkosítási kulcs URL-címe&lt;https://&gt;keyvault-name&lt;.vault.azure.net/key/&gt;kulcsnév formátumban. Ha nem kíván KEK-et használni, hagyja üresen ezt a mezőt. |
| volumeType (kötettípusa) | A titkosítási művelet által végrehajtott kötet típusa. Érvényes értékek: _OPERÁCIÓS rendszer_, _Adatok_és _Mind_. 
| forceUpdateTag | Adja át egy egyedi értéket, például egy GUID-ot minden alkalommal, amikor a műveletet erőfuttatásra van szüksége. |
| átméretezésOSDisk | Ha az operációs rendszer partíció átméretezni, hogy elfoglalja a teljes operációs rendszer virtuális merevlemeze a rendszerkötet felosztása előtt. |
| location | Az összes erőforrás helye. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Új IaaS virtuális gépek, amelyek ügyfél által titkosított virtuális merevlemezből és titkosítási kulcsokból készültek

Ebben a forgatókönyvben egy új virtuális gép egy előre titkosított virtuális merevlemez és a kapcsolódó titkosítási kulcsok powershell-parancsmagok vagy CLI-parancsok használatával hozhat létre. 

Kövesse az [Előre titkosított Windows virtuális merevlemez előkészítése](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)című útmutatót. A rendszerkép létrehozása után a következő szakaszban leírt lépésekkel létrehozhat egy titkosított Azure-beli virtuális gép.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Virtuális gépek titkosítása előre titkosított virtuális számítógépekkel az Azure PowerShell használatával
A titkosított virtuális merevlemezen a [Set-AzVMOSDisk PowerShell-parancsmag](/powershell/module/az.compute/set-azvmosdisk#examples)segítségével engedélyezheti a lemeztitkosítást. Az alábbi példa néhány gyakori paramétert ad. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Titkosítás engedélyezése újonnan hozzáadott adatlemezen
Új [lemezt adhat hozzá egy Windows virtuális géphez](attach-disk-ps.md)a PowerShell használatával vagy [az Azure Portalon keresztül.](attach-managed-disk-portal.md) 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Titkosítás engedélyezése újonnan hozzáadott lemezen az Azure PowerShell használatával
 Ha a Powershell használatával titkosít egy új lemezt a Windows virtuális gépekhez, új szekvenciaverziót kell megadni. A szekvencia verziónak egyedinek kell lennie. Az alábbi parancsfájl létrehoz egy GUID-ot a szekvenciaverzióhoz. Bizonyos esetekben előfordulhat, hogy egy újonnan hozzáadott adatlemez automatikusan titkosítja az Azure Disk Encryption bővítmény. Automatikus titkosítás általában akkor fordul elő, amikor a virtuális gép újraindul, miután az új lemez online állapotba kerül. Ennek oka általában az, hogy "Minden" lett megadva a kötet típusához, amikor a lemeztitkosítás korábban futott a virtuális gépen. Ha automatikus titkosítás történik egy újonnan hozzáadott adatlemezen, javasoljuk, hogy futassza újra a Set-AzVmDiskEncryptionExtension parancsmagát az új szekvenciaverzióval. Ha az új adatlemez automatikusan titkosított, és nem szeretné, hogy titkosítsa, először fejtse vissza az összes meghajtót, majd fejteni egy új szekvenciaverzióval, amely a kötettípusoperációs rendszert adja meg. 
  
 

-  **Futó virtuális gép titkosítása:** Az alábbi parancsfájl inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension parancsmamot. Az erőforráscsoport, a virtuális gép és a key vault már létre kell hozni az előfeltételek. Cserélje le a MyKeyVaultResourceGroup, a MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értékeket az Ön értékeire. Ez a példa az "Összes" parancsot használja a -VolumeType paraméterhez, amely az operációs rendszer és az adatköteteket is tartalmazza. Ha csak az operációs rendszer kötetét szeretné titkosítani, használja az "OPERÁCIÓS rendszer" szót a -VolumeType paraméterhez. 

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
- **Futó virtuális gép titkosítása KEK használatával:** Ez a példa az "Összes" parancsot használja a -VolumeType paraméterhez, amely az operációs rendszer és az adatköteteket is tartalmazza. Ha csak az operációs rendszer kötetét szeretné titkosítani, használja az "OPERÁCIÓS rendszer" szót a -VolumeType paraméterhez.

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
    > A lemeztitkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás-kulcs paraméter értékének szintaxisa a KEK teljes URI-ja, mint például: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Titkosítás engedélyezése újonnan hozzáadott lemezen az Azure CLI-vel
 Az Azure CLI parancs automatikusan egy új szekvenciaverziót biztosít a titkosítás engedélyezéséhez a parancs futtatásakor. A példa az "Összes" szót használja a kötettípus paraméterhez. Előfordulhat, hogy módosítania kell a kötettípus paraméterét operációs rendszerre, ha csak az operációs rendszer lemezét titkosítja. A Powershell szintaxisával ellentétben a CLI nem követeli meg a felhasználótól, hogy a titkosítás engedélyezésekor egyedi szekvenciaverziót adjon meg. A CLI automatikusan létrehozza és felhasználja a saját egyedi szekvencia verzióértékét.   

-  **Futó virtuális gép titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Futó virtuális gép titkosítása KEK használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Titkosítás letiltása
Letilthatja a titkosítást az Azure PowerShell, az Azure CLI vagy a Resource Manager sablon használatával. A windowsos virtuális gépek adatlemezeinek titkosítása nem a várt módon működik, ha az operációs rendszer és az adatlemezek egyaránt titkosítottak. Tiltsa le a titkosítást az összes lemezen.

- **Lemeztitkosítás letiltása az Azure PowerShell használatával:** A titkosítás letiltásához használja az [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagját. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Tiltsa le a titkosítást az Azure CLI-vel:** A titkosítás letiltásához használja az [az vm titkosítás letiltása](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Titkosítás letiltása Erőforrás-kezelő sablonnal:** 

    1. Kattintson **a Telepítés az Azure-ba** a [Lemeztitkosítás letiltása Windows vm sablonon.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
    2. Válassza ki az előfizetést, az erőforráscsoportot, a helyet, a virtuális gépet, a kötet típusát, a jogi feltételeket és a megállapodást.
    3.  Kattintson **a Vásárlás** gombra a lemeztitkosítás letiltásához egy futó Windows virtuális gépen. 

## <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

Az Azure Disk Encryption nem működik a következő forgatókönyvek, szolgáltatások és technológiák esetén:

- A klasszikus virtuálisgép-létrehozási módszerrel létrehozott alapvető szintű virtuális gép vagy virtuális gépek titkosítása.
- Szoftveralapú RAID-rendszerekkel konfigurált virtuális gépek titkosítása.
- A Közvetlen tárolóhelyekkel (S2D) vagy a Windows Server 2016 előtti, a Windows tárolóhelyekkel konfigurált verziókkal konfigurált virtuális gépek titkosítása.
- Integráció a helyszíni kulcskezelő rendszerrel.
- Azure Files (megosztott fájlrendszer).
- hálózati fájlrendszer (NFS).
- Dinamikus kötetek.
- Windows Server-tárolók, amelyek dinamikus köteteket hoznak létre az egyes tárolókhoz.
- Rövid élettartamú operációs rendszer lemezek.
- Megosztott/elosztott fájlrendszerek titkosítása, például (de nem kizárólagosan) a DFS, gfs, DRDB és CephFS.
- Titkosított virtuális gépek áthelyezése egy másik előfizetésbe.

## <a name="next-steps"></a>További lépések

- [Azure lemeztitkosítás – áttekintés](disk-encryption-overview.md)
- [Azure Disk Encryption – mintaszkriptek](disk-encryption-sample-scripts.md)
- [Azure Disk Encryption – hibaelhárítás](disk-encryption-troubleshooting.md)

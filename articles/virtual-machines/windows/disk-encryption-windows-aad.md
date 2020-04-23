---
title: Azure lemeztitkosítás az Azure AD windowsos virtuális gépekhez (előző kiadás)
description: Ez a cikk a Microsoft Azure lemeztitkosítás windowsi iaas virtuális gépekhez való engedélyezésével kapcsolatos utasításokat tartalmaz.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 8677fa2620c1edc646dcffe120938f03fd13a0e5
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085621"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Azure lemeztitkosítás az Azure AD windowsos virtuális gépekhez (előző kiadás)

**Az Azure Disk Encryption új kiadása kiküszöböli az Azure AD-alkalmazás paraméter biztosításának követelményét a virtuális gép lemeztitkosításának engedélyezéséhez. Az új kiadás, már nem kell megadnia az Azure AD hitelesítő adatait a titkosítás engedélyezése a titkosítási lépés során. Minden új virtuális gép titkosítására az Azure AD alkalmazás paraméterei az új kiadás használatával. A virtuális gép lemeztitkosításának az új kiadással történő engedélyezésére vonatkozó utasítások megtekintéséhez olvassa el az Azure Disk Encryption for Windows VMS című [témakört.](disk-encryption-windows.md) Az Azure AD-alkalmazás paramétereivel már titkosított virtuális gépek továbbra is támogatottak, és továbbra is karban kell tartani az AAD szintaxissal.**


Számos lemeztitkosítási forgatókönyvet engedélyezhet, és a lépések a forgatókönyvtől függően változhatnak. A következő szakaszok részletesebben ismertetik a Windows IaaS virtuális gépek forgatókönyveit. A lemeztitkosítás használata előtt az [Azure disk encryption előfeltételeit](disk-encryption-overview-aad.md) be kell fejezni. 


>[!IMPORTANT]
> - A lemezek titkosítása előtt [készítsen pillanatképet](snapshot-copy-managed-disk.md) és/vagy hozzon létre biztonsági másolatot. A biztonsági mentések biztosítják, hogy a helyreállítási lehetőség lehetséges legyen, ha a titkosítás során váratlan hiba lép fel. A felügyelt lemezekkel rendelkező virtuális gépeknek biztonsági mentésre van szükségük a titkosítás előtt. A biztonsági mentés tkövetően a [Set-AzVMDiskEncryptionExtension parancsmag](/powershell/module/az.compute/set-azvmdiskencryptionextension) segítségével titkosíthatja a felügyelt lemezeket a -skipVmBackup paraméter megadásával. A titkosított virtuális gépek biztonsági és visszaállítási módjáról a [Titkosított Azure virtuális gép biztonsági és visszaállítása](../../backup/backup-azure-vms-encryption.md)című témakörben talál további információt. 
>
> - Titkosítás vagy titkosítás letiltása okozhat a virtuális gép újraindítása.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Titkosítás engedélyezése a Piactérről létrehozott új IaaS virtuális gépeken
Az Azure-beli Piactérről engedélyezheti a lemeztitkosítást az új IaaS Windows virtuális gépeken egy Resource Manager-sablon használatával. A sablon új, titkosított Windows virtuális géplétrehozása a Windows Server 2012 galérialemez lemezképének használatával.

1. Az [Erőforrás-kezelő sablonon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)kattintson a **Telepítés az Azure-ba**elemre.

2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, a paramétereket, a jogi feltételeket és a megállapodást. Kattintson **a Vásárlás** gombra egy új IaaS virtuális gép üzembe helyezéséhez, ahol engedélyezve van a titkosítás.

3. A sablon üzembe helyezése után ellenőrizze a virtuális gép titkosítási állapotát az előnyben részesített módszerrel:
     - Ellenőrizze az Azure CLI az [az vm titkosítási show](/cli/azure/vm/encryption#az-vm-encryption-show) paranccsal. 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Ellenőrizze az Azure PowerShell segítségével a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmag használatával. 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Válassza ki a virtuális gép, majd kattintson a **lemezek** a **Beállítások** fejléc alatt a titkosítási állapot ellenőrzéséhez a portálon. A **titkosítás**csoportban láthatja, hogy engedélyezve van-e. 
           ![Azure portal – lemeztitkosítás engedélyezve](../media/disk-encryption/disk-encryption-fig2.png)

Az alábbi táblázat az Azure AD-ügyfélazonosítót használó Marketplace-forgatókönyvből származó új virtuális gépek erőforrás-kezelősablon-paramétereit sorolja fel:

| Paraméter | Leírás | 
| --- | --- |
| adminUserName | A virtuális gép rendszergazdai felhasználóneve. |
| adminPassword | Rendszergazdai felhasználói jelszó a virtuális géphez. |
| newStorageAccountName | Az operációs rendszer és az adatvirtuális gépek tárolására szolgáló tárfiók neve. |
| vmSize | A virtuális gép mérete. Jelenleg csak a Standard A, D és G sorozatok támogatottak. |
| virtualNetworkName | Annak a virtuális hálózatnak a neve, amelyhez a virtuális hálózati adapternek tartoznia kell. |
| alhálózatneve | A virtuális hálózati adapterhez tartozó virtuális hálózat alhálózatának neve. |
| AADClientID | Az Azure AD-alkalmazás ügyfélazonosítója, amely rendelkezik a kulcstak a key vault ba írási engedélyekkel. |
| AADClientSecret | Az Azure AD-alkalmazás ügyféltka-tka, amely rendelkezik a kulcstak a key vault ba írási engedélyekkel. |
| keyVaultURL | Annak a key vaultnak az URL-címe, amelybe a BitLocker-kulcsot fel kell tölteni. Beszerezheti a parancsmag `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` vagy az Azure CLI használatával`az keyvault show --name "MySecureVault" --query properties.vaultUri` |
| keyEncryptionKeyURL | A létrehozott BitLocker kulcs titkosításához használt kulcstitkosítási kulcs URL-címe (nem kötelező). </br> </br>A KeyEncryptionKeyURL egy választható paraméter. Saját KEK-et hozhat a kulcstartóban lévő adattitkosítási kulcs (jelszótitkos kulcs) további védelme érdekében. |
| keyVaultResourceGroup | A kulcstartó erőforráscsoportja. |
| vmName | Annak a virtuális gépnek a neve, amelyen a titkosítási műveletet végre kell hajtani. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a>Titkosítás engedélyezése meglévő vagy futó IaaS Windows virtuális gépeken
Ebben az esetben engedélyezheti a titkosítást egy sablon, PowerShell-parancsmag vagy CLI-parancsok használatával. A következő szakaszok részletesebben ismertetik, hogyan engedélyezheti az Azure Disk Encryption. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a>Titkosítás engedélyezése meglévő vagy futó virtuális gépeken az Azure PowerShell használatával 
A [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezze a titkosítást egy futó IaaS virtuális gépen az Azure-ban. A titkosítás PowerShell-parancsmagokkal történő engedélyezéséről az [Azure PowerShell használatával az Azure Lemeztitkosítás felfedezése – 1. rész](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) és [az Azure Lemeztitkosítás felfedezése az Azure PowerShell](https://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)használatával – 2.

-  **Futó virtuális gép titkosítása ügyféltitkos adathasználatával:** Az alábbi parancsfájl inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension parancsmamot. Az erőforráscsoport, a virtuális gép, a key vault, az AAD-alkalmazás és az ügyféltitkos kulcsot már létre kell hozni előfeltételeként. Cserélje le a MyKeyVaultResourceGroup, a MyVirtualMachineResourceGroup, a MySecureVM, a MySecureVault, a My-AAD-client-ID és a My-AAD-client-secret értéket az Ön értékeivel.
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
- **Az ügyféltitok burkolására a KEK használatával futó virtuális gép titkosítása:** Az Azure Disk Encryption lehetővé teszi, hogy adjon meg egy meglévő kulcsot a kulcstartóban a titkosítás engedélyezése közben létrehozott lemeztitkosítási titkos kulcsok burkolásához. Kulcstitkosítási kulcs megadása esetén az Azure Disk Encryption ezt a kulcsot használja a titkosítási titkos kulcsok beburkolásához, mielőtt a Key Vaultba írna. 

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
   > A lemeztitkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás-kulcs paraméter értékének szintaxisa a KEK teljes URI-ja, mint például: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítottak-e:** Az IaaS virtuális gép titkosítási állapotának ellenőrzéséhez használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmag. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Lemeztitkosítás letiltása:** A titkosítás letiltásához használja az [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmag. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a>Titkosítás engedélyezése meglévő vagy futó virtuális gépeken az Azure CLI-vel
Használja az [az vm titkosításengedélyezése](/cli/azure/vm/encryption#az-vm-encryption-enable) parancs titkosítás engedélyezéséhez egy futó IaaS virtuális gép az Azure-ban.

- **Futó virtuális gép titkosítása ügyféltitkos adathasználatával:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Az ügyféltitok burkolására a KEK használatával futó virtuális gép titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > A lemeztitkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> A kulcs-titkosítás-kulcs paraméter értékének szintaxisa a KEK teljes URI-ja, mint például: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítottak-e:** Az IaaS virtuális gép titkosítási állapotának ellenőrzéséhez használja az [az vm titkosítási show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Titkosítás letiltása:** A titkosítás letiltásához használja az [az vm titkosítás letiltása](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>Az Erőforráskezelő sablon használata
A Lemeztitkosítást az Azure-ban meglévő vagy futó IaaS Windows-virtuális gépeken engedélyezheti az [Erőforrás-kezelő sablon használatával egy futó Windows virtuális gép titkosításához.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)


1. Az Azure gyorsindítási sablonján kattintson **a Telepítés az Azure-ba**elemre.

2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, a paramétereket, a jogi feltételeket és a megállapodást. Kattintson **a Vásárlás** gombra a meglévő vagy futó IaaS virtuális gép titkosításának engedélyezéséhez.

Az alábbi táblázat az Azure AD-ügyfélazonosítót használó meglévő vagy futó virtuális gépek Erőforrás-kezelő sablonparamétereit sorolja fel:

| Paraméter | Leírás |
| --- | --- |
| AADClientID | Az Azure AD-alkalmazás ügyfélazonosítója, amely rendelkezik a kulcstitok-beírási engedélyekkel. |
| AADClientSecret | Az Azure AD-alkalmazás ügyféltka-tka, amely rendelkezik a kulcstak a key vault ba írási engedélyekkel. |
| keyVaultName | Annak a kulcstartónak a neve, amelybe a BitLocker-kulcsot fel kell tölteni. Beszerezheti a parancsmag `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` vagy az Azure CLI parancs használatával`az keyvault list --resource-group "MySecureGroup"`|
|  keyEncryptionKeyURL | A létrehozott BitLocker-kulcs titkosításához használt kulcstitkosítási kulcs URL-címe. Ez a paraméter nem kötelező, ha **nokek** lehetőséget választ a UseExistingKek legördülő listában. Ha a UseExistingKek legördülő listában a **kek** lehetőséget választja, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| volumeType (kötettípusa) | A titkosítási művelet által végrehajtott kötet típusa. Érvényes értékek: _OPERÁCIÓS rendszer_, _Adatok_és _Mind_. |
| sequenceVersion (sequenceVersion) | A BitLocker művelet szekvenciaverziója. Ez a verziószám minden alkalommal, amikor egy lemez-titkosítási művelet et hajtanak végre ugyanazon a virtuális gépen. |
| vmName | Annak a virtuális gépnek a neve, amelyen a titkosítási műveletet végre kell hajtani. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Új IaaS virtuális gépek, amelyek ügyfél által titkosított virtuális merevlemezből és titkosítási kulcsokból készültek
Ebben az esetben engedélyezheti a titkosítást az Erőforrás-kezelő sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával. A következő szakaszok részletesebben ismertetik az Erőforrás-kezelő sablont és a CLI-parancsokat. 

A függelékben található utasításokat az Azure-ban használható előre titkosított lemezképek előkészítéséhez használja. A rendszerkép létrehozása után a következő szakaszban leírt lépésekkel létrehozhat egy titkosított Azure-beli virtuális gép.

* [Előre titkosított Windows virtuális merevlemez előkészítése](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"> </a> Virtuális gépek titkosítása előre titkosított virtuális számítógépekkel az Azure PowerShell használatával
A titkosított virtuális merevlemezen a [Set-AzVMOSDisk PowerShell-parancsmag](/powershell/module/az.compute/set-azvmosdisk#examples)segítségével engedélyezheti a lemeztitkosítást. Az alábbi példa néhány gyakori paramétert ad. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Titkosítás engedélyezése újonnan hozzáadott adatlemezen
Új [lemezt adhat hozzá egy Windows virtuális géphez](attach-disk-ps.md)a PowerShell használatával vagy [az Azure Portalon keresztül.](attach-managed-disk-portal.md) 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Titkosítás engedélyezése újonnan hozzáadott lemezen az Azure PowerShell használatával
 Ha a Powershell használatával titkosít egy új lemezt a Windows virtuális gépekhez, új szekvenciaverziót kell megadni. A szekvencia verziónak egyedinek kell lennie. Az alábbi parancsfájl létrehoz egy GUID-ot a szekvenciaverzióhoz. Bizonyos esetekben előfordulhat, hogy egy újonnan hozzáadott adatlemez automatikusan titkosítja az Azure Disk Encryption bővítmény. Automatikus titkosítás általában akkor fordul elő, amikor a virtuális gép újraindul, miután az új lemez online állapotba kerül. Ennek oka általában az, hogy "Minden" lett megadva a kötet típusához, amikor a lemeztitkosítás korábban futott a virtuális gépen. Ha automatikus titkosítás történik egy újonnan hozzáadott adatlemezen, javasoljuk, hogy futassza újra a Set-AzVmDiskEncryptionExtension parancsmagát az új szekvenciaverzióval. Ha az új adatlemez automatikusan titkosított, és nem szeretné, hogy titkosítsa, először fejtse vissza az összes meghajtót, majd fejteni egy új szekvenciaverzióval, amely a kötettípusoperációs rendszert adja meg. 
 

-  **Futó virtuális gép titkosítása ügyféltitkos adathasználatával:** Az alábbi parancsfájl inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension parancsmamot. Az erőforráscsoport, a virtuális gép, a key vault, az AAD-alkalmazás és az ügyféltitkos kulcsot már létre kell hozni előfeltételeként. Cserélje le a MyKeyVaultResourceGroup, a MyVirtualMachineResourceGroup, a MySecureVM, a MySecureVault, a My-AAD-client-ID és a My-AAD-client-secret értéket az Ön értékeivel. Ez a példa az "Összes" parancsot használja a -VolumeType paraméterhez, amely az operációs rendszer és az adatköteteket is tartalmazza. Ha csak az operációs rendszer kötetét szeretné titkosítani, használja az "OPERÁCIÓS rendszer" szót a -VolumeType paraméterhez. 

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
- **Az ügyféltitok burkolására a KEK használatával futó virtuális gép titkosítása:** Az Azure Disk Encryption lehetővé teszi, hogy adjon meg egy meglévő kulcsot a kulcstartóban a titkosítás engedélyezése közben létrehozott lemeztitkosítási titkos kulcsok burkolásához. Kulcstitkosítási kulcs megadása esetén az Azure Disk Encryption ezt a kulcsot használja a titkosítási titkos kulcsok beburkolásához, mielőtt a Key Vaultba írna. Ez a példa az "Összes" parancsot használja a -VolumeType paraméterhez, amely az operációs rendszer és az adatköteteket is tartalmazza. Ha csak az operációs rendszer kötetét szeretné titkosítani, használja az "OPERÁCIÓS rendszer" szót a -VolumeType paraméterhez. 

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
    > A lemeztitkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás-kulcs paraméter értékének szintaxisa a KEK teljes URI-ja, mint például: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Titkosítás engedélyezése újonnan hozzáadott lemezen az Azure CLI-vel
  Az Azure CLI parancs automatikusan egy új szekvenciaverziót biztosít a titkosítás engedélyezéséhez a parancs futtatásakor. A volume-yype paraméter elfogadható értékei: All, OS és Data. Előfordulhat, hogy módosítania kell a kötet-típusú paraméteroperációs rendszer vagy adat, ha csak egy típusú lemezt titkosít a virtuális gép. A példák a "Minden" a kötet-típusú paraméter. 

-  **Futó virtuális gép titkosítása ügyféltitkos adathasználatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Az ügyféltitok burkolására a KEK használatával futó virtuális gép titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Engedélyezze a titkosítást az Azure AD-ügyfél tanúsítványalapú hitelesítésével.
Az ügyféltanúsítvány-hitelesítés kek-vel vagy anélkül is használható. A PowerShell-parancsfájlok használata előtt már a tanúsítvány feltöltve a key vaultba, és a virtuális gépüzembe helyezett. Ha a KEK-et is használja, a KEK-nek már léteznie kell. További információkért tekintse meg a [tanúsítvány-alapú hitelesítés](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) az Azure AD az előfeltételek cikk.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Titkosítás engedélyezése tanúsítványalapú hitelesítéssel az Azure PowerShell használatával

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
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Titkosítás engedélyezése tanúsítványalapú hitelesítéssel és KEK-vel az Azure PowerShell használatával

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

## <a name="disable-encryption"></a>Titkosítás letiltása
Letilthatja a titkosítást az Azure PowerShell, az Azure CLI vagy a Resource Manager sablon használatával. 

- **Lemeztitkosítás letiltása az Azure PowerShell használatával:** A titkosítás letiltásához használja az [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmag. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Tiltsa le a titkosítást az Azure CLI-vel:** A titkosítás letiltásához használja az [az vm titkosítás letiltása](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Titkosítás letiltása Erőforrás-kezelő sablonnal:** 

    1. Kattintson **a Telepítés az Azure-ba** a [Lemeztitkosítás letiltása Windows vm sablonon.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)
    2. Válassza ki az előfizetést, az erőforráscsoportot, a helyet, a virtuális gép, a jogi feltételeket és a megállapodást.
    3.  Kattintson **a Vásárlás** gombra a lemeztitkosítás letiltásához egy futó Windows virtuális gépen. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure lemeztitkosítás – áttekintés](disk-encryption-overview.md)

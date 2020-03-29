---
title: Azure disk encryption with Azure AD App Linux IaaS virtuális gépek (előző kiadás)
description: Ez a cikk a Microsoft Azure lemeztitkosítás linuxos IaaS virtuális gépekhez való engedélyezésével kapcsolatos utasításokat tartalmaz.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: ee365d37a957350fa8a68da0f34149d3210d6238
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970607"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Az Azure Disk Encryption engedélyezése az Azure AD-vel Linux os virtuális gépeken (előző kiadás)

Az Azure Disk Encryption új kiadása kiküszöböli az Azure Active Directory (Azure AD) alkalmazásparaméter biztosításának követelményét a virtuális gép lemeztitkosításának engedélyezéséhez. Az új kiadás, már nem kell megadnia az Azure AD hitelesítő adatait a titkosítás engedélyezése lépés során. Minden új virtuális gép titkosítására az Azure AD-alkalmazás paraméterei az új kiadás használatával kell titkosítani. A virtuális gép lemeztitkosításának az új kiadás használatával történő engedélyezéséről az [Azure Disk Encryption for Linux VMS](disk-encryption-linux.md)című témakörben talál útmutatást. Az Azure AD-alkalmazás paramétereivel már titkosított virtuális gépek továbbra is támogatottak, és továbbra is karban kell tartani az AAD szintaxissal.

Számos lemeztitkosítási forgatókönyvet engedélyezhet, és a lépések a forgatókönyvtől függően változhatnak. A következő szakaszok részletesebben ismertetik a linuxos infrastruktúra szolgáltatásként (IaaS) virtuális gépek forgatókönyveit. Csak a [támogatott virtuális gépméretű és operációs rendszerű](disk-encryption-overview.md#supported-vms-and-operating-systems)virtuális gépekre alkalmazhat lemeztitkosítást. Az alábbi előfeltételeknek is meg kell felelnie:

- [A virtuális gépekre vonatkozó további követelmények](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Hálózat- és csoportházirend](disk-encryption-overview-aad.md#networking-and-group-policy)
- [Titkosítási kulcs tárolási követelményei](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

Készítsen [pillanatképet,](snapshot-copy-managed-disk.md)készítsen biztonsági másolatot, vagy mindkettőt a lemezek titkosítása előtt. A biztonsági mentések biztosítják, hogy a helyreállítási lehetőség lehetséges legyen, ha a titkosítás során váratlan hiba lép fel. A felügyelt lemezekkel rendelkező virtuális gépeknek biztonsági mentésre van szükségük a titkosítás előtt. A biztonsági mentés után a Set-AzVMDiskEncryptionExtension parancsmag segítségével titkosíthatja a felügyelt lemezeket a -skipVmBackup paraméter megadásával. A titkosított virtuális gépek biztonsági mentéséről és visszaállításáról az [Azure Backup című témakörben](../../backup/backup-azure-vms-encryption.md)talál további információt. 

>[!WARNING]
 > - Ha korábban az [Azure Disk Encryption az Azure AD alkalmazással](disk-encryption-overview-aad.md) a virtuális gép titkosításához, továbbra is ezt a lehetőséget kell használnia a virtuális gép titkosításához. Az [Azure Disk Encryption](disk-encryption-overview.md) nem használható ezen a titkosított virtuális gépen, mert ez nem támogatott forgatókönyv, ami azt jelenti, hogy a titkosított virtuális gép hez való váltás az Azure AD-alkalmazásról való váltás még nem támogatott.
 > - Győződjön meg arról, hogy a titkosítási titkok nem lépik át a regionális határokat, az Azure Disk Encryption szüksége van a kulcstartó és a virtuális gépek, hogy ugyanabban a régióban található. Hozzon létre és használjon egy key vault, amely ugyanabban a régióban, mint a virtuális gép titkosítva.
 > - A Linux operációs rendszer köteteinek titkosításakor a folyamat néhány órát is igénybe vehet. Normális, hogy a Linux operációs rendszer kötetei hosszabb időt vesznek igénybe, mint az adatkötetek titkosítása.
> - Linux operációs rendszer kötetek titkosításakor a virtuális gép nem érhető el. Javasoljuk, hogy kerülje az SSH bejelentkezéseket, amíg a titkosítás folyamatban van, hogy elkerülje a titkosítási folyamat során elérendő nyitott fájlok blokkolását. A folyamat ellenőrzéséhez használja a [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) vagy [a vm titkosítási show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsokat. Ez a folyamat várhatóan néhány órát vesz igénybe egy 30 GB-os operációs rendszer kötet, valamint további időt az adatkötetek titkosítására. Az adatkötet-titkosítási idő arányos az adatkötetek méretével és mennyiségével, kivéve, ha az **összes titkosítási formátumot** használja. 
 > - A titkosítás letiltása linuxos virtuális gépeken csak az adatkötetek esetén támogatott. Nem támogatott az adatok vagy az operációs rendszer kötetei, ha az operációs rendszer kötete titkosítva van. 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a> Titkosítás engedélyezése meglévő vagy futó IaaS Linux virtuális számítógépen

Ebben a forgatókönyvben engedélyezheti a titkosítást az Azure Resource Manager sablon, a PowerShell-parancsmagok vagy az Azure CLI-parancsok használatával. 

>[!IMPORTANT]
 >Az Azure Disk Encryption engedélyezésén kívül és előtt kötelező pillanatképet készíteni, vagy biztonsági másolatot készíteni egy felügyelt lemezalapú virtuálisgép-példányról. Pillanatképet készíthet a felügyelt lemezről az Azure Portalról, vagy használhatja az [Azure Backup szolgáltatást.](../../backup/backup-azure-vms-encryption.md) A biztonsági mentések biztosítják, hogy a titkosítás során benem szidható hiba esetén a helyreállítási lehetőség lehetséges legyen. A biztonsági mentés után a Set-AzVMDiskEncryptionExtension parancsmag segítségével titkosítsa a felügyelt lemezeket a -skipVmBackup paraméter megadásával. A Set-AzVMDiskEncryptionExtension parancs sikertelen a felügyelt lemezalapú virtuális gépeken, amíg biztonsági mentés készül, és ez a paraméter meg nem van adva. 
>
>Titkosítás vagy titkosítás letiltása okozhat a virtuális gép újraindítása. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Titkosítás engedélyezése meglévő vagy futó Linux virtuális számítógépen az Azure CLI használatával 
Az [Azure CLI 2.0](/cli/azure) parancssori eszköz telepítésével és használatával engedélyezheti a lemeztitkosítást a titkosított virtuális merevlemezen. Használhatja a böngészőjében az [Azure Cloud Shell-lel](../../cloud-shell/overview.md), vagy telepítheti a helyi gépen, és használhatja bármely PowerShell-munkamenetben. A titkosítás engedélyezéséhez az Azure-ban meglévő vagy futó IaaS Linux-virtuális gépeken a következő CLI-parancsokat használja:

Használja az [az vm titkosításengedélyezése](/cli/azure/vm/encryption#az-vm-encryption-enable) parancs titkosítás engedélyezéséhez egy futó IaaS virtuális gép az Azure-ban.

-  **Futó virtuális gép titkosítása ügyféltitkos adat használatával:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Az ügyféltitok titkosítása a KEK használatával:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > A lemeztitkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br> A kulcstitkosítás-kulcs paraméter értékének szintaxisa a KEK teljes URI-ja, mint például: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Az IaaS virtuális gép titkosítási állapotának ellenőrzéséhez használja az [az vm titkosítási show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Titkosítás letiltása:** A titkosítás letiltásához használja az [az vm titkosítás letiltása](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. A titkosítás letiltása csak linuxos virtuális gépek adatkötetein engedélyezett.
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"> </a> Titkosítás engedélyezése meglévő vagy linuxos virtuális számítógépen a PowerShell használatával
A [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezze a titkosítást egy futó IaaS virtuális gépen az Azure-ban. Készítsen [pillanatképet,](snapshot-copy-managed-disk.md) vagy készítsen biztonsági másolatot a virtuális gépről az [Azure Backup](../../backup/backup-azure-vms-encryption.md) szolgáltatással a lemezek titkosítása előtt. A -skipVmBackup paraméter már meg van adva a PowerShell-parancsfájlokban egy futó Linux virtuális gép titkosításához.

- **Futó virtuális gép titkosítása ügyféltitkos adat használatával:** A következő parancsfájl inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension parancsmag. Az erőforráscsoport, a virtuális gép, a key vault, az Azure AD-alkalmazás és az ügyféltitkos kulcsot már létre kell hozni előfeltételekként. Cserélje le a MyVirtualMachineResourceGroup, a MyKeyVaultResourceGroup, a MySecureVM, a MySecureVault, a My-AAD-client-ID és a My-AAD-client-secret értéket az Ön értékeivel. Módosítsa a -VolumeType paramétert a titkosítani kívánt lemezek megadásához.

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
- **Az ügyféltitok titkosítása a KEK használatával:** Az Azure Disk Encryption lehetővé teszi, hogy adjon meg egy meglévő kulcsot a kulcstartóban a titkosítás engedélyezése közben létrehozott lemeztitkosítási titkos kulcsok burkolásához. Kulcstitkosítási kulcs megadása esetén az Azure Disk Encryption ezt a kulcsot használja a titkosítási titkos kulcsok burkolásához, mielőtt a key vaultba írna. Módosítsa a -VolumeType paramétert a titkosítani kívánt lemezek megadásához. 

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
  > A lemeztitkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name].</br> </br>
  A kulcstitkosítás-kulcs paraméter értékének szintaxisa a KEK teljes URI-ja, mint például: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]. 
    
- **Ellenőrizze, hogy a lemezek titkosítva vannak-e:** Az IaaS virtuális gép titkosítási állapotának ellenőrzéséhez használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmag. 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Lemeztitkosítás letiltása:** A titkosítás letiltásához használja az [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmag. A titkosítás letiltása csak linuxos virtuális gépek adatkötetein engedélyezett.
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a> Titkosítás engedélyezése meglévő vagy futó IaaS Linux virtuális számítógépen sablonnal

A [Resource Manager sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)használatával engedélyezheti a lemeztitkosítást egy meglévő vagy futó IaaS Linux virtuális gépen az Azure-ban.

1. Válassza **az Üzembe helyezés az Azure-ban** az Azure gyorsindítási sablonon lehetőséget.

2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, a paramétereket, a jogi feltételeket és a megállapodást. Válassza **a Létrehozás lehetőséget** a meglévő vagy futó IaaS virtuális gép titkosításának engedélyezéséhez.

Az alábbi táblázat az Azure AD-ügyfélazonosítót használó meglévő vagy futó virtuális gépek Erőforrás-kezelősablon-paramétereit sorolja fel:

| Paraméter | Leírás |
| --- | --- |
| AADClientID | Az Azure AD-alkalmazás ügyfélazonosítója, amely rendelkezik a kulcstitok-beírási engedélyekkel. |
| AADClientSecret | Az Azure AD-alkalmazás ügyféltka-tka, amely rendelkezik a kulcstak a key vault ba írási engedélyekkel. |
| keyVaultName | Annak a kulcstartónak a neve, amelybe a kulcsot fel kell tölteni. Az Azure CLI paranccsal `az keyvault show --name "MySecureVault" --query KVresourceGroup`szerezheti be. |
|  keyEncryptionKeyURL | A létrehozott kulcs titkosításához használt kulcstitkosítási kulcs URL-címe. Ez a paraméter nem kötelező, ha **nokek** lehetőséget választ a **UseExistingKek** legördülő listában. Ha a **UseExistingKek** legördülő listában a **kek** lehetőséget választja, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| volumeType (kötettípusa) | A titkosítási művelet által végrehajtott kötet típusa. Az érvényes támogatott értékek az _operációs rendszer_ vagy _az Összes_. (Lásd a támogatott Linux-disztribúciók és azok verzióit operációs rendszer és adatlemezek az előfeltételek szakaszban korábban.) |
| sequenceVersion (sequenceVersion) | A BitLocker művelet szekvenciaverziója. Ez a verziószám minden alkalommal, amikor egy lemez-titkosítási művelet et hajtanak végre ugyanazon a virtuális gépen. |
| vmName | Annak a virtuális gépnek a neve, amelyen a titkosítási műveletet végre kell hajtani. |
| Hozzáférési kód | Írjon be egy erős jelszót adattitkosítási kulcsként. |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>A UsecryptFormatAll szolgáltatás használata a Linux IaaS virtuális gépeken lévő adatlemezekhez
Az EncryptFormatAll paraméter csökkenti a Linux-adatlemezek titkosítási idejét. A bizonyos feltételeknek megfelelő partíciók formázása (a jelenlegi fájlrendszerükkel) történik. Aztán visszaállnak oda, ahol a parancs végrehajtása előtt voltak. Ha ki szeretne zárni egy olyan adatlemezt, amely megfelel a feltételeknek, a parancs futtatása előtt leválaszthatja azt.

 A parancs futtatása után a korábban csatlakoztatott meghajtók formázásra kerülnek. Ezután a titkosítási réteg a most üres meghajtó tetején kezdődik. Ha ez a beállítás be van jelölve, a virtuális géphez csatlakoztatott ideiglenes erőforráslemez is titkosítva lesz. Ha a rövid élettartamú meghajtó alaphelyzetbe áll, a virtuális gép újraés titkosított, és újra titkosítja az Azure Disk Encryption megoldás a következő lehetőséget.

>[!WARNING]
> A EncryptFormatAll nem használható, ha a virtuális gép adatkötetein szükséges adatok szükségesek. A lemezeket leválasztással kizárhatja a titkosításból. Próbálja ki a EncryptFormatAll paramétert egy teszt virtuális gépen, hogy először is megértse a jellemző paramétert és annak kihancendulációját, mielőtt megpróbálna az éles virtuális gépen. Az EncryptFormatAll beállítás formázza az adatlemezt, így a rajta lévő összes adat elvész. A folytatás előtt ellenőrizze, hogy a kizárni kívánt lemezek megfelelően vannak-e csatlakoztatva. </br></br>
 >Ha a titkosítási beállítások frissítésekénél ezt a paramétert állítja be, az a tényleges titkosítás előtti újraindításhoz vezethet. Ebben az esetben azt a lemezt is el szeretné távolítani, amelyet nem kíván formázni az fstab fájlból. Hasonlóképpen a titkosítási művelet megkezdése előtt hozzá kell adnia a titkosítani kívánt partíciót az fstab fájlhoz. 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"> </a> FormatAll titkosítási feltétel
A paraméter végigmegy az összes partíción, és titkosítja őket, feltéve, hogy *megfelelnek* az alábbi feltételek mindegyikének: 
- Nem gyökér/OS/boot partíció
- Nincs még titkosítva
- Nem BEK-kötet
- Nem RAID-kötet
- Nem LVM-kötet
- Csatlakoztatva van

A RAID vagy LVM kötetet alkotó lemezek titkosítása a RAID vagy LVM kötet helyett.

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a> Az EncryptFormatAll paraméter használata sablonnal
A Usethe EncryptFormatAll beállítás használatához használjon olyan meglévő Azure Resource Manager sablont, amely titkosítja a Linux virtuális gépeket, és módosítja az AzureDiskEncryption erőforrás **EncryptionOperation** mezőjét.

1. Például az [Erőforrás-kezelő sablon használatával titkosíthatja a futó Linux IaaS virtuális gép.](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm) 
2. Válassza **az Üzembe helyezés az Azure-ban** az Azure gyorsindítási sablonon lehetőséget.
3. Módosítsa a **EncryptionOperation** mezőt **enableencryption** ról **EnableEncryptionFormatAl (EnableEncryptionFormatAl )** mezőre.
4. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, az egyéb paramétereket, a jogi feltételeket és a megállapodást. Válassza **a Létrehozás lehetőséget** a meglévő vagy futó IaaS virtuális gép titkosításának engedélyezéséhez.


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"> </a> A EncryptFormatAll paraméter használata PowerShell-parancsmaggal
Használja a [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmagát az EncryptFormatAll paraméterrel.

**A futó virtuális gép titkosítása ügyféltitkos adattal és EncryptFormatAll használatával:** Például a következő parancsfájl inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension parancsmamot az EncryptFormatAll paraméterrel. Az erőforráscsoport, a virtuális gép, a key vault, az Azure AD-alkalmazás és az ügyféltitkos kulcsot már létre kell hozni előfeltételekként. Cserélje le a MyKeyVaultResourceGroup, a MyVirtualMachineResourceGroup, a MySecureVM, a MySecureVault, a My-AAD-client-ID és a My-AAD-client-secret értéket az Ön értékeivel.
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"> </a> A EncryptFormatAll paraméter használata logikai kötetkezelővel (LVM) 
LvM-on-crypt beállítást ajánlunk. Az alábbi példákban cserélje le az eszköz elérési útját és csatlakoztatási pontjait a használati esetnek megfelelőre. Ez a beállítás a következőképpen végezhető el:

- Adja hozzá a virtuális gép alkotó adatlemezeket.
- Formázza, csatlakoztassa és adja hozzá ezeket a lemezeket az fstab fájlhoz.

    1. Formázza az újonnan hozzáadott lemezt. Az Azure által generált symlinks-et itt használjuk. A symlinks használatával elkerülhetők az eszköznevek módosításával kapcsolatos problémák. További információt az [Eszköznevekkel kapcsolatos problémák elhárítása című témakörben](troubleshoot-device-names-problems.md)talál.
    
             `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
        
    2. Csatlakoztassa a lemezeket.
         
             `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`    
        
    3. Add hozzá az fstab-hoz.
         
            `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
        
    4. Futtassa a Set-AzVMDiskEncryptionExtension PowerShell-parancsmagját a -EncryptFormatAll kapcsolóval a lemezek titkosításához.
             ```azurepowershell-interactive
             Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
             ```
    5. Állítsa be az LVM-et az új lemezek re. Vegye figyelembe, hogy a titkosított meghajtók zárolása fel van oldva, miután a virtuális gép elindult. Így az LVM szerelését is később el kell halasztani.




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a> Új IaaS virtuális gépek, amelyek ügyfél által titkosított virtuális merevlemezből és titkosítási kulcsokból készültek
Ebben az esetben engedélyezheti a titkosítást az Erőforrás-kezelő sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával. A következő szakaszok részletesebben ismertetik az Erőforrás-kezelő sablont és a CLI-parancsokat. 

A függelékben található utasításokat az Azure-ban használható előre titkosított lemezképek előkészítéséhez használja. A rendszerkép létrehozása után a következő szakaszban leírt lépésekkel létrehozhat egy titkosított Azure-beli virtuális gép.

* [Előre titkosított Linux-virtuális merevlemez előkészítése](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Az Azure Disk Encryption engedélyezésén kívül és előtt kötelező pillanatképet készíteni, vagy biztonsági másolatot készíteni egy felügyelt lemezalapú virtuálisgép-példányról. Pillanatképet készíthet a felügyelt lemezről a portálról, vagy használhatja az [Azure Backup szolgáltatást.](../../backup/backup-azure-vms-encryption.md) A biztonsági mentések biztosítják, hogy a titkosítás során benem szidható hiba esetén a helyreállítási lehetőség lehetséges legyen. A biztonsági mentés után a Set-AzVMDiskEncryptionExtension parancsmag segítségével titkosítsa a felügyelt lemezeket a -skipVmBackup paraméter megadásával. A Set-AzVMDiskEncryptionExtension parancs sikertelen a felügyelt lemezalapú virtuális gépeken, amíg biztonsági mentés készül, és ez a paraméter meg nem van adva. 
>
>Titkosítás vagy titkosítás letiltása okozhat a virtuális gép újraindítása.



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a> Az Azure PowerShell használatával titkosíthatja az IaaS virtuális gépeket előre titkosított virtuális számítógépekkel 
A titkosított virtuális merevlemezen a [Set-AzVMOSDisk PowerShell-parancsmag](/powershell/module/az.compute/set-azvmosdisk#examples)segítségével engedélyezheti a lemeztitkosítást. A következő példa néhány gyakori paramétert ad. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Titkosítás engedélyezése újonnan hozzáadott adatlemezen
Új adatlemezt adhat hozzá [az vm lemezcsatolás](add-disk.md) vagy [az Azure Portal használatával.](attach-disk-portal.md) A titkosítás előtt először csatlakoztatnia kell az újonnan csatlakoztatott adatlemezt. Az adatmeghajtó titkosítását kell kérnie, mert a meghajtó használhatatlan lesz, amíg a titkosítás folyamatban van. 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Titkosítás engedélyezése újonnan hozzáadott lemezen az Azure CLI-vel
 Ha a virtuális gép korábban "All" titkosítással volt titkosítva, akkor a --volume-type paraméternek az összesnek kell maradnia. Minden tartalmazza mind az operációs rendszer és az adatlemezek. Ha a virtuális gép korábban titkosítva volt egy "os" kötettípussal, akkor a --volume-type paramétert all-ra kell módosítani, hogy mind az operációs rendszer, mind az új adatlemez szerepeljen. Ha a virtuális gép csak az "Adatok" kötettípussal volt titkosítva, akkor az adatok maradhatnak az itt bemutatott módon. Új adatlemez hozzáadása és csatolása a virtuális géphez nem elegendő a titkosítás előkészítése. A titkosítás engedélyezése előtt az újonnan csatlakoztatott lemezt is formázni és megfelelően csatlakoztatni kell a virtuális gépen belül. Linux alatt a lemezt az /etc/fstab kapcsolóba kell csatlakoztatni [egy állandó blokkeszköz névvel.](troubleshoot-device-names-problems.md) 

A Powershell szintaxisával ellentétben a CLI nem követeli meg, hogy a titkosítás engedélyezésekor egyedi szekvenciaverziót adjon meg. A CLI automatikusan létrehozza és felhasználja a saját egyedi szekvencia verzióértékét.

-  **Futó virtuális gép titkosítása ügyféltitkos adat használatával:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Az ügyféltitok titkosítása a KEK használatával:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Titkosítás engedélyezése újonnan hozzáadott lemezen az Azure PowerShell használatával
 Ha a Powershell használatával titkosít egy új linuxos lemezt, új sorozatverziót kell megadni. A szekvencia verziónak egyedinek kell lennie. A következő parancsfájl létrehoz egy GUID-ot a szekvenciaverzióhoz. 
 

-  **Futó virtuális gép titkosítása ügyféltitkos adat használatával:** A következő parancsfájl inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension parancsmag. Az erőforráscsoport, a virtuális gép, a key vault, az Azure AD-alkalmazás és az ügyféltitkos kulcsot már létre kell hozni előfeltételekként. Cserélje le a MyVirtualMachineResourceGroup, a MyKeyVaultResourceGroup, a MySecureVM, a MySecureVault, a My-AAD-client-ID és a My-AAD-client-secret értéket az Ön értékeivel. A -VolumeType paraméter adatlemezekre van beállítva, nem pedig az operációs rendszer lemezére. Ha a virtuális gép korábban "OS" vagy "All" kötettípussal volt titkosítva, akkor a -VolumeType paramétert all-ra kell módosítani, hogy mind az operációs rendszer, mind az új adatlemez szerepeljen.

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
- **Az ügyféltitok titkosítása a KEK használatával:** Az Azure Disk Encryption lehetővé teszi, hogy adjon meg egy meglévő kulcsot a kulcstartóban a titkosítás engedélyezése közben létrehozott lemeztitkosítási titkos kulcsok burkolásához. Kulcstitkosítási kulcs megadása esetén az Azure Disk Encryption ezt a kulcsot használja a titkosítási titkos kulcsok burkolásához, mielőtt a key vaultba írna. A -VolumeType paraméter adatlemezekre van beállítva, nem pedig az operációs rendszer lemezére. Ha a virtuális gép korábban "OS" vagy "All" kötettípussal volt titkosítva, akkor a -VolumeType paramétert all-ra kell módosítani, hogy mind az operációs rendszer, mind az új adatlemez szerepeljen.

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
> A lemeztitkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]. </br> </br>
A kulcstitkosítás-kulcs paraméter értékének szintaxisa a KEK teljes URI-ja, mint például: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id].

## <a name="disable-encryption-for-linux-vms"></a>Linuxos virtuális gépek titkosításának letiltása
Letilthatja a titkosítást az Azure PowerShell, az Azure CLI vagy a Resource Manager sablon használatával. 

>[!IMPORTANT]
>A titkosítás letiltása az Azure Disk Encryption linuxos virtuális gépeken csak az adatkötetek támogatott. Nem támogatott az adatok vagy az operációs rendszer kötetei, ha az operációs rendszer kötete titkosítva van. 

- **Lemeztitkosítás letiltása az Azure PowerShell használatával:** A titkosítás letiltásához használja az [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagját. 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Tiltsa le a titkosítást az Azure CLI-vel:** A titkosítás letiltásához használja az [az vm titkosítás letiltása](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Titkosítás letiltása Erőforrás-kezelő sablonnal:** A titkosítás letiltásához használja a [Titkosítás letiltása egy futó Linux vm](https://aka.ms/decrypt-linuxvm) sablont.
     1. Válassza **a Telepítés az Azure-ba**lehetőséget.
     2. Válassza ki az előfizetést, az erőforráscsoportot, a helyet, a virtuális gép, a jogi feltételeket és a megállapodást.
     3. Válassza a **Vásárlás** lehetőséget a lemeztitkosítás letiltásához egy futó Windows virtuális gépen. 


## <a name="next-steps"></a>További lépések

- [Azure Disk Encryption for Linux – áttekintés](disk-encryption-overview-aad.md)
- [Az Azure Disk Encryption kulcstárolójának létrehozása és konfigurálása az Azure AD-vel (előző kiadás)](disk-encryption-key-vault-aad.md)

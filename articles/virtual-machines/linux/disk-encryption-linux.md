---
title: Azure Disk Encryption-forgatókönyvek Linux rendszerű virtuális gépekhez
description: Ez a cikk a Microsoft Azure lemeztitkosítás linuxos virtuális gépekhez való engedélyezésére vonatkozó utasításokat tartalmazza a különböző forgatókönyvekhez
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 6b60ccc7a635e4b6071b43d7ff75e182aa96cd08
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313629"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Azure Disk Encryption-forgatókönyvek Linux rendszerű virtuális gépekhez


Az Azure Disk Encryption for Linux virtuális gépek (VM-ek) a Linux DM-Crypt szolgáltatását használja az operációs rendszer lemezének és adatlemezeinek teljes lemeztitkosításának biztosításához. Emellett biztosítja a rövid élettartamú erőforráslemez titkosítását az EncryptFormatAll szolgáltatás használatakor.

Az Azure Disk Encryption integrálva van az [Azure Key Vaultszolgáltatással,](disk-encryption-key-vault.md) így szabályozhatja és kezelheti a lemeztitkosítási kulcsokat és titkos kulcsokat. A szolgáltatás áttekintését az Azure Disk Encryption for Linux virtuális gépek című [témakörben találja.](disk-encryption-overview.md)

Csak a [támogatott virtuális gépméretű és operációs rendszerű](disk-encryption-overview.md#supported-vms-and-operating-systems)virtuális gépekre alkalmazhat lemeztitkosítást. Az alábbi előfeltételeknek is meg kell felelnie:

- [A virtuális gépekre vonatkozó további követelmények](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Hálózati követelmények](disk-encryption-overview.md#networking-requirements)
- [Titkosítási kulcs tárolási követelményei](disk-encryption-overview.md#encryption-key-storage-requirements)

A lemezek titkosítása előtt minden esetben [készítsen pillanatképet](snapshot-copy-managed-disk.md) és/vagy hozzon létre biztonsági másolatot. A biztonsági mentések biztosítják, hogy a helyreállítási lehetőség lehetséges legyen, ha a titkosítás során váratlan hiba lép fel. A felügyelt lemezekkel rendelkező virtuális gépeknek biztonsági mentésre van szükségük a titkosítás előtt. A biztonsági mentés tkövetően a [Set-AzVMDiskEncryptionExtension parancsmag](/powershell/module/az.compute/set-azvmdiskencryptionextension) segítségével titkosíthatja a felügyelt lemezeket a -skipVmBackup paraméter megadásával. A titkosított virtuális gépek biztonsági mentéséről és visszaállításáról az Azure Backup cikkében olvashat [bővebben.](../../backup/backup-azure-vms-encryption.md) 

>[!WARNING]
> - Ha korábban már használta az Azure Lemeztitkosítás t az Azure AD-vel egy virtuális gép titkosításához, továbbra is ezt a lehetőséget kell használnia a virtuális gép titkosításához. A részletekért tekintse meg [az Azure Disk Encryption with Azure AD (előző kiadás)](disk-encryption-overview-aad.md) című témakört. 
>
> - Linux operációs rendszer kötetek titkosításakor a virtuális gép nem érhető el. Javasoljuk, hogy kerülje az SSH bejelentkezéseket, amíg a titkosítás folyamatban van, hogy elkerülje a nyitott fájlok blokkolását, amelyeket a titkosítási folyamat során el kell érni. A folyamat ellenőrzéséhez használja a [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell parancsmag vagy a [vm titkosítási show](/cli/azure/vm/encryption#az-vm-encryption-show) CLI parancsot. Ez a folyamat várhatóan néhány órát vesz igénybe egy 30 GB-os operációs rendszer kötet, valamint további időt az adatkötetek titkosítására. Az adatkötet-titkosítási idő arányos lesz az adatkötetek méretével és mennyiségével, kivéve, ha az összes titkosítási formátumot használja. 
> - A titkosítás letiltása linuxos virtuális gépeken csak az adatkötetek esetén támogatott. Adat- vagy operációsrendszer-kötetek en nem támogatott, ha az operációs rendszer kötete titkosítva van.  

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és csatlakozás az Azure-hoz

Az Azure Disk Encryption az [Azure CLI-n](/cli/azure) és az [Azure PowerShellen](/powershell/azure/new-azureps-module-az)keresztül engedélyezhető és kezelhető. Ehhez helyileg kell telepítenie az eszközöket, és csatlakoznia kell az Azure-előfizetéséhez.

### <a name="azure-cli"></a>Azure CLI

Az [Azure CLI 2.0](/cli/azure) egy parancssori eszköz az Azure-erőforrások kezeléséhez. A CLI-t úgy tervezték, hogy rugalmasan lekérdezze az adatokat, támogassa a hosszú ideig futó műveleteket nem blokkoló folyamatként, és megkönnyítse a parancsfájlok futtatását. Helyileg telepítheti az [Azure CLI telepítése](/cli/azure/install-azure-cli?view=azure-cli-latest)című témakör lépéseit követve.

 

Jelentkezzen [be az Azure-fiókjába az Azure CLI-vel,](/cli/azure/authenticate-azure-cli)használja az [az bejelentkezési](/cli/azure/reference-index?view=azure-cli-latest#az-login) parancsot.

```azurecli
az login
```

Ha ki szeretne választani egy bérlőt, aki be szeretne jelentkezni, használja a következőt:
    
```azurecli
az login --tenant <tenant>
```

Ha több előfizetéssel rendelkezik, és meg szeretne adni egy adott előfizetési listát, az [az-fióklistával](/cli/azure/account#az-account-list) kapja meg az előfizetési listát, és adja meg [az az fiókkészlettel.](/cli/azure/account#az-account-set)
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

További információ: [Az Azure CLI 2.0 – Első lépések.](/cli/azure/get-started-with-azure-cli) 

### <a name="azure-powershell"></a>Azure PowerShell
Az [Azure PowerShell az modul](/powershell/azure/new-azureps-module-az) egy parancsmagkészletet biztosít, amely az Azure Resource [Manager](../../azure-resource-manager/management/overview.md) modellt használja az Azure-erőforrások kezeléséhez. Használhatja a böngészőben az [Azure Cloud Shell,](../../cloud-shell/overview.md)vagy telepítheti a helyi gépen az Utasítások [telepítése az Azure PowerShell modul.](/powershell/azure/install-az-ps) 

Ha már telepítette helyileg, győződjön meg arról, hogy az Azure PowerShell SDK-verzió legújabb verzióját használja az Azure Disk Encryption konfigurálásához. Töltse le az [Azure PowerShell-kiadás](https://github.com/Azure/azure-powershell/releases)legújabb verzióját.

Ha [be szeretne jelentkezni az Azure PowerShell használatával az Azure-fiókba,](/powershell/azure/authenticate-azureps?view=azps-2.5.0)használja a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) parancsmagját.

```powershell
Connect-AzAccount
```

Ha több előfizetéssel rendelkezik, és meg szeretne adni egyet, a [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) parancsmag segítségével sorolja fel őket, majd a [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) parancsmag:

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

A [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) parancsmag futtatása ellenőrzi, hogy a megfelelő előfizetés lett-e kiválasztva.

Az Azure Disk Encryption parancsmagok telepítésének ellenőrzéséhez használja a [Get-command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) parancsmab:
     
```powershell
Get-command *diskencryption*
```
További információ: [Az Azure PowerShell – első lépések.](/powershell/azure/get-started-azureps) 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Titkosítás engedélyezése meglévő vagy futó Linux virtuális gépen
Ebben az esetben engedélyezheti a titkosítást az Erőforrás-kezelő sablon, a PowerShell-parancsmagok vagy a CLI-parancsok használatával. Ha sémainformációkra van szüksége a virtuálisgép-bővítményhez, tekintse meg az [Azure Disk Encryption for Linux extension](../extensions/azure-disk-enc-linux.md) cikket.

>[!IMPORTANT]
 >Az Azure Disk Encryption engedélyezését megelőzően kötelező pillanatképet készíteni és/vagy biztonsági másolatot készíteni egy felügyelt lemezalapú virtuálisgép-példányról. A felügyelt lemez pillanatképe a portálról vagy az [Azure Backup szolgáltatásból](../../backup/backup-azure-vms-encryption.md)készíthető. A biztonsági mentések biztosítják, hogy a titkosítás során benem szidható hiba esetén a helyreállítási lehetőség lehetséges legyen. A biztonsági mentés tkövetően a Set-AzVMDiskEncryptionExtension parancsmag a -skipVmBackup paraméter megadásával titkosíthatja a felügyelt lemezeket. A Set-AzVMDiskEncryptionExtension parancs sikertelen lesz a felügyelt lemezalapú virtuális gépeken, amíg nem készült biztonsági másolat, és meg nem adta ezt a paramétert. 
>
>Titkosítás vagy titkosítás letiltása okozhat a virtuális gép újraindítása. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Titkosítás engedélyezése meglévő vagy futó Linux os virtuális számítógépen az Azure CLI használatával 

Az [Azure CLI parancssori](/cli/azure/?view=azure-cli-latest) eszköz telepítésével és használatával engedélyezheti a lemeztitkosítást a titkosított virtuális merevlemezen. Használhatja a böngészőjében az [Azure Cloud Shell-lel](../../cloud-shell/overview.md), vagy telepítheti a helyi gépen, és használhatja bármely PowerShell-munkamenetben. A titkosítás engedélyezéséhez az Azure-ban meglévő vagy futó Linux-virtuális gépeken a következő CLI-parancsokat használja:

Használja az [az vm titkosításengedélyezése](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show) parancs titkosítás engedélyezéséhez egy futó virtuális gépen az Azure-ban.

- **Futó virtuális gép titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Futó virtuális gép titkosítása KEK használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > A lemeztitkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
A kulcs-titkosítás-kulcs paraméter értékének szintaxisa a KEK teljes URI-ja, mint például: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Ellenőrizze, hogy a lemezek titkosítottak-e:** A virtuális gép titkosítási állapotának ellenőrzéséhez használja az [az vm titkosítási show](/cli/azure/vm/encryption#az-vm-encryption-show) parancsot. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Titkosítás letiltása:** A titkosítás letiltásához használja az [az vm titkosítás letiltása](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. A titkosítás letiltása csak linuxos virtuális gépek adatkötetein engedélyezett.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Titkosítás engedélyezése meglévő vagy linuxos virtuális számítógépen a PowerShell használatával
A [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmag használatával engedélyezze a titkosítást egy futó virtuális gépen az Azure-ban. Készítsen [pillanatképet,](snapshot-copy-managed-disk.md) és/vagy készítsen biztonsági másolatot a virtuális gépről az [Azure Backup](../../backup/backup-azure-vms-encryption.md) szolgáltatással, mielőtt a lemezek titkosítva lesznek. A -skipVmBackup paraméter már meg van adva a PowerShell-parancsfájlokban egy futó Linux virtuális gép titkosításához.

-  **Futó virtuális gép titkosítása:** Az alábbi parancsfájl inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension parancsmamot. Az erőforráscsoport, a virtuális gép és a key vault előfeltételekként jött létre. Cserélje le a MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értékeket. Módosítsa a -VolumeType paramétert a titkosítani kívánt lemezek megadásához.

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
- **Futó virtuális gép titkosítása KEK használatával:** Előfordulhat, hogy a -VolumeType paramétert kell hozzáadnia, ha adatlemezeket titkosít, és nem az operációs rendszer lemezét. 

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
    > A lemeztitkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás-kulcs paraméter értékének szintaxisa a KEK teljes URI-ja, mint például: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Ellenőrizze, hogy a lemezek titkosítottak-e:** A virtuális gép titkosítási állapotának ellenőrzéséhez használja a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsmag. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Lemeztitkosítás letiltása:** A titkosítás letiltásához használja az [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagját. A titkosítás letiltása csak linuxos virtuális gépek adatkötetein engedélyezett.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Titkosítás engedélyezése meglévő vagy linuxos virtuális gépen sablonnal

A [Resource Manager sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)használatával engedélyezheti a lemeztitkosítást egy meglévő vagy linuxos virtuális gépen az Azure-ban.

1. Kattintson **a Telepítés az Azure-ba** az Azure gyorsindítási sablonon.

2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, a paramétereket, a jogi feltételeket és a megállapodást. Kattintson a **Létrehozás** gombra a meglévő vagy a futó virtuális gép titkosításának engedélyezéséhez.

Az alábbi táblázat a meglévő vagy futó virtuális gépek Erőforrás-kezelő sablonparamétereit sorolja fel:

| Paraméter | Leírás |
| --- | --- |
| vmName | A titkosítási művelet futtatásához használt virtuális gép neve. |
| keyVaultName | Annak a kulcstartónak a neve, amelybe a titkosítási kulcsot fel kell tölteni. A parancsmag `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` vagy az Azure CLI parancs `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`használatával szerezheti be.|
| keyVaultResourceGroup | A key vaultot tartalmazó erőforráscsoport neve. |
|  keyEncryptionKeyURL | A titkosítási kulcs titkosításához használt kulcstitkosítási kulcs URL-címe. Ez a paraméter nem kötelező, ha **nokek** lehetőséget választ a UseExistingKek legördülő listában. Ha a UseExistingKek legördülő listában a **kek** lehetőséget választja, meg kell adnia a _keyEncryptionKeyURL_ értéket. |
| volumeType (kötettípusa) | A titkosítási művelet által végrehajtott kötet típusa. Érvényes értékek: _OPERÁCIÓS rendszer_, _Adatok_és _Mind_. 
| forceUpdateTag | Adja át egy egyedi értéket, például egy GUID-ot minden alkalommal, amikor a műveletet erőfuttatásra van szüksége. |
| location | Az összes erőforrás helye. |

A Linux vm lemeztitkosítási sablon konfigurálásáról az [Azure Disk Encryption for Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/azure-disk-enc-linux)című témakörben olvashat bővebben.

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Az EncryptFormatAll szolgáltatás használata linuxos virtuális gépeken lévő adatlemezekhez

Az **EncryptFormatAll** paraméter csökkenti a Linux-adatlemezek titkosítási idejét. A bizonyos feltételeknek megfelelő partíciók formázásra kerülnek (a jelenlegi fájlrendszerrel), majd visszakerülnek oda, ahol a parancs végrehajtása előtt volt. Ha olyan adatlemezt szeretne kizárni, amely megfelel a feltételeknek, a parancs futtatása előtt leválaszthatja azt.

 A parancs futtatása után a korábban csatlakoztatott meghajtók formázódnak, és a titkosítási réteg a most üres meghajtó tetején indul el. Ha ez a beállítás be van jelölve, a virtuális géphez csatlakoztatott ideiglenes erőforráslemez is titkosítva lesz. Ha a rövid élettartamú meghajtó alaphelyzetbe áll, a virtuális gép újraés titkosított, és újra titkosítja az Azure Disk Encryption megoldás a következő lehetőséget. Miután az erőforráslemez titkosítva lett, a [Microsoft Azure Linux-ügynök](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) nem fogja tudni kezelni az erőforráslemezt, és nem fogja tudni engedélyezni a lapozófájlt, de manuálisan konfigurálhatja a lapozófájlt.

>[!WARNING]
> A EncryptFormatAll nem használható, ha a virtuális gép adatkötetein szükséges adatok szükségesek. A lemezeket leválaszthatja a titkosításból. Először próbálja ki a EncryptFormatAll először egy teszt virtuális gép, megérteni a jellemző paraméter és annak következménye, mielőtt megpróbálja az éles virtuális gép. Az EncryptFormatAll beállítás formázza az adatlemezt, és a rajta lévő összes adat elvész. A folytatás előtt ellenőrizze, hogy a kizárni kívánt lemezek megfelelően vannak-e csatlakoztatva. </br></br>
 >Ha ezt a paramétert a titkosítási beállítások frissítése közben állítja be, az újraindításhoz vezethet a tényleges titkosítás előtt. Ebben az esetben azt a lemezt is el kell távolítania, amelyet nem kíván formázni az fstab fájlból. Hasonlóképpen a titkosítási művelet megkezdése előtt hozzá kell adnia a titkosítani kívánt partíciót az fstab fájlhoz. 

### <a name="encryptformatall-criteria"></a>FormatAll titkosítási feltétel
A paraméter megy, ha az összes partíciót, és titkosítja őket, amíg azok megfelelnek az **összes** alábbi kritériumoknak: 
- Nem gyökér/OS/boot partíció
- Nincs még titkosítva
- Nem BEK-kötet
- Nem RAID-kötet
- Nem LVM-kötet
- Csatlakoztatva van

A RAID vagy LVM kötetet alkotó lemezek titkosítása a RAID vagy LVM kötet helyett.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>A EncryptFormatAll paraméter használata az Azure CLI-vel
Használja az [az vm titkosításengedélyezése](/cli/azure/vm/encryption#az-vm-encryption-enable) parancs titkosítás engedélyezéséhez egy futó virtuális gépen az Azure-ban.

-  **Futó virtuális gép titkosítása az EncryptFormatAll használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>A EncryptFormatAll paraméter használata PowerShell-parancsmaggal
Használja a [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) parancsmagát az EncryptFormatAll paraméterrel. 

**Futó virtuális gép titkosítása az EncryptFormatAll használatával:** Például az alábbi parancsfájl inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension parancsmamot az EncryptFormatAll paraméterrel. Az erőforráscsoport, a virtuális gép és a key vault előfeltételekként jött létre. Cserélje le a MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értékeket.
  
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


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>A EncryptFormatAll paraméter használata logikai kötetkezelővel (LVM) 
LvM-on-crypt beállítást ajánlunk. Az alábbi példákban cserélje le az eszköz elérési útját és csatlakoztatási pontjait a használati esetnek megfelelőre. Ez a beállítás a következőképpen végezhető el:

- Adja hozzá a virtuális gép alkotó adatlemezeket.
- Formázza, csatlakoztassa és adja hozzá ezeket a lemezeket az fstab fájlhoz.

    1. Válasszon egy partíciószabványt, hozzon létre egy partíciót, amely a teljes meghajtóra kiterjed, majd formázza a partíciót. Az Azure által generált symlinks-et itt használjuk. A symlinks használatával elkerülhetők az eszköznevek módosításával kapcsolatos problémák. További információt az [Eszköznevek hibáinak elhárítása](troubleshoot-device-names-problems.md) című cikkben talál.
    
         ```azurepowershell-interactive
         parted /dev/disk/azure/scsi1/lun0 mklabel gpt
         parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
         
         mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
         ```
    
    1. Csatlakoztassa a lemezeket.
         
         `mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint`
    
    1. Add hozzá az fstab-hoz.
         
        `echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab`
    
    1. Futtassa a Set-AzVMDiskEncryptionExtension PowerShell-parancsmagját a -EncryptFormatAll kapcsolóval a lemezek titkosításához.

       ```azurepowershell-interactive
       $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
           
       Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri  -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
       ```

    1. Állítsa be az LVM-et az új lemezek re. Vegye figyelembe, hogy a titkosított meghajtók zárolása fel van oldva, miután a virtuális gép elindult. Így az LVM szerelését is később el kell halasztani.


## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Ügyfél által titkosított virtuális merevlemezből és titkosítási kulcsokból létrehozott új virtuális gépek
Ebben az esetben engedélyezheti a titkosítást PowerShell-parancsmagokkal vagy CLI-parancsokkal. 

Az Azure Disk titkosítási utasítások ugyanazokat a parancsfájlokat az Azure-ban használható előre titkosított lemezképek előkészítéséhez. A rendszerkép létrehozása után a következő szakaszban leírt lépésekkel létrehozhat egy titkosított Azure-beli virtuális gép.

* [Előre titkosított Linux-virtuális merevlemez előkészítése](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Az Azure Disk Encryption engedélyezését megelőzően kötelező pillanatképet készíteni és/vagy biztonsági másolatot készíteni egy felügyelt lemezalapú virtuálisgép-példányról. A felügyelt lemez pillanatképe készíthető a portálról, vagy az [Azure Backup](../../backup/backup-azure-vms-encryption.md) használható. A biztonsági mentések biztosítják, hogy a titkosítás során benem szidható hiba esetén a helyreállítási lehetőség lehetséges legyen. A biztonsági mentés tkövetően a Set-AzVMDiskEncryptionExtension parancsmag a -skipVmBackup paraméter megadásával titkosíthatja a felügyelt lemezeket. A Set-AzVMDiskEncryptionExtension parancs sikertelen lesz a felügyelt lemezalapú virtuális gépeken, amíg nem készült biztonsági másolat, és meg nem adta ezt a paramétert. 
>
> Titkosítás vagy titkosítás letiltása okozhat a virtuális gép újraindítása. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Az Azure PowerShell használata előre titkosított virtuális gépek titkosításához 
A titkosított virtuális merevlemezen a [Set-AzVMOSDisk PowerShell-parancsmag](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples)segítségével engedélyezheti a lemeztitkosítást. Az alábbi példa néhány gyakori paramétert ad. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Titkosítás engedélyezése újonnan hozzáadott adatlemezen

Új adatlemezt adhat hozzá [az vm lemezcsatolással](add-disk.md)vagy [az Azure Portalon keresztül.](attach-disk-portal.md) A titkosítás előtt először csatlakoztatnia kell az újonnan csatlakoztatott adatlemezt. Az adatmeghajtó titkosítását kell kérnie, mivel a meghajtó használhatatlan lesz, amíg a titkosítás folyamatban van. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Titkosítás engedélyezése újonnan hozzáadott lemezen az Azure CLI-vel

 Ha a virtuális gép korábban "All" titkosítással volt titkosítva, akkor a --volume-type paraméternek "All" marad. Minden tartalmazza mind az operációs rendszer és az adatlemezek. Ha a virtuális gép korábban titkosítva volt egy kötet típusú "operációs rendszer", majd a --volume-type paramétert kell módosítani a "Minden", hogy mind az operációs rendszer és az új adatlemez is szerepelni fog. Ha a virtuális gép csak az "Adatok" kötettípussal volt titkosítva, akkor az alább bemutatott "Adatok" maradhat. Új adatlemez hozzáadása és csatolása a virtuális géphez nem elegendő a titkosítás előkészítése. Az újonnan csatlakoztatott lemezt is kell formázni, és megfelelően csatlakoztatva kell lennie a virtuális gépen belül a titkosítás engedélyezése előtt. Linux on a lemezt az /etc/fstab kapcsolóba kell csatlakoztatni [egy állandó blokkeszköz névvel](troubleshoot-device-names-problems.md).  

A Powershell szintaxisával ellentétben a CLI nem követeli meg a felhasználótól, hogy a titkosítás engedélyezésekor egyedi szekvenciaverziót adjon meg. A CLI automatikusan létrehozza és felhasználja a saját egyedi szekvencia verzióértékét.

-  **Futó virtuális gép adatköteteinek titkosítása:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Egy futó virtuális gép adatköteteinek titkosítása KEK használatával:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Titkosítás engedélyezése újonnan hozzáadott lemezen az Azure PowerShell használatával
 Ha a Powershell használatával titkosít egy új lemezt Linuxhoz, új sorozatverziót kell megadni. A szekvencia verziónak egyedinek kell lennie. Az alábbi parancsfájl létrehoz egy GUID-ot a szekvenciaverzióhoz. Készítsen [pillanatképet,](snapshot-copy-managed-disk.md) és/vagy készítsen biztonsági másolatot a virtuális gépről az [Azure Backup](../../backup/backup-azure-vms-encryption.md) szolgáltatással, mielőtt a lemezek titkosítva lesznek. A -skipVmBackup paraméter már meg van adva a PowerShell-parancsfájlokban egy újonnan hozzáadott adatlemez titkosításához.
 

-  **Futó virtuális gép adatköteteinek titkosítása:** Az alábbi parancsfájl inicializálja a változókat, és futtatja a Set-AzVMDiskEncryptionExtension parancsmamot. Az erőforráscsoport, a virtuális gép és a key vault már létre kell hozni az előfeltételek. Cserélje le a MyVirtualMachineResourceGroup, a MySecureVM és a MySecureVault értékeket. A -VolumeType paraméter elfogadható értékei az Összes, az operációs rendszer és az Adatok. Ha a virtuális gép korábban "OS" vagy "All" kötettípussal volt titkosítva, akkor a -VolumeType paramétert "All" -ra kell módosítani, hogy mind az operációs rendszer, mind az új adatlemez szerepeljen.

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
- **Egy futó virtuális gép adatköteteinek titkosítása KEK használatával:** A -VolumeType paraméter elfogadható értékei az Összes, az operációs rendszer és az Adatok. Ha a virtuális gép korábban "OS" vagy "All" kötettípussal volt titkosítva, akkor a -VolumeType paramétert all-ra kell módosítani, hogy mind az operációs rendszer, mind az új adatlemez szerepeljen.

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
    > A lemeztitkosítás-keyvault paraméter értékének szintaxisa a teljes azonosító karakterlánc: /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> A kulcs-titkosítás-kulcs paraméter értékének szintaxisa a KEK teljes URI-ja, mint például: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Linuxos virtuális gépek titkosításának letiltása
Letilthatja a titkosítást az Azure PowerShell, az Azure CLI vagy a Resource Manager sablon használatával. 

>[!IMPORTANT]
>A titkosítás letiltása az Azure Disk Encryption linuxos virtuális gépeken csak az adatkötetek támogatott. Adat- vagy operációsrendszer-kötetek en nem támogatott, ha az operációs rendszer kötete titkosítva van.  

- **Lemeztitkosítás letiltása az Azure PowerShell használatával:** A titkosítás letiltásához használja az [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsmagját. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Tiltsa le a titkosítást az Azure CLI-vel:** A titkosítás letiltásához használja az [az vm titkosítás letiltása](/cli/azure/vm/encryption#az-vm-encryption-disable) parancsot. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Titkosítás letiltása Erőforrás-kezelő sablonnal:** A [titkosítás letiltása egy futó Linux vm sablonon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) a titkosítás letiltása.
     1. Kattintson az **Üzembe helyezés az Azure-ban** lehetőségre.
     2. Válassza ki az előfizetést, az erőforráscsoportot, a helyet, a virtuális gép, a jogi feltételeket és a megállapodást.

## <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

Az Azure Disk Encryption nem működik a következő Linux-forgatókönyvek, -funkciók és -technológiák esetén:

- A klasszikus virtuálisgép-létrehozási módszerrel létrehozott alapvető szintű virtuális gép vagy virtuális gépek titkosítása.
- A titkosítás letiltása egy operációs rendszer meghajtóján vagy egy Linux virtuális gép adatmeghajtóján, ha az operációsrendszer-meghajtó titkosítva van.
- Az operációsrendszer-meghajtó titkosítása Linux-gépméretezési készletekhez.
- Egyéni lemezképek titkosítása Linux virtuális gépeken.
- Integráció a helyszíni kulcskezelő rendszerrel.
- Azure Files (megosztott fájlrendszer).
- hálózati fájlrendszer (NFS).
- Dinamikus kötetek.
- Rövid élettartamú operációs rendszer lemezek.
- Megosztott/elosztott fájlrendszerek titkosítása, például (de nem kizárólagosan): DFS, GFS, DRDB és CephFS.
- Titkosított virtuális gép áthelyezése egy másik előfizetésbe.
- Kernel összeomlási memóriakép (kdump).
- Oracle ACFS (ASM cluster fájlrendszer)
- Gen2 virtuális gépek (lásd: [2. generációs virtuális gépek támogatása az Azure-ban)](generation-2.md#generation-1-vs-generation-2-capabilities)
- Lsv2 sorozatú virtuális gépek (lásd: [Lsv2 sorozat](../lsv2-series.md))

## <a name="next-steps"></a>További lépések

- [Azure lemeztitkosítás – áttekintés](disk-encryption-overview.md)
- [Azure Disk Encryption – mintaszkriptek](disk-encryption-sample-scripts.md)
- [Azure Disk Encryption – hibaelhárítás](disk-encryption-troubleshooting.md)

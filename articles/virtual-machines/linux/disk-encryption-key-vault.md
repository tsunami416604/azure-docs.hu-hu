---
title: Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz
description: Ez a cikk az Azure Disk Encryption szolgáltatással használható kulcstároló létrehozásának és konfigurálásának lépéseit ismerteti.
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: a818d9fe9707d1789fbe8e77489fc380fd2c92dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970628"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz

Az Azure Disk Encryption az Azure Key Vault segítségével szabályozza és kezeli a lemeztitkosítási kulcsokat és titkos kulcsokat.  A kulcstartókról az [Azure Key Vault – és](../../key-vault/key-vault-get-started.md) a Key Vault [biztonságossá tétele](../../key-vault/key-vault-secure-your-key-vault.md)című témakörben talál további információt. 

> [!WARNING]
> - Ha korábban már használta az Azure Lemeztitkosítás t az Azure AD-vel egy virtuális gép titkosításához, továbbra is ezt a lehetőséget kell használnia a virtuális gép titkosításához. A részleteket az Azure Disk Encryption és az [Azure AD (előző kiadás) key vault létrehozása és konfigurálása](disk-encryption-key-vault-aad.md) című témakörben találja.

Az Azure Disk Encryption szolgáltatással való használatra szolgáló kulcstartó létrehozása és konfigurálása három lépésből áll:

1. Erőforráscsoport létrehozása, ha szükséges.
2. Kulcstartó létrehozása. 
3. A key vault speciális hozzáférési szabályzatok beállítása.

Ezeket a lépéseket a következő rövid útmutatók szemléltetik:

- [Linux rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel](disk-encryption-cli-quickstart.md)
- [Linux rendszerű virtuális gép létrehozása és titkosítása az Azure PowerShell-lel](disk-encryption-cli-quickstart.md)

Ha kívánja, kulcstitkosítási kulcsot (KEK) is létrehozhat vagy importálhat.

> [!Note]
> A cikkben ismertetett lépések automatikusan az [Azure Disk Encryption előfeltételei CLI-parancsfájl](https://github.com/ejarvi/ade-cli-getting-started) és az [Azure Disk Encryption előfeltételei PowerShell-parancsfájl.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és csatlakozás az Azure-hoz

A cikkben ismertetett lépések az [Azure CLI,](/cli/azure/)az [Azure PowerShell Az modul](/powershell/azure/overview)vagy az Azure [Portal](https://portal.azure.com)segítségével hajthatók végre. 

Bár a portál elérhető a böngészőn keresztül, az Azure CLI és az Azure PowerShell helyi telepítést igényel; A részleteket [lásd: Azure Disk Encryption for Linux: Install tools.](disk-encryption-linux.md#install-tools-and-connect-to-azure)

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Az Azure CLI vagy az Azure PowerShell használata előtt először csatlakoznia kell az Azure-előfizetéshez. Ehhez jelentkezzen [be az Azure CLI-vel,](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) [jelentkezzen be az Azure Powershellnel,](/powershell/azure/authenticate-azureps?view=azps-2.5.0)vagy adja meg hitelesítő adatait az Azure Portalon, amikor a rendszer kéri.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

*Ha már rendelkezik erőforráscsoporttal, ugorjon [a Key Vault létrehozása](#create-a-key-vault)lehetőségre.*

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

Hozzon létre egy erőforráscsoportot az [az csoport létrehozása](/cli/azure/group?view=azure-cli-latest#az-group-create) Azure CLI parancs, a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell parancs, vagy az [Azure Portalon.](https://portal.azure.com)

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

*Ha már rendelkezik egy key vault, akkor ugorjon [a Key Vault speciális hozzáférési szabályzatok beállítása.](#set-key-vault-advanced-access-policies)*

Hozzon létre egy key vault segítségével az [az keyvault hozzon létre](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) az Azure CLI parancs, a [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure Powershell parancs, az [Azure Portal](https://portal.azure.com), vagy egy Resource [Manager sablon.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)

>[!WARNING]
> Annak érdekében, hogy a titkosítási titkos kulcsok ne lépjék át a regionális határokat, az Azure Disk Encryption megköveteli, hogy a Key Vault és a virtuális gépek ugyanabban a régióban legyenek. Hozzon létre és használjon egy Key Vault, amely ugyanabban a régióban, mint a virtuális gépek titkosítani kell. 

Minden Key Vault kell egy egyedi nevet. Cserélje le <egyedi keyvault-neve> a kulcstartó nevét az alábbi példákban.

### <a name="azure-cli"></a>Azure CLI

Amikor az Azure CLI használatával hoz létre egy kulcstartót, adja hozzá az "-enabled-for-disk-encryption" jelzőt.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Amikor az Azure PowerShell használatával hoz létre egy kulcstartót, adja hozzá a "-EnabledForDiskEncryption" jelzőt.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager-sablon

Az [Erőforrás-kezelő sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)használatával is létrehozhat kulcstartót.

1. Az Azure gyorsindítási sablonján kattintson **a Telepítés az Azure-ba**elemre.
2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, a Kulcstár nevét, az objektumazonosítót, a jogi feltételeket és a megállapodást, majd kattintson a **Vásárlás gombra.** 


##  <a name="set-key-vault-advanced-access-policies"></a>A kulcstartó speciális hozzáférési szabályzatainak beállítása

Az Azure platform hozzáférést igényel a titkosítási kulcsokat vagy titkos kulcsokat a key vaultban, hogy azok elérhetővé a virtuális gép a kulcsok indításához és a kötetek visszafejtéséhez. 

Ha a létrehozás időpontjában nem engedélyezte a kulcstartót a lemeztitkosításhoz, telepítéshez vagy sablontelepítéshez (ahogy azt az előző lépésben is szemléltette), frissítenie kell a speciális hozzáférési szabályzatait.  

### <a name="azure-cli"></a>Azure CLI

Használja [az keyvault frissítés](/cli/azure/keyvault#az-keyvault-update) a kulcstartó lemeztitkosítás engedélyezéséhez. 

 - **A Kulcstároló engedélyezése lemeztitkosításhoz:** A lemezre engedélyezett titkosítás szükséges. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Szükség esetén engedélyezze a Key Vault ot a telepítéshez:** Lehetővé teszi, hogy a Microsoft.Compute erőforrás-szolgáltató titkos kulcsokat olvasson le ebből a kulcstartóból, amikor erre a key vaultra hivatkozik az erőforrás-létrehozássorán, például egy virtuális gép létrehozásakor.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Szükség esetén engedélyezze a Key Vaultot a sablon telepítéséhez:** Engedélyezze az Erőforrás-kezelőnek, hogy titkokat olvasson le a tárolóból.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Használja a Key Vault PowerShell-parancsmag [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) a kulcstartó lemeztitkosítás engedélyezéséhez.

  - **A Kulcstároló engedélyezése lemeztitkosításhoz:** Az Azure Disk titkosításához enabledForDiskEncryption szükséges.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Szükség esetén engedélyezze a Key Vault ot a telepítéshez:** Lehetővé teszi, hogy a Microsoft.Compute erőforrás-szolgáltató titkos kulcsokat olvasson le ebből a kulcstartóból, amikor erre a key vaultra hivatkozik az erőforrás-létrehozássorán, például egy virtuális gép létrehozásakor.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Szükség esetén engedélyezze a Key Vaultot a sablon telepítéséhez:** Lehetővé teszi, hogy az Azure Resource Manager titkokat szerezzen be ebből a kulcstartóból, amikor a kulcstartóra hivatkozik egy sablon központi telepítésében.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure portál

1. Válassza ki a kulcstartót, nyissa meg az **Access Policies**lehetőséget, és **kattintson ide a speciális hozzáférési házirendek megjelenítéséhez.**
2. Jelölje be az **Azure Disk Encryption hozzáférés engedélyezése kötettitkosításhoz**feliratú jelölőnégyzetet.
3. Válassza **a Hozzáférés engedélyezése az Azure virtuális gépek üzembe helyezéshez** és/vagy az Access engedélyezése az Azure Resource Manager a sablon **telepítéséhez**, ha szükséges. 
4. Kattintson a **Mentés** gombra.

    ![Az Azure key vault speciális hozzáférési szabályzatai](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Kulcstitkosítási kulcs (KEK) beállítása

Ha kulcstitkosítási kulcsot (KEK) szeretne használni a titkosítási kulcsok további biztonsági rétegéhez, adjon hozzá egy KEK-et a kulcstartóhoz. Kulcstitkosítási kulcs megadása esetén az Azure Disk Encryption ezt a kulcsot használja a titkosítási titkos kulcsok beburkolásához, mielőtt a Key Vaultba írna.

Új KEK-et hozhat létre az Azure CLI [az keyvault-kulcslétrehozási](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) paranccsal, az Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) parancsmagjával vagy az [Azure Portalon.](https://portal.azure.com/) Létre kell hoznia egy RSA-kulcstípust; Az Azure Disk Encryption még nem támogatja az Elliptikus ív kulcsok használatát.

Ehelyett importálhat egy KEK-et a helyszíni kulcsfelügyeleti HSM-ből. További információt a [Key Vault dokumentációjában](../../key-vault/key-vault-hsm-protected-keys.md)talál. 

A key vault KEK URL-címeket kell verziószámozott. Az Azure kényszeríti ezt a verziószámozási korlátozást. Az érvényes titkos és KEK URL-címeket az alábbi példákban talál:

* Példa érvényes titkos URL-címre:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Példa érvényes KEK URL-re:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Az Azure Disk Encryption nem támogatja a portszámok megadását a key vault titkos kulcsai és a KEK URL-címek részeként. A nem támogatott és támogatott kulcstartó URL-címeiaz alábbi példákban láthatók:

  * Elfogadható kulcstartó URL-címe:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Elfogadhatatlan kulcstartó URL-címe:*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI [az keyvault keyvault-alapú kulcstároló létrehozása](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) paranccsal új KEK-et hozhat létre, és tárolhatja azt a key vaultban.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

. Ehelyett importálhat egy személyes kulcsot az Azure CLI [az keyvault keyvault key vault import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) paranccsal:

Mindkét esetben adja meg a KEK nevét az Azure CLI [az vm titkosítási engedélyezése](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --key-encryption-key paraméter. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Az Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) parancsmag használatával új KEK-et hozhat létre, és tárolhatja azt a key vaultban.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Ehelyett importálhat egy személyes kulcsot az Azure PowerShell [az keyvault keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) paranccsal.

Mindkét esetben megadja a KEK-kulcstároló azonosítóját és a KEK URL-címét az Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId és -KeyEncryptionKeyVaultId paramétereknek. Vegye figyelembe, hogy ez a példa feltételezi, hogy ugyanazt a key vault ot használja mind a lemeztitkosítási kulcshoz, mind a KEK-hez.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
 
## <a name="next-steps"></a>További lépések

- [Az Azure Disk Encryptions CLI-parancsfájl előfeltételei](https://github.com/ejarvi/ade-cli-getting-started)
- [Az Azure disk encryptions powershell-parancsfájl előfeltételei](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Ismerje meg [az Azure disk encryption forgatókönyveit Linuxos virtuális gépeken](disk-encryption-linux.md)
- Az [Azure lemeztitkosítás hibáinak elhárítása](disk-encryption-troubleshooting.md)
- Az [Azure disk encryption mintparancsfájlok olvasása](disk-encryption-sample-scripts.md)

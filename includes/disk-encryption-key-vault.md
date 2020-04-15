---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 6f7f319d2ebb4cd39933addf04f249df02d7819f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314096"
---
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
> A key vault és a virtuális gépek ugyanabban az előfizetésben kell lennie. Annak érdekében is, hogy a titkosítási titkos kulcsok ne lépjék át a regionális határokat, az Azure Disk Encryption megköveteli, hogy a Key Vault és a virtuális gépek ugyanabban a régióban legyenek. Hozzon létre és használjon egy Key Vault, amely ugyanabban az előfizetésben és régióban, mint a virtuális gépek titkosítani kell. 

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

### <a name="azure-portal"></a>Azure Portal

1. Válassza ki a kulcstartót, nyissa meg az **Access Policies**lehetőséget, és **kattintson ide a speciális hozzáférési házirendek megjelenítéséhez.**
2. Jelölje be az **Azure Disk Encryption hozzáférés engedélyezése kötettitkosításhoz**feliratú jelölőnégyzetet.
3. Válassza **a Hozzáférés engedélyezése az Azure virtuális gépek üzembe helyezéshez** és/vagy az Access engedélyezése az Azure Resource Manager a sablon **telepítéséhez**, ha szükséges. 
4. Kattintson a **Save** (Mentés) gombra.

    ![Az Azure key vault speciális hozzáférési szabályzatai](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Kulcstitkosítási kulcs (KEK) beállítása

Ha kulcstitkosítási kulcsot (KEK) szeretne használni a titkosítási kulcsok további biztonsági rétegéhez, adjon hozzá egy KEK-et a kulcstartóhoz. Kulcstitkosítási kulcs megadása esetén az Azure Disk Encryption ezt a kulcsot használja a titkosítási titkos kulcsok beburkolásához, mielőtt a Key Vaultba írna.

Új KEK-et hozhat létre az Azure CLI [az keyvault-kulcslétrehozási](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) paranccsal, az Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) parancsmagjával vagy az [Azure Portalon.](https://portal.azure.com/) Létre kell hoznia egy RSA-kulcstípust; Az Azure Disk Encryption még nem támogatja az Elliptikus ív kulcsok használatát.

Ehelyett importálhat egy KEK-et a helyszíni kulcsfelügyeleti HSM-ből. További információt a [Key Vault dokumentációjában](/azure/key-vault/key-vault-hsm-protected-keys)talál.

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

Ehelyett importálhat egy személyes kulcsot az Azure CLI [az keyvault keyvault key vault import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) paranccsal:

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

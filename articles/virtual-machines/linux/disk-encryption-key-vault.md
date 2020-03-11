---
title: Kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz
description: Ez a cikk a Key Vault létrehozásával és konfigurálásával kapcsolatos lépéseket ismerteti Azure Disk Encryption
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: a818d9fe9707d1789fbe8e77489fc380fd2c92dd
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970628"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz

A Azure Disk Encryption a Azure Key Vault használatával vezérli és kezeli a lemezes titkosítási kulcsokat és titkokat.  További információ a kulcstartókkal kapcsolatban: [Bevezetés a Azure Key Vault használatába](../../key-vault/key-vault-get-started.md) és [a kulcstartó biztonságossá tétele](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Ha korábban már használta Azure Disk Encryption az Azure AD-vel egy virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. További részletekért lásd: [kulcstartó létrehozása és konfigurálása az Azure Disk Encryptionhoz az Azure ad-vel (előző kiadás)](disk-encryption-key-vault-aad.md) .

A Key Vault létrehozásához és konfigurálásához Azure Disk Encryption a következő három lépésből áll:

1. Ha szükséges, hozzon létre egy erőforráscsoportot.
2. Kulcstartó létrehozása. 
3. A Key Vault speciális hozzáférési házirendjeinek beállítása.

Ezeket a lépéseket az alábbi rövid útmutatók szemléltetik:

- [Linux rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel](disk-encryption-cli-quickstart.md)
- [Linux rendszerű virtuális gép létrehozása és titkosítása az Azure PowerShell-lel](disk-encryption-cli-quickstart.md)

Ha szeretné, létrehozhat vagy importálhat egy kulcs-titkosítási kulcsot (KEK) is.

> [!Note]
> A cikkben ismertetett lépések a [Azure Disk Encryption előfeltételként megadott parancssori felület parancsfájljában](https://github.com/ejarvi/ade-cli-getting-started) és [Azure Disk Encryption előfeltételek PowerShell-parancsfájlban](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)találhatók.

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és az Azure-hoz való kapcsolódás

A cikkben ismertetett lépések az [Azure CLI](/cli/azure/)-vel, az Azure PowerShell az [module](/powershell/azure/overview)vagy a [Azure Portal](https://portal.azure.com)használatával végezhetők el. 

Amíg a portál elérhető a böngészőben, az Azure CLI és a Azure PowerShell helyi telepítést igényel; További részletekért lásd [: Azure Disk Encryption for Linux (eszközök telepítése](disk-encryption-linux.md#install-tools-and-connect-to-azure) ).

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Az Azure CLI vagy a Azure PowerShell használata előtt először csatlakoznia kell az Azure-előfizetéséhez. Ehhez [Jelentkezzen be az Azure CLI-vel](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [Jelentkezzen be az Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)-lel, vagy adja meg a hitelesítő adatait a Azure Portalhoz, amikor a rendszer kéri.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

*Ha már rendelkezik erőforráscsoporthoz, ugorjon a [kulcstartó létrehozása](#create-a-key-vault)lehetőségre.*

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

Hozzon létre egy erőforráscsoportot az az [Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) Azure CLI paranccsal, a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell paranccsal vagy a [Azure Portal](https://portal.azure.com).

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

*Ha már rendelkezik kulcstartóval, ugorjon a [Key Vault speciális hozzáférési házirendjeinek beállítása](#set-key-vault-advanced-access-policies)lehetőségre.*

Hozzon létre egy Key vaultot az az kulcstartó [create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) Azure CLI parancs, a [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure PowerShell-parancs, a [Azure Portal](https://portal.azure.com)vagy egy [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)használatával.

>[!WARNING]
> Annak biztosítása érdekében, hogy a titkosítási titkok ne haladják meg a regionális határokat, Azure Disk Encryption megköveteli, hogy a Key Vault és a virtuális gépek ugyanabban a régióban legyenek. Hozzon létre és használjon olyan Key Vault, amely ugyanabban a régióban található, mint a titkosítani kívánt virtuális gépek. 

Minden Key Vault egyedi névvel kell rendelkeznie. A következő példákban cserélje le a < az egyedi-kulcstartó-Name > a Key Vault nevét.

### <a name="azure-cli"></a>Azure CLI

Amikor kulcstartót hoz létre az Azure CLI használatával, adja hozzá a "--enabled-for-Disk-Encryption" jelzőt.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Ha Azure PowerShell használatával hoz létre kulcstartót, adja hozzá a "-EnabledForDiskEncryption" jelzőt.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager-sablon

A Key vaultot a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)használatával is létrehozhatja.

1. Az Azure Gyorsindítás sablonon kattintson az **üzembe helyezés az Azure**-ban lehetőségre.
2. Válassza ki az előfizetést, az erőforráscsoportot, az erőforráscsoport helyét, Key Vault nevét, az objektumazonosító, a jogi feltételek és a szerződés elemet, majd kattintson a **vásárlás**elemre. 


##  <a name="set-key-vault-advanced-access-policies"></a>A Key Vault speciális hozzáférési házirendjeinek beállítása

Az Azure platform a titkosítási kulcsok vagy titkos kódok, hogy elérhetők legyenek a rendszerindítást, és visszafejti a köteteket a virtuális géphez a key vaultban lévő hozzá kell férnie. 

Ha a Key vaultot nem engedélyezte a lemez titkosításához, üzembe helyezéséhez vagy a sablon üzembe helyezéséhez a létrehozáskor (ahogy az az előző lépésben látható), frissítenie kell a speciális hozzáférési szabályzatokat.  

### <a name="azure-cli"></a>Azure CLI

A Key Vault lemezes titkosításának engedélyezéséhez használja az [az kulcstartó frissítést](/cli/azure/keyvault#az-keyvault-update) . 

 - **Key Vault engedélyezése a lemezes titkosításhoz:** Engedélyezve van a-Disk-Encryption szükséges. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Key Vault telepítésének engedélyezése, ha szükséges:** Engedélyezi a Microsoft. számítási erőforrás-szolgáltató számára, hogy a kulcstartóból beolvassa a titkos kulcsokat, amikor ez a kulcstartó az erőforrás-létrehozásban hivatkozik, például virtuális gép létrehozásakor.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Szükség esetén Key Vault engedélyezése a sablonok telepítéséhez:** Annak engedélyezése, hogy a Resource Manager beolvassa a titkos kulcsokat a tárból.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 A Key Vault PowerShell-parancsmagjának [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) használatával engedélyezheti a lemez titkosítását.

  - **Key Vault engedélyezése a lemezes titkosításhoz:** Az Azure Disk Encryption EnabledForDiskEncryption szükséges.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Key Vault telepítésének engedélyezése, ha szükséges:** Engedélyezi a Microsoft. számítási erőforrás-szolgáltató számára, hogy a kulcstartóból beolvassa a titkos kulcsokat, amikor ez a kulcstartó az erőforrás-létrehozásban hivatkozik, például virtuális gép létrehozásakor.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Szükség esetén Key Vault engedélyezése a sablonok telepítéséhez:** Lehetővé teszi a Azure Resource Manager számára, hogy a kulcstartóból beolvassa a titkos kulcsokat, ha a kulcstároló a sablon központi telepítésében van hivatkozva.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure Portal

1. Válassza ki a kulcstartót, lépjen a **hozzáférési házirendek**elemre, és **kattintson ide a speciális hozzáférési szabályzatok megjelenítéséhez**.
2. Jelölje be a **Azure Disk Encryptionhoz való hozzáférés engedélyezése a kötetek titkosításához**jelölőnégyzetet.
3. Ha szükséges, jelölje be az **Azure Virtual Machines való hozzáférés engedélyezése az üzembe helyezéshez** és/vagy az **Azure Resource Manager hozzáférésének engedélyezése a sablonok telepítéséhez**lehetőséget. 
4. Kattintson a **Save** (Mentés) gombra.

    ![Az Azure key vault speciális hozzáférési szabályzatok](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Kulcs titkosítási kulcs (KEK) beállítása

Egy további titkosítási kulcsok biztonsági szintet szeretne kulcstitkosítási kulcs-(KEK) használatára, ha egy KEK hozzáadása a key vaultban. Amikor egy kulcsalapú titkosítás kulcsa van megadva, az Azure Disk Encryption a kulcs segítségével burkolhatja a titkosítási titkos kulcsait a Key Vault írása előtt.

Létrehozhat egy új KEK-t az Azure CLI az [kulcstartó kulcs létrehozása](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) parancs, a Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) parancsmag vagy a [Azure Portal](https://portal.azure.com/)használatával. RSA-kulcs típusát kell előállítania; Azure Disk Encryption még nem támogatja az elliptikus görbe kulcsait.

Ehelyett egy KEK-t is importálhat a helyszíni kulcskezelő HSM-ből. További információ: [Key Vault dokumentáció](../../key-vault/key-vault-hsm-protected-keys.md). 

A Key Vault KEK URL-címeinek verziószámozással kell rendelkezniük. Az Azure ezt a korlátozást, versioning, érvénybe lépteti. Érvényes titkos kulcsot, és KEK URL-címeket tekintse meg az alábbi példák:

* Érvényes titkos URL-cím – példa: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Érvényes KEK URL-cím – példa: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Az Azure Disk Encryption részeként a key vault titkos kódok és KEK URL-címek megadásához használt portszámok nem támogatja. Nem támogatott, és támogatott a key vault URL-címek példákért lásd az alábbi példákat:

  * Elfogadható Key Vault URL-cím: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Nem elfogadható Key Vault URL-cím: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI az kulcstartó [kulcs létrehozása](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) paranccsal létrehozhat egy új KEK-t, és tárolhatja azt a kulcstartóban.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA-HSM
```

. Ehelyett egy titkos kulcsot is importálhat az Azure CLI az [kulcstartó kulcs importálása](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) paranccsal:

Mindkét esetben meg kell adnia a KEK nevét az Azure CLI-hez az [VM encryption Enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) --Key-encryption-Key paraméterrel. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Egy új KEK létrehozásához használja a Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) parancsmagot, és tárolja azt a Key vaultban.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Ehelyett egy titkos kulcsot is importálhat az Azure PowerShell az [kulcstartó kulcs importálása](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) paranccsal.

Mindkét esetben meg kell adnia a KEK Key Vault AZONOSÍTÓját és a KEK URL-címét a Azure PowerShell [set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) -KeyEncryptionKeyVaultId és-KeyEncryptionKeyUrl paraméterekben. Vegye figyelembe, hogy ez a példa feltételezi, hogy ugyanazt a kulcstartót használja, mint a lemez-titkosítási kulcs és a KEK.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```
 
## <a name="next-steps"></a>Következő lépések

- [Előfeltételként Azure Disk Encryption parancssori felület parancsfájlja](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption előfeltételek PowerShell-parancsfájl](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [A Linux rendszerű virtuális gépeken Azure Disk Encryption forgatókönyvek](disk-encryption-linux.md) ismertetése
- Tudnivalók a [Azure Disk Encryption hibakereséséről](disk-encryption-troubleshooting.md)
- A [Azure Disk Encryption minta parancsfájljainak](disk-encryption-sample-scripts.md) beolvasása

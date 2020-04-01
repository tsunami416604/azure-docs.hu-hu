---
title: Ügyfél által kezelt kulcsok konfigurálása a PowerShell használatával
titleSuffix: Azure Storage
description: Megtudhatja, hogyan konfigurálhatja az ügyfelek által felügyelt kulcsokat az Azure Storage-titkosításhoz a PowerShell használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 590f129d0ce41c3a8afc80340f26bc31c2fc789a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478184"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Ügyfél által felügyelt kulcsok konfigurálása az Azure Key Vault használatával a PowerShell használatával

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Key Vault ügyfél által felügyelt kulcsok at PowerShell használatával. Ha meg szeretné tudni, hogyan hozhat létre egy kulcstartót az Azure CLI használatával, olvassa el [a rövid útmutató: Titkos kulcs beállítása és beolvasása az Azure Key Vaultból a PowerShell használatával című témakört.](../../key-vault/quick-create-powershell.md)

## <a name="assign-an-identity-to-the-storage-account"></a>Identitás hozzárendelése a tárfiókhoz

Az ügyfél által felügyelt kulcsok engedélyezéséhez a tárfiókhoz először rendeljen hozzá egy rendszeráltal hozzárendelt felügyelt identitást a tárfiókhoz. Ezt a felügyelt identitást fogja használni a tárfiók engedélyek megadásához a key vault eléréséhez.

Felügyelt identitás hozzárendeléséhez a PowerShell használatával hívja meg [a Set-AzStorageAccount .to](/powershell/module/az.storage/set-azstorageaccount)assign a managed identity using PowerShell, call Set-AzStorageAccount . Ne felejtse el a zárójelben lévő helyőrző értékeket a saját értékeire cserélni.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

A rendszeráltal hozzárendelt felügyelt identitások PowerShell-lel történő konfigurálásáról további információt az [Azure-erőforrások felügyelt identitásának konfigurálása Azure-erőforrásokhoz a PowerShell használatával című témakörben talál.](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)

## <a name="create-a-new-key-vault"></a>Új kulcstartó létrehozása

Ha új kulcstartót szeretne létrehozni a PowerShell használatával, hívja meg a [New-AzKeyVault .To](/powershell/module/az.keyvault/new-azkeyvault)create a new key vault. Az Azure Storage-titkosítás ügyfél által felügyelt kulcsainak tárolására használt kulcstartónak két kulcsvédelmi beállítással kell rendelkeznie: **a Helyreállítható törlés** és **a Ne ürítés.**

Ne felejtse el a zárójelben lévő helyőrző értékeket a saját értékeire cserélni.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

A PowerShell rel rendelkező meglévő kulcstartók **on-re** történő ideiglenes törlés és **a nem kiürítés** engedélyezéséről a [PowerShell](../../key-vault/key-vault-soft-delete-powershell.md)használatával a **Helyreállítható törlés** engedélyezése és a **Kiürítési védelem engedélyezése** című szakaszokban olvashat.

## <a name="configure-the-key-vault-access-policy"></a>A key vault-hozzáférési házirend konfigurálása

Ezután konfigurálja a hozzáférési szabályzatot a key vault, hogy a tárfiók rendelkezik hozzáféréssel. Ebben a lépésben a tárfiókhoz korábban hozzárendelt felügyelt identitást fogja használni.

A kulcstartó hozzáférési házirendjének beállításához hívja meg a [Set-AzKeyVaultAccessPolicy programot.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és használja az előző példákban meghatározott változókat.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Új kulcs létrehozása

Ezután hozzon létre egy új kulcsot a key vaultban. Új kulcs létrehozásához hívja meg [az Add-AzKeyVaultKey billentyűt.](/powershell/module/az.keyvault/add-azkeyvaultkey) Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és használja az előző példákban meghatározott változókat.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

Csak 2048 bites RSA és RSA-HSM kulcsok at az Azure Storage titkosítása támogatja. A kulcsokról további információt az [Azure Key Vault-kulcsok, titkos kulcsok és tanúsítványok –](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys) **Kulcstároló-kulcsok** című témakörben talál.

## <a name="configure-encryption-with-customer-managed-keys"></a>Titkosítás konfigurálása ügyfél által kezelt kulcsokkal

Alapértelmezés szerint az Azure Storage titkosítása Microsoft által felügyelt kulcsokat használ. Ebben a lépésben konfigurálja az Azure Storage-fiókot az ügyfél által felügyelt kulcsok használatára, és adja meg a tárfiókhoz társítani kívánt kulcsot.

Hívja meg [a Set-AzStorageAccount-ot](/powershell/module/az.storage/set-azstorageaccount) a tárfiók titkosítási beállításainak frissítéséhez, ahogy az a következő példában látható. A **-KeyvaultEncryption** kapcsolóval engedélyezheti az ügyfél által felügyelt kulcsokat a tárfiókhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és használja az előző példákban meghatározott változókat.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>A kulcsverzió frissítése

Amikor egy kulcs új verzióját hozza létre, frissítenie kell a tárfiókot az új verzió használatához. Először hívja a [Get-AzKeyVaultKey-t](/powershell/module/az.keyvault/get-azkeyvaultkey) a kulcs legújabb verziójának bekéséhez. Ezután hívja meg [a Set-AzStorageAccount-ot](/powershell/module/az.storage/set-azstorageaccount) a tárfiók titkosítási beállításainak frissítéséhez a kulcs új verziójának használatához, ahogy az az előző szakaszban látható.

## <a name="use-a-different-key"></a>Másik kulcs használata

Az Azure Storage titkosításához használt kulcs módosításához hívja meg a [Set-AzStorageAccount-ot](/powershell/module/az.storage/set-azstorageaccount) a [Titkosítás konfigurálása ügyfél által kezelt kulcsokkal](#configure-encryption-with-customer-managed-keys) című részben látható módon, és adja meg az új kulcsnevet és -verziót. Ha az új kulcs egy másik key vaultban található, frissítse a key vault URI-ját is.

## <a name="revoke-customer-managed-keys"></a>Ügyfél által kezelt kulcsok visszavonása

Ha úgy véli, hogy egy kulcs biztonsága sérülhet, visszavonhatja az ügyfél által felügyelt kulcsok eltávolításával a key vault hozzáférési szabályzat. Az ügyfél által felügyelt kulcs visszavonásához hívja meg az [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) parancsot, ahogy az a következő példában látható. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és használja az előző példákban meghatározott változókat.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Ügyfél által kezelt kulcsok letiltása

Ha letiltja az ügyfél által felügyelt kulcsokat, a tárfiók ismét titkosítva lesz a Microsoft által felügyelt kulcsokkal. Az ügyfél által kezelt kulcsok letiltásához hívja `-StorageEncryption` meg a [Set-AzStorageAccount-ot](/powershell/module/az.storage/set-azstorageaccount) a beállítással, ahogy az a következő példában látható. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és használja az előző példákban meghatározott változókat.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>További lépések

- [Az Azure Storage titkosítása az inaktív adatokhoz](storage-service-encryption.md)
- [Mi az Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

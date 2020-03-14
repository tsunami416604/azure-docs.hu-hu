---
title: Az ügyfél által felügyelt kulcsok konfigurálása a PowerShell-lel
titleSuffix: Azure Storage
description: Ismerje meg, hogyan konfigurálhatja az Azure Storage-titkosításhoz az ügyfél által felügyelt kulcsokat a PowerShell használatával. Az ügyfél által felügyelt kulcsok lehetővé teszik a hozzáférés-vezérlések létrehozását, elforgatását, letiltását és visszavonását.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d7e4843bfbd622ad99cad4d9048e91a0cb49b1c1
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136244"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Ügyfél által felügyelt kulcsok konfigurálása Azure Key Vault a PowerShell használatával

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Ez a cikk bemutatja, hogyan konfigurálhat egy Azure Key Vaultt az ügyfél által felügyelt kulcsokkal a PowerShell használatával. Ha meg szeretné tudni, hogyan hozhat létre kulcstartót az Azure CLI használatával, tekintse meg a következőt [: gyors üzembe helyezés és a titkos kód beolvasása Azure Key Vault a PowerShell használatával](../../key-vault/quick-create-powershell.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Identitás kiosztása a Storage-fiókhoz

Ha engedélyezni szeretné az ügyfél által felügyelt kulcsokat a Storage-fiókhoz, először rendeljen hozzá egy rendszerhez rendelt felügyelt identitást a Storage-fiókhoz. Ezt a felügyelt identitást fogja használni a Storage-fiók engedélyeinek megadásához a kulcstartó eléréséhez.

Felügyelt identitás a PowerShell használatával történő hozzárendeléséhez hívja a [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

A rendszerhez rendelt felügyelt identitások PowerShell-lel való konfigurálásával kapcsolatos további információkért lásd: [felügyelt identitások konfigurálása](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)Azure-beli virtuális gépeken a PowerShell használatával.

## <a name="create-a-new-key-vault"></a>Új kulcstartó létrehozása

Ha új kulcstartót szeretne létrehozni a PowerShell használatával, hívja a [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Az Azure Storage-titkosításhoz az ügyfél által felügyelt kulcsok tárolásához használt kulcstartónak engedélyezve kell lennie két kulcsfontosságú védelmi beállítás, a helyreállítható törlés és a **nem végleges** **Törlés** .

Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

Ha szeretné megtudni, hogyan engedélyezheti a helyreállítható **törlést** **, és ne** töröljön egy meglévő kulcstartót a PowerShell-lel, tekintse meg a következő szakaszt: a **Soft-delete engedélyezése** és a **végleges Törlés engedélyezése** a [PowerShell használatával](../../key-vault/key-vault-soft-delete-powershell.md).

## <a name="configure-the-key-vault-access-policy"></a>A Key Vault hozzáférési szabályzatának konfigurálása

Ezután konfigurálja a Key Vault hozzáférési házirendjét, hogy a Storage-fióknak hozzáférési jogosultsága legyen az eléréséhez. Ebben a lépésben a korábban a Storage-fiókhoz rendelt felügyelt identitást fogja használni.

A Key Vault hozzáférési házirendjének beállításához hívja a [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Új kulcs létrehozása

Ezután hozzon létre egy új kulcsot a Key vaultban. Új kulcs létrehozásához hívja a [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Titkosítás konfigurálása az ügyfél által felügyelt kulcsokkal

Alapértelmezés szerint az Azure Storage-titkosítás a Microsoft által felügyelt kulcsokat használja. Ebben a lépésben konfigurálja az Azure Storage-fiókját az ügyfél által felügyelt kulcsok használatára, és adja meg a Storage-fiókhoz társítandó kulcsot.

Hívja a [set-AzStorageAccount-](/powershell/module/az.storage/set-azstorageaccount) t a Storage-fiók titkosítási beállításainak frissítéséhez, ahogy az az alábbi példában is látható. A **-KeyvaultEncryption** beállítással engedélyezheti az ügyfél által felügyelt kulcsokat a Storage-fiókhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>A kulcs verziójának frissítése

A kulcsok új verziójának létrehozásakor frissítenie kell a Storage-fiókot az új verzió használatára. Először hívja meg a [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) a kulcs legújabb verziójának beszerzéséhez. Ezután hívja meg a [set-AzStorageAccount-](/powershell/module/az.storage/set-azstorageaccount) t, hogy frissítse a Storage-fiók titkosítási beállításait a kulcs új verziójának használatára, ahogy az az előző szakaszban is látható.

## <a name="use-a-different-key"></a>Másik kulcs használata

Ha módosítani szeretné az Azure Storage-titkosításhoz használt kulcsot, hívja a [set-AzStorageAccount-](/powershell/module/az.storage/set-azstorageaccount) t a [titkosítás konfigurálása az ügyfél által felügyelt kulcsokkal beállításnál](#configure-encryption-with-customer-managed-keys) látható módon, majd adja meg az új kulcs nevét és verzióját. Ha az új kulcs egy másik kulcstartóban található, frissítse a Key Vault URI-JÁT is.

## <a name="revoke-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok visszavonása

Ha úgy véli, hogy egy kulcs biztonsága sérült, visszavonhatja az ügyfél által felügyelt kulcsokat a Key Vault hozzáférési házirendjének eltávolításával. Az ügyfél által felügyelt kulcs visszavonásához hívja meg a [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) parancsot az alábbi példában látható módon. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok letiltása

Ha letiltja az ügyfél által felügyelt kulcsokat, a Storage-fiók újból titkosítva lesz a Microsoft által felügyelt kulcsokkal. Az ügyfél által felügyelt kulcsok letiltásához hívja a [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) parancsot a `-StorageEncryption` kapcsolóval, az alábbi példában látható módon. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Következő lépések

- [Azure Storage-titkosítás a REST-adatokhoz](storage-service-encryption.md)
- [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?

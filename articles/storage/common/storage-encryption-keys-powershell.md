---
title: Ügyfél által felügyelt kulcsok konfigurálása az Azure Storage-titkosításhoz a PowerShellből
description: Ismerje meg, hogyan konfigurálhatja az Azure Storage-titkosításhoz az ügyfél által felügyelt kulcsokat a PowerShell használatával. Az ügyfél által felügyelt kulcsok lehetővé teszik a hozzáférés-vezérlések létrehozását, elforgatását, letiltását és visszavonását.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 1b94f1686c2893c1c69bc846d3a0d29084975b7a
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002176"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>Ügyfél által felügyelt kulcsok konfigurálása az Azure Storage-titkosításhoz a PowerShellből

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Ez a cikk bemutatja, hogyan konfigurálhat egy Key vaultot az ügyfél által felügyelt kulcsokkal a PowerShell használatával.

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok Azure Storage-titkosítással való használata megköveteli, hogy a Key Vault két szükséges tulajdonsággal rendelkezzen, a helyreállítható **törléssel** és a **kiürítéssel**. Ezek a tulajdonságok alapértelmezés szerint engedélyezve vannak, amikor új kulcstartót hoz létre a Azure Portal. Ha azonban egy meglévő kulcstartón kell engedélyeznie ezeket a tulajdonságokat, akkor a PowerShellt vagy az Azure CLI-t kell használnia.
> Csak az RSA-kulcsok és a 2048-es kulcs mérete támogatott.

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

Hívja a [set-AzStorageAccount-](/powershell/module/az.storage/set-azstorageaccount) t a Storage-fiók titkosítási beállításainak frissítéséhez. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

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

## <a name="next-steps"></a>További lépések

- [Azure Storage-titkosítás a REST-adatokhoz](storage-service-encryption.md) 
- [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?

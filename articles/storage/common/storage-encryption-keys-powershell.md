---
title: Az ügyfél által felügyelt kulcsok konfigurálása a PowerShell-lel
titleSuffix: Azure Storage
description: Ismerje meg, hogyan konfigurálhatja az Azure Storage-titkosításhoz az ügyfél által felügyelt kulcsokat a PowerShell használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/24/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 1c928056ec0e7b101d991c8d8c8db3bd659251ba
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799128"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-powershell"></a>Ügyfél által felügyelt kulcsok konfigurálása Azure Key Vault a PowerShell használatával

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Ez a cikk bemutatja, hogyan konfigurálhat egy Azure Key Vaultt az ügyfél által felügyelt kulcsokkal a PowerShell használatával. Ha meg szeretné tudni, hogyan hozhat létre kulcstartót az Azure CLI használatával, tekintse meg a következőt [: gyors üzembe helyezés és a titkos kód beolvasása Azure Key Vault a PowerShell használatával](../../key-vault/secrets/quick-create-powershell.md).

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

Új kulcstartó PowerShell használatával történő létrehozásához telepítse az az [.](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) kulcstartó PowerShell-modul 2.0.0 vagy újabb verzióját. Ezután hívja a [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) új kulcstartó létrehozásához.

Az Azure Storage-titkosításhoz az ügyfél által felügyelt kulcsok tárolásához használt kulcstartónak engedélyezve kell lennie két kulcsfontosságú védelmi beállítás, a helyreállítható törlés és a **nem végleges** **Törlés** . Az az. kulcstartó modul 2.0.0 és újabb verziójában a Soft delete alapértelmezés szerint engedélyezve van, amikor új kulcstárolót hoz létre.

Az alábbi példa egy új kulcstartót hoz létre, amely a **Soft delete** és a **not unpurge** Properties engedélyezve van. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

Ha szeretné megtudni, hogyan engedélyezheti a helyreállítható **törlést** **, és ne** töröljön egy meglévő kulcstartót a PowerShell-lel, tekintse meg a következő szakaszt: a **Soft-delete engedélyezése** és a **végleges Törlés engedélyezése** a [PowerShell használatával](../../key-vault/general/soft-delete-powershell.md).

## <a name="configure-the-key-vault-access-policy"></a>A Key Vault hozzáférési szabályzatának konfigurálása

Ezután konfigurálja a Key Vault hozzáférési házirendjét, hogy a Storage-fióknak hozzáférési jogosultsága legyen az eléréséhez. Ebben a lépésben a korábban a Storage-fiókhoz rendelt felügyelt identitást fogja használni.

A Key Vault hozzáférési házirendjének beállításához hívja a [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

## <a name="create-a-new-key"></a>Új kulcs létrehozása

Ezután hozzon létre egy új kulcsot a Key vaultban. Új kulcs létrehozásához hívja a [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

Az Azure Storage encryption a 2048, 3072 és 4096 méretű RSA-és RSA-HSM-kulcsokat támogatja. A kulcsokkal kapcsolatos további információkért tekintse meg a kulcsok [, titkos kódok és tanúsítványok](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys) **Key Vault kulcsait** Azure Key Vault ismertető témakört.

## <a name="configure-encryption-with-customer-managed-keys"></a>Titkosítás konfigurálása az ügyfél által felügyelt kulcsokkal

Alapértelmezés szerint az Azure Storage-titkosítás a Microsoft által felügyelt kulcsokat használja. Ebben a lépésben az Azure Storage-fiókot úgy konfigurálja, hogy az ügyfél által felügyelt kulcsokat Azure Key Vault használatával használja, majd adja meg a Storage-fiókhoz társítandó kulcsot.

Ha ügyfél által felügyelt kulcsokkal konfigurálja a titkosítást, beállíthatja, hogy a rendszer automatikusan frissítse a titkosításhoz használt kulcsot, amikor a kulcs verziója megváltozik a társított kulcstartóban. Másik lehetőségként explicit módon megadhatja a titkosításhoz használni kívánt verziót, amíg a kulcs verzióját manuálisan nem frissíti.

> [!NOTE]
> A kulcs elforgatásához hozza létre a kulcs új verzióját Azure Key Vault. Az Azure Storage nem kezeli a kulcs elforgatását Azure Key Vaultban, ezért manuálisan kell elforgatnia a kulcsot, vagy létre kell hoznia egy függvényt, amellyel elforgathatja azt ütemterv szerint.

### <a name="configure-encryption-to-automatically-update-the-key-version"></a>A titkosítási konfiguráció konfigurálása a kulcs verziójának automatikus frissítéséhez

Ha a titkosítást az ügyfél által felügyelt kulcsokkal szeretné konfigurálni a kulcs verziójának automatikus frissítéséhez, telepítse az az [. Storage](https://www.powershellgallery.com/packages/Az.Storage) modult, a Version 2.0.0 vagy az újabb verziót.

Az ügyfél által felügyelt kulcs verziószámának automatikus frissítéséhez hagyja ki a kulcs verzióját, ha a Storage-fiókhoz az ügyfél által felügyelt kulcsokkal konfigurálja a titkosítást. A [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) a Storage-fiók titkosítási beállításainak frissítésére szolgál, ahogy az az alábbi példában is látható, és a **-KeyvaultEncryption** beállítással engedélyezheti az ügyfél által felügyelt kulcsokat a Storage-fiókhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>Titkosítási konfiguráció konfigurálása a kulcsfontosságú verziók manuális frissítéséhez

Ha explicit módon meg szeretné adni a titkosításhoz használni kívánt verziót, adja meg a kulcs verziószámát, ha a titkosítást az ügyfél által felügyelt kulcsokkal konfigurálja a Storage-fiókhoz. A [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) a Storage-fiók titkosítási beállításainak frissítésére szolgál, ahogy az az alábbi példában is látható, és a **-KeyvaultEncryption** beállítással engedélyezheti az ügyfél által felügyelt kulcsokat a Storage-fiókhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

Amikor manuálisan frissíti a kulcs verzióját, frissítenie kell a Storage-fiók titkosítási beállításait az új verzió használatára. Először hívja meg a [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) a kulcs legújabb verziójának beszerzéséhez. Ezután hívja meg a [set-AzStorageAccount-](/powershell/module/az.storage/set-azstorageaccount) t, hogy frissítse a Storage-fiók titkosítási beállításait a kulcs új verziójának használatára az előző példában látható módon.

## <a name="use-a-different-key"></a>Másik kulcs használata

Ha módosítani szeretné az Azure Storage-titkosításhoz használt kulcsot, hívja a [set-AzStorageAccount-](/powershell/module/az.storage/set-azstorageaccount) t a [titkosítás konfigurálása az ügyfél által felügyelt kulcsokkal beállításnál](#configure-encryption-with-customer-managed-keys) látható módon, majd adja meg az új kulcs nevét és verzióját. Ha az új kulcs egy másik kulcstartóban található, frissítse a Key Vault URI-JÁT is.

## <a name="revoke-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok visszavonása

Az ügyfél által felügyelt kulcsok visszavonásához távolítsa el a Key Vault hozzáférési házirendjét. Az ügyfél által felügyelt kulcs visszavonásához hívja meg a [Remove-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) parancsot az alábbi példában látható módon. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

## <a name="disable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok letiltása

Ha letiltja az ügyfél által felügyelt kulcsokat, a Storage-fiók újból titkosítva lesz a Microsoft által felügyelt kulcsokkal. Az ügyfél által felügyelt kulcsok letiltásához hívja [meg a set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) `-StorageEncryption` kapcsolót az alábbi példában látható módon. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

## <a name="next-steps"></a>Következő lépések

- [Inaktív adatok Azure Storage-titkosítása](storage-service-encryption.md)
- [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?

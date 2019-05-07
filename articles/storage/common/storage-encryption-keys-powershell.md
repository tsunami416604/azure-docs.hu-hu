---
title: Ügyfél által felügyelt kulcsok az Azure Storage-titkosítás a PowerShell konfigurálása
description: Megtudhatja, hogyan ügyfél által felügyelt kulcsok az Azure Storage-titkosítás konfigurálása a PowerShell használatával. Ügyfél által felügyelt kulcsokat hozhat létre, elforgatása, tiltsa le, és visszavonhatja a hozzáférés-vezérlés lehetővé teszik.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0eeae1451e77d9000c87b1aff7ad73323e74f7ee
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154125"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>Ügyfél által felügyelt kulcsok az Azure Storage-titkosítás a PowerShell konfigurálása

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja a key vault PowerShell-lel, ügyfél által felügyelt kulcsokkal.

## <a name="assign-an-identity-to-the-storage-account"></a>Identitás hozzárendelése a storage-fiók

Ahhoz, hogy az ügyfél által felügyelt kulcsokat a tárfiók, először hozzárendel egy rendszer által hozzárendelt felügyelt identitás a storage-fiókba. A tárolási fiók szükséges engedélyek biztosítása a kulcstartó elérését a felügyelt identitást fogja használni.

Rendelje hozzá egy felügyelt identitás PowerShell-lel, hívja meg [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Ne felejtse el a zárójeleket helyőrzőértékeket cserélje le a saját értékeire.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Felügyelt identitások alapértelmezett konfigurálása a PowerShell-lel kapcsolatos további információkért lásd: [konfigurálása felügyelt identitások az Azure-erőforrások PowerShell-lel, egy Azure virtuális Gépen található](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Új kulcstartó létrehozása

Hozzon létre egy új kulcstartót, PowerShell-lel, hívja meg [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). A key vaultban, ügyfél által felügyelt kulcsok tárolására, az Azure Storage-titkosítás kell rendelkeznie a két kulcs védelme beállítás engedélyezve van, amellyel **a helyreállítható Törlés** és **tegye végleges törlés**. 

Ne felejtse el a zárójeleket helyőrzőértékeket cserélje le a saját értékeire. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>A kulcstartó-hozzáférési házirend konfigurálása

Ezután konfigurálja a kulcstartó hozzáférési házirendje, úgy, hogy a tárfiók engedélyeket az eléréséhez. Ebben a lépésben a felügyelt identitás, amelyet korábban a tárfiókhoz rendelt fogja használni.

A kulcstartó hozzáférési házirend beállítása, hívja [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Ne feledje, hogy zárójelben helyőrzőértékeket cserélje le a saját értékeire és az előző lépéseknél definiált változókat használja.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Új kulcs létrehozása

Ezután hozzon létre egy új kulcsot a kulcstartóban. Hozzon létre egy új kulcsot, hívja meg [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Ne feledje, hogy zárójelben helyőrzőértékeket cserélje le a saját értékeire és az előző lépéseknél definiált változókat használja.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Ügyfél által felügyelt kulcsokkal titkosítás konfigurálása

Alapértelmezés szerint az Azure Storage-titkosítás használja a Microsoft által felügyelt kulcsokkal. Ebben a lépésben konfigurálja az Azure Storage-fiók felhasználó által kezelt kulcsok használata, és adja meg a kulcsot a storage-fiókhoz társít.

Hívás [Set-AzStorageAccount](/powershell/module/az.keyvault/set-azstorageaccount) a tárfiók titkosítási beállításainak frissítése. Ne feledje, hogy zárójelben helyőrzőértékeket cserélje le a saját értékeire és az előző lépéseknél definiált változókat használja.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Frissítés a kulcs verziója

Amikor létrehoz egy kulcs új verziója, szüksége frissíteni a tárfiókot, hogy az új verziót használja. Először hívja [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) a kulcs legújabb verziójának beszerzéséhez. Ezután hívja meg [Set-AzStorageAccount](/powershell/module/az.keyvault/set-azstorageaccount) frissíteni a tárfiók titkosítási beállítások használatához a kulcs új verziója, az előző szakaszban látható módon.

## <a name="next-steps"></a>További lépések

- [Inaktív adatok az Azure Storage-titkosítás](storage-service-encryption.md) 
- [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?

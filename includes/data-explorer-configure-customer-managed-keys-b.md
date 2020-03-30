---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 03/25/2020
ms.author: orspodek
ms.openlocfilehash: 081ba777f6ab19be774f127383e359ff761e7f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297876"
---
## <a name="create-a-new-key-vault"></a>Új kulcstartó létrehozása

Ha új kulcstartót szeretne létrehozni a PowerShell használatával, hívja meg a [New-AzKeyVault .To](/powershell/module/az.keyvault/new-azkeyvault)create a new key vault. Az Azure Data Explorer titkosításához az ügyfelek által felügyelt kulcsok tárolására használt kulcstartónak két kulcsvédelmi beállítással kell rendelkeznie: **A kezelés nélküli törlés** és a Ne **ürítés.** Cserélje le a zárójelben lévő helyőrző értékeket a saját értékeire az alábbi példában.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>A key vault-hozzáférési házirend konfigurálása

Ezután konfigurálja a kulcstartó hozzáférési szabályzatát úgy, hogy a fürt nek engedélye legyen a eléréséhez. Ebben a lépésben a rendszer által hozzárendelt felügyelt identitást fogja használni, amelyet korábban a fürthöz rendelt. A kulcstartó hozzáférési házirendjének beállításához hívja meg a [Set-AzKeyVaultAccessPolicy programot.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) Cserélje le a szögletes zárójelben lévő helyőrző értékeket a saját értékeire, és használja az előző példákban meghatározott változókat.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Új kulcs létrehozása

Ezután hozzon létre egy új kulcsot a key vaultban. Új kulcs létrehozásához hívja meg [az Add-AzKeyVaultKey billentyűt.](/powershell/module/az.keyvault/add-azkeyvaultkey) Cserélje le a szögletes zárójelben lévő helyőrző értékeket a saját értékeire, és használja az előző példákban meghatározott változókat.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

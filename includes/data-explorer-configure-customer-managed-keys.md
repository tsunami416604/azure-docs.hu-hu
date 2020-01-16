---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 5443ee6912c30b89cee6fdb43f84f3bc1fbcfe68
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021035"
---
Az Azure Adatkezelő a Storage-fiókban tárolt összes adathalmazt titkosítja. Alapértelmezés szerint az adattitkosítás a Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további szabályozásához megadhatja az ügyfél által felügyelt kulcsokat, amelyeket az adattitkosításhoz használhat. Az ügyfél által felügyelt kulcsokat egy [Azure Key Vault](/azure/key-vault/key-vault-overview)kell tárolni. Létrehozhat saját kulcsokat, és tárolhatja őket egy kulcstartóban, vagy használhat egy Azure Key Vault API-t kulcsok létrehozásához. Az Azure Adatkezelő-fürtnek és a Key vaultnak ugyanabban a régióban kell lennie, de különböző előfizetésekben is lehetnek. Az ügyfél által felügyelt kulcsok részletes ismertetését lásd: [ügyfél által felügyelt kulcsok Azure Key Vault](/azure/storage/common/storage-service-encryption). Ez a cikk bemutatja, hogyan konfigurálhatja az ügyfél által felügyelt kulcsokat.

> [!Note]
> Az ügyfél által felügyelt kulcsok Azure Adatkezelő-vel való konfigurálásához [két tulajdonságot kell megadnia a kulcstartóban](/azure/key-vault/key-vault-ovw-soft-delete): **Soft delete** és **not Purge**. Ezek a tulajdonságok alapértelmezés szerint nem engedélyezettek. A tulajdonságok engedélyezéséhez használja a [PowerShellt](/azure/key-vault/key-vault-soft-delete-powershell) vagy az [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli)-t. Csak az RSA-kulcsok és a 2048-es kulcs mérete támogatott.

## <a name="assign-an-identity-to-the-cluster"></a>Identitás társítása a fürthöz

Az ügyfél által felügyelt kulcsok a fürthöz való engedélyezéséhez először rendeljen hozzá egy rendszerhez rendelt felügyelt identitást a fürthöz. Ezzel a felügyelt identitással biztosíthatja a fürtnek a kulcstartó eléréséhez szükséges engedélyeket. A rendszerhez rendelt felügyelt identitások konfigurálásához lásd: [felügyelt identitások](/azure/data-explorer/managed-identities).

## <a name="create-a-new-key-vault"></a>Új kulcstartó létrehozása

Ha új kulcstartót szeretne létrehozni a PowerShell használatával, hívja a [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Az Azure Adatkezelő-titkosításhoz az ügyfél által felügyelt kulcsok tárolásához használt kulcstartónak engedélyezve kell lennie két kulcsfontosságú védelmi beállítás, a helyreállítható törlés és a **nem végleges** **Törlés** . Cserélje le a zárójelben lévő helyőrző értékeket az alábbi példában szereplő saját értékekre.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>A Key Vault hozzáférési szabályzatának konfigurálása

Ezután konfigurálja a Key Vault hozzáférési házirendjét, hogy a fürt hozzáférjen az eléréséhez. Ebben a lépésben a rendszerhez rendelt felügyelt identitást fogja használni, amelyet korábban a fürthöz rendelt hozzá. A Key Vault hozzáférési házirendjének beállításához hívja a [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Cserélje le a zárójelben lévő helyőrző értékeket a saját értékeire, és használja az előző példákban definiált változókat.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Új kulcs létrehozása

Ezután hozzon létre egy új kulcsot a Key vaultban. Új kulcs létrehozásához hívja a [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Cserélje le a zárójelben lévő helyőrző értékeket a saját értékeire, és használja az előző példákban definiált változókat.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

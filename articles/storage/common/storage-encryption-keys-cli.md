---
title: Ügyfél által felügyelt kulcsok konfigurálása Azure Storage-titkosításhoz az Azure CLI-ből
description: Ismerje meg, hogyan konfigurálhatja az Azure Storage-titkosításhoz az ügyfél által felügyelt kulcsokat az Azure CLI használatával. Az ügyfél által felügyelt kulcsok lehetővé teszik a hozzáférés-vezérlések létrehozását, elforgatását, letiltását és visszavonását.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ba6cc03a93b664e05a667116cbf845e777416c6b
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155415"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Ügyfél által felügyelt kulcsok konfigurálása Azure Storage-titkosításhoz az Azure CLI-ből

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Ez a cikk bemutatja, hogyan konfigurálhat egy Key vaultot az ügyfél által felügyelt kulcsokkal az Azure CLI használatával.

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok Azure Storage-titkosítással való használata megköveteli, hogy a Key Vault két szükséges tulajdonsággal rendelkezzen, a helyreállítható **törléssel** és a **kiürítéssel**. Ezek a tulajdonságok alapértelmezés szerint engedélyezve vannak, amikor új kulcstartót hoz létre a Azure Portal. Ha azonban egy meglévő kulcstartón kell engedélyeznie ezeket a tulajdonságokat, akkor a PowerShellt vagy az Azure CLI-t kell használnia.
> Csak az RSA-kulcsok és a 2048-es kulcs mérete támogatott.

## <a name="assign-an-identity-to-the-storage-account"></a>Identitás kiosztása a Storage-fiókhoz

Ha engedélyezni szeretné az ügyfél által felügyelt kulcsokat a Storage-fiókhoz, először rendeljen hozzá egy rendszerhez rendelt felügyelt identitást a Storage-fiókhoz. Ezt a felügyelt identitást fogja használni a Storage-fiók engedélyeinek megadásához a kulcstartó eléréséhez.

Felügyelt identitás az Azure CLI-vel való hozzárendeléséhez hívja [az az Storage Account Update](/cli/azure/storage/account#az-storage-account-update)lehetőséget. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

A rendszerhez rendelt felügyelt identitások Azure CLI-vel való konfigurálásával kapcsolatos további információkért lásd: [felügyelt identitások konfigurálása](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)Azure-beli virtuális gépeken az Azure CLI használatával.

## <a name="create-a-new-key-vault"></a>Új kulcstartó létrehozása

Az Azure Storage-titkosításhoz az ügyfél által felügyelt kulcsok tárolásához használt kulcstartónak engedélyezve kell lennie két kulcsfontosságú védelmi beállítás, a helyreállítható törlés és a **nem végleges** **Törlés** . Ha a PowerShell vagy az Azure CLI használatával szeretne új kulcstartót létrehozni ezekkel a beállításokkal, hajtsa végre a következő parancsokat. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire. 

Ha új kulcstartót szeretne létrehozni az Azure CLI használatával, hívja [az az kulcstartó Create](/cli/azure/keyvault#az-keyvault-create). Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>A Key Vault hozzáférési szabályzatának konfigurálása

Ezután konfigurálja a Key Vault hozzáférési házirendjét, hogy a Storage-fióknak hozzáférési jogosultsága legyen az eléréséhez. Ebben a lépésben a korábban a Storage-fiókhoz rendelt felügyelt identitást fogja használni.

A Key Vault hozzáférési házirendjének beállításához hívja az [az kulcstartó set-Policy](/cli/azure/keyvault#az-keyvault-set-policy). Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Új kulcs létrehozása

Ezután hozzon létre egy kulcsot a Key vaultban. A kulcs létrehozásához hívja [az az kulcstartó kulcs létrehozása](/cli/azure/keyvault/key#az-keyvault-key-create)lehetőséget. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Titkosítás konfigurálása az ügyfél által felügyelt kulcsokkal

Alapértelmezés szerint az Azure Storage-titkosítás a Microsoft által felügyelt kulcsokat használja. Konfigurálja az Azure Storage-fiókot az ügyfél által felügyelt kulcsokhoz, és adja meg a Storage-fiókhoz társítandó kulcsot.

A Storage-fiók titkosítási beállításainak frissítéséhez hívja az [az Storage Account Update](/cli/azure/storage/account#az-storage-account-update)menüpontot. Ez a példa a Key Vault URI-JÁT és a kulcs verziószámát is lekérdezi, és mindkét értékre szükség van ahhoz, hogy a kulcsot társítsa a Storage-fiókhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update 
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>A kulcs verziójának frissítése

A kulcsok új verziójának létrehozásakor frissítenie kell a Storage-fiókot az új verzió használatára. Első lépésként a Key Vault URI-JÁT az [az kulcstartó show](/cli/azure/keyvault#az-keyvault-show)paranccsal hívhatja meg, és a kulcs verziószámát az az [kulcstartó Key List-Versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions)paranccsal hívja meg. Ezt követően az az [Storage Account Update](/cli/azure/storage/account#az-storage-account-update) paranccsal frissítse a Storage-fiók titkosítási beállításait úgy, hogy az az előző szakaszban látható módon használják a kulcs új verzióját.

## <a name="next-steps"></a>További lépések

- [Azure Storage-titkosítás a REST-adatokhoz](storage-service-encryption.md) 
- [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?

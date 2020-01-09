---
title: Az ügyfél által felügyelt kulcsok konfigurálása az Azure CLI használatával
titleSuffix: Azure Storage
description: Megtudhatja, hogyan konfigurálhatja az Azure CLI-vel az ügyfelek által felügyelt kulcsokat a Azure Key Vault az Azure Storage encryption szolgáltatással. Az ügyfél által felügyelt kulcsok lehetővé teszik a hozzáférés-vezérlések létrehozását, elforgatását, letiltását és visszavonását.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: db0b5db98f654c140a640f10df2c22c22c85c848
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665298"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Ügyfél által felügyelt kulcsok konfigurálása Azure Key Vault az Azure CLI használatával

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Ez a cikk bemutatja, hogyan konfigurálhat egy Azure Key Vaultt az ügyfél által felügyelt kulcsokkal az Azure CLI használatával. Ha meg szeretné tudni, hogyan hozhat létre kulcstartót az Azure CLI használatával, tekintse meg a következőt [: gyors üzembe helyezés és a titkos kód beolvasása Azure Key Vault az Azure CLI használatával](../../key-vault/quick-create-cli.md).

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

Ha szeretné megtudni, hogyan engedélyezheti a helyreállítható törlést **, és ne** **távolítson** el egy meglévő kulcstartót az Azure CLI-vel, tekintse meg a következő szakaszt: a **Soft-delete engedélyezése** és a végleges törlés **engedélyezése** a [CLI használatával](../../key-vault/key-vault-soft-delete-cli.md).

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

A Storage-fiók titkosítási beállításainak frissítéséhez hívja az az [Storage Account Update](/cli/azure/storage/account#az-storage-account-update)menüpontot az alábbi példában látható módon. Adja meg a `--encryption-key-source` paramétert, és állítsa be `Microsoft.Keyvault`re, hogy engedélyezze az ügyfél által felügyelt kulcsokat a Storage-fiókhoz. A példa a Key Vault URI-JÁT és a kulcs legújabb verzióját is lekérdezi, és mindkét értékre szükség van a kulcs a Storage-fiókhoz való hozzárendeléséhez. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
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

## <a name="use-a-different-key"></a>Másik kulcs használata

Az Azure Storage-titkosításhoz használt kulcs módosításához hívja az az [Storage Account Update](/cli/azure/storage/account#az-storage-account-update) ( [titkosítás konfigurálása az ügyfél által felügyelt kulcsokkal](#configure-encryption-with-customer-managed-keys) ) lehetőséget, és adja meg az új kulcs nevét és verzióját. Ha az új kulcs egy másik kulcstartóban található, frissítse a Key Vault URI-JÁT is.

## <a name="disable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok letiltása

Az ügyfél által felügyelt kulcsok letiltásakor a rendszer a Storage-fiókot a Microsoft által felügyelt kulcsokkal titkosítja. Az ügyfél által felügyelt kulcsok letiltásához hívja meg az az [Storage Account Update](/cli/azure/storage/account#az-storage-account-update) parancsot, és állítsa a `--encryption-key-source parameter` `Microsoft.Storage`re, ahogy az alábbi példában is látható. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```powershell
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>Következő lépések

- [Azure Storage-titkosítás a REST-adatokhoz](storage-service-encryption.md) 
- [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?

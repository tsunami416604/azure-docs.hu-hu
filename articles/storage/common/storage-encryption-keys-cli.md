---
title: Az ügyfél által felügyelt kulcsok konfigurálása az Azure CLI használatával
titleSuffix: Azure Storage
description: Megtudhatja, hogyan konfigurálhatja az Azure CLI-vel az ügyfelek által felügyelt kulcsokat a Azure Key Vault az Azure Storage encryption szolgáltatással.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 351fe5acd8d607b5b60817c235161ac09e530e99
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495012"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Ügyfél által felügyelt kulcsok konfigurálása Azure Key Vault az Azure CLI használatával

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Ez a cikk bemutatja, hogyan konfigurálhat egy Azure Key Vaultt az ügyfél által felügyelt kulcsokkal az Azure CLI használatával. Ha meg szeretné tudni, hogyan hozhat létre kulcstartót az Azure CLI használatával, tekintse meg a következőt [: gyors üzembe helyezés és a titkos kód beolvasása Azure Key Vault az Azure CLI használatával](../../key-vault/secrets/quick-create-cli.md).

## <a name="assign-an-identity-to-the-storage-account"></a>Identitás kiosztása a Storage-fiókhoz

Ha engedélyezni szeretné az ügyfél által felügyelt kulcsokat a Storage-fiókhoz, először rendeljen hozzá egy rendszerhez rendelt felügyelt identitást a Storage-fiókhoz. Ezt a felügyelt identitást fogja használni a Storage-fiók engedélyeinek megadásához a kulcstartó eléréséhez.

Felügyelt identitás az Azure CLI-vel való hozzárendeléséhez hívja [az az Storage Account Update](/cli/azure/storage/account#az-storage-account-update)lehetőséget. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```azurecli-interactive
az login
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

Ha szeretné megtudni, hogyan engedélyezheti a helyreállítható törlést **, és ne** **távolítson** el egy meglévő kulcstartót az Azure CLI-vel, tekintse meg a következő szakaszt: a **Soft-delete engedélyezése** és a végleges törlés **engedélyezése** a [CLI használatával](../../key-vault/general/soft-delete-cli.md).

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
    --key-permissions get unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Új kulcs létrehozása

Ezután hozzon létre egy kulcsot a Key vaultban. A kulcs létrehozásához hívja [az az kulcstartó kulcs létrehozása](/cli/azure/keyvault/key#az-keyvault-key-create)lehetőséget. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

Az Azure Storage encryption a 2048, 3072 és 4096 méretű RSA-és RSA-HSM-kulcsokat támogatja. A kulcsokkal kapcsolatos további információkért tekintse meg a kulcsok [, titkos kódok és tanúsítványok](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys) **Key Vault kulcsait** Azure Key Vault ismertető témakört.

## <a name="configure-encryption-with-customer-managed-keys"></a>Titkosítás konfigurálása az ügyfél által felügyelt kulcsokkal

Alapértelmezés szerint az Azure Storage-titkosítás a Microsoft által felügyelt kulcsokat használja. Ebben a lépésben az Azure Storage-fiókot úgy konfigurálja, hogy az ügyfél által felügyelt kulcsokat Azure Key Vault használatával használja, majd adja meg a Storage-fiókhoz társítandó kulcsot.

Ha az ügyfél által felügyelt kulcsokkal konfigurálja a titkosítást, dönthet úgy, hogy automatikusan elforgatja a titkosításhoz használt kulcsot, amikor a verzió módosul a társított kulcstartóban. Másik lehetőségként explicit módon megadhatja a titkosításhoz használni kívánt verziót, amíg a kulcs verzióját manuálisan nem frissíti.

### <a name="configure-encryption-for-automatic-rotation-of-customer-managed-keys"></a>Titkosítás konfigurálása az ügyfél által felügyelt kulcsok automatikus elforgatásához

Az ügyfél által felügyelt kulcsok automatikus elforgatására szolgáló titkosítás konfigurálásához telepítse az [Azure CLI-verziót 2.4.0 vagy újabb verzióra](/cli/azure/release-notes-azure-cli#april-21-2020) . További információ: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Az ügyfél által felügyelt kulcsok automatikus elforgatásához hagyja ki a kulcs verzióját, ha az ügyfél által felügyelt kulcsokat konfigurálja a Storage-fiókhoz. Az alábbi példában látható módon frissítse a Storage-fiók titkosítási beállításait az az [Storage Account Update](/cli/azure/storage/account#az-storage-account-update) paranccsal. Adja meg a `--encryption-key-source` paramétert, és állítsa be úgy, hogy `Microsoft.Keyvault` engedélyezze az ügyfél által felügyelt kulcsokat a fiókhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

### <a name="configure-encryption-for-manual-rotation-of-key-versions"></a>Titkosítás konfigurálása a kulcsfontosságú verziók manuális elforgatásához

Ha explicit módon meg szeretné adni a titkosításhoz használni kívánt verziót, adja meg a kulcs verziószámát, ha a titkosítást az ügyfél által felügyelt kulcsokkal konfigurálja a Storage-fiókhoz. Az alábbi példában látható módon frissítse a Storage-fiók titkosítási beállításait az az [Storage Account Update](/cli/azure/storage/account#az-storage-account-update) paranccsal. Adja meg a `--encryption-key-source` paramétert, és állítsa be úgy, hogy `Microsoft.Keyvault` engedélyezze az ügyfél által felügyelt kulcsokat a fiókhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

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

Amikor manuálisan elforgatja a kulcs verzióját, frissítenie kell a Storage-fiók titkosítási beállításait az új verzió használatára. Első lépésként a Key Vault URI-JÁT az [az kulcstartó show](/cli/azure/keyvault#az-keyvault-show)paranccsal hívhatja meg, és a kulcs verziószámát az az [kulcstartó Key List-Versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions)paranccsal hívja meg. Ezt követően az az [Storage Account Update](/cli/azure/storage/account#az-storage-account-update) paranccsal frissítse a Storage-fiók titkosítási beállításait a kulcs új verziójának használatára az előző példában látható módon.

## <a name="use-a-different-key"></a>Másik kulcs használata

Az Azure Storage-titkosításhoz használt kulcs módosításához hívja az az [Storage Account Update](/cli/azure/storage/account#az-storage-account-update) ( [titkosítás konfigurálása az ügyfél által felügyelt kulcsokkal](#configure-encryption-with-customer-managed-keys) ) lehetőséget, és adja meg az új kulcs nevét és verzióját. Ha az új kulcs egy másik kulcstartóban található, frissítse a Key Vault URI-JÁT is.

## <a name="revoke-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok visszavonása

Az ügyfél által felügyelt kulcsok visszavonásához távolítsa el a Key Vault hozzáférési házirendjét. Az ügyfél által felügyelt kulcs visszavonásához hívja meg az az kulcstartó [delete-Policy](/cli/azure/keyvault#az-keyvault-delete-policy) parancsot az alábbi példában látható módon. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>Ügyfél által felügyelt kulcsok letiltása

Ha letiltja az ügyfél által felügyelt kulcsokat, a Storage-fiók újból titkosítva lesz a Microsoft által felügyelt kulcsokkal. Az ügyfél által felügyelt kulcsok letiltásához hívja meg az az [Storage Account Update](/cli/azure/storage/account#az-storage-account-update) parancsot, és állítsa be a parancsot `--encryption-key-source parameter` `Microsoft.Storage` az alábbi példában látható módon. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és az előző példákban definiált változókat használni.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>További lépések

- [Inaktív adatok Azure Storage-titkosítása](storage-service-encryption.md) 
- [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?

---
title: Ügyfél által kezelt kulcsok konfigurálása az Azure CLI segítségével
titleSuffix: Azure Storage
description: Ismerje meg, hogyan konfigurálhatja az ügyfelek által felügyelt kulcsokat az Azure Key Vault titkosítással az Azure CLI használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c20fcdf6a725da9d7b053f0ad98efec6aca88f81
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478212"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-azure-cli"></a>Ügyfél által felügyelt kulcsok konfigurálása az Azure Key Vault használatával az Azure CLI használatával

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Key Vault az ügyfelek által felügyelt kulcsok az Azure CLI használatával. Ha meg szeretné tudni, hogyan hozhat létre egy kulcstartót az Azure CLI használatával, olvassa el [a rövid útmutató: Titkos kulcs beállítása és beolvasása az Azure Key Vaultból az Azure CLI használatával című témakört.](../../key-vault/quick-create-cli.md)

## <a name="assign-an-identity-to-the-storage-account"></a>Identitás hozzárendelése a tárfiókhoz

Az ügyfél által felügyelt kulcsok engedélyezéséhez a tárfiókhoz először rendeljen hozzá egy rendszeráltal hozzárendelt felügyelt identitást a tárfiókhoz. Ezt a felügyelt identitást fogja használni a tárfiók engedélyek megadásához a key vault eléréséhez.

Ha felügyelt identitást szeretne hozzárendelni az Azure CLI használatával, hívja meg [az az storage-fiók frissítését.](/cli/azure/storage/account#az-storage-account-update) Ne felejtse el a zárójelben lévő helyőrző értékeket a saját értékeire cserélni.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

A rendszeráltal hozzárendelt felügyelt identitások Azure CLI-vel való konfigurálásáról az [Azure-erőforrások felügyelt identitásának konfigurálása Azure-alapú virtuális gépeken az Azure CLI használatával című](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)témakörben talál további információt.

## <a name="create-a-new-key-vault"></a>Új kulcstartó létrehozása

Az Azure Storage-titkosítás ügyfél által felügyelt kulcsainak tárolására használt kulcstartónak két kulcsvédelmi beállítással kell rendelkeznie: **a Helyreállítható törlés** és **a Ne ürítés.** Ha új kulcstartót szeretne létrehozni a PowerShell vagy az Azure CLI használatával, és ezeket a beállításokat engedélyezve van, hajtsa végre a következő parancsokat. Ne felejtse el a zárójelben lévő helyőrző értékeket a saját értékeire cserélni.

Ha új kulcstartót szeretne létrehozni az Azure CLI használatával, hívja meg [az keyvault create](/cli/azure/keyvault#az-keyvault-create). Ne felejtse el a zárójelben lévő helyőrző értékeket a saját értékeire cserélni.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

Ha meg szeretné tudni, hogyan engedélyezheti a **helyreállítható törlést** és a **Nem engedélyezést** egy meglévő kulcstartóban az Azure CLI-vel, olvassa el a **"A soft-delete"** és a **Kiürítési védelem engedélyezése** című szakaszokat a [soft-delete használata a CLI-vel](../../key-vault/key-vault-soft-delete-cli.md)című szakaszban.

## <a name="configure-the-key-vault-access-policy"></a>A key vault-hozzáférési házirend konfigurálása

Ezután konfigurálja a hozzáférési szabályzatot a key vault, hogy a tárfiók rendelkezik hozzáféréssel. Ebben a lépésben a tárfiókhoz korábban hozzárendelt felügyelt identitást fogja használni.

A kulcstartó hozzáférési szabályzatának beállításához hívja meg az [keyvault set-policy.](/cli/azure/keyvault#az-keyvault-set-policy) Ne felejtse el a zárójelben lévő helyőrző értékeket a saját értékeire cserélni.

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

Ezután hozzon létre egy kulcsot a key vaultban. Kulcs létrehozásához hívja meg [az keyvault-kulcslétrehozás.](/cli/azure/keyvault/key#az-keyvault-key-create) Ne felejtse el a zárójelben lévő helyőrző értékeket a saját értékeire cserélni.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

Csak 2048 bites RSA és RSA-HSM kulcsok at az Azure Storage titkosítása támogatja. A kulcsokról további információt az [Azure Key Vault-kulcsok, titkos kulcsok és tanúsítványok –](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys) **Kulcstároló-kulcsok** című témakörben talál.

## <a name="configure-encryption-with-customer-managed-keys"></a>Titkosítás konfigurálása ügyfél által kezelt kulcsokkal

Alapértelmezés szerint az Azure Storage titkosítása Microsoft által felügyelt kulcsokat használ. Konfigurálja az Azure Storage-fiókot az ügyfél által kezelt kulcsokhoz, és adja meg a tárfiókhoz társítandó kulcsot.

A tárfiók titkosítási beállításainak frissítéséhez hívja meg az [az storage fiók frissítését,](/cli/azure/storage/account#az-storage-account-update)ahogy az a következő példában látható. Adja `--encryption-key-source` meg a paramétert, és állítsa be, hogy `Microsoft.Keyvault` engedélyezze az ügyfél által felügyelt kulcsokat a tárfiókhoz. A példa is lekérdezi a key vault URI és a legújabb kulcsverziója, mindkettő értékek szükségesek a kulcs társításához a tárfiókhoz. Ne felejtse el a zárójelben lévő helyőrző értékeket a saját értékeire cserélni.

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

## <a name="update-the-key-version"></a>A kulcsverzió frissítése

Amikor egy kulcs új verzióját hozza létre, frissítenie kell a tárfiókot az új verzió használatához. Először a key vault URI lekérdezésével az [keyvault show,](/cli/azure/keyvault#az-keyvault-show)és a kulcs verzió hívásával [az keyvault key list-versions.](/cli/azure/keyvault/key#az-keyvault-key-list-versions) Ezután hívja [meg az az storage fiók frissítését](/cli/azure/storage/account#az-storage-account-update) a tárfiók titkosítási beállításainak frissítéséhez a kulcs új verziójának használatához, ahogy az az előző szakaszban látható.

## <a name="use-a-different-key"></a>Másik kulcs használata

Az Azure Storage-titkosításhoz használt kulcs módosításához hívja meg [az az storage-fiók frissítését](/cli/azure/storage/account#az-storage-account-update) a [Titkosítás konfigurálása az ügyfél által kezelt kulcsokkal](#configure-encryption-with-customer-managed-keys) című részben látható módon, és adja meg az új kulcsnevet és -verziót. Ha az új kulcs egy másik key vaultban található, frissítse a key vault URI-ját is.

## <a name="revoke-customer-managed-keys"></a>Ügyfél által kezelt kulcsok visszavonása

Ha úgy véli, hogy egy kulcs biztonsága sérülhet, visszavonhatja az ügyfél által felügyelt kulcsok eltávolításával a key vault hozzáférési szabályzat. Az ügyfél által felügyelt kulcs visszavonásához hívja meg az [az keyvault delete-policy](/cli/azure/keyvault#az-keyvault-delete-policy) parancsot, ahogy az alábbi példában látható. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és használja az előző példákban meghatározott változókat.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

## <a name="disable-customer-managed-keys"></a>Ügyfél által kezelt kulcsok letiltása

Ha letiltja az ügyfél által felügyelt kulcsokat, a tárfiók ismét titkosítva lesz a Microsoft által felügyelt kulcsokkal. Az ügyfél által kezelt kulcsok letiltásához hívja `--encryption-key-source parameter` `Microsoft.Storage`meg az [az tárfiók frissítését,](/cli/azure/storage/account#az-storage-account-update) és állítsa be a beállítását a beállításra, ahogy az a következő példában látható. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire, és használja az előző példákban meghatározott változókat.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

## <a name="next-steps"></a>További lépések

- [Az Azure Storage titkosítása az inaktív adatokhoz](storage-service-encryption.md) 
- [Mi az Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

---
title: Ügyfél által felügyelt kulcsok az Azure CLI Azure Storage-titkosítás konfigurálása
description: Útmutató az Azure CLI használatával az ügyfél által felügyelt kulcsok az Azure Storage-titkosítás konfigurálása. Ügyfél által felügyelt kulcsokat hozhat létre, elforgatása, tiltsa le, és visszavonhatja a hozzáférés-vezérlés lehetővé teszik.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ca2cfc9369fd6fb001b2a2dc401c33e5ddfd38c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65142909"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Ügyfél által felügyelt kulcsok az Azure CLI Azure Storage-titkosítás konfigurálása

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Ez a cikk bemutatja, hogyan konfigurálása a key vault Azure parancssori felületével ügyfél által felügyelt kulcsokkal.

## <a name="assign-an-identity-to-the-storage-account"></a>Identitás hozzárendelése a storage-fiók

Ahhoz, hogy az ügyfél által felügyelt kulcsokat a tárfiók, először hozzárendel egy rendszer által hozzárendelt felügyelt identitás a storage-fiókba. A tárolási fiók szükséges engedélyek biztosítása a kulcstartó elérését a felügyelt identitást fogja használni.

Rendelje hozzá egy felügyelt identitás, az Azure CLI-vel, hívja meg [storage-fiók frissítése az](/cli/azure/storage/account#az-storage-account-update). Ne felejtse el a zárójeleket helyőrzőértékeket cserélje le a saját értékeire.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Felügyelt identitások alapértelmezett konfigurálása az Azure CLI-vel kapcsolatos további információkért lásd: [konfigurálása felügyelt identitások az Azure-erőforrások egy Azure virtuális gépen az Azure CLI-vel](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Új kulcstartó létrehozása

A key vaultban, ügyfél által felügyelt kulcsok tárolására, az Azure Storage-titkosítás kell rendelkeznie a két kulcs védelme beállítás engedélyezve van, amellyel **a helyreállítható Törlés** és **tegye végleges törlés**. Hozzon létre egy új kulcstartót, PowerShell vagy az Azure CLI használatával az ezekkel a beállításokkal, hajtsa végre a következő parancsokat. Ne felejtse el a zárójeleket helyőrzőértékeket cserélje le a saját értékeire. 

Hozzon létre egy új kulcstartót, Azure CLI-vel, hívja meg [az keyvault létrehozása](/cli/azure/keyvault#az-keyvault-create). Ne felejtse el a zárójeleket helyőrzőértékeket cserélje le a saját értékeire.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>A kulcstartó-hozzáférési házirend konfigurálása

Ezután konfigurálja a kulcstartó hozzáférési házirendje, úgy, hogy a tárfiók engedélyeket az eléréséhez. Ebben a lépésben a felügyelt identitás, amelyet korábban a tárfiókhoz rendelt fogja használni.

A kulcstartó hozzáférési házirend beállítása, hívja [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). Ne felejtse el a zárójeleket helyőrzőértékeket cserélje le a saját értékeire.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    -name <storage-account> \
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

Ezután hozzon létre egy kulcsot a kulcstartóban. A kulcs létrehozásához hívja [az keyvault key létrehozása](/cli/azure/keyvault/key#az-keyvault-key-create). Ne felejtse el a zárójeleket helyőrzőértékeket cserélje le a saját értékeire.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Ügyfél által felügyelt kulcsokkal titkosítás konfigurálása

Alapértelmezés szerint az Azure Storage-titkosítás használja a Microsoft által felügyelt kulcsokkal. Az Azure Storage-fiók felhasználó által kezelt kulcsokkal konfigurálja, és adja meg a, a storage-fiókhoz társít.

Titkosítási beállítások a storage-fiók frissítéséhez hívja meg [storage-fiók frissítése az](/cli/azure/storage/account#az-storage-account-update). Ebben a példában is lekérdezi a key vault URI és kulcs verziója, mely értékeket a kulcs társítása a storage-fiók szükséges. Ne felejtse el a zárójeleket helyőrzőértékeket cserélje le a saját értékeire.

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

## <a name="update-the-key-version"></a>Frissítés a kulcs verziója

Amikor létrehoz egy kulcs új verziója, szüksége frissíteni a tárfiókot, hogy az új verziót használja. Első lépésként lekérdezni a key vault URI-t a meghívásával [az keyvault show](/cli/azure/keyvault#az-keyvault-show), és a kulcs verzióját is meghívásával [az keyvault key list-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Ezután hívja meg [storage-fiók frissítése az](/cli/azure/storage/account#az-storage-account-update) frissíteni a tárfiók titkosítási beállítások használatához a kulcs új verziója, az előző szakaszban látható módon.

## <a name="next-steps"></a>További lépések

- [Inaktív adatok az Azure Storage-titkosítás](storage-service-encryption.md) 
- [Mi az Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?

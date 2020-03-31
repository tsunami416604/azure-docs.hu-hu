---
title: Olyan fiók létrehozása, amely támogatja a táblák és várólisták ügyféláltal kezelt kulcsait
titleSuffix: Azure Storage
description: Ismerje meg, hogyan hozhat létre olyan tárfiókot, amely támogatja az ügyfél által felügyelt kulcsok konfigurálását táblákhoz és várólistákhoz. Az Azure CLI vagy egy Azure Resource Manager-sablon használatával hozzon létre egy tárfiókot, amely az Azure Storage titkosításához a fiók titkosítási kulcsára támaszkodik. Ezután konfigurálhatja a fiók ügyfél által kezelt kulcsait.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 09558a8d1e4e2dc68cefd2c870f54e008d10b97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083546"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Olyan fiók létrehozása, amely támogatja a táblák és várólisták ügyféláltal kezelt kulcsait

Az Azure Storage titkosítja az összes adatot egy tárolófiókban inaktív. Alapértelmezés szerint a várólista-tároló és a Table storage olyan kulcsot használ, amely a szolgáltatás hatóköre, és amelyet a Microsoft kezel. Dönthet úgy is, hogy ügyfél által kezelt kulcsokat használ a várólista- vagy táblaadatok titkosításához. Az ügyfél által felügyelt kulcsok várólistákkal és táblákkal való használatához először létre kell hoznia egy olyan tárfiókot, amely a fiókhoz, nem pedig a szolgáltatáshoz tartozó titkosítási kulcsot használ. Miután létrehozott egy fiókot, amely a fiók titkosítási kulcsa a várólista- és táblaadatok, konfigurálhatja az ügyfél által kezelt kulcsok az Azure Key Vault az adott tárfiókhoz.

Ez a cikk ismerteti, hogyan hozhat létre egy tárfiókot, amely a fiók hatóköre támaszkodik. A fiók első létrehozásakor a Microsoft a fiókkulcs segítségével titkosítja a fiókban lévő adatokat, és a Microsoft kezeli a kulcsot. Ezt követően beállíthatja az ügyfél által felügyelt kulcsokat a fiókhoz, hogy kihasználhassa ezeket az előnyöket, beleértve a saját kulcsok biztosítását, a kulcsverzió frissítését, a kulcsok elforgatását és a hozzáférés-vezérlés visszavonását.

## <a name="about-the-feature"></a>A funkcióról

Hozzon létre egy tárfiókot, amely támaszkodik a fiók titkosítási kulcs a várólista és a table storage, először regisztrálnia kell használni ezt a funkciót az Azure-ral. A korlátozott kapacitás miatt vegye figyelembe, hogy a hozzáférési kérelmek jóváhagyása több hónapot is igénybe vehet.

Létrehozhat egy olyan tárfiókot, amely a következő régiókban a várólista- és táblatárolás fióktitkosítási kulcsára támaszkodik:

- USA keleti régiója
- USA déli középső régiója
- USA nyugati régiója, 2.  

### <a name="register-to-use-the-account-encryption-key"></a>Regisztráció a fióktitkosítási kulcs használatához

A fiók titkosítási kulcsának várólistával vagy table storage-szal való használatához használja a PowerShellt vagy az Azure CLI-t.

# <a name="powershell"></a>[Powershell](#tab/powershell)

A PowerShell használatával való regisztrációhoz hívja meg a [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) parancsot.

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-vel való regisztrációhoz hívja meg az [az szolgáltatásregiszter](/cli/azure/feature#az-feature-register) parancsot.

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Sablon](#tab/template)

N/A

---

### <a name="check-the-status-of-your-registration"></a>A regisztráció állapotának ellenőrzése

A regisztráció állapotának ellenőrzéséhez a várólista vagy a table storage, használja a PowerShell vagy az Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/powershell)

A Regisztráció állapotának ellenőrzéséhez a PowerShell, hívja meg a [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) parancsot.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-vel való regisztráció állapotának ellenőrzéséhez hívja meg az [az funkcióparancsot.](/cli/azure/feature#az-feature-show)

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Sablon](#tab/template)

N/A

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Regisztrálja újra az Azure Storage-erőforrás-szolgáltatót

A regisztráció jóváhagyása után újra regisztrálnia kell az Azure Storage-erőforrás-szolgáltatót. A PowerShell vagy az Azure CLI használatával újra regisztrálhatja az erőforrás-szolgáltatót.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Az erőforrás-szolgáltató újra regisztrálásához a PowerShell segítségével hívja meg a [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) parancsot.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az erőforrás-szolgáltató újbóli regisztrálásához az Azure CLI-vel hívja meg az [az provider register](/cli/azure/provider#az-provider-register) parancsot.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Sablon](#tab/template)

N/A

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Fióktitkosítási kulcsot használó fiók létrehozása

Be kell állítania egy új tárfiókot, hogy a fiók titkosítási kulcsa a várólisták és táblák létrehozásakor a tárfiók. A titkosítási kulcs hatóköre a fiók létrehozása után nem módosítható.

A tárfióknak általános célú v2 típusúnak kell lennie. Létrehozhatja a tárfiókot, és konfigurálhatja, hogy az Azure CLI vagy egy Azure Resource Manager-sablon használatával támaszkodjon a fiók titkosítási kulcsára.

> [!NOTE]
> Csak a várólista- és táblatároló konfigurálható úgy, hogy a tárfiók létrehozásakor titkosítsa az adatokat a fiók titkosítási kulccsal. A Blob storage és az Azure Files mindig a fiók titkosítási kulcsát használja az adatok titkosításához.

# <a name="powershell"></a>[Powershell](#tab/powershell)

A PowerShell használatával hozzon létre egy tárfiókot, amely a fiók titkosítási kulcstámaszkodik, győződjön meg arról, hogy telepítette az Azure PowerShell modul, 3.4.0-s vagy újabb verzió. További információ: [Az Azure PowerShell-modul telepítése.](/powershell/azure/install-az-ps)

Ezután hozzon létre egy általános célú v2 tárfiókot a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancs felhívásával a megfelelő paraméterekkel:

- Adja `-EncryptionKeyTypeForQueue` meg a lehetőséget, `Account` és állítsa be annak értékét, hogy a fióktitkosítási kulcs használatával titkosítsa az adatokat a várólista-tárolóban.
- Adja `-EncryptionKeyTypeForTable` meg a lehetőséget, `Account` és állítsa be annak értékét, hogy a fióktitkosítási kulcs használatával titkosítsa az adatokat a Table storage-ban.

A következő példa bemutatja, hogyan hozhat létre egy általános célú v2 tárfiókot, amely az olvasási hozzáférésű georedundáns tárolásra (RA-GRS) van konfigurálva, és amely a fiók titkosítási kulcsát használja a várólista és a table tár adatainak titkosításához. Ne felejtse el a zárójelben lévő helyőrző értékeket a saját értékeire cserélni:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha az Azure CLI használatával hozzon létre egy tárfiókot, amely a fiók titkosítási kulcsra támaszkodik, győződjön meg arról, hogy telepítette az Azure CLI 2.0.80-as vagy újabb verzióját. További információ: [Install the Azure CLI](/cli/azure/install-azure-cli).

Ezután hozzon létre egy általános célú v2-es tárfiókot az [az storage-fiók create](/cli/azure/storage/account#az-storage-account-create) parancs ának felhívásával a megfelelő paraméterekkel:

- Adja `--encryption-key-type-for-queue` meg a lehetőséget, `Account` és állítsa be annak értékét, hogy a fióktitkosítási kulcs használatával titkosítsa az adatokat a várólista-tárolóban.
- Adja `--encryption-key-type-for-table` meg a lehetőséget, `Account` és állítsa be annak értékét, hogy a fióktitkosítási kulcs használatával titkosítsa az adatokat a Table storage-ban.

A következő példa bemutatja, hogyan hozhat létre egy általános célú v2 tárfiókot, amely az olvasási hozzáférésű georedundáns tárolásra (RA-GRS) van konfigurálva, és amely a fiók titkosítási kulcsát használja a várólista és a table tár adatainak titkosításához. Ne felejtse el a zárójelben lévő helyőrző értékeket a saját értékeire cserélni:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Sablon](#tab/template)

A következő JSON-példa létrehoz egy általános célú v2-es tárfiókot, amely olvasási hozzáférésű georedundáns tárolásra (RA-GRS) van konfigurálva, és amely a fiók titkosítási kulcsát használja a várólista és a table tár adatainak titkosításához. Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Miután létrehozott egy fiókot, amely a fiók titkosítási kulcsára támaszkodik, tekintse meg az alábbi cikkek egyikét az ügyfél által felügyelt kulcsok Azure Key Vault használatával történő konfigurálásához:

- [Ügyfél által felügyelt kulcsok konfigurálása az Azure Key Vault használatával az Azure Portal használatával](storage-encryption-keys-portal.md)
- [Ügyfél által felügyelt kulcsok konfigurálása az Azure Key Vault használatával a PowerShell használatával](storage-encryption-keys-powershell.md)
- [Ügyfél által felügyelt kulcsok konfigurálása az Azure Key Vault használatával az Azure CLI használatával](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>A fiók titkosítási kulcsának ellenőrzése

Annak ellenőrzéséhez, hogy egy tárfiókban lévő szolgáltatás a fiók titkosítási kulcsát használja-e, hívja meg az Azure CLI [az storage-fiók parancsot.](/cli/azure/storage/account#az-storage-account-show) Ez a parancs a tárfiók tulajdonságainak és értékeinek készletét adja vissza. Keresse meg `keyType` a titkosítási tulajdonságon belüli egyes szolgáltatások `Account`mezőjét, és ellenőrizze, hogy a beállítása .

# <a name="powershell"></a>[Powershell](#tab/powershell)

Annak ellenőrzéséhez, hogy egy tárfiókban lévő szolgáltatás használja-e a fiók titkosítási kulcsát, hívja meg a [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) parancsot. Ez a parancs a tárfiók tulajdonságainak és értékeinek készletét adja vissza. Keresse meg `KeyType` a `Encryption` tulajdonságon belüli egyes szolgáltatások mezőjét, és ellenőrizze, hogy a beállítása `Account`.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Annak ellenőrzéséhez, hogy egy tárfiókban lévő szolgáltatás használja-e a fiók titkosítási kulcsát, hívja meg az [az storage-fiók](/cli/azure/storage/account#az-storage-account-show) parancsot. Ez a parancs a tárfiók tulajdonságainak és értékeinek készletét adja vissza. Keresse meg `keyType` a titkosítási tulajdonságon belüli egyes szolgáltatások `Account`mezőjét, és ellenőrizze, hogy a beállítása .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Sablon](#tab/template)

N/A

---

## <a name="next-steps"></a>További lépések

- [Az Azure Storage titkosítása az inaktív adatokhoz](storage-service-encryption.md) 
- [Mi az Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

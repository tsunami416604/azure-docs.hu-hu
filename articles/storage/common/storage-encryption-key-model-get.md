---
title: A Storage-fiókhoz használt titkosítási kulcs modellének meghatározása
titleSuffix: Azure Storage
description: A Azure Portal, a PowerShell vagy az Azure CLI használatával megtudhatja, hogyan kezelje a titkosítási kulcsokat a Storage-fiókhoz. A kulcsokat a Microsoft (alapértelmezett) vagy az ügyfél kezelheti. Az ügyfél által felügyelt kulcsokat Azure Key Vault kell tárolni.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 3806fead9226978c277e87f3d97b14ee38d9552d
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665412"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>A Storage-fiókhoz használt Azure Storage titkosítási kulcs modelljének meghatározása

A Storage-fiókban tárolt adatait a rendszer automatikusan titkosítja az Azure Storage-ban. Az Azure Storage encryption két lehetőséget kínál a titkosítási kulcsok kezelésére a Storage-fiók szintjén:

- **Microsoft által felügyelt kulcsok.** Alapértelmezés szerint a Microsoft kezeli a Storage-fiók titkosításához használt kulcsokat.
- **Ügyfél által felügyelt kulcsok.** Lehetősége van a Storage-fiók titkosítási kulcsainak kezelésére is. Az ügyfél által felügyelt kulcsokat Azure Key Vault kell tárolni.

Emellett egy titkosítási kulcsot is megadhat egy egyedi kérelem szintjén egy blob Storage-művelethez. Ha a kérelemben titkosítási kulcs van megadva, a kulcs felülbírálja a Storage-fiókban aktív titkosítási kulcsot. További információ: a [felhasználó által megadott kulcs megadása a blob Storage-ra vonatkozó kérelemben](../blobs/storage-blob-customer-provided-key.md).

A titkosítási kulcsokkal kapcsolatos további információkért lásd: [Az Azure Storage titkosítása inaktív adatokhoz](storage-service-encryption.md).

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>A Storage-fiók titkosítási kulcsának modellje

Az alábbi módszerek egyikével meghatározhatja, hogy a Storage-fiók Microsoft által felügyelt kulcsokat vagy az ügyfél által felügyelt kulcsokat használja-e a titkosításhoz.

# <a name="azure-portaltabportal"></a>[Azure Portal](#tab/portal)

A következő lépésekkel ellenőrizheti a Storage-fiók titkosítási modelljét a Azure Portal használatával:

1. Az Azure Portalon lépjen a tárfiókra.
1. Válassza ki a **titkosítási** beállítást, és jegyezze fel a beállítást.

Az alábbi képen egy olyan Storage-fiók látható, amelyben az ügyfél által felügyelt kulcsokat használják a titkosításhoz:

![A titkosítási kulcs beállítását bemutató képernyőfelvétel Azure Portal](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Ha a PowerShell használatával szeretné megtekinteni a Storage-fiók titkosítási modelljét, hívja meg a [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) parancsot, majd tekintse meg a fiókhoz tartozó **forrás** tulajdonságot.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Ha a **forrás** tulajdonság értéke `Microsoft.Storage`, akkor a fiók a Microsoft által felügyelt kulcsokkal van titkosítva. Ha a **forrás** tulajdonság értéke `Microsoft.Keyvault`, akkor a fiók az ügyfél által felügyelt kulcsokkal van titkosítva.

# <a name="azure-clitabcli"></a>[Azure CLI](#tab/cli)

Ha az Azure CLI használatával szeretné megtekinteni a Storage-fiók titkosítási modelljét, hívja meg az az [Storage Account show](/cli/azure/storage/account#az-storage-account-show) parancsot, majd tekintse meg a fiókhoz tartozó **forrás** tulajdonságot.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Ha a **forrás** tulajdonság értéke `Microsoft.Storage`, akkor a fiók a Microsoft által felügyelt kulcsokkal van titkosítva. Ha a **forrás** tulajdonság értéke `Microsoft.Keyvault`, akkor a fiók az ügyfél által felügyelt kulcsokkal van titkosítva.

---

## <a name="next-steps"></a>Következő lépések

[Azure Storage-titkosítás a REST-adatokhoz](storage-service-encryption.md)

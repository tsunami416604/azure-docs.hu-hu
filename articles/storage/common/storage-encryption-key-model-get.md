---
title: Annak meghatározása, hogy melyik titkosítási kulcsmodell van használatban a tárfiókhoz
titleSuffix: Azure Storage
description: Az Azure Portalon, a PowerShellben vagy az Azure CLI-ben ellenőrizheti, hogy a tárfiók titkosítási kulcsainak kezelése hogyan történik. A kulcsokat a Microsoft (az alapértelmezett) vagy az ügyfél is kezelheti. Az ügyfél által felügyelt kulcsokat az Azure Key Vaultban kell tárolni.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0df0ba4ce76d249bcb4738b41c94677e061f14ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409855"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Annak meghatározása, hogy melyik Azure Storage titkosítási kulcsmodell van használatban a tárfiókhoz

A tárfiókban lévő adatokat az Azure Storage automatikusan titkosítja. Az Azure Storage titkosítása két lehetőséget kínál a titkosítási kulcsok kezelésére a tárfiók szintjén:

- **Microsoft által felügyelt kulcsok.** Alapértelmezés szerint a Microsoft kezeli a tárfiók titkosításához használt kulcsokat.
- **Ügyfél által felügyelt kulcsok.** Igény szerint kezelheti a tárfiók titkosítási kulcsait. Az ügyfél által felügyelt kulcsokat az Azure Key Vaultban kell tárolni.

Emellett egy titkosítási kulcsot biztosíthat bizonyos Blob-tárolási műveletek egyéni kérésének szintjén. Ha a kérelemben meg van adva egy titkosítási kulcs, az a kulcs felülbírálja a tárfiókban aktív titkosítási kulcsot. További információ: [Ügyfél által biztosított kulcs megadása a Blob storage kérésre című témakörben.](../blobs/storage-blob-customer-provided-key.md)

A titkosítási kulcsokról az [Azure Storage titkosítása az inaktív adatokért](storage-service-encryption.md)című témakörben talál további információt.

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>A tárfiók titkosítási kulcsmodelljének ellenőrzése

Annak megállapításához, hogy egy tárfiók Microsoft által kezelt vagy ügyfél által felügyelt kulcsokat használ-e a titkosításhoz, használja az alábbi módszerek egyikét.

# <a name="azure-portal"></a>[Azure-portál](#tab/portal)

A tárfiók titkosítási modelljének ellenőrzéséhez az Azure Portal használatával hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon lépjen a tárfiókra.
1. Válassza ki a **Titkosítás** beállítást, és jegyezze fel a beállítást.

Az alábbi képen egy Microsoft által felügyelt kulcsokkal titkosított tárfiók látható:

![Microsoft által kezelt kulccsal titkosított fiók megtekintése](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

Az alábbi képen egy ügyfél által felügyelt kulcsokkal titkosított tárfiók látható:

![Képernyőkép a titkosítási kulcs beállításról az Azure Portalon](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

A tárfiók titkosítási modelljének powershell használatával történő ellenőrzéséhez hívja meg a [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) parancsot, majd ellenőrizze a fiók **KeySource** tulajdonságát.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

Ha a **KeySource** tulajdonság `Microsoft.Storage`értéke , akkor a fiók microsoftáltal kezelt kulcsokkal van titkosítva. Ha a **KeySource** tulajdonság `Microsoft.Keyvault`értéke, akkor a fiók ügyfél által kezelt kulcsokkal van titkosítva.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

A tárfiók titkosítási modelljének ellenőrzéséhez az Azure CLI használatával hívja meg az [az storage-fiók show](/cli/azure/storage/account#az-storage-account-show) parancsát, majd ellenőrizze a fiók **keySource** tulajdonságát.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

Ha a **keySource** tulajdonság `Microsoft.Storage`értéke , akkor a fiók microsoft által kezelt kulcsokkal van titkosítva. Ha a **keySource** tulajdonság `Microsoft.Keyvault`értéke, akkor a fiók ügyfél által kezelt kulcsokkal van titkosítva.

---

## <a name="next-steps"></a>További lépések

- [Az Azure Storage titkosítása az inaktív adatokhoz](storage-service-encryption.md)
- [Az Azure Storage titkosításának kezeléséhez használja az ügyfelek által felügyelt kulcsokat az Azure Key Vault segítségével](encryption-customer-managed-keys.md)
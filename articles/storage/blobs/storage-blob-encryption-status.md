---
title: Blob titkosítási állapotának ellenőrzése – Azure Storage
description: Ismerje meg, hogyan használhatja az Azure Portalon, a PowerShellt vagy az Azure CLI-t annak ellenőrzéséhez, hogy egy adott blob titkosított-e. Ha egy blob nincs titkosítva, ismerje meg, hogyan kényszerítheti az AzCopy titkosítást a blob letöltésével és újbóli feltöltésével.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 5cef0e94a43b3ef16d45f7f43658f962e07b5345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74707596"
---
# <a name="check-the-encryption-status-of-a-blob"></a>Blob titkosítási állapotának ellenőrzése

2017. október 20-a után az Azure Storage-ba írt minden blokkblob, hozzáfűző blob vagy lapblob az Azure Storage titkosításával van titkosítva. Az ezt a dátum előtt létrehozott blobokat továbbra is egy háttérfolyamat titkosítja.

Ez a cikk bemutatja, hogyan állapítható meg, hogy egy adott blob titkosítva van-e.

## <a name="check-a-blobs-encryption-status"></a>Blob titkosítási állapotának ellenőrzése

Az Azure Portalon, a PowerShellben vagy az Azure CLI-ben határozza meg, hogy egy blob kód nélkül van-e titkosítva.

### <a name="azure-portal"></a>[Azure-portál](#tab/portal)

Ha az Azure Portal on szeretné ellenőrizni, hogy egy blob titkosítva van-e, kövesse az alábbi lépéseket:

1. Az Azure Portalon lépjen a tárfiókra.
1. Válassza **a Tárolók** lehetőséget a fiókban lévő tárolók listájának megugrásához.
1. Keresse meg a blobot, és jelenítse meg **az Áttekintés** lapot.
1. Tekintse meg a **Server Encrypted** tulajdonságot. Ha **igaz**, ahogy az az alábbi képen látható, majd a blob titkosított lesz. Figyelje meg, hogy a blob tulajdonságai is tartalmazzák a blob létrehozásának dátumát és időpontját.

    ![Képernyőkép a Server titkosított tulajdonság ellenőrzéséről az Azure Portalon](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[Powershell](#tab/powershell)

Ha a PowerShell használatával ellenőrizze, hogy egy blob titkosítva van-e, ellenőrizze a blob **IsServerEncrypted** tulajdonságát. Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

A blob létrehozásának időpontjának megállapításához ellenőrizze a **Létrehozott** tulajdonság értékét:

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Ha az Azure CLI használatával ellenőrizni szeretné, hogy egy blob titkosítva van-e, ellenőrizze a blob **IsServerEncrypted** tulajdonságát. Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

A blob létrehozásának időpontjának megállapításához ellenőrizze a **létrehozott** tulajdonság értékét.

---

## <a name="force-encryption-of-a-blob"></a>Blob titkosításának kényszerítése

Ha egy blob, amely előtt létrehozott október 20, 2017 még nem titkosított a háttérben folyamat, kényszerítheti a titkosítást, hogy azonnal megtörténjen a blob letöltésével és újra feltöltésével. Egy egyszerű módja ennek az AzCopy.

Ha az AzCopy segítségével szeretne letölteni egy blobot a helyi fájlrendszerbe, használja az alábbi szintaxist:

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

A blob újra feltöltéséhez az Azure Storage-ba az AzCopy segítségével használja az alábbi szintaxist:

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

A blobadatok másolásának az AzCopy használatával kapcsolatos további tudnivalókért olvassa el az [Adatok átvitele az AzCopy és a Blob tárházával című témakört.](../common/storage-use-azcopy-blobs.md)

## <a name="next-steps"></a>További lépések

[Az Azure Storage titkosítása az inaktív adatokhoz](../common/storage-service-encryption.md)

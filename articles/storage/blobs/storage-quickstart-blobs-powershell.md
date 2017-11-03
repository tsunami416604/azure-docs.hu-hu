---
title: "Azure gyors üzembe helyezés - átviteli objektumok az Azure Blob storage PowerShell-lel |} Microsoft Docs"
description: "Gyorsan elsajátíthatják átvitele az objektumok és a PowerShell használatával az Azure Blob-tároló"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/19/2017
ms.author: robinsh
ms.openlocfilehash: 1a9941b21b92c70dd0a46ce2e4c75142e1786650
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-azure-powershell"></a>Az Azure PowerShell használata Azure Blob storage adatátviteli objektumok

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató adatokat PowerShell használatával történő fájlátvitel a helyi lemezek és az Azure Blob-tároló között.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

A gyorsútmutatóhoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Tároló létrehozása

Blobok egy tárolóba mindig feltöltése. Ez lehetővé teszi, hogy csoportokba rendezheti a blobok például rendezheti a fájlokat a számítógépre, a mappákban.

Állítsa be a tároló neve, majd hozzon létre a tárolót használó [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer), a "blobra", hogy a nyilvános hozzáférést a fájlok engedélyeinek beállítása. Ebben a példában a tároló neve *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. Infrastruktúra-szolgáltatási virtuális gépek biztonsági használt VHD-fájlok a lapblobokat. Hozzáfűző blobok használt naplózást, például a kívánt fájlra és majd hozzáadni további információkat. A legtöbb Blob storage-ban tárolt fájlok blokk blobokat. 

Fájl feltöltése a blokkblob, beolvasni a tároló hivatkozását, majd a blokkblob mutató hivatkozás beszerzése az adott tároló. Miután a blobhivatkozást, az adatait feltöltheti azt a [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Ez a művelet a blob hoz létre, ha már nem létezik, vagy felülírja, ha már létezik.

Az alábbi példák kép001.jpg és a D: a Image002.png feltöltése\\_TestImages mappa meg az imént létrehozott tárolót a helyi lemezen.

```powershell
# upload a file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

A folytatás előtt tetszés szerinti számú fájlok feltöltése.

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A blobot, amely a tárolót használja listáját [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob). Ebben a példában csak a blobok feltöltése nevét tartalmazza.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Blobok letöltése

A blobok letöltése a helyi lemezre. Minden egyes BLOB le kell tölteni, állítsa be a nevet és a hívás [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) letölteni a blob.

Ez a példa letölti a blobokat D:\\_TestImages\Downloads a helyi lemezen. 

```powershell
# download first blob
Get-AzureStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzureStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 
```

## <a name="data-transfer-with-azcopy"></a>Bejövő adatátvitel az AzCopy

A [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) segédprogram lehetősége, hogy az Azure Storage nagy teljesítményű parancsfájlok futtatására alkalmas adatok átvitelét. AzCopy vihet át adatokat, illetve onnan Blob, a fájl és a Table storage.

Gyors példaként, itt van az AzCopy parancs feltölteni a fájlt nevű *sajatfajl.txt* számára a *mystoragecontainer* egy PowerShell-ablakot a tárolóban.

```PowerShell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Távolítsa el az összes létrehozott eszközök. Ennek legegyszerűbb módja, a csoport törléséhez. Ez törli a csoportban található összes erőforrást is. Ebben az esetben azt eltávolítja, a tárfiók és az erőforráscsoport magát.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezési megtanulta, hogyan viheti át a fájlok helyi lemezre és az Azure Blob Storage tárolóban közötti. További információt a Blob storage használata, továbbra is a Blob-tároló útmutató.

> [!div class="nextstepaction"]
> [A BLOB Storage műveletek útmutató](storage-how-to-use-blobs-powershell.md)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>A Microsoft Azure PowerShell tárolás parancsmagok referencia
* [Storage PowerShell-parancsmagok](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
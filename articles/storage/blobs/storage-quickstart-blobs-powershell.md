---
title: Gyorsútmutató – Blob létrehozása a PowerShell használatával
titleSuffix: Azure Storage
description: Ebben a rövid útmutatóban az Azure PowerShell használatával kezelheti az objektumtárat (blobtárat). Majd a PowerShell segítségével feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 03/31/2020
ms.author: tamram
ms.openlocfilehash: 3b005bc359b3c1b0cafe663b7ce2b599b10973a1
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474000"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>Gyorsútmutató: Blobok feltöltése, letöltése és listázása a PowerShell használatával

Az Azure PowerShell-modullal létrehozhat és kezelhet Azure-erőforrásokat. Az Azure-erőforrások létrehozása és kezelése végrehajtható a PowerShell-parancsból vagy szkriptekkel. Ez az útmutató a fájloknak a helyi lemez és az Azure Blob Storage közötti, a PowerShell-lel történő átvitelét ismerteti.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Az Azure Storage eléréséhez szüksége lesz egy Azure-előfizetésre. Ha még nem rendelkezik előfizetéssel, akkor a kezdés előtt hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ehhez a rövid útmutatóhoz az Azure PowerShell-modul, az Az 0.7-es vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: `Get-InstalledModule -Name Az -AllVersions | select Name,Version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Tároló létrehozása

A blobok minden esetben egy tárolóba lesznek feltöltve. A blobok csoportjait hasonló módon rendszerezheti, mint a fájlokat a számítógép mappáiban.

Állítsa be a tároló nevét, majd hozza létre a tárolót a [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)használatával. A fájlokhoz való nyilvános hozzáférés engedélyezéséhez állítsa `blob` értékűre az engedélyeket. A tároló neve ebben a példában: *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. Az IaaS típusú virtuális gépek biztonsági mentéséhez használt VHD-fájlok lapblobok. A hozzáfűző blobok a naplózáshoz használhatók, például amikor egy fájlba szeretne írni, majd folyamatosan újabb információkat szeretne hozzáadni. A blobtárolókban tárolt fájlok a legtöbb esetben blokkblobok. 

Fájlok blokkblobba való feltöltéséhez szerezze be a tároló hivatkozását, majd a blokkblob hivatkozását az adott tárolóban. Miután rendelkezik a blob hivatkozással, adatokat tölthet fel a [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent)használatával. Ez az eljárás létrehozza a blobot, ha az még nem létezett, vagy felülírja azt, ha már igen.

A következő példák feltölteni *Image001.jpg* és *Image002.png* a *D:\\_TestImages* mappát a helyi lemezen a létrehozott tárolóba.

```powershell
# upload a file
Set-AzStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

Mielőtt továbblépne, töltsön fel annyi fájlt, amennyit csak szeretne.

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob)használatával a tárolóban lévő blobok listájának beszereznie. A példában csak a feltöltött blobok neve látható.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Blobok letöltése

Töltse le a blobokat a helyi lemezre. Minden letölteni kívánt blobhoz állítsa be a nevet, és hívja meg a [Get-AzStorageBlobContent metódust](/powershell/module/az.storage/get-azstorageblobcontent) a blob letöltéséhez.

Ez a példa letölti a blobokat *D:\\_TestImages\Letöltések* a helyi lemezen. 

```powershell
# download first blob
Get-AzStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>Adatátvitel az AzCopy használatával

Az AzCopy parancssori segédprogram nagy teljesítményű, parancsfájlalapú adatátvitelt kínál az Azure Storage számára. Az AzCopy segítségével adatokat vihet át a Blob storage és az Azure Files. Az AzCopy legújabb verziójáról, az AzCopy programmal kapcsolatos további tudnivalókért olvassa el az AzCopy – Első lépések az [AzCopy programot című témakört.](../common/storage-use-azcopy-v10.md) Az AzCopy v10 blobtármal való használatáról az [Adatok átvitele az AzCopy és a Blob storage használatával (Átvitel az AzCopy és a Blob storage) témakörben](../common/storage-use-azcopy-blobs.md)olvashat.

A következő példa az AzCopy segítségével feltölt egy helyi fájlt egy blobba. Ne felejtse el helyettesíteni a mintaértékeket a saját értékeivel:

```powershell
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Távolítsa el az összes létrehozott adategységet. Az adategységek eltávolításának legegyszerűbb módja az erőforráscsoport törlése. Az erőforráscsoport törlésével a csoportban található összes erőforrást is törli. A következő példában az erőforráscsoport eltávolítása eltávolítja a tárfiókot és magát az erőforráscsoportot is.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban fájlokat vitt át egy helyi lemez és az Azure Blob Storage között. Ha bővebb információra van szüksége a Blob Storage és a PowerShell használatával kapcsolatban, lépjen tovább az Azure PowerShell és az Azure Storage használatáról szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Using Azure PowerShell with Azure Storage (Az Azure PowerShell és az Azure Storage együttes használata)](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>A Microsoft Azure PowerShell tárolási parancsmagjainak leírása

* [Tárolási PowerShell-parancsmagok](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.

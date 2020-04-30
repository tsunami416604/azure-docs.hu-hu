---
title: Rövid útmutató – blob létrehozása a PowerShell-lel
titleSuffix: Azure Storage
description: Ebben a rövid útmutatóban az Azure PowerShell használatával kezelheti az objektumtárat (blobtárat). Majd a PowerShell segítségével feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 03/31/2020
ms.author: tamram
ms.openlocfilehash: bca04317acf589e8bae46f086c6c79dfc82152a8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82176651"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>Rövid útmutató: Blobok feltöltése, letöltése és listázása a PowerShell-lel

Az Azure PowerShell-modullal létrehozhat és kezelhet Azure-erőforrásokat. Az Azure-erőforrások létrehozása és kezelése végrehajtható a PowerShell-parancsból vagy szkriptekkel. Ez az útmutató a fájloknak a helyi lemez és az Azure Blob Storage közötti, a PowerShell-lel történő átvitelét ismerteti.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Az Azure Storage eléréséhez Azure-előfizetésre lesz szüksége. Ha még nem rendelkezik előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ehhez a rövid útmutatóhoz a Azure PowerShell modul az 0,7-es vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: `Get-InstalledModule -Name Az -AllVersions | select Name,Version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket.

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

Fájlok blokkblobba való feltöltéséhez szerezze be a tároló hivatkozását, majd a blokkblob hivatkozását az adott tárolóban. A blob-hivatkozás után az adatok a [set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent)használatával tölthetők fel. Ez az eljárás létrehozza a blobot, ha az még nem létezett, vagy felülírja azt, ha már igen.

Az alábbi példákban feltöltheti a *kép001. jpg* és a *Image002. png* fájlokat a helyi lemez *D:\\_TestImages* mappájából a létrehozott tárolóba.

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

Szerezze be a tárolóban lévő Blobok listáját a [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob)használatával. A példában csak a feltöltött blobok neve látható.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Blobok letöltése

Töltse le a blobokat a helyi lemezre. Minden letölteni kívánt blob esetében állítsa be a [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent) nevet, és hívja le a blobot.

Ez a példa a blobokat a helyi lemezen lévő *D:\\_TestImages \downloads* tölti le. 

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

A AzCopy parancssori segédprogram nagy teljesítményű, parancsfájl-továbbítást biztosít az Azure Storage-hoz. A AzCopy használatával adatok vihetők át a blob Storage-ba és a Azure Filesba. A AzCopy legújabb verziójával kapcsolatos további információkért lásd: AzCopy v10, a [AzCopy első lépései](../common/storage-use-azcopy-v10.md). Az AzCopy v10 blob Storage-mel történő használatáról további információt az [adatok átvitele a AzCopy és a blob Storage](../common/storage-use-azcopy-blobs.md)szolgáltatással című témakörben talál.

A következő példa a AzCopy-t használja egy helyi fájl blobba való feltöltéséhez. Ne felejtse el lecserélni a minták értékeit a saját értékeire:

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

Ebben a rövid útmutatóban a fájlokat a helyi fájlrendszer és az Azure Blob Storage között helyezi át. Ha többet szeretne megtudni a blob Storage PowerShell használatával történő használatáról, tekintse meg Azure PowerShell mintákat a blob Storage-hoz.

> [!div class="nextstepaction"]
> [Azure PowerShell-minták az Azure Blob Storage-hoz](storage-samples-blobs-powershell.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>A Microsoft Azure PowerShell tárolási parancsmagjainak leírása

* [Tárolási PowerShell-parancsmagok](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.

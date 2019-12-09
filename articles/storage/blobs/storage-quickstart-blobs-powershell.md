---
title: Rövid útmutató – blob létrehozása a PowerShell-lel
titleSuffix: Azure Storage
description: Ebben a rövid útmutatóban az Azure PowerShell használatával kezelheti az objektumtárat (blobtárat). Majd a PowerShell segítségével feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c5d32b91043d310e51143357ad51631463f7e991
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892498"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>Rövid útmutató: Blobok feltöltése, letöltése és listázása a PowerShell-lel

Az Azure PowerShell-modullal létrehozhat és kezelhet Azure-erőforrásokat. Az Azure-erőforrások létrehozása és kezelése végrehajtható a PowerShell-parancsból vagy szkriptekkel. Ez az útmutató a fájloknak a helyi lemez és az Azure Blob Storage közötti, a PowerShell-lel történő átvitelét ismerteti.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

Az Azure Storage eléréséhez Azure-előfizetésre lesz szüksége. Ha még nem rendelkezik előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ehhez a rövid útmutatóhoz a Azure PowerShell modul az 0,7-es vagy újabb verziója szükséges. A verzió azonosításához futtassa a következőt: `Get-InstalledModule -Name Az -AllVersions | select Name,Version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket.

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Tároló létrehozása

A blobok minden esetben egy tárolóba lesznek feltöltve. A blobok csoportjait hasonló módon rendszerezheti, mint a fájlokat a számítógép mappáiban.

Állítsa be a tároló nevét, majd hozza létre a tárolót a [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer)használatával. A fájlokhoz való nyilvános hozzáférés engedélyezéséhez állítsa `blob` értékűre az engedélyeket. A tároló neve ebben a példában: *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. Az IaaS típusú virtuális gépek biztonsági mentéséhez használt VHD-fájlok lapblobok. A hozzáfűző blobok a naplózáshoz használhatók, például amikor egy fájlba szeretne írni, majd folyamatosan újabb információkat szeretne hozzáadni. A blobtárolókban tárolt fájlok a legtöbb esetben blokkblobok. 

Fájlok blokkblobba való feltöltéséhez szerezze be a tároló hivatkozását, majd a blokkblob hivatkozását az adott tárolóban. A blob-hivatkozás után az adatok a [set-AzStorageBlobContent](/powershell/module/az.storage/set-AzStorageblobcontent)használatával tölthetők fel. Ez az eljárás létrehozza a blobot, ha az még nem létezett, vagy felülírja azt, ha már igen.

Az alábbi példák az *Image001.jpg* és *Image002.png* képet töltik fel a helyi lemez *D:\\_TestImages* mappájából a létrehozott tárolóba.

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

Szerezze be a tárolóban lévő Blobok listáját a [Get-AzStorageBlob](/powershell/module/az.storage/get-AzStorageblob)használatával. A példában csak a feltöltött blobok neve látható.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Blobok letöltése

Töltse le a blobokat a helyi lemezre. Minden letölteni kívánt blob esetében állítsa be a [Get-AzStorageBlobContent](/powershell/module/az.storage/get-AzStorageblobcontent) nevet, és hívja le a blobot.

A példában a blobok a helyi lemez *D:\\_TestImages\Downloads* mappájába lesznek letöltve. 

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

Az [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) segédprogram az Azure Storage esetében egy további lehetőséget kínál az adatok nagy teljesítményű, parancsfájlalapú átvitelére. Az AzCopy segítségével blob, fájl és tábla típusú tárolókból és tárolókba vihet át adatokat.

Gyors példaként íme a *myfile.txt* nevű fájlnak a *mystoragecontainer* tárolóba való feltöltésére szolgáló, egy PowerShell-ablakból futtatható AzCopy-parancs.

```powershell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Távolítsa el az összes létrehozott adategységet. Az adategységek eltávolításának legegyszerűbb módja az erőforráscsoport törlése. Az erőforráscsoport törlésével a csoportban található összes erőforrást is törli. A következő példában az erőforráscsoport eltávolítása eltávolítja a tárfiókot és magát az erőforráscsoportot is.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban fájlokat vitt át egy helyi lemez és az Azure Blob Storage között. Ha bővebb információra van szüksége a Blob Storage és a PowerShell használatával kapcsolatban, lépjen tovább az Azure PowerShell és az Azure Storage használatáról szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Using Azure PowerShell with Azure Storage (Az Azure PowerShell és az Azure Storage együttes használata)](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>A Microsoft Azure PowerShell tárolási parancsmagjainak leírása

* [Tárolási PowerShell-parancsmagok](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.

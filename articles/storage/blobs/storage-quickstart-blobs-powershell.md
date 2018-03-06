---
title: "Azure rövid útmutató – Blobok feltöltése, letöltése és listázása az Azure Storage-ban az Azure PowerShell használatával | Microsoft Docs"
description: "Ebben a rövid útmutatóban egy tárfiókot és egy tárolót hoz létre az Azure PowerShell használatával. Majd a PowerShell segítségével feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat."
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: tamram
ms.openlocfilehash: 1ffed1eea8d159ccd05331d741624d8bc9e84b50
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-azure-powershell"></a>Rövid útmutató: blobok feltöltése, letöltése és listázása az Azure PowerShell használatával

Az Azure PowerShell-modul az Azure-erőforrások PowerShell-parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató a fájlok a helyi lemez és az Azure Blob Storage közötti, a PowerShell-lel történő átvitelét mutatja be részletesen.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Ehhez a rövid útmutatóhoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Tároló létrehozása

A blobok minden esetben egy tárolóba lesznek feltöltve. A blobok csoportjait hasonló módon rendszerezheti, mint a fájlokat a számítógép mappáiban.

Adja meg a tároló nevét, majd hozza létre a [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer) paranccsal. A „blob” engedélyeket úgy konfigurálja, hogy engedélyezzék a nyilvános hozzáférést a fájlokhoz. A tároló neve ebben a példában: *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. Az IaaS típusú virtuális gépek biztonsági mentéséhez használt VHD-fájlok lapblobok. A hozzáfűző blobok a naplózáshoz használhatók, például amikor egy fájlba szeretne írni, majd folyamatosan újabb információkat szeretne hozzáadni. A blobtárolókban tárolt fájlok a legtöbb esetben blokkblobok. 

Fájlok blokkblobba való feltöltéséhez szerezze be a tároló hivatkozását, majd a blokkblob hivatkozását az adott tárolóban. Ha megszerezte a blobhivatkozást, adatokat a [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent) használatával tölthet fel rá. Ez az eljárás létrehozza a blobot, ha az még nem létezett, és felülírja, ha már igen.

Az alábbi példák az Image001.jpg és Image002.png képet töltik fel a helyi lemez D:\\_TestImages mappájából a létrehozott tárolóba.

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

Mielőtt továbblépne, töltsön fel annyi fájlt, amennyit csak szeretne.

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A tárolóban lévő blobok listáját a [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) használatával kérheti le. A példában csak a feltöltött blobok neve látható.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Blobok letöltése

Töltse le a blobokat a helyi lemezre. Mindegyik letölteni kívánt blob esetében adja meg a nevet, és hívja meg a [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) metódust a blob letöltéséhez.

A példában a blobok a helyi lemez D:\\_TestImages\Downloads mappájába lesznek letöltve. 

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

## <a name="data-transfer-with-azcopy"></a>Adatátvitel az AzCopy használatával

Az [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) segédprogram az Azure Storage esetében egy további lehetőséget kínál az adatok nagy teljesítményű, parancsfájlalapú átvitelére. Az AzCopy segítségével blob, fájl és tábla típusú tárolókból és tárolókba vihet át adatokat.

Egy példaként íme a *myfile.txt* nevű fájl a *mystoragecontainer* tárolóba való feltöltésére szolgáló, egy PowerShell-ablakból futtatható AzCopy-parancs.

```PowerShell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Távolítsa el az összes létrehozott adategységet. Ennek a legegyszerűbb módja az erőforráscsoport törlése. Így törli a csoportban lévő összes erőforrást is. Ebben az esetben eltávolítja a tárfiókot és magát az erőforráscsoportot is.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között. Ha bővebb információra van szüksége a Blob Storage használatával kapcsolatban, lépjen tovább a Blob Storage használati útmutatójára.

> [!div class="nextstepaction"]
> [Blob Storage-műveletek használati útmutatója](storage-how-to-use-blobs-powershell.md)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>A Microsoft Azure PowerShell tárolási parancsmagjainak leírása
* [Tárolási PowerShell-parancsmagok](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
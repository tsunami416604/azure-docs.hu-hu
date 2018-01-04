---
title: "Műveletek végzése Azure Blob storage (object storage) a PowerShell használatával |} Microsoft Docs"
description: "Az oktatóanyag - műveleteket hajtson végre a Azure Blob storage (object storage) a PowerShell használatával"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: 170c3091efc90f640792682377ed10e2eab0cab3
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="perform-azure-blob-storage-operations-with-azure-powershell"></a>Azure Blob storage műveleteket az Azure PowerShell

Az Azure Blob Storage szolgáltatás strukturálatlan adatok, például szövegek vagy bináris adatok nagy mennyiségben történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolaton keresztül a világon bárhonnan elérhetők. Ez a cikk ismerteti az Azure Blob storage feltöltését, letöltése és törlése a blobok például alapvető műveleteket. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Tároló létrehozása 
> * Blobok feltöltése
> * A tárolóban lévő blobok listázása 
> * Blobok letöltése
> * Blobok másolása
> * Blobok törlése
> * Megtekintése, valamint egy blob metaadatok és a tulajdonságok beállítása
> * A megosztott hozzáférési aláírásokkal biztonság kezelése

Az oktatóanyaghoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Tároló létrehozása

A blobok minden esetben egy tárolóba lesznek feltöltve. Tárolók hasonlóak a könyvtárak a számítógépen, hogy lehetővé teszi a csoportokba rendezheti a tárolókban lévő blobokat, mint a fájlok és mappák rendezheti a számítógépen. A tárfiók lehet korlátlan számú tárolót tárolhat; csak a tárfiókban lévő tárterület mérete korlátozza (legfeljebb 500TB). 

Amikor létrehoz egy tárolót, beállíthatja a hozzáférési szintet, amely segít a meghatározása, hogy ki férhet hozzá az adott tárolóban lévő blobok. Például el saját (hozzáférési szint = `Off`), azaz senki sem érhetik el azokat a közös hozzáférésű jogosultságkód vagy a tárfiók hozzáférési kulcsainak nélkül. Ha a hozzáférési szint nem ad meg, ha a tároló létrehozása, alapértelmezés szerint személyes.

Érdemes lehet képek nyilvánosan hozzáférhető legyen a tárolóban. Például a webhelyen megjelenő képek tárolni szeretné, ha azt szeretné nyilvánosak legyenek. Ebben az esetben beállíthatja a tároló hozzáférési szint beállítása azokhoz a `blob`, és egy adott tárolóban lévő blob mutató URL-címet birtokában bárki hozzáférhet a blob.

Hozza létre a tárolót, állítsa be a tároló nevét, majd hozza létre a tárolót, a "blobra" engedélyeinek beállítása. A tárolónévnek betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és a kötőjel (-) karaktert tartalmazhat. Blobok és a tárolók elnevezésére vonatkozó további szabályok talál [elnevezési és hivatkozó tárolók, Blobok és metaadatok](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Az új tároló [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer). A hozzáférési szint beállítása nyilvános. Ebben a példában a tároló neve *howtoblobs*.

```powershell
$containerName = "howtoblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-into-a-container"></a>Töltse fel blobok egy tárolóba

Az Azure Blob Storage támogatja a blokkblobokat, hozzáfűző blobokat és lapblobokat.  Az IaaS típusú virtuális gépek biztonsági mentéséhez használt VHD-fájlok lapblobok. A hozzáfűző blobok a naplózáshoz használhatók, például amikor egy fájlba szeretne írni, majd folyamatosan újabb információkat szeretne hozzáadni. A blobtárolókban tárolt fájlok a legtöbb esetben blokkblobok. 

Fájlok blokkblobba való feltöltéséhez szerezze be a tároló hivatkozását, majd a blokkblob hivatkozását az adott tárolóban. Ha megszerezte a blobhivatkozást, adatokat a [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent) használatával tölthet fel rá. Ez a művelet a blob hoz létre, ha nem létezik, vagy felülírja, ha már létezik.

A következő bemutatja, hogyan tölthetők fel blobok egy tárolóba. Első lépésként állítsa be a változókat, amelyek a helyi számítógépen, ahol a fájlok találhatók, és állítsa be a változót a feltölteni kívánt fájl nevét a könyvtárba. Ez akkor hasznos, ha szeretné ismételten hajtsa végre a műveletet. Töltse fel néhány fájlt, így több bejegyzés is láthatják, a tárolóban lévő blobok listázása során.

Az alábbi példák kép001.jpg és Image002.png betöltése D:\\_TestImages meg az imént létrehozott tárolót a helyi lemezen.

```powershell
$localFileDirectory = "D:\_TestImages\"

$blobName = "Image001.jpg"
$localFile = $localFileDirectory + $blobName
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx 
```

Töltsön fel másik fájlt. 

```powershell
$blobName = "Image002.png"
$localFile = $localFileDirectory + $blobName 
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx
```

Mielőtt továbblépne, töltsön fel annyi fájlt, amennyit csak szeretne.

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A blobot, amely a tárolót használja listáját [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) , majd válassza a blob neve megjelenik.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Blobok letöltése

Töltse le a blobokat a helyi lemezre. Első lépésként állítsa be a helyi mappát, amely letölti a blobokat szeretné változó. Majd minden egyes BLOB le kell tölteni, állítsa be a nevet és hívás [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) letölteni a blob.

Ebben a példában a blobok másolja D:\\_TestImages\Downloads a helyi lemezen. 

```powershell
# local directory to which to download the files
# example "D:\_TestImages\Downloads\"
$localTargetDirectory = "D:\_TestImages\Downloads\"

# download the first blob
$blobName = "Image001.jpg"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 

# download another blob
$blobName = "Image002.png"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 
```

## <a name="copy-blobs"></a>Blobok másolása

Számos szempontot figyelembe kell venni a bináris objektumok másolásakor. Ön sikerült kell csak másolja a blob egy új nevet ugyanazon a helyen. Akkor lehetett kell másol a blob külön tárfiókot. Akkor lehetett kell egy nagy blobot másol (például egy VHD-fájlt) egy másik tárolási fiókot. Ezek mindegyike hajtja végre eltérő.

### <a name="simple-blob-copy"></a>Egyszerű blob másolása

A blobok egyik példányát be egy új blob másolásával tehet egy tárolót. Ebben a példában másolja azt egy eltérő nevű azonos tárolóba, de ahogy egyszerűen létrehozhat egy másik tárolóban és másolhatja azt van. A példa bemutatja, hogyan használható [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) a blob másolása. Akkor adja meg a forrás és cél blob és tároló nevét is.

```powershell
$blobName = "Image002.png"
$newBlobName = "CopyOf_" + $blobName 

Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName `
  -DestBlob $newBlobName `
  -Context $ctx 

# get list of blobs to verify the new one has been created
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

### <a name="copy-a-blob-to-a-different-storage-account"></a>A blob másolása során eltérő tárfiók 

Érdemes lehet külön tárfiókot a blob másolása. Mindezt egy példa egy másolja a VHD-fájl, amely ahhoz, hogy készítsen biztonsági másolatot készít a virtuális gépek közül egy másik tárolási fiókot. 

Állítson be egy másik tárolási fiókot, beolvasni a környezetben, egy tárolóját, hogy a storage-fiók beállítása és a másolás végrehajtásához. Ez a kijelző, a parancsfájl majdnem megegyezik a parancsfájl fent a második tárfiók helyett az első kivételével.

```powershell
#create new storage account, get context 
$storageAccount2Name = "blobstutorialtestcopy"
$storageAccount2 = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccount2Name `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx2 = $storageAccount2.Context

#create a container in the second storage account 
$containerName2 = "tutorialblobscopied"
New-AzureStorageContainer -Name $containerName2 `
  -Context $ctx2 `
  -Permission blob

# copy one of the blobs from the first storage account to the second one 
# specify the source and destination container, blob name, and context
Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -SrcContext $ctx `
  -DestContext $ctx2 

# list the blobs in the container in the second storage account
# to verify that the newly copied blob is there
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

### <a name="copying-very-large-blobs-asynchronously"></a>Nagyon nagy blobok aszinkron módon másolása

Ha nagyon nagy blob, amely több GB másolni, kihasználhatja az aszinkron példány elindításával, és majd hamarosan vissza és az állapot ellenőrzése, amíg befejeződik. Ezzel a módszerrel, nem kötődnek hajt végre a másolási művelet közben.

Ha egy tárfiókon belül másolja át, a másolási nagyon gyorsan. Két tárfiókok ugyanabban a régióban közötti másolása, a-példány lefut. Azonban ha a forrás és cél külön régióban, akkor átveheti a Másolás parancsot, távolság és a fájl méretétől függően több órát. 

A parancsfájl az utolsó másolás példában azonos változókkal. A különbség az, hogy erre a másolat állapotának rögzíti, és lekérdezi az ismételten minden 10 másodpercet, amíg befejeződik. Előfordulhat, hogy a nagyon nagy blob feltöltése a tárfiókhoz, ez több iterációs befejezéséhez érvénybe megjelenítéséhez.

Használjon [Get-AzureStorageBlobCopyState](/powershell/module/azure.storage/get-azurestorageblobcopystate) a másolat állapotának lekérdezése. 

```powershell
# start the blob copy, and assign the result to $blobResult
$blobResult = Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -Context $ctx `
  -DestContext $ctx2

# get the status of the blob copy
$status = $blobResult | Get-AzureStorageBlobCopyState 
# show the value of the status
$status 

# loop until it finishes copying, pausing for 10 seconds after each iteration
# it is finished when the status is no longer 'Pending'
while ($status.Status -eq "Pending") {
    $status = $blobResult | Get-AzureStorageBlobCopyState 
    $status
    Start-Sleep 10
}

# get the list of blobs to see the new file in the second storage account 
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

## <a name="delete-blobs"></a>Blobok törlése

A tárfiók a BLOB eltávolításához használja [Remove-AzureStorageBlob](/powershell/module/azure.storage/Remove-AzureStorageBlob). 

```powershell
$blobName = "Image001.jpg"
Remove-AzureStorageBlob -Blob $blobName -Container $containerName -Context $ctx

# get list of blobs to see the one you deleted is gone
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

## <a name="read-and-write-a-blobs-properties-and-metadata"></a>Olvasási és írási egy blob tulajdonságok és metaadatok

A hozzáférés a tulajdonságok és metódusok visszaadásakor **IListBlobItem**, típussá (vagy átalakíthatja a) a egy **CloudBlockBlob**, **CloudPageBlob**, vagy **CloudBlobDirectory** objektum. Ha a típus ismereten, akkor a típusellenőrzés segítségével határozza meg, hogy milyen típussá kell átalakítani. A következő kód bemutatja, hogyan kérhető le és küldhető a korábban feltöltött blobon tulajdonságairól [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) és az eredmény CloudBlockBlob objektumhoz.

```powershell
# blob properties
# get a reference to the blob you uploaded -- this is an **IListBlobItem** -- then
# convert it to a CloudBlockBlob, giving you access to the properties 
# and methods of the blob 
$blobName = "Image001.jpg"
$blob = Get-AzureStorageBlob -Context $ctx `
   -Container $containerName `
   -Blob $blobName 

#convert $blob to a CloudBlockBlob object
$cloudBlockBlob = [Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob] $blob.ICloudBlob

# you can view the properties by typing in $cloudBlockBlob 
#   and hitting the period key; this brings up IntelliSense,
#   which shows you all of the available properties
Write-Host "blob type = " $cloudBlockBlob.BlobType
Write-Host "blob name = " $cloudBlockBlob.Name
Write-Host "blob uri = " $cloudBLockBlob.Uri
```

A Rendszertulajdonságok FetchAttributes meghívásával feltöltéséhez, majd ezek a tulajdonságok megtekintéséhez. A Tulajdonságok írható, és azok értékeit módosíthatja. Ez a példa bemutatja, hogyan módosíthatja a tartalomtípus, más néven a MIME-típus.

```powershell
# populate the system properties
$cloudBlockBlob.FetchAttributes()

# view some of the system properties
# contentType is commonly referred to as MIME type
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
Write-Host "size = " $cloudBlockBlob.Properties.Length 

# change the content type to image/png
$contentType = "image/jpg"
$cloudBlockBlob.Properties.ContentType = $contentType 
$cloudBlockBlob.SetProperties() 

# get the system properties again to show that the content type has changed
$cloudBlockBlob.FetchAttributes()
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
```

Minden egyes blob rendelkezik saját metaadatait, amely lehet. Például előfordulhat, hogy tárolási feltöltése a blob, vagy a dátum/idő, először feltöltöttnek felhasználó nevét. A metaadatok kulcs-érték párokból áll. Ez módosítható, az alábbiak szerint PowerShell-lel.

```powershell
# "filename" is the key, "original file name" is the value
$cloudBlockBlob.Metadata["filename"] = "original file name"

# "owner" is the key, "RobinS" is the value
$cloudBlockBlob.Metadata["owner"] = "RobinS"

# save the metadata and then display it
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata

# clear the metadata, save it, and show it
$cloudBlockBlob.Metadata.Clear()
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata
```

## <a name="managing-security-for-blobs"></a>A blobok biztonságának kezelése 

Alapértelmezés szerint Azure Storage biztosítja az adatok védelmét korlátozza a hozzáférést a fiók tulajdonosára, aki a fiók tárelérési kulcsok rendelkezik. Ha a tárfiókjában lévő blobadatokat kell megosztania, fontos, hogy azt a fiók tárelérési kulcsai biztonságának fenyegetése nélkül tegye. Ehhez használhatja egy megosztott hozzáférési aláírást URL-CÍMÉT, vagyis az eszköz, amely tartalmazza a lekérdezés-paraméterek és egy biztonsági jogkivonatot, amely lehetővé teszi, hogy egy adott időtartamig engedély valamilyen konkrét szintje URL-CÍMÉT. Érdemes lehet például a olvasási hozzáférésének titkos blob 5 percig, valaki megtekintheti. 

### <a name="set-the-access-level-of-the-container-and-its-blobs-to-private"></a>Magán a tároló és a benne található blobokat hozzáférési szint beállítása

Első lépésként állítsa be a hozzáférési szint a tárolót, hogy a `Off`, amely jelöli meg, hogy nincs-e a közös hozzáférésű jogosultságkód és a fiókkulcsot nélkül nem lehet hozzáférni. Használja a [Set-AzureStorageContainerAcl](/powershell/module/azure.storage/Set-AzureStorageContainerAcl).

```powershell
Set-AzureStorageContainerAcl -Name $containerName -Context $ctx -Permission Off 
```

### <a name="test-private-access"></a>Magánfelhő-hozzáférés tesztelése

Győződjön meg arról, hogy rendelkezik-e a tárolóban lévő blobok nem lehet hozzáférni, az URL-CÍMEK valamelyikét a blobok nélkül egy közös hozzáférésű jogosultságkódot létrehozni, és a blob megtekintésekor. Az URL-címet fog a HTTPS protokoll használatával, a következő formátumban:

    `https://storageaccountname.blob.core.windows.net/containername/blobname`

Ez bemutatja, hogyan hozhat létre a blob URL-CÍMÉT.

```powershell
$blobUrl = "https://" `
  $storageAccountName ".blob.core.windows.net/" + `
  $containerName +  "/" $blobName

Write-Host "Blob URL = " $blobUrl 
```

Másolja a blob URL-címet, majd illessze be a saját böngészőablakban--meg fog jelenni engedélyezési hiba történt, mert a blob személyes, és nem tartozik egy közös hozzáférésű jogosultságkódot. 

### <a name="create-the-sas-uri"></a>A SAS URI-t létrehozni

Hozzon létre egy SAS URI-t – a hivatkozásra kattintva a blob, beleértve a lekérdezés-paraméterek és a biztonsági jogkivonatot, amely a SAS alkotják azt. Ezt az URI illessze be egy privát böngészés ablak--meg fog jelenni a lemezképet. 

Először létre kell hoznia a kezdő dátum/idő és a lejárati dátum és idő, a hozzáférés. Egy 2 perces ablak használja. 

```powershell
# set the start time to the current date/time 
# set the end time to 2 minutes in the future
$StartTime = Get-Date
$EndTime = $StartTime.AddMinutes(2.0)
```

A SAS URI-JÁNAK használatával hozzon létre [New-AzureStorageBlobSASToken](/powershell/module/azure.storage/new-azurestorageblobsastoken). A tároló neve, a blob neve, a tárfiók környezetét, a adható meg (Ez esetben olvasási, írási és törlési), a engedélyekkel kell, és a hozzáférés a kezdő és záró idő. 

```powershell
$SASURI = New-AzureStorageBlobSASToken -Container $containerName `
    -Blob $blobName `
    -Context $ctx `
    -Permission "rwd" `
    -StartTime $StartTime `
    -ExpiryTime $EndTime `
    -FullUri

Write-Host "URL with SAS = " $SASURI
```

A SAS URI másolása és egy személyes böngészőablakban; a kép az meg fog jelenni.

Várja meg, amíg az URL-cím (ebben a példában 2 percet) lejár, majd illessze be az URL-címet egy másik InPrivate-böngészőablakot a. Ennek az időnek engedélyezési hiba elérhetővé válik, és azt nem jelenik a képet, mert lejárt a SAS URI.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Távolítsa el az összes eszköz hozott létre. Ehhez az szükséges, távolítsa el az erőforráscsoportot, amely a csoporton belül található összes erőforrást is törli. Ebben az esetben azt eltávolítja az összes a storage-fiókok és az erőforráscsoport magát.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóprogramban megismerte például hogyan az alapvető Blob tárolók kezelése:

> [!div class="checklist"]
> * Tároló létrehozása 
> * Blobok feltöltése
> * A tárolóban lévő blobok listázása 
> * Blobok letöltése
> * Blobok másolása
> * Blobok törlése
> * Olvasási és írási egy blob metaadatait és tulajdonságai
> * A megosztott hozzáférési aláírásokkal biztonság kezelése

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>A Microsoft Azure PowerShell tárolási parancsmagok
* [Tárolási PowerShell-parancsmagok](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.

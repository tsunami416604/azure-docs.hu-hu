---
title: Rövid útmutató az Azure-fájlmegosztásoknak az Azure PowerShell-lel történő felügyeletéhez
description: Ebből a rövid útmutatóból megtudhatja, hogyan felügyelheti az Azure-fájlmegosztásokat az Azure PowerShell-lel.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c419c2127b1c5fe3aaa60c6e828ff0c5a6676c07
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77598544"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Rövid útmutató: Azure-fájlmegosztások létrehozása és felügyelete az Azure PowerShell-lel 
Ez az útmutató az [Azure-fájlmegosztások](storage-files-introduction.md) PowerShell-lel való használatának alapvető lépéseit mutatja be. Az Azure-fájlmegosztások nem különböznek más fájlmegosztásoktól, a tárolásuk azonban a felhőben történik, és az Azure platform nyújt számukra támogatást. Az Azure-fájlmegosztások támogatják az iparági szabvány SMB protokollt, és lehetővé teszik a több gép, alkalmazás és példány közötti fájlmegosztást. 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha szeretné telepíteni és használni a PowerShell helyileg, ez az útmutató megköveteli az Azure PowerShell-modul Az 0.7-es vagy újabb verzióját. A jelenleg futtatott Azure PowerShell-modul verziójának kiderítéséhez futtassa a következő parancsot: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor a `Login-AzAccount` parancsot is futtatnia kell az Azure-fiókba való bejelentkezéshez.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ha még nem rendelkezik Egy Azure-erőforráscsoport, hozhat létre egy újat a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag. 

A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az USA nyugati régiójában:

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

## <a name="create-a-storage-account"></a>Create a storage account
A tárfiók egy megosztott tárkészlet, amely segítségével üzembe helyezheti az Azure-fájlmegosztásokat. Egy tárfiók korlátlan számú megosztást tartalmazhat, egy megosztás pedig korlátlan számú fájl tárolására használható, egészen a tárfiók kapacitásának korlátjáig. Ez a példa létrehoz egy általános célú 2-es verziójú (GPv2 tárfiók), amely a merevlemez-meghajtók (HDD) rotációs adathordozókon szabványos Azure-fájlmegosztásokat vagy más tárolási erőforrásokat, például blobokat vagy várólistákat tárolhat. Az Azure Files támogatja a prémium szintű ssd-meghajtókat is; Prémium szintű Azure-fájlmegosztások hozhatók létre a FileStorage tárfiókokban.

Ez a példa létrehoz egy tárfiókot a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancsmag használatával. A tárfiók neve *mystorageaccount\<véletlen szám>,* és a tárfiókra mutató hivatkozás a **$storageAcct**változóban tárolódik. A tárfiókok névnek egyedinek kell lenniük, ezért a `Get-Random` paranccsal fűzzön számot a névhez. 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_ZRS `
    -EnableLargeFileShare
```

> [!Note]  
> Az 5 TiB-nél nagyobb (részvényenként legfeljebb 100 TiB-ig) nagyobb részvények csak helyileg redundáns (LRS) és zónaredundáns (ZRS) tárfiókokban érhetők el. Georedundáns (GRS) vagy geozónaredundáns (GZRS) tárfiók `-EnableLargeFileShare` létrehozásához távolítsa el a paramétert.

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Most létrehozhatja az első Azure-fájlmegosztását. A [New-AzRmStorageShare](/powershell/module/az.storage/New-AzRmStorageShare) parancsmag használatával fájlmegosztást hozhat létre. Ez a példa egy `myshare` nevű fájlmegosztást hoz létre.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -QuotaGiB 1024 | Out-Null
```

A fájlmegosztások neve csak kisbetűket, számokat és kötőjeleket tartalmazhat, de nem kezdődhet kötőjellel. A fájlmegosztások és fájlok elnevezésére vonatkozó információkért lásd: [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása).

## <a name="use-your-azure-file-share"></a>Az Azure-fájlmegosztás használata
Az Azure Files két módszert biztosít a fájloknak és mappáknak az Azure-fájlmegosztásban való használatához: az iparági szabvány [Server Message Block (SMB) protokollt](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) és a [Fájl REST protokollt](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

A fájlmegosztás SMB-vel való csatlakoztatásához tekintse meg a használt operációs rendszernek megfelelő dokumentumot az alábbiak közül:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [Macos](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Azure-fájlmegosztások használata a Fájl REST protokollal 
Lehetséges, hogy a fájl REST protokoll közvetlenül (azaz a handcrafting REST HTTP-hívások saját maga), de a leggyakoribb módja a File REST protokoll használata az Azure PowerShell modul, az [Azure CLI,](storage-how-to-use-files-cli.md)vagy egy Azure Storage SDK, amelyek mindegyike egy szép burkoló körül a File REST protokoll a parancsfájl-készítési/programozási nyelv az Ön által választott.  

Az Azure-fájlmegosztást a legtöbb esetben az SMB protokollon keresztül fogja használni, mivel ez lehetővé teszi a mások által is vélhetően használt meglévő alkalmazások és eszközök használatát. A Fájl REST API használata azonban számos előnnyel jár az SMB-vel szemben, például a következő esetekben:

- A fájlmegosztást a PowerShell Cloud Shellben böngészi (amely nem tud fájlmegosztásokat csatlakoztatni az SMB-n keresztül).
- Ki szeretné használni a kiszolgáló nélküli erőforrások, például az [Azure Functions](../../azure-functions/functions-overview.md) előnyeit. 
- Olyan értéknövelő szolgáltatást hoz létre, amely számos Azure-fájlmegosztással kommunikál, például biztonsági mentést vagy vírusvédelmi vizsgálatokat hajt végre.

Az alábbi példák bemutatják, hogyan használhatja az Azure PowerShell-modult az Azure-fájlmegosztás a File REST protokollsegítségével. A `-Context` paraméter a tárfiók kulcsának lekéréséhez szolgál a fájlmegosztással szembeni jelzett műveletek végrehajtásához. A tárfiók kulcsának lekéréséhez rbac `Owner` szerepkört kell rendelkeznie a tárfiókban.

#### <a name="create-directory"></a>Könyvtár létrehozása
Ha az Azure-fájlmegosztás gyökerében egy *myDirectory* nevű új könyvtárat szeretne létrehozni, használja a [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory) parancsmagát.

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Fájl feltöltése
A [Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) parancsmag használatával egy fájl feltöltésének bemutatásához először létre kell hoznunk egy fájlt a PowerShell Cloud Shell platformmeghajtóján a feltöltéshez. 

Ez a példa az aktuális dátumot és időt helyezi az ideiglenes meghajtón lévő új fájlba, majd feltölti a fájlt a fájlmegosztásra.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Ha helyileg futtatja a PowerShellt, helyettesítse be a `~/CloudDrive/` elérési utat a gép egy meglévő elérési útjával.

A fájl feltöltése után a [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) parancsmag segítségével ellenőrizheti, hogy a fájl feltöltve lett-e az Azure-fájlmegosztásra. 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```

#### <a name="download-a-file"></a>Fájl letöltése
A [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) parancsmag segítségével letöltheti a Cloud Shell karcolja meg a fájl egy példányát.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

A fájl letöltése után a `Get-ChildItem` parancsmaggal tekintheti meg, hogy a fájl le lett-e töltve a PowerShell Cloud Shell ideiglenes meghajtójára.

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

#### <a name="copy-files"></a>Fájlok másolása
Az egyik gyakori feladat a fájlok másolása az egyik fájlmegosztásról a másikra. Ennek a funkciónak a bemutatásához hozzon létre egy új megosztást, és másolja az új megosztásra feltöltött fájlt a [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) parancsmag használatával. 

```azurepowershell-interactive
$otherShareName = "myshare2"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $otherShareName `
    -QuotaGiB 1024 | Out-Null
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $otherShareName `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName $shareName `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName $otherShareName `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Ha most listázza az új megosztásban lévő fájlokat, meg kell jelennie a fájl másolatának.

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $otherShareName `
    -Path "myDirectory2" 
```

Bár `Start-AzStorageFileCopy` a parancsmag kényelmes az ad hoc fájl áthelyezéséhez az Azure fájlmegosztások, `robocopy` a `rsync` migrálásés a nagyobb adatmozgások, javasoljuk a Windows és a macOS és linux. `robocopy`és `rsync` az SMB segítségével hajtsa végre az adatmozgásokat a FileREST API helyett.

## <a name="create-and-manage-share-snapshots"></a>Megosztási pillanatképek létrehozása és felügyelete
Az Azure-fájlmegosztással végezhető egyik további hasznos feladat a megosztási pillanatképek létrehozása. A pillanatképek megőrzik az Azure-fájlmegosztások adott időpontban látható állapotát. A megosztási pillanatképek hasonlóak az esetleg már ismert operációsrendszer-technológiákhoz, például a következőkhöz:

- [Kötet árnyékmásolata szolgáltatás (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) Windows fájlrendszerekhez (például NTFS és ReFS).
- [Logikai kötetkezelő (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pillanatképek Linux rendszerekhez.
- [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) pillanatképek macOS. 

Share-pillanatképet hozhat létre egy megosztáshoz a `Snapshot` PowerShell-objektumon lévő metódus használatával egy fájlmegosztáshoz, amelyet a [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare) parancsmag lehív. 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name $shareName
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Böngészés a megosztási pillanatképekben
A pillanatkép referenciáját (`$snapshot`) a `Get-AzStorageFile` parancsmag `-Share` paraméterébe illesztve böngészhet a megosztási pillanatkép tartalmában.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Megosztási pillanatképek felsorolása
A következő paranccsal tekintheti meg a megosztáshoz készített pillanatképek listáját.

```azurepowershell-interactive
Get-AzStorageShare `
        -Context $storageAcct.Context | `
    Where-Object { $_.Name -eq $shareName -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Visszaállítás megosztási pillanatképből
A fájlokat a korábban már használt `Start-AzStorageFileCopy` paranccsal állíthatja vissza. A rövid útmutató során először töröljük a korábban feltöltött `SampleUpload.txt` fájlt, hogy visszaállíthassuk a pillanatképből.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName $shareName `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Megosztási pillanatkép törlése
A megosztáspillanatképet az [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) parancsmag segítségével törölheti, a `$snapshot` `-Share` paraméterre való hivatkozást tartalmazó változóval.

```azurepowershell-interactive
Remove-AzStorageShare `
    -Share $snapshot `
    -Confirm:$false `
    -Force
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha végzett, az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmaggal eltávolíthatja az erőforráscsoportot és az összes kapcsolódó erőforrást. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Egyesével is eltávolíthatja az erőforrásokat:

- A gyors útmutatóhoz létrehozott Azure-fájlmegosztások eltávolítása.

    ```azurepowershell-interactive
    Get-AzRmStorageShare -StorageAccount $storageAcct | Remove-AzRmStorageShare -Force
    ```

    > [!Note]  
    > Az Azure-fájlmegosztás törlése előtt törölnie kell az Azure-fájlmegosztások összes megosztási pillanatképét.

- Magának a tárfióknak az eltávolítása (ez implicit módon eltávolítja a létrehozott Azure-fájlmegosztásokat, valamint az esetlegesen létrehozott egyéb tárolási erőforrásokat, például az Azure Blob Storage-tárolókat).

    ```azurepowershell-interactive
    Remove-AzStorageAccount `
        -ResourceGroupName $storageAcct.ResourceGroupName `
        -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Mi az Azure Files?](storage-files-introduction.md)
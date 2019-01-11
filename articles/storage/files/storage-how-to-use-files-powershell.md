---
title: Rövid útmutató az Azure-fájlmegosztásoknak az Azure PowerShell-lel történő felügyeletéhez
description: Ebből a rövid útmutatóból megtudhatja, hogyan felügyelheti az Azure-fájlmegosztásokat az Azure PowerShell-lel.
services: storage
author: wmgries
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 64e041d61c628a54b7a55b11fceba0973f3f427b
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214963"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Gyors útmutató: Létrehozása és kezelése az Azure-fájlmegosztások az Azure PowerShell használatával 
Ez az útmutató az [Azure-fájlmegosztások](storage-files-introduction.md) PowerShell-lel való használatának alapvető lépéseit mutatja be. Az Azure-fájlmegosztások nem különböznek más fájlmegosztásoktól, a tárolásuk azonban a felhőben történik, és az Azure platform nyújt számukra támogatást. Az Azure-fájlmegosztások támogatják az iparági szabvány SMB protokollt, és lehetővé teszik a több gép, alkalmazás és példány közötti fájlmegosztást. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha szeretné helyi telepítése és használata a PowerShell, az útmutatóhoz az Azure PowerShell-modul Az 0,7 vagy újabb verziója. A jelenleg futtatott Azure PowerShell-modul verziójának kiderítéséhez futtassa a következő parancsot: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor a `Login-AzAccount` parancsot is futtatnia kell az Azure-fiókba való bejelentkezéshez.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ha még nem rendelkezik Azure-erőforráscsoporttal, létrehozhat egy újat a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmagot. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az USA keleti régiójában:

```azurepowershell-interactive
New-AzResourceGroup `
    -Name myResourceGroup `
    -Location EastUS
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
A tárfiókok az Azure-fájlmegosztások vagy más tárolási erőforrások, például blobok vagy üzenetsorok üzembe helyezéséhez használható tárolók közös készletei. Egy tárfiók korlátlan számú megosztást tartalmazhat, egy megosztás pedig korlátlan számú fájl tárolására használható, egészen a tárfiók kapacitásának korlátjáig.

Ez a példa létrehoz egy tárfiók tárfiókkulcsait a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancsmagot. A tárfiók neve *mystorageaccount<random number>*, a tárfiókra mutató hivatkozást pedig a **$storageAcct** változó tárolja. A tárfiókok névnek egyedinek kell lenniük, ezért a `Get-Random` paranccsal fűzzön számot a névhez. 

```azurepowershell-interactive 
$storageAcct = New-AzStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Most létrehozhatja az első Azure-fájlmegosztását. Hozhat létre fájlmegosztást a [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) parancsmagot. Ez a példa egy `myshare` nevű fájlmegosztást hoz létre.

```azurepowershell-interactive
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

A fájlmegosztások neve csak kisbetűket, számokat és kötőjeleket tartalmazhat, de nem kezdődhet kötőjellel. A fájlmegosztások és fájlok elnevezésére vonatkozó információkért lásd: [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása).

## <a name="use-your-azure-file-share"></a>Az Azure-fájlmegosztás használata
Az Azure Files két módszert biztosít a fájloknak és mappáknak az Azure-fájlmegosztásban való használatához: az iparági szabvány [Server Message Block (SMB) protokollt](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) és a [Fájl REST protokollt](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

A fájlmegosztás SMB-vel való csatlakoztatásához tekintse meg a használt operációs rendszernek megfelelő dokumentumot az alábbiak közül:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Azure-fájlmegosztások használata a Fájl REST protokollal 
Lehetséges dolgozhat közvetlenül a fájl REST protokoll közvetlenül (azaz handcrafting REST HTTP-hívásokat saját kezűleg), a leggyakoribb módja a fájl REST protokoll használatát az Azure PowerShell-modul használatára, de a [Azure CLI-vel](storage-how-to-use-files-cli.md), vagy az Azure-beli Storage SDK-t, amelyek mindegyike adja meg a a parancsfájl-kezelési és programozási nyelven, a fájl REST protokoll hasznos burkolója.  

Az Azure-fájlmegosztást a legtöbb esetben az SMB protokollon keresztül fogja használni, mivel ez lehetővé teszi a mások által is vélhetően használt meglévő alkalmazások és eszközök használatát. A Fájl REST API használata azonban számos előnnyel jár az SMB-vel szemben, például a következő esetekben:

- A fájlmegosztást a PowerShell Cloud Shellben böngészi (amely nem tud fájlmegosztásokat csatlakoztatni az SMB-n keresztül).
- Olyan ügyfélről kell szkriptet vagy alkalmazást futtatnia, amely nem tud SMB-megosztásokat csatlakoztatni, például a helyszíni ügyfelekről, amelyekhez nincs feloldva a 445-ös port.
- Ki szeretné használni a kiszolgáló nélküli erőforrások, például az [Azure Functions](../../azure-functions/functions-overview.md) előnyeit. 

A következő példák bemutatják, hogyan lehet az Azure PowerShell-modul segítségével módosíthatja az Azure-fájlmegosztást a fájl REST protokoll. 

#### <a name="create-directory"></a>Könyvtár létrehozása
Nevű új könyvtár létrehozása *myDirectory* az Azure-fájlmegosztás gyökérmappájában, használja a [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory) parancsmagot.

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Fájl feltöltése
A fájlok feltöltésének bemutatásához a [Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) parancsmagot, először létre kell hozni egy fájlt feltölteni a PowerShell Cloud Shell ideiglenes meghajtóján. 

Ez a példa az aktuális dátumot és időt helyezi az ideiglenes meghajtón lévő új fájlba, majd feltölti a fájlt a fájlmegosztásra.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Ha helyileg futtatja a PowerShellt, helyettesítse be a `C:\Users\ContainerAdministrator\CloudDrive\` elérési utat a gép egy meglévő elérési útjával.

A fájl feltöltése után is használhatja [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) parancsmaggal ellenőrizheti, győződjön meg arról, hogy a fájl feltöltése az Azure-fájlmegosztásba. 

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

#### <a name="download-a-file"></a>Fájl letöltése
Használhatja a [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) parancsmaggal letöltheti a Cloud Shell ideiglenes meghajtójára a feltöltött fájl másolatát.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item 
    `-Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

A fájl letöltése után a `Get-ChildItem` parancsmaggal tekintheti meg, hogy a fájl le lett-e töltve a PowerShell Cloud Shell ideiglenes meghajtójára.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

#### <a name="copy-files"></a>Fájlok másolása
A fájlok egy fájlmegosztásról egy másikra, vagy Azure Blob Storage-tárolóról/-tárolóra való másolása gyakori feladat. Ez a funkció bemutatása érdekében létrehozhat egy új megosztást és az imént feltöltött fájl keresztül az új megosztás használatával másolja a [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy) parancsmagot. 

```azurepowershell-interactive
New-AzStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Ha most listázza az új megosztásban lévő fájlokat, meg kell jelennie a fájl másolatának.

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

Bár a `Start-AzStorageFileCopy` parancsmag kényelmes megoldás az Azure-fájlmegosztások és az Azure Blob Storage-tárolók közötti alkalmi fájlmozgatásokhoz, a nagyobb léptékű áthelyezésekhez (az áthelyezett fájlok száma és mérete tekintetében) az AzCopy használata ajánlott. További információ: [AzCopy segédprogram Windows rendszeren](../common/storage-use-azcopy.md) és [AzCopy segédprogram Linux rendszeren](../common/storage-use-azcopy-linux.md). Az AzCopy parancsmagnak helyileg kell telepítve lennie – a Cloud Shellen nem érhető el. 

## <a name="create-and-manage-share-snapshots"></a>Megosztási pillanatképek létrehozása és felügyelete
Az Azure-fájlmegosztással végezhető egyik további hasznos feladat a megosztási pillanatképek létrehozása. A pillanatképek megőrzik az Azure-fájlmegosztások adott időpontban látható állapotát. A megosztási pillanatképek hasonlóak az esetleg már ismert operációsrendszer-technológiákhoz, például a következőkhöz:
- [Kötet árnyékmásolata szolgáltatás (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) Windows fájlrendszerekhez (például NTFS és ReFS)
- [Logikaikötet-kezelő (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pillanatképek Linux rendszerekhez
- [Apple fájlrendszer (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) pillanatképek macOS rendszerhez. 
 Létrehozhat egy megosztás megosztási pillanatképek használatával a `Snapshot` módszer a PowerShell-objektumot egy fájlmegosztást, amely kérhető le a [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare) parancsmagot. 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name "myshare"
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
Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Visszaállítás megosztási pillanatképből
A fájlokat a korábban már használt `Start-AzStorageFileCopy` paranccsal állíthatja vissza. A rövid útmutató során először töröljük a korábban feltöltött `SampleUpload.txt` fájlt, hogy visszaállíthassuk a pillanatképből.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"
 # Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Megosztási pillanatkép törlése
Megosztási pillanatképek használatával törölheti az [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) parancsmag tartalmazó változóval a `$snapshot` referenciáját a `-Share` paraméter.

```azurepowershell-interactive
Remove-AzStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Amikor elkészült, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmag segítségével távolítsa el az erőforráscsoportot és az összes kapcsolódó erőforrást. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Egyesével is eltávolíthatja az erőforrásokat:

- A gyors útmutatóhoz létrehozott Azure-fájlmegosztások eltávolítása.

    ```azurepowershell-interactive
    Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
        Remove-AzStorageShare -Context $storageAcct.Context -Name $_.Name
    }
    ```

- Magának a tárfióknak az eltávolítása (ez implicit módon eltávolítja a létrehozott Azure-fájlmegosztásokat, valamint az esetlegesen létrehozott egyéb tárolási erőforrásokat, például az Azure Blob Storage-tárolókat).

    ```azurepowershell-interactive
    Remove-AzStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Mi az Azure Files?](storage-files-introduction.md)

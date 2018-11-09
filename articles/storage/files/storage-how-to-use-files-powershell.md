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
ms.openlocfilehash: 119853df5b5234b65bdade890df1fecb72c326b7
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50157377"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Rövid útmutató: Azure-fájlmegosztások létrehozása és felügyelete az Azure PowerShell-lel 
Ez az útmutató az [Azure-fájlmegosztások](storage-files-introduction.md) PowerShell-lel való használatának alapvető lépéseit mutatja be. Az Azure-fájlmegosztások nem különböznek más fájlmegosztásoktól, a tárolásuk azonban a felhőben történik, és az Azure platform nyújt számukra támogatást. Az Azure-fájlmegosztások támogatják az iparági szabvány SMB protokollt, és lehetővé teszik a több gép, alkalmazás és példány közötti fájlmegosztást. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az útmutatóhoz az Azure PowerShell-modul 5.1.1-es vagy újabb verziójára lesz szükség. A jelenleg futtatott Azure PowerShell-modul verziójának kiderítéséhez futtassa a következő parancsot: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor a `Login-AzureRmAccount` parancsot is futtatnia kell az Azure-fiókba való bejelentkezéshez.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ha még nem rendelkezik Azure-erőforráscsoporttal, létrehozhat egy újat a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmaggal. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az USA keleti régiójában:

```azurepowershell-interactive
New-AzureRmResourceGroup `
    -Name myResourceGroup `
    -Location EastUS
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
A tárfiókok az Azure-fájlmegosztások vagy más tárolási erőforrások, például blobok vagy üzenetsorok üzembe helyezéséhez használható tárolók közös készletei. Egy tárfiók korlátlan számú megosztást tartalmazhat, egy megosztás pedig korlátlan számú fájl tárolására használható, egészen a tárfiók kapacitásának korlátjáig.

Ez a példa létrehoz egy tárfiókot a [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) parancsmaggal. A tárfiók neve *mystorageaccount<random number>*, a tárfiókra mutató hivatkozást pedig a **$storageAcct** változó tárolja. A tárfiókok névnek egyedinek kell lenniük, ezért a `Get-Random` paranccsal fűzzön számot a névhez. 

```azurepowershell-interactive 
$storageAcct = New-AzureRmStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Most létrehozhatja az első Azure-fájlmegosztását. A [New-AzureStorageShare](/powershell/module/azurerm.storage/new-azurestorageshare) parancsmaggal hozhat létre fájlmegosztást. Ez a példa egy `myshare` nevű fájlmegosztást hoz létre.

```azurepowershell-interactive
New-AzureStorageShare `
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
A Fájl REST protokollal közvetlenül is dolgozhat (vagyis saját maga készítheti el a REST HTTP-hívásokat), de a Fájl REST protokoll használatának legáltalánosabb módja az AzureRM PowerShell-modul, az [Azure CLI](storage-how-to-use-files-cli.md) vagy egy Azure Storage SDK használata, amelyek megfelelő burkolóval látják el a Fájl REST protokollt a választott parancs-/programozási nyelven.  

Az Azure-fájlmegosztást a legtöbb esetben az SMB protokollon keresztül fogja használni, mivel ez lehetővé teszi a mások által is vélhetően használt meglévő alkalmazások és eszközök használatát. A Fájl REST API használata azonban számos előnnyel jár az SMB-vel szemben, például a következő esetekben:

- A fájlmegosztást a PowerShell Cloud Shellben böngészi (amely nem tud fájlmegosztásokat csatlakoztatni az SMB-n keresztül).
- Olyan ügyfélről kell szkriptet vagy alkalmazást futtatnia, amely nem tud SMB-megosztásokat csatlakoztatni, például a helyszíni ügyfelekről, amelyekhez nincs feloldva a 445-ös port.
- Ki szeretné használni a kiszolgáló nélküli erőforrások, például az [Azure Functions](../../azure-functions/functions-overview.md) előnyeit. 

Az alábbi példák azt mutatják be, hogyan használhatja az AzureRM PowerShell-modult az Azure-fájlmegosztás Fájl REST protokollal való módosítására. 

#### <a name="create-directory"></a>Könyvtár létrehozása
Ha egy új, *myDirectory* nevű könyvtárat szeretne létrehozni az Azure-fájlmegosztás gyökérmappájában, használja a [New-AzureStorageDirectory](/powershell/module/azurerm.storage/new-azurestoragedirectory) parancsmagot.

```azurepowershell-interactive
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Fájl feltöltése
A fájlok [Set-AzureStorageFileContent](/powershell/module/azure.storage/set-azurestoragefilecontent) parancsmaggal való feltöltésének bemutatásához először létre kell hoznunk egy feltölteni kívánt fájlt a PowerShell Cloud Shell ideiglenes meghajtóján. 

Ez a példa az aktuális dátumot és időt helyezi az ideiglenes meghajtón lévő új fájlba, majd feltölti a fájlt a fájlmegosztásra.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzureStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Ha helyileg futtatja a PowerShellt, helyettesítse be a `C:\Users\ContainerAdministrator\CloudDrive\` elérési utat a gép egy meglévő elérési útjával.

A fájl feltöltése után a [Get-AzureStorageFile](/powershell/module/Azure.Storage/Get-AzureStorageFile) parancsmaggal ellenőrizheti, hogy a fájl fel lett-e töltve az Azure-fájlmegosztásba. 

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

#### <a name="download-a-file"></a>Fájl letöltése
A [Get-AzureStorageFileContent](/powershell/module/azure.storage/get-azurestoragefilecontent) parancsmaggal letöltheti a Cloud Shell ideiglenes meghajtójára az imént feltöltött fájl másolatát.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item 
    `-Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzureStorageFileContent `
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
A fájlok egy fájlmegosztásról egy másikra, vagy Azure Blob Storage-tárolóról/-tárolóra való másolása gyakori feladat. A funkció bemutatása érdekében létrehozhat egy új megosztást, és az imént feltöltött fájlt átmásolhatja erre az új megosztásra a [Start-AzureStorageFileCopy](/powershell/module/azure.storage/start-azurestoragefilecopy) parancsmaggal. 

```azurepowershell-interactive
New-AzureStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzureStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Ha most listázza az új megosztásban lévő fájlokat, meg kell jelennie a fájl másolatának.

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

Bár a `Start-AzureStorageFileCopy` parancsmag kényelmes megoldás az Azure-fájlmegosztások és az Azure Blob Storage-tárolók közötti alkalmi fájlmozgatásokhoz, a nagyobb léptékű áthelyezésekhez (az áthelyezett fájlok száma és mérete tekintetében) az AzCopy használata ajánlott. További információ: [AzCopy segédprogram Windows rendszeren](../common/storage-use-azcopy.md) és [AzCopy segédprogram Linux rendszeren](../common/storage-use-azcopy-linux.md). Az AzCopy parancsmagnak helyileg kell telepítve lennie – a Cloud Shellen nem érhető el. 

## <a name="create-and-manage-share-snapshots"></a>Megosztási pillanatképek létrehozása és felügyelete
Az Azure-fájlmegosztással végezhető egyik további hasznos feladat a megosztási pillanatképek létrehozása. A pillanatképek megőrzik az Azure-fájlmegosztások adott időpontban látható állapotát. A megosztási pillanatképek hasonlóak az esetleg már ismert operációsrendszer-technológiákhoz, például a következőkhöz:
- [Kötet árnyékmásolata szolgáltatás (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) Windows fájlrendszerekhez (például NTFS és ReFS)
- [Logikaikötet-kezelő (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pillanatképek Linux rendszerekhez
- [Apple fájlrendszer (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) pillanatképek macOS rendszerhez. 
 A fájlmegosztások PowerShell-objektumán a `Snapshot` metódussal hozhatja létre a megosztás pillanatképét, amely a [Get-AzureStorageShare](/powershell/module/azure.storage/get-azurestorageshare) parancsmaggal kérhető le. 

```azurepowershell-interactive
$share = Get-AzureStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Böngészés a megosztási pillanatképekben
A pillanatkép referenciáját (`$snapshot`) a `Get-AzureStorageFile` parancsmag `-Share` paraméterébe illesztve böngészhet a megosztási pillanatkép tartalmában.

```azurepowershell-interactive
Get-AzureStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Megosztási pillanatképek felsorolása
A következő paranccsal tekintheti meg a megosztáshoz készített pillanatképek listáját.

```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Visszaállítás megosztási pillanatképből
A fájlokat a korábban már használt `Start-AzureStorageFileCopy` paranccsal állíthatja vissza. A rövid útmutató során először töröljük a korábban feltöltött `SampleUpload.txt` fájlt, hogy visszaállíthassuk a pillanatképből.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzureStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"
 # Restore SampleUpload.txt from the share snapshot
Start-AzureStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Megosztási pillanatkép törlése
A megosztási pillanatképek törléséhez a [Remove-AzureStorageShare](/powershell/module/azure.storage/remove-azurestorageshare) parancsmagot használhatja a `-Share` paraméter `$snapshot` referenciáját tartalmazó változóval.

```azurepowershell-interactive
Remove-AzureStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Amikor végzett, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal távolíthatja el az erőforráscsoportot és az összes kapcsolódó erőforrást. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Egyesével is eltávolíthatja az erőforrásokat:

- A gyors útmutatóhoz létrehozott Azure-fájlmegosztások eltávolítása.
```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
    Remove-AzureStorageShare -Context $storageAcct.Context -Name $_.Name
}
```

- Magának a tárfióknak az eltávolítása (ez implicit módon eltávolítja a létrehozott Azure-fájlmegosztásokat, valamint az esetlegesen létrehozott egyéb tárolási erőforrásokat, például az Azure Blob Storage-tárolókat).
```azurepowershell-interactive
Remove-AzureRmStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Mi az Azure Files?](storage-files-introduction.md)
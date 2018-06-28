---
title: Azure-fájlmegosztások felügyelete az Azure PowerShell használatával
description: Megismerheti, hogyan felügyelheti az Azure-fájlmegosztásokat az Azure PowerShell használatával.
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 8fcc654b7f5068037ec0818e19d8dff09bc07537
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030189"
---
# <a name="managing-azure-file-shares-with-azure-powershell"></a>Azure-fájlmegosztások felügyelete az Azure PowerShell használatával 
Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure-fájlmegosztások Windows, Linux és macOS rendszeren csatlakoztathatók. Ez az útmutató az Azure-fájlmegosztások a PowerShell segítségével való használatának alapvető lépéseit mutatja be. Ebben a cikkben az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Erőforráscsoport és tárfiók létrehozása
> * Azure-fájlmegosztás létrehozása 
> * Könyvtár létrehozása
> * Fájl feltöltése 
> * Fájl letöltése
> * Megosztási pillanatkép létrehozása és használata

Ha nem rendelkezik Azure-előfizetéssel, első lépésként létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az útmutatóhoz az Azure PowerShell-modul 5.1.1-es vagy újabb verziójára lesz szükség. A jelenleg futtatott Azure PowerShell-modul verziójának kiderítéséhez futtassa a következő parancsot: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ha még nem rendelkezik Azure-erőforráscsoporttal, létrehozhat egy újat a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) parancsmaggal. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az USA keleti régiójában:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Create a storage account
A tárfiókok az Azure-fájlmegosztások vagy más tárolási erőforrások, például blobok vagy üzenetsorok üzembe helyezéséhez használható tárolók közös készletei. Egy tárfiók korlátlan számú megosztást tartalmazhat, egy megosztás pedig korlátlan számú fájl tárolására használható, egészen a tárfiók kapacitásának korlátjáig.

Ez a példa egy `mystorageacct<random number>` nevű tárfiókot hoz létre, és a tárfiók referenciáját a **$storageAcct** változóba helyezi. A tárfiókok névnek egyedinek kell lenniük, ezért a `Get-Random` paranccsal fűzzön számot a névhez. 

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

> [!Important]  
> A fájlmegosztások neve kisbetűket, számokat és kötőjeleket tartalmazhat, de nem kezdődhet kötőjellel. A fájlmegosztások és fájlok elnevezésére vonatkozó információkért lásd: [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása).

## <a name="work-with-the-contents-of-the-azure-file-share"></a>Az Azure-fájlmegosztás tartalmának használata
Most, hogy létrehozott egy Azure-fájlmegosztást, csatlakoztathatja azt az SMB szolgáltatással [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) vagy [macOS](storage-how-to-use-files-mac.md) rendszeren. Másik megoldásként az Azure PowerShell-modullal is használhatja az Azure-fájlmegosztást. Ez azért előnyösebb a fájlmegosztás SMB-vel való csatlakoztatásával szemben, mert a PowerShell-lel intézett összes kérelem a fájl REST API-jával történik, lehetővé téve a fájlok és könyvtárak létrehozását, módosítását és törlését a fájlmegosztásban a következőkből:

- A PowerShell Cloud Shell (amely nem tud fájlmegosztásokat csatlakoztatni az SMB-n keresztül).
- Az SMB-megosztások csatlakoztatására képtelen ügyfelek, például olyan helyszíni ügyfelek, amelyekhez nincs feloldva a 445-ös port.
- Kiszolgáló nélküli forgatókönyvek, például az [Azure Functions](../../azure-functions/functions-overview.md) eszközben. 


### <a name="create-directory"></a>Könyvtár létrehozása
Ha egy új, *myDirectory* nevű könyvtárat szeretne létrehozni az Azure-fájlmegosztás gyökérmappájában, használja a [New-AzureStorageDirectory](/powershell/module/azurerm.storage/new-azurestoragedirectory) parancsmagot.


```azurepowershell-interactive
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

### <a name="upload-a-file"></a>Fájl feltöltése
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

### <a name="download-a-file"></a>Fájl letöltése
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

### <a name="copy-files"></a>Fájlok másolása
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

## <a name="create-and-modify-share-snapshots"></a>Megosztási pillanatképek létrehozása és módosítása
Az Azure-fájlmegosztással végezhető egyik további hasznos feladat a megosztási pillanatképek létrehozása. A pillanatképek megőrzik az Azure-fájlmegosztások adott időpontban látható állapotát. A megosztási pillanatképek hasonlóak az esetleg már ismert operációsrendszer-technológiákhoz, például a következőkhöz:
- [Kötet árnyékmásolata szolgáltatás (VSS)](https://docs.microsoft.com/en-us/windows/desktop/VSS/volume-shadow-copy-service-portal) Windows fájlrendszerekhez (például NTFS és ReFS)
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
- [Fájlmegosztások felügyelete az Azure Portal használatával](storage-how-to-use-files-portal.md)
- [Fájlmegosztások felügyelete az Azure CLI használatával](storage-how-to-use-files-cli.md)
- [Fájlmegosztások felügyelete a Storage Explorer használatával](storage-how-to-use-files-storage-explorer.md)
- [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)

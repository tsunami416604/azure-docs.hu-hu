---
title: Rövid útmutató az Azure-fájlmegosztások felügyeletéhez az Azure CLI-vel
description: Ebből a rövid útmutatóból megtudhatja, hogyan felügyelheti az Azure Files szolgáltatást az Azure CLI használatával.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 95d7abca27ec9db46a72140bc8a61b2841c63fcb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77598595"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Rövid útmutató: Azure-fájlmegosztások létrehozása és felügyelete az Azure CLI-vel
Ez az útmutató az [Azure-fájlmegosztások](storage-files-introduction.md) Azure CLI-vel történő használatának alapvető lépéseit mutatja be. Az Azure-fájlmegosztások nem különböznek más fájlmegosztásoktól, a tárolásuk azonban a felhőben történik, és az Azure platform nyújt számukra támogatást. Az Azure-fájlmegosztások támogatják az iparági szabvány SMB protokollt, és lehetővé teszik a több gép, alkalmazás és példány közötti fájlmegosztást. 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI helyi telepítését és használatát választja, akkor a jelen cikkben szereplő lépésekhez az Azure CLI 2.0.4-es vagy újabb verzióját kell futtatnia. Az Azure CLI-verzió megkereséséhez futtassa az **az --version** parancsot. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

Az Azure CLI-parancsok alapértelmezés szerint JavaScript Object Notation- (JSON-) választ adnak vissza. A JSON az üzenetek REST API-kba való küldésének és onnan történő fogadásának szabványos módja. A JSON-válaszok használatának megkönnyítése érdekében a jelen cikk néhány példája a *query* paramétert használja az Azure CLI-parancsokon. Ez a paraméter a [JMESPath lekérdezési nyelvet](http://jmespath.org/) használja a JSON elemzéséhez. Az Azure CLI-parancsok eredményeinek JMESPath lekérdezési nyelvvel való használatáról a [JMESPath oktatóanyagában](http://jmespath.org/tutorial.html) talál további információt.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Az erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ha még nem rendelkezik Azure-erőforráscsoporttal, létrehozhat egyet az [az group create](/cli/azure/group) paranccsal. 

A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *USA nyugati részén 2* helyen:

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

## <a name="create-a-storage-account"></a>Create a storage account
A tárfiókok az Azure-fájlmegosztások vagy más tárolási erőforrások, például blobok vagy üzenetsorok üzembe helyezéséhez használható tárolók közös készletei. Egy tárfiók korlátlan számú fájlmegosztást tartalmazhat. a megosztásokban pedig tetszőleges számú fájlt tárolhat, egészen a tárfiókja kapacitásának korlátjáig.

A következő példa létrehoz egy tárfiókot az [az storage-fiók létrehozási](/cli/azure/storage/account) parancs használatával. A tárfiókok névnek egyedinek kell lenniük, ezért a `$RANDOM` paranccsal fűzzön számot a névhez.

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```

> [!Note]  
> Az 5 TiB-nél nagyobb (részvényenként legfeljebb 100 TiB-ig) nagyobb részvények csak helyileg redundáns (LRS) és zónaredundáns (ZRS) tárfiókokban érhetők el. Georedundáns (GRS) vagy geozónaredundáns (GZRS) tárfiók `--enable-large-file-share` létrehozásához távolítsa el a paramétert.

### <a name="get-the-storage-account-key"></a>A tárfiókkulcs lekérése
A tárfiókkulcsok vezérlik a tárfiókokban lévő erőforrások elérését. A kulcsok automatikusan jönnek létre a tárfiókok létrehozásakor. Az [az storage account keys list](/cli/azure/storage/account/keys) paranccsal kérheti le a tárfiók tárfiókkulcsait: 

```azurecli-interactive 
export storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Most létrehozhatja az első Azure-fájlmegosztását. Az [az storage share create](/cli/azure/storage/share) paranccsal hozhat létre fájlmegosztásokat. Ez a példa egy *myshare* nevű Azure-fájlmegosztást hoz létre: 

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --output none
```

A fájlmegosztások neve csak kisbetűket, számokat és kötőjeleket tartalmazhat (de nem kezdődhet kötőjellel). A fájlmegosztások és -fájlok elnevezésével kapcsolatos részletes információt a [megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Az Azure-fájlmegosztás használata
Az Azure Files két módszert biztosít a fájloknak és mappáknak az Azure-fájlmegosztásban való használatához: az iparági szabvány [Server Message Block (SMB) protokollt](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) és a [Fájl REST protokollt](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

A fájlmegosztás SMB-vel való csatlakoztatásához tekintse meg a használt operációs rendszernek megfelelő dokumentumot az alábbiak közül:
- [Linux](storage-how-to-use-files-linux.md)
- [Macos](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Azure-fájlmegosztások használata a Fájl REST protokollal 
Lehetséges, hogy közvetlenül a File REST protokollközvetlenül (handcrafting REST HTTP-hívások magát), de a leggyakoribb módja a File REST protokoll használata az Azure CLI, az [Azure PowerShell modul,](storage-how-to-use-files-powershell.md)vagy egy Azure Storage SDK, amelyek mindegyike egy szép burkoló körül a File REST protokoll a parancsfájl-készítési/programozási nyelv az Ön által választott.  

Arra számítunk, hogy a legtöbb Azure Files-felhasználó az SMB protokollon keresztül kíván majd dolgozni az Azure-fájlmegosztásával, mivel ez lehetővé teszi számukra a mások által is vélhetően használt meglévő alkalmazások és eszközök használatát. A Fájl REST API használata azonban számos előnnyel jár az SMB-vel szemben, például a következő esetekben:

- A fájlmegosztást az Azure Bash Cloud Shellben böngészi (amely nem tud fájlmegosztásokat csatlakoztatni az SMB-n keresztül).
- Ki szeretné használni a kiszolgáló nélküli erőforrások, például az [Azure Functions](../../azure-functions/functions-overview.md) előnyeit. 
- Olyan értéknövelő szolgáltatást hoz létre, amely számos Azure-fájlmegosztással kommunikál, például biztonsági mentést vagy vírusvédelmi vizsgálatokat hajt végre.

Az alábbi példák bemutatják, hogyan használhatja az Azure CLI az Azure-fájlmegosztás a File REST protokoll segítségével. 

### <a name="create-a-directory"></a>Könyvtár létrehozása
Ha egy új, *myDirectory* nevű könyvtárat szeretne létrehozni az Azure-fájlmegosztás gyökérmappájában, használja az [`az storage directory create`](/cli/azure/storage/directory) parancsot:

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

### <a name="upload-a-file"></a>Fájl feltöltése
Ha be szeretné mutatni, hogyan [`az storage file upload`](/cli/azure/storage/file) tölthet fel egy fájlt a paranccsal, először hozzon létre egy fájlt a Cloud Shell scratch drive-ra. A következő példában létrehozzuk, majd feltöltjük a fájlt:

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Ha helyileg futtatja az Azure CLI-t, cserélje le a `~/clouddrive` elérési utat a gép egy meglévő elérési útjára.

A fájl feltöltése után [`az storage file list`](/cli/azure/storage/file) a paranccsal meggyőződhet arról, hogy a fájl feltöltve lett az Azure-fájlmegosztásba:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Fájl letöltése
A [`az storage file download`](/cli/azure/storage/file) paranccsal letöltheti a Cloud Shell kaparós meghajtóra feltöltött fájl másolatát:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

### <a name="copy-files"></a>Fájlok másolása
Gyakori feladat a fájlok másolása egyik fájlmegosztásról egy másik fájlmegosztásra. A funkció bemutatásához hozzon létre egy új megosztást. Másolja a feltöltött fájlt erre az új megosztásra az [az storage file copy](/cli/azure/storage/file/copy) paranccsal: 

```azurecli-interactive
otherShareName="myshare2"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $otherShareName \
    --quota 1024 \
    --output none

az storage directory create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --name "myDirectory2" \
    --output none

az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-share $shareName \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share $otherShareName \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Ha most listázza az új megosztásban lévő fájlokat, megjelenik az átmásolt fájl:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --path "myDirectory2" \
    --output table
```

Bár `az storage file copy start` a parancs kényelmes fájláthelyezések között Azure fájlmegosztások, a migrálás és a nagyobb adatmozgások, javasoljuk `rsync` a macOS és a Linux és `robocopy` a Windows. `rsync`és `robocopy` az SMB segítségével hajtsa végre az adatmozgásokat a FileREST API helyett.

## <a name="create-and-manage-share-snapshots"></a>Megosztási pillanatképek létrehozása és felügyelete
Az Azure-fájlmegosztással végezhető egyik további hasznos feladat a megosztási pillanatképek létrehozása. A pillanatképek megőrzik az Azure-fájlmegosztások adott időpontban látható másolatát. A megosztási pillanatképek hasonlók néhány, esetleg már ismert operációsrendszer-technológiához:

- [Logikai kötetkezelő (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pillanatképek Linux rendszerekhez.
- [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) pillanatképek macOS.
- [Kötet árnyékmásolata szolgáltatás (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) Windows fájlrendszerekhez, például NTFS és ReFS rendszerekhez.
 
Megosztási pillanatképet a [`az storage share snapshot`](/cli/azure/storage/share) következő paranccsal hozhat létre:

```azurecli-interactive
snapshot=$(az storage share snapshot \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Böngészés a megosztási pillanatképek tartalmában
A megosztási pillanatképek tartalmában való böngészéshez illessze a rögzített megosztási pillanatkép időbélyegét az `az storage file list` parancs `$snapshot` változójába:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --snapshot $snapshot \
    --output table
```

### <a name="list-share-snapshots"></a>Megosztási pillanatképek felsorolása
A következő paranccsal tekintheti meg a megosztásról készített pillanatképek listáját:

```azurecli-interactive
az storage share list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --include-snapshot \
    --query "[? name== '$shareName' && snapshot!=null].snapshot" \
    --output tsv
```

### <a name="restore-from-a-share-snapshot"></a>Visszaállítás megosztási pillanatképből
A fájlokat a korábban már használt `az storage file copy start` paranccsal állíthatja vissza. Először törölje a feltöltött SampleUpload.txt fájlt, hogy visszaállíthassa a pillanatképből:

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --output none

# Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI$shareName"/myDirectory/SampleUpload.txt?sharesnapshot="$snapshot

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-uri $URI \
    --destination-share $shareName \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Megosztási pillanatkép törlése
A megosztáspillanatképet a [`az storage share delete`](/cli/azure/storage/share) parancs segítségével törölheti. Használja a `--snapshot` paraméterre mutató `$SNAPSHOT` hivatkozást tartalmazó változót:

```azurecli-interactive
az storage share delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --snapshot $snapshot \
    --output none
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha végzett, a [`az group delete`](/cli/azure/group) paranccsal eltávolíthatja az erőforráscsoportot és az összes kapcsolódó erőforrást: 

```azurecli-interactive 
az group delete --name $resourceGroupName
```

Alternatív megoldásként egyenként is eltávolíthatja az erőforrásokat.
- A jelen cikkhez létrehozott Azure-fájlmegosztások eltávolítása:

    ```azurecli-interactive
    az storage share list \
            --account-name $storageAccountName \
            --account-key $storageAccountKey \
            --query "[].name" \
            --output tsv | \
        xargs -L1 bash -ec '\
            az storage share delete \
                --account-name "$storageAccountName" \
                --account-key "$storageAccountKey" \
                --name $0 \
                --delete-snapshots include \
                --output none'
    ```

- Magának a tárfióknak az eltávolítása. (Ez implicit módon eltávolítja a létrehozott Azure-fájlmegosztásokat, valamint az esetlegesen létrehozott egyéb tárolási erőforrásokat, például az Azure Blob Storage-tárolókat.)

    ```azurecli-interactive
    az storage account delete \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --yes
    ```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Mi az Azure Files?](storage-files-introduction.md)
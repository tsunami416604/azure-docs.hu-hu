---
title: Azure-fájlmegosztások felügyelete az Azure CLI használatával
description: Itt megismerheti, hogyan felügyelheti az Azure Files szolgáltatást az Azure CLI használatával.
services: storage
documentationcenter: na
author: wmgries
manager: jeconnoc
editor: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 066a43b553be18a5a0bc889fff441824df301b98
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-using-the-azure-cli"></a>Azure-fájlmegosztások felügyelete az Azure CLI használatával
Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure-fájlmegosztások Windows, Linux és macOS rendszeren csatlakoztathatók. Ez az útmutató az Azure-fájlmegosztások Azure CLI-vel való használatának alapvető lépéseit mutatja be. Az alábbiak végrehajtásának módját ismerheti meg: 

> [!div class="checklist"]
> * Erőforráscsoport és tárfiók létrehozása
> * Azure-fájlmegosztás létrehozása 
> * Könyvtár létrehozása
> * Fájl feltöltése 
> * Fájl letöltése
> * Megosztási pillanatkép létrehozása és használata

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI helyi telepítését és használatát választja, akkor ehhez az útmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következő parancsot: **az --version**. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

Alapértelmezés szerint az Azure CLI-parancsok JSON (JavaScript Object Notation) választ adnak vissza, amely az üzenetek REST API-kból való küldésének és fogadásának tényleges módja. A JSON-válaszok használatának megkönnyítése érdekében az útmutató néhány példája a lekérdezési paramétert használja az Azure CLI-parancsokon. Ez a paraméter a [JMESPath lekérdezi nyelvet](http://jmespath.org/) használja a JSON elemzéséhez. Az Azure CLI parancsok eredményeinek használatáról a [JMESPath oktatóanyagában](http://jmespath.org/tutorial.html) talál további információt.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ha még nem rendelkezik Azure-erőforráscsoporttal, létrehozhat egy újat az [az group create](/cli/azure/group#create) paranccsal. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *USA keleti régiója* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Create a storage account
A tárfiókok az Azure-fájlmegosztások vagy más tárolási erőforrások, például blobok vagy üzenetsorok üzembe helyezéséhez használható tárolók közös készletei. Egy tárfiók korlátlan számú fájlmegosztást tartalmazhat, egy megosztás pedig korlátlan számú fájl tárolására használható, egészen a tárfiók kapacitásának korlátjáig.

Ez a példa létrehoz egy `mystorageaccount<random number>` nevű tárfiókot az [az storage account create](/cli/azure/storage/account#create) paranccsal, majd a tárfiók nevét a `$STORAGEACCT` változóba helyezi. A tárfiókok névnek egyedinek kell lenniük; a `$RANDOM` változó egy számot fűz a tárfiók nevének végére, hogy egyedivé tegye azt. 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>A tárfiókkulcs lekérése
A tárfiókkulcsokkal vezérelhető a tárfiókokban lévő erőforrások elérése. Ezek automatikusan jönnek létre a tárfiókok létrehozásakor. Az [az storage account keys list](/cli/azure/storage/account/keys#list) paranccsal kérheti le a tárfiók tárfiókkulcsait. 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Most létrehozhatja az első Azure-fájlmegosztását. Az [az storage share create](/cli/azure/storage/share#create) paranccsal hozhat létre fájlmegosztásokat. Ez a példa egy `myshare` nevű Azure-fájlmegosztást hoz létre. 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

> [!Important]  
> A fájlmegosztások neve kisbetűket, számokat és kötőjeleket tartalmazhat, de nem kezdődhet kötőjellel. A fájlmegosztások és fájlok elnevezésére vonatkozó információkért lásd: [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása).

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Az Azure-fájlmegosztás tartalmának módosítása
Most, hogy létrehozott egy Azure-fájlmegosztást, csatlakoztathatja azt az SMB szolgáltatással [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) vagy [macOS](storage-how-to-use-files-mac.md) rendszeren. Másik megoldásként az Azure CLI-vel is módosíthatja az Azure-fájlmegosztást. Ez azért előnyösebb a fájlmegosztás SMB-vel való csatlakoztatásával szemben, mert az Azure CLI-vel intézett összes kérelem a fájl REST API-jával történik, lehetővé téve a fájlok és könyvtárak létrehozását, módosítását és törlését a fájlmegosztáson a következőkből:

- A Bash Cloud Shell (amely nem tud fájlmegosztásokat csatlakoztatni az SMB-n keresztül).
- Az SMB-megosztások csatlakoztatására képtelen ügyfelek, például olyan helyszíni ügyfelek, amelyekhez nincs feloldva a 445-ös port.
- Kiszolgáló nélküli forgatókönyvek, például az [Azure Functions](../../azure-functions/functions-overview.md) eszközben. 

### <a name="create-directory"></a>Könyvtár létrehozása
Ha egy új, *myDirectory* nevű könyvtárat szeretne létrehozni az Azure-fájlmegosztás gyökérmappájában, használja a [`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create) parancsot.

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>Fájl feltöltése
A fájlok [`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload) paranccsal való feltöltésének bemutatásához először létre kell hozni egy feltölteni kívánt fájlt az Azure CLI Cloud Shell ideiglenes meghajtóján. A következő példában létrehozzuk, majd feltöltjük a fájlt.

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Ha helyileg futtatja az Azure CLI-t, helyettesítse be a `~/clouddrive` elérési utat a gép egy meglévő elérési útjával.

A fájl feltöltése után a [`az storage file list`](/cli/azure/storage/file#az_storage_file_list) paranccsal ellenőrizheti, hogy a fájl fel lett-e töltve az Azure-fájlmegosztásba.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Fájl letöltése
A [`az storage file download`](/cli/azure/storage/file#az_storage_file_download) paranccsal letöltheti a Cloud Shell ideiglenes meghajtójára a feltöltött fájl másolatát.

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>Fájlok másolása
A fájlok egy fájlmegosztásról egy másikra, vagy Azure Blob Storage-tárolóról/-tárolóra való másolása gyakori feladat. A funkció bemutatása érdekében létrehozhat egy új megosztást, és a feltöltött fájlt átmásolhatja erre az új megosztásra az [az storage file copy](/cli/azure/storage/file/copy) paranccsal. 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Ha most listázza az új megosztásban lévő fájlokat, meg kell jelennie a fájl másolatának.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

Bár a `az storage file copy start` parancs kényelmes megoldás az Azure-fájlmegosztások és az Azure Blob Storage-tárolók közötti alkalmi fájlmozgatásokhoz, a nagyobb léptékű áthelyezésekhez (az áthelyezett fájlok száma és mérete tekintetében) az AzCopy használata ajánlott. További információ: [AzCopy segédprogram Linux rendszeren](../common/storage-use-azcopy-linux.md) és [AzCopy segédprogram Windows rendszeren](../common/storage-use-azcopy.md). Az AzCopy parancsmagnak helyileg kell telepítve lennie – a Cloud Shellen nem érhető el 

## <a name="create-and-modify-share-snapshots"></a>Megosztási pillanatképek létrehozása és módosítása
Az Azure-fájlmegosztással végezhető egyik további hasznos feladat a megosztási pillanatképek létrehozása. A pillanatképek megőrzik az Azure-fájlmegosztások adott időpontban látható állapotát. A megosztási pillanatképek hasonlóak az esetleg már ismert operációsrendszer-technológiákhoz, például a következőkhöz:
- [Logikaikötet-kezelő (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pillanatképek Linux rendszerekhez
- [Apple fájlrendszer (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) pillanatképek macOS rendszerhez. 
- [Kötet árnyékmásolata szolgáltatás (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) Windows fájlrendszerekhez (például NTFS és ReFS)

A megosztási pillanatképek az [`az storage share snapshot`](/cli/azure/storage/share#az_storage_share_snapshot) paranccsal hozhatók létre.

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Böngészés a megosztási pillanatképek tartalmában
A megosztási pillanatképek tartalmában való böngészéshez illessze a rögzített megosztási pillanatkép időbélyegét az `az storage file list` parancs `$SNAPSHOT` változójába.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>Megosztási pillanatképek felsorolása
A következő paranccsal tekintheti meg a megosztáshoz készített pillanatképek listáját.

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>Visszaállítás megosztási pillanatképből
A fájlokat a korábban már használt `az storage file copy start` paranccsal állíthatja vissza. Először töröljük a feltöltött `SampleUpload.txt` fájlt, hogy visszaállíthassuk azt a pillanatképből.

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"

# Build the source URI for snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Megosztási pillanatkép törlése
Az [`az storage share delete`](/cli/azure/storage/share#az_storage_share_delete) paranccsal törölhet megosztási pillanatképet a `--snapshot` paraméter `$SNAPSHOT` referenciáját tartalmazó változóval.

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Amikor végzett, az [`az group delete`](/cli/azure/group#delete) paranccsal távolítsa el az erőforráscsoportot és az összes kapcsolódó erőforrást. 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

Egyesével is eltávolíthatja az erőforrásokat:
- A gyors útmutatóhoz létrehozott Azure-fájlmegosztások eltávolítása.

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --delete-snapshots include

az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2" \
    --delete-snapshots include
```

- Magának a tárfióknak az eltávolítása (ez implicit módon eltávolítja a létrehozott Azure-fájlmegosztásokat, valamint az esetlegesen létrehozott egyéb tárolási erőforrásokat, például az Azure Blob Storage-tárolókat).

```azurecli-interactive
az storage account delete \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --yes
```

## <a name="next-steps"></a>További lépések
- [Fájlmegosztások felügyelete az Azure Portal használatával](storage-how-to-use-files-portal.md)
- [Fájlmegosztások felügyelete az Azure PowerShell használatával](storage-how-to-use-files-powershell.md)
- [Fájlmegosztások felügyelete a Storage Explorer használatával](storage-how-to-use-files-storage-explorer.md)
- [Az Azure Files üzembe helyezésének megtervezése](storage-files-planning.md)
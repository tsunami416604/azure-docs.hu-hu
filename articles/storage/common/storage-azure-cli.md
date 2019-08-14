---
title: Az Azure CLI használata az Azure Storage szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan használhatja az Azure parancssori felületet (Azure CLI) az Azure Storage-ban a Storage-fiókok létrehozásához és kezeléséhez, valamint az Azure-blobok és-fájlok használatához.
services: storage
author: tamram
ms.service: storage
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: d259ea02824937f0c496bb253fa4b6c1f5cea412
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015636"
---
# <a name="using-the-azure-cli-with-azure-storage"></a>Using the Azure CLI with Azure Storage (Az Azure CLI és az Azure Storage együttes használata)

A nyílt forráskódú, platformfüggetlen Azure CLI számos parancsot biztosít az Azure platformmal való használathoz. Ez a funkció számos, a Azure Portalban található funkciót [](https://portal.azure.com)biztosít, beleértve a gazdag adatelérést is.

Ebben az útmutatóban bemutatjuk, hogyan használhatja az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) -t az Azure Storage-fiók erőforrásaival kapcsolatos feladatok elvégzéséhez. Javasoljuk, hogy az útmutató használata előtt töltse le és telepítse vagy frissítse a CLI legújabb verzióját.

Az útmutatóban szereplő példák feltételezik a bash-rendszerhéj Ubuntu-on való használatát, de a többi platformnak hasonlóképpen kell működnie. 

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató feltételezi, hogy ismeri az Azure Storage alapfogalmait. Azt is feltételezi, hogy képes megfelelni az Azure-hoz és a Storage szolgáltatáshoz alább megadott fiók-létrehozási követelményeknek.

### <a name="accounts"></a>Fiókok
* **Azure-fiók**: Ha még nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/).
* **Storage-fiók**: Lásd: [Storage-fiók létrehozása](storage-quickstart-create-account.md) az [Azure Storage](storage-create-storage-account.md)-fiókokról.

### <a name="install-the-azure-cli"></a>Telepítse az Azure CLI-t

Töltse le és telepítse az Azure CLI-t az [Azure CLI telepítése](/cli/azure/install-az-cli2)című témakörben ismertetett utasítások szerint.

> [!TIP]
> Ha problémája van a telepítéssel, tekintse meg [](/cli/azure/install-az-cli2) a cikk telepítésének hibaelhárítási szakaszát, valamint a githubon a [hibaelhárítási](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) útmutatót.
>

## <a name="working-with-the-cli"></a>A parancssori felület használata

Miután telepítette a CLI-t, használhatja az `az` parancsot a parancssori felületen (bash, Terminal, parancssor) az Azure CLI-parancsok eléréséhez. Írja be `az` a parancsot az alapparancsok teljes listájának megjelenítéséhez (a következő példában a kimenet csonkítva lett):

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account          : Manage subscriptions.
    acr              : Manage Azure container registries.
    acs              : Manage Azure Container Services.
    ad               : Synchronize on-premises directories and manage Azure Active Directory
                       resources.
    ...
```

A parancssori felületen futtassa a `az storage --help` parancsot a `storage` parancs alcsoportok listázásához. Az alcsoportok leírása áttekintést nyújt arról, hogy az Azure CLI milyen funkciókat biztosít a tárolási erőforrások kezeléséhez.

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Origin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Use queues to effectively scale applications according to traffic.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>A CLI összekötése az Azure-előfizetéssel

Az Azure-előfizetéséhez tartozó erőforrásokkal való együttműködéshez először be kell jelentkeznie az Azure- `az login`fiókjába. Többféleképpen is bejelentkezhet:

* **Interaktív bejelentkezés**:`az login`
* **Bejelentkezés felhasználónévvel és jelszóval**:`az login -u johndoe@contoso.com -p VerySecret`
  * Ez a funkció nem működik a többtényezős hitelesítést használó Microsoft-fiókokkal vagy fiókokkal.
* **Jelentkezzen be egy egyszerű szolgáltatással**:`az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-sample-script"></a>Azure CLI-minta parancsfájl

Ezután egy kis rendszerhéj-szkripttel fogunk dolgozni, amely néhány alapszintű Azure CLI-parancsot ad ki az Azure Storage-erőforrásokkal való kommunikációhoz. A szkript először létrehoz egy új tárolót a Storage-fiókban, majd feltölt egy meglévő fájlt (blobként) a tárolóba. Ezután felsorolja a tárolóban lévő összes blobot, és végül letölti a fájlt a megadott helyi számítógépen található célhelyre.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create --name $container_name

echo "Uploading the file..."
az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

echo "Listing the blobs..."
az storage blob list --container-name $container_name --output table

echo "Downloading the file..."
az storage blob download --container-name $container_name --name $blob_name --file $destination_file --output table

echo "Done"
```

**A parancsfájl konfigurálása és futtatása**

1. Nyissa meg a kedvenc szövegszerkesztőjét, majd másolja és illessze be az előző szkriptet a szerkesztőbe.

2. Ezután frissítse a szkript változóit a konfigurációs beállításoknak megfelelően. Cserélje le a következő értékeket a megadott értékekre:

   * storage_account_name a Storage-fiók nevét. **\<\>**
   * a storage_account_key elsődleges vagy másodlagos elérési kulcsának megadása. **\<\>**
   * container_name a létrehozandó új tároló nevét, például: "Azure-CLI-Sample-Container". **\<\>**
   * a blob_name a tárolóban található cél blob nevét adja meg. **\<\>**
   * file_to_upload a helyi számítógépen lévő kis fájl elérési útját, például "~/images/HelloWorld.png". **\<\>**
   * destination_file a célfájl elérési útját (például "~/downloadedImage.png"). **\<\>**

3. A szükséges változók frissítése után mentse a parancsfájlt, és zárja be a szerkesztőt. A következő lépések feltételezik, hogy elnevezte a **my_storage_sample. sh**parancsfájlt.

4. A parancsfájlt végrehajthatóként kell megjelölnie, ha szükséges:`chmod +x my_storage_sample.sh`

5. Futtassa a szkriptet. Például a Bashben:`./my_storage_sample.sh`

Ekkor az alábbihoz hasonló kimenetnek kell megjelennie **\<,\>** és a parancsfájlban megadott destination_file meg kell jelennie a helyi számítógépen.

```
Creating the container...
{
  "created": true
}
Uploading the file...
Percent complete: %100.0
Listing the blobs...
Name       Blob Type      Length  Content Type              Last Modified
---------  -----------  --------  ------------------------  -------------------------
README.md  BlockBlob        6700  application/octet-stream  2017-05-12T20:54:59+00:00
Downloading the file...
Name
---------
README.md
Done
```

> [!TIP]
> Az előző kimenet táblázatos formátumú. Megadhatja, hogy melyik kimeneti formátumot szeretné használni a CLI `--output` -parancsok argumentumának megadásával, vagy globálisan `az configure`a használatával.
>

## <a name="manage-storage-accounts"></a>Tárfiókok kezelése

### <a name="create-a-new-storage-account"></a>Új tárfiók létrehozása
Az Azure Storage használatához tárfiókra van szüksége. Létrehozhat egy új Azure Storage-fiókot, miután konfigurálta a számítógépet az előfizetéshez való kapcsolódáshoz.

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location`[Kötelező]: Helyen. Például: "West US".
* `--name`[Kötelező]: A tárfiók neve. A névnek 3 – 24 karakter hosszúnak kell lennie, és csak kisbetűs alfanumerikus karaktereket használjon.
* `--resource-group`[Kötelező]: Erőforráscsoport neve.
* `--sku`[Kötelező]: A Storage-fiók SKU-jának. Megengedett értékek:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`
  * `Standard_GZRS`előnézet
  * `Standard_RAGZRS`előnézet

### <a name="set-default-azure-storage-account-environment-variables"></a>Alapértelmezett Azure Storage-fiók környezeti változóinak beállítása

Az Azure-előfizetése több Storage-fiókkal is rendelkezhet. Az alábbi környezeti változók megadásával választhatja ki, hogy az összes további tárolási parancshoz használni tudja az egyiket:

Elsőként jelenítse meg a tárfiókkulcsokat az [az storage account keys list](/cli/azure/storage/account/keys) parancs segítségével:

```azurecli-interactive
az storage account keys list \
    --account-name <account_name> \
    --resource-group <resource_group> \
    --output table
```

Most, hogy már rendelkezik a kulccsal, megadhatja a nevet és a fióknevet környezeti változókként:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_KEY=<key>
```

Egy másik módszer az alapértelmezett Storage-fiók beállítása egy kapcsolódási karakterlánc használatával. Először szerezze be a (z) `show-connection-string` paranccsal a kapcsolatok karakterláncát:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Ezután másolja ki a kimeneti kapcsolódási karakterláncot `AZURE_STORAGE_CONNECTION_STRING` , és állítsa be a környezeti változót (Előfordulhat, hogy a kapcsolódási karakterláncot idézőjelek közé kell foglalni):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> A cikk alábbi fejezeteiben szereplő példák azt feltételezik, hogy beállította a `AZURE_STORAGE_ACCOUNT` és `AZURE_STORAGE_KEY` a környezeti változókat.

## <a name="create-and-manage-blobs"></a>Blobok létrehozása és kezelése
Az Azure Blob Storage egy olyan szolgáltatás, amely nagy mennyiségű strukturálatlan adat, például szöveges vagy bináris adatok tárolására szolgál, amelyek HTTP-vagy HTTPS-kapcsolaton keresztül bárhonnan elérhetők a világ bármely pontján. Ez a szakasz azt feltételezi, hogy már ismeri az Azure Blob Storage-fogalmakat. Részletes információkért lásd: [Az Azure Blob Storage használatának első lépései a .net](../blobs/storage-dotnet-how-to-use-blobs.md) -és blob-szolgáltatásokkal kapcsolatos [fogalmakkal](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Tároló létrehozása
Az Azure Storage-ban minden blobnak tárolóban kell lennie. A tárolót a `az storage container create` parancs használatával hozhatja létre:

```azurecli
az storage container create --name <container_name>
```

Az új tárolók esetében a választható `--public-access` argumentum megadásával beállíthatja az olvasási hozzáférés három szintje egyikét:

* `off`(alapértelmezett): A tárolók személyesek a fiók tulajdonosa számára.
* `blob`: Nyilvános olvasási hozzáférés a blobokhoz.
* `container`: Nyilvános olvasási és listázási hozzáférés a teljes tárolóhoz.

További információkért lás a [tárolók és blobok névtelen olvasási hozzáférésének kezelésével](../blobs/storage-manage-access-to-resources.md) foglalkozó témakört.

### <a name="upload-a-blob-to-a-container"></a>Blob feltöltése tárolóba
Az Azure Blob Storage támogatja a blokk, a Hozzáfűzés és az oldal blobokat. Blobok feltöltése egy tárolóba a `blob upload` paranccsal:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

Ha közvetlenül a Storage-fiókban lévő tárolóban lévő mappába szeretne feltölteni, cserélje le `--name <blob_name>` a `--name <folder/blob_name>`következőt:.

 Alapértelmezés szerint a parancs `blob upload` feltölti a *. vhd fájlokat az oldal blobokra, vagy más módon blokkolja a blobokat. Ha másik típust szeretne megadni egy blob feltöltésekor, használhatja `--type` az argumentumot – az engedélyezett `append`értékek `block`:, és `page`.

 A különböző blob-típusokkal kapcsolatos további információkért lásd: a Blobok [, a Blobok hozzáfűzése és az oldal Blobok ismertetése](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).


### <a name="download-a-blob-from-a-container"></a>Blob letöltése tárolóból
Ez a példa azt szemlélteti, hogyan tölthető le egy blob egy tárolóból:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A tárolóban lévő Blobok listázása az az [Storage blob List](/cli/azure/storage/blob) paranccsal.

```azurecli
az storage blob list \
    --container-name mycontainer \
    --output table
```

### <a name="copy-blobs"></a>Blobok másolása
Tárfiókokon és régiókon belül vagy azok között aszinkron módon másolhatja át a blobokat.

Az alábbi példa azt mutatja be, hogyan másolhat át blobokat egyik tárfiókból a másikba. Először a forrás tárfiókban hozunk létre tárolót, az abban lévő blobok számára pedig nyilvános olvasási hozzáférést adunk. Ezt követően feltöltünk egy fájlt a tárolóba, végül átmásoljuk a blobot ebből a tárolóból a céltárfiókban lévő tárolóba.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/Pictures/sourcefile.png \
    --name sourcefile.png

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

A fenti példában a cél tárolónak már léteznie kell a cél Storage-fiókban ahhoz, hogy a másolási művelet sikeres legyen. Ezenkívül a `--source-uri` argumentumban megadott forrásblobnak tartalmaznia kell egy közös hozzáférésű jogosultságkód (SAS-) tokent, vagy nyilvánosnak kell lennie, ahogy ebben a példában is szerepel.

### <a name="delete-a-blob"></a>Blob törlése
BLOB törléséhez használja a `blob delete` következő parancsot:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

### <a name="set-the-content-type"></a>A tartalomtípus beállítása

A tartalomtípus (más néven MIME-típus) a blobban lévő adatok formátumát azonosítja. A böngészők és egyéb szoftverek a tartalomtípus segítségével állapítják meg az adatok feldolgozásának a módját. Például a PNG-lemezképek `image/png`tartalomtípusa a következő:. A tartalom típusának beállításához használja a `blob update` következő parancsot:

```azurecli
az storage blob update
    --container-name <container_name> 
    --name <blob_name>
    --content-type <content_type>
```

## <a name="create-and-manage-file-shares"></a>Fájlmegosztás létrehozása és kezelése
Azure Files a Server Message Block (SMB) protokollt használó alkalmazások számára biztosít megosztott tárterületet. Microsoft Azure a virtuális gépek és a Cloud Services, valamint a helyszíni alkalmazások is megoszthatják a fájlokat a csatlakoztatott megosztásokon keresztül. Az Azure CLI-n keresztül kezelheti a fájlmegosztást és a fájlokat. További információ a Azure Filesről: a [Azure Files bemutatása](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Fájlmegosztás létrehozása
Az Azure-fájlmegosztás egy SMB-fájlmegosztás az Azure-ban. Az összes könyvtárat és fájlt egy fájlmegosztási fájlban kell létrehozni. Egy fiók korlátlan számú megosztást tartalmazhat, és egy megosztás korlátlan számú fájlt tárolhat, a Storage-fiók kapacitásának korlátaival együtt. A következő példa egy **MyShare**nevű fájlmegosztást hoz létre.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Könyvtár létrehozása
A címtár hierarchikus struktúrát biztosít egy Azure-fájlmegosztás számára. A következő példában létrehozunk egy **könyvtárnév** nevű könyvtárat a fájlmegosztás számára.

```azurecli
az storage directory create --name myDir --share-name myshare
```

A könyvtár elérési útja több szintet is tartalmazhat, például **dir1/dir2**. Az alkönyvtárak létrehozása előtt azonban győződjön meg arról, hogy az összes szülő-könyvtár létezik. Például a Path **dir1/dir2**esetében először létre kell hoznia a címtár- **dir1**, majd létre kell hoznia a címtár- **dir2**.

### <a name="upload-a-local-file-to-a-share"></a>Helyi fájl feltöltése egy megosztásba
A következő példa egy fájlt tölt fel a **~/temp/samplefile.txt** -ből a **MyShare** -fájlmegosztás gyökerébe. Az `--source` argumentum a meglévő helyi fájlt adja meg a feltöltéshez.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

A címtár létrehozásához hasonlóan a megosztáson belül is megadhatja a könyvtár elérési útját, hogy feltöltse a fájlt egy meglévő könyvtárba a megosztáson belül:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

A megosztásban található fájlok mérete akár 1 TB is lehet.

### <a name="list-the-files-in-a-share"></a>Megosztásban található fájlok listázása
A megosztásban található fájlokat és címtárakat a `az storage file list` parancs használatával listázhatja:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Fájlok másolása      
Fájlokat másolhat egy másik fájlba, egy fájlt egy blobba vagy egy blobot egy fájlba. Ha például egy fájlt egy másik megosztásban lévő könyvtárba szeretne másolni:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Megosztási pillanatkép létrehozása
A megosztási pillanatképet a `az storage share snapshot` parancs használatával hozhatja létre:

```cli
az storage share snapshot -n <share name>
```

Minta kimenete
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="list-share-snapshots"></a>Megosztási pillanatképek felsorolása

Az egyes megosztások megosztási pillanatképeit a használatával listázhatja`az storage share list --include-snapshots`

```cli
az storage share list --include-snapshots
```

**Minta kimenete**
```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

### <a name="browse-share-snapshots"></a>Böngészés a megosztási pillanatképekben
Egy adott megosztási pillanatképen is megtekintheti a tartalmát a használatával `az storage file list`. Az egyiknek meg kell adnia a `--share-name <snare name>` megosztás nevét és az időbélyeget.`--snapshot '2017-10-04T19:45:18.0000000Z'`

```azurecli-interactive
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

**Minta kimenete**
```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
### <a name="restore-from-share-snapshots"></a>Visszaállítás a megosztási pillanatképekről

A fájlokat visszaállíthatja egy megosztási pillanatképből származó fájl másolásával vagy letöltésével a parancs használatával `az storage file download`

```azurecli-interactive
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```
**Minta kimenete**
```
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```
## <a name="delete-share-snapshot"></a>Megosztási pillanatkép törlése
A megosztási pillanatképet a parancs `az storage share delete` `--snapshot` használatával törölheti, ha a megosztás pillanatképének időbélyegét megadó paraméterrel rendelkezik:

```cli
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Minta kimenete
```json
{
  "deleted": true
}
```

## <a name="next-steps"></a>További lépések
Íme néhány további erőforrás az Azure CLI használatáról. 

* [Ismerkedés az Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Azure CLI-parancsok – dokumentáció](/cli/azure)
* [Azure CLI a GitHubon](https://github.com/Azure/azure-cli)

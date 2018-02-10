---
title: "Az Azure parancssori felület 2.0 használatával az Azure Storage |} Microsoft Docs"
description: "Megtudhatja, hogyan használható az Azure parancssori felület (CLI) 2.0 az Azure Storage létrehozása és a storage-fiókok kezelése és a munkahelyi Azure-blobokat és fájlokat. Az Azure CLI 2.0 pythonban írt platformfüggetlen eszköz."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: tamram
ms.openlocfilehash: 509c702054961c9d9fa525242ce0542059e32d81
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="using-the-azure-cli-20-with-azure-storage"></a>Az Azure parancssori felület 2.0 használatával az Azure Storage

A nyílt forráskódú, platformok közötti Azure CLI 2.0 parancsokat biztosít az Azure platformon való munkához. Nagy része megtalálható ugyanezeket a funkciókat biztosít a [Azure-portálon](https://portal.azure.com), beleértve a funkciógazdag adatelérési.

Ebben az útmutatóban megmutatjuk, hogyan használható a [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) működik-e az Azure-tárfiók erőforrásokat több feladatok elvégzéséhez. Azt javasoljuk, hogy letöltése és telepítése vagy frissítése a legújabb verzióra a CLI 2.0 az útmutató használata előtt.

Az útmutatóban szereplő példák a Bash rendszerhéjat Ubuntu használatát feltételezik, de más platformokon hasonló módon végre kell hajtania. 

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató feltételezi, hogy tudomásul veszi az Azure Storage alapvető fogalmait. Azt is feltételezi, hogy megfelelnek a fiók létrehozása az Azure és a tárolás szolgáltatás alatt megadott esetén van.

### <a name="accounts"></a>Fiókok
* **Azure-fiók**: Ha még nem rendelkezik Azure-előfizetéssel, [egy ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/).
* **Tárfiók**: Lásd a [Tudnivalók az Azure Storage-fiókokról](storage-create-storage-account.md) cikk [Tárfiók létrehozása](storage-create-storage-account.md#create-a-storage-account) szakaszát.

### <a name="install-the-azure-cli-20"></a>Az Azure CLI 2.0 telepítése

Töltse le és telepítse az Azure CLI 2.0 ismertetett lépéseket követve [Azure CLI 2.0 telepítése](/cli/azure/install-az-cli2).

> [!TIP]
> Ha problémája merül fel a a telepítés, tekintse meg a [telepítési hibák elhárítása](/cli/azure/install-az-cli2#installation-troubleshooting) cikkének, és a [telepítése hibaelhárítási](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) útmutató a Githubon.
>

## <a name="working-with-the-cli"></a>A parancssori felület használata

A parancssori felület telepítése után is használhatja a `az` parancsot a parancssori felület (Bash, terminált, parancssor) az Azure parancssori felület parancsait eléréséhez. Típus a `az` parancsot az alap parancsokat (a következő egy példa a kimenetre csonkolódtak) teljes listájának megtekintéséhez:

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

A parancssori felületen, a parancs végrehajtása `az storage --help` listájára a `storage` alcsoportokat parancsot. A alcsoportokat leírásait nyújt áttekintést. a funkció az Azure parancssori felület biztosít a tároló-erőforrások használata.

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

## <a name="connect-the-cli-to-your-azure-subscription"></a>A parancssori felület csatlakozni az Azure-előfizetéshez

Az Azure-előfizetés az erőforrások használatához kell először jelentkezik be az Azure-fiókja `az login`. Többféleképpen is bejelentkezhet:

* **Interaktív bejelentkezés**:`az login`
* **Jelentkezzen be a felhasználónevet és jelszót**:`az login -u johndoe@contoso.com -p VerySecret`
  * A Microsoft és fiókok számára, hogy a többtényezős hitelesítés használata mellett nem működik.
* **Jelentkezzen be egy egyszerű**:`az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-20-sample-script"></a>Az Azure CLI 2.0 parancsfájlpéldát

Dolgozunk lesz ezután az egy kis héjparancsfájlt, amely néhány alapvető Azure CLI 2.0 parancs interaktív állít ki az Azure Storage-erőforrások. A parancsfájl először létrehoz egy új tárolót a tárfiókban lévő, majd feltölt egy már létező fájlt (a blob) tárolóban. Majd felsorolja az összes BLOB a tárolóban, és végül letölti a fájlt a helyi számítógépen, amely megadja egy célra.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

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

**Konfigurálja és futtassa a parancsprogramot**

1. Nyissa meg a kedvenc szövegszerkesztőjével, majd másolja, és az előző parancsfájl illessze be a szerkesztőt.

2. Következő lépésként frissítse a parancsfájl-változókat megfelelően a konfigurációs beállításokat. Cserélje le a következő értékeket a megadott:

   * **\<storage_account_name\>**  a tárfiók nevét.
   * **\<storage_account_key\>**  az elsődleges vagy másodlagos elérési kulcsot a tárfiók.
   * **\<container_name\>**  A nevet az új tároló létrehozásához, például az "azure-cli-minta-container".
   * **\<blob_name\>**  egy nevet a cél BLOB a tárolóban.
   * **\<file_to_upload\>**  a helyi számítógépen, például a fájl elérési útját kis "~ / images/HelloWorld.png".
   * **\<destination_file\>**  a cél fájl elérési útját, például a "~ / downloadedImage.png".

3. Miután frissítette a szükséges változók, mentse a parancsfájlt, és zárja be a szerkesztőt. A következő lépések azt feltételezik, hogy a parancsfájl nevű **my_storage_sample.sh**.

4. Szükség esetén jelölje meg a parancsfájl végrehajtható, mint:`chmod +x my_storage_sample.sh`

5. Hajtsa végre a parancsfájlt. Például a Bash:`./my_storage_sample.sh`

A következőhöz hasonló kimenetnek kell megjelennie, és a  **\<destination_file\>**  az megjelenjen-e a parancsfájl a helyi számítógépen.

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
> Az előző eredmény **tábla** formátumban. Megadhat, amely kimeneti megadásával formátumát a `--output` argumentumának a parancssori felület parancsait, vagy állítsa az segítségével globálisan `az configure`.
>

## <a name="manage-storage-accounts"></a>Tárfiókok kezelése

### <a name="create-a-new-storage-account"></a>Új tárfiók létrehozása
Az Azure Storage használatához tárfiókra van szüksége. A számítógép beállítása után létrehozhat egy új Azure Storage-fiók [csatlakozás az előfizetéshez](#connect-to-your-azure-subscription).

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location`[Szükséges]: helyét. Például "USA nyugati régiója".
* `--name`[Szükséges]: A tárfiók nevét. A névnek kell 3 – 24 karakter hosszúságú lehet, és csak kisbetűs alfanumerikus karaktereket használjon.
* `--resource-group`[Szükséges]: erőforráscsoport nevét.
* `--sku`[Szükséges]: A tárfiók Termékváltozat. Megengedett értékek:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`


### <a name="set-default-azure-storage-account-environment-variables"></a>Környezeti változók értékét az alapértelmezett Azure storage-fiók
Az Azure-előfizetéshez több tárfiókot is lehet. Válassza ki az egyiket minden ezt követő tárolási parancs, állítsa be ezen környezeti változókkal:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Egy alapértelmezett tárfiókot beállításához másik úgy, hogy a kapcsolati karakterlánc használatával. Először a a kapcsolati karakterlánc beolvasása a `show-connection-string` parancs:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Ezután másolja a kimeneti kapcsolati karakterláncot, és állítsa be a `AZURE_STORAGE_CONNECTION_STRING` környezeti változó (szükség lehet a kapcsolati karakterlánc tegye idézőjelek közé foglalt):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> Ez a cikk a következő szakaszok a összes példák feltételezik, hogy beállított a `AZURE_STORAGE_ACCOUNT` és `AZURE_STORAGE_ACCESS_KEY` környezeti változókat.
>

## <a name="create-and-manage-blobs"></a>Hozzon létre és blobok kezelése
Az Azure Blob storage egy olyan szolgáltatás nagy mennyiségű strukturálatlan adatok, például szövegek vagy bináris adatok, hozzáfér a bárhol a világon HTTP vagy HTTPS PROTOKOLLON keresztül tárolásához. Ez a szakasz feltételezi, hogy Ön már ismeri a Azure Blob storage fogalmakat. Részletes információkért lásd: [az Azure Blob storage .NET használatának első lépései](../blobs/storage-dotnet-how-to-use-blobs.md) és [Blob szolgáltatással kapcsolatos fogalmak](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Tároló létrehozása
Az Azure storage összes blobjának egy tárolóban kell lennie. Egy tároló segítségével létrehozható a `az storage container create` parancs:

```azurecli
az storage container create --name <container_name>
```

Beállíthatja három szintjének olvasási hozzáférés egy új tároló az opcionális megadásával `--public-access` argumentum:

* `off`(alapértelmezett): tároló adatokat a fiók tulajdonosának a saját.
* `blob`: Blobok nyilvános olvasási hozzáférés.
* `container`: A teljes tárolóhoz nyilvános olvasási és lista eléréséhez.

További információkért lásd: [tárolók és blobok névtelen olvasási hozzáférés kezelése](../blobs/storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Blob feltöltése tárolóba
Az Azure Blob storage blokkméretet támogatja, hozzáfűzése, és a lapblobokat. Töltse fel blobok egy tárolóba használatával a `blob upload` parancs:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

 Alapértelmezés szerint a `blob upload` parancs a lapblobokat, vagy egyéb blokkblobokat fel a *.vhd fájlokat. Ha feltölt egy blobot egy másik megadásához használja a `--type` argumentum--engedélyezett értékek `append`, `block`, és `page`.

 A különböző blob típusokon további információkért lásd: [ismertetése Blokkblobokat, hozzáfűző blobokat és Lapblobokat](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).


### <a name="download-a-blob-from-a-container"></a>Blob letöltése tárolóból
Ez a példa bemutatja, hogyan töltheti le a blob egy tárolóba:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A tárolóban lévő blobok listázása a [az tárolási blob lista](/cli/azure/storage/blob#az_storage_blob_list) parancsot.

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

A fenti példában a célként megadott tárolót már léteznie kell a cél tárfiókkal a másolási művelet sikeres legyen. Ezenkívül a `--source-uri` argumentumban megadott forrásblobnak tartalmaznia kell egy közös hozzáférésű jogosultságkód (SAS-) tokent, vagy nyilvánosnak kell lennie, ahogy ebben a példában is szerepel.

### <a name="delete-a-blob"></a>Blob törlése
Egy blob törléséhez használja a `blob delete` parancs:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

## <a name="create-and-manage-file-shares"></a>Hozzon létre és fájlmegosztások kezelése
Az Azure Files a Server Message Block (SMB) protokollt használó alkalmazások számára közös tárterületet biztosít. A Microsoft Azure virtuális gépek és felhőszolgáltatások, valamint a helyszíni alkalmazások megosztott csatlakoztatott megosztásokon keresztül. A fájlmegosztások és a fájladatok az Azure parancssori felület használatával kezelheti. Azure fájlokon további információkért lásd: [Bevezetés az Azure Fileshoz](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Fájlmegosztás létrehozása
Egy Azure fájlmegosztás egy SMB-fájlmegosztás, az Azure-ban. Minden könyvtárak és fájlok fájlmegosztást kell létrehozni. Egy fiók korlátlan számú megosztást tartalmazhat, és a megosztás tud tárolni a fájlokat, a tárfiók a kapacitás határértékekig korlátlan számú. Az alábbi példa létrehoz egy nevű fájlmegosztás **megosztás**.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Könyvtár létrehozása
Egy könyvtárat biztosít az Azure fájlmegosztások hierarchikus struktúra. Az alábbi példa létrehoz egy könyvtárat nevű **könyvtárnév** a fájlmegosztásban.

```azurecli
az storage directory create --name myDir --share-name myshare
```

Az elérési út például tartalmazhatnak több szintjéről **dir1/dir2**. Azonban úgy kell beállítania, hogy létezik-e minden szülő-könyvtár egy alkönyvtár létrehozása előtt. Például a következő elérési út **dir1/dir2**, először létre kell hoznia directory **dir1**, majd hozza létre a könyvtár **dir2**.

### <a name="upload-a-local-file-to-a-share"></a>Egy helyi fájl feltöltése
Az alábbi példa feltölt egy fájlt a **~/temp/samplefile.txt** gyökérben, a **megosztás** fájlmegosztást. A `--source` argumentum meghatározza a meglévő helyi fájl feltöltése.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Mint létrehozni a könyvtárat, megadhatja a megosztás lehet feltölteni a fájlt meglévő címtárhoz a megosztáson belüli belül az elérési út:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

A megosztásban található egy fájl akár 1 TB méretű lehet.

### <a name="list-the-files-in-a-share"></a>Olyan megosztáson található fájlok listázása
Fájlok és könyvtárak olyan megosztáson található is listázhatja a a `az storage file list` parancs:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Fájlok másolása      
Fájl másolása másik fájlba, egy fájlt egy blobba vagy egy blobot egy fájlba. Ha például a fájl másolása másik megosztásban egy könyvtárat:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Megosztás pillanatkép létrehozása
Megosztás pillanatkép segítségével létrehozható a `az storage share snapshot` parancs:

```cli
az storage share snapshot -n <share name>
```

Minta kimenet
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

### <a name="list-share-snapshots"></a>Lista megosztás pillanatképek

Előfordulhat, hogy felsorolja megosztás pillanatképek egy adott használatával`az storage share list --include-snapshots`

```cli
az storage share list --include-snapshots
```

**Minta kimenet**
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

### <a name="browse-share-snapshots"></a>Keresse meg a megosztás pillanatképek
Előfordulhat, hogy is Tallózás a pillanatkép-megtekintéséhez a tartalom használatával egy adott megosztás `az storage file list`. Egy megosztás nevének megadására van `--share-name <snare name>` és az időbélyeg`--snapshot '2017-10-04T19:45:18.0000000Z'`

```azurecli-interactive
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

**Minta kimenet**
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
### <a name="restore-from-share-snapshots"></a>A megosztás pillanatképek visszaállítása

Másolás vagy a fájl letöltése pillanatkép-használatával megosztásból visszaállíthatja egy fájl `az storage file download` parancs

```azurecli-interactive
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```
**Minta kimenet**
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
## <a name="delete-share-snapshot"></a>Megosztás pillanatkép törlése
Megosztás pillanatkép használatával törölheti a `az storage share delete` parancs megadásával `--snapshot` megosztás pillanatkép Timestamp értékkel rendelkező paraméter:

```cli
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Minta kimenet
```json
{
  "deleted": true
}
```

## <a name="next-steps"></a>További lépések
Az alábbiakban néhány további források további, az Azure CLI 2.0 használatával kapcsolatban.

* [Ismerkedés az Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Az Azure CLI 2.0 parancsdokumentációja](/cli/azure)
* [Az Azure CLI 2.0 a Githubon](https://github.com/Azure/azure-cli)

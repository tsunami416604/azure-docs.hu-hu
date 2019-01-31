---
title: Az Azure CLI-vel és az Azure Storage |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure parancssori felület (Azure CLI) az Azure Storage létrehozása és a storage-fiókok kezelése és használata Azure-blobok és fájlok.
services: storage
author: roygara
ms.service: storage
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: 707ee508b0615ab873b60b45eb1d9f0294313367
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464568"
---
# <a name="using-the-azure-cli-with-azure-storage"></a>Using the Azure CLI with Azure Storage (Az Azure CLI és az Azure Storage együttes használata)

A nyílt forráskódú, platformfüggetlen Azure parancssori felület parancsokat biztosít az Azure platform használatához. Nagy része megtalálható ugyanazokat a funkciókat biztosít a [az Azure portal](https://portal.azure.com), beleértve a részletes adatok elérésére.

Ez az útmutató bemutatjuk, hogyan használható a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) több feladatait az Azure Storage-fiókjában található erőforrásokkal dolgozik. Azt javasoljuk, hogy letöltése és telepítése vagy frissítése a legújabb verzióra a parancssori felület az útmutató használata előtt.

Az útmutatóban szereplő példák feltételezik a bash on Ubuntu használatát, de más platformokon hasonló módon kell végrehajtania. 

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató feltételezi, hogy ismeri az Azure Storage az alapvető fogalmait. Azt is feltételezi, hogy az képes az Azure és a Storage szolgáltatás az alább megadott fióklétrehozási követelmények teljesülnek.

### <a name="accounts"></a>Fiókok
* **Azure-fiók**: Ha még nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/).
* **Storage-fiók**: Lásd: [hozzon létre egy tárfiókot](storage-quickstart-create-account.md) a [tudnivalók az Azure storage-fiókok](storage-create-storage-account.md).

### <a name="install-the-azure-cli"></a>Telepítse az Azure CLI-t

Töltse le és telepítse az Azure CLI leírt utasítások alapján [az Azure CLI telepítése](/cli/azure/install-az-cli2).

> [!TIP]
> Ha problémája akad a telepítés, tekintse meg a [telepítési hibák elhárítása](/cli/azure/install-az-cli2#installation-troubleshooting) cikkének, és a [telepítése hibaelhárítási](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) útmutatóban a Githubon.
>

## <a name="working-with-the-cli"></a>A parancssori felület használata

Miután telepítette a parancssori felület, a `az` parancsot a parancssori felületről (Bash, terminál, parancssor) eléréséhez az Azure CLI-parancsokat. Írja be a `az` parancsot (az alábbi példa kimenetében csonkolta) alapparancsok teljes listáját lásd:

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

Hajtsa végre a parancsot a parancssori felület `az storage --help` listához a `storage` alcsoportok parancsot. Az alcsoportok leírásait nyújt áttekintést a funkció az Azure CLI használata a storage-erőforrásokkal tartalmaz.

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

## <a name="connect-the-cli-to-your-azure-subscription"></a>Csatlakoztassa a CLI-t az Azure-előfizetés

Működik az Azure-előfizetésében lévő erőforrásokat, meg kell először jelentkezzen be az Azure-fiókjába `az login`. Számos módon jelentkezhet be:

* **Interaktív bejelentkezés**: `az login`
* **Jelentkezzen be a felhasználónevet és jelszót**: `az login -u johndoe@contoso.com -p VerySecret`
  * Ez a Microsoft-fiókok vagy a multi-factor Authentication hitelesítést használó fiókokhoz nem működik.
* **Jelentkezzen be egy egyszerű szolgáltatást**: `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-sample-script"></a>Az Azure CLI-példaszkript

Ezután fogja együttműködünk egy kis héjparancsfájlt, amely néhány alapvető Azure CLI-parancsokat használhatja az Azure Storage-erőforrások. A szkript először létrehoz egy új tárolót a storage-fiókban, majd feltölti a már meglévő fájl (blob) az adott tárolóhoz. Ezután kilistázza a tárolóban lévő összes BLOB, és végül letölti a fájlt a helyi számítógépen, amely megad egy célhelyre.

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

**Konfigurálja és futtassa a parancsfájlt**

1. Nyissa meg a kedvenc szövegszerkesztőjével, majd másolja és illessze be az előző parancsfájlt a szerkesztőbe.

2. Ezután frissítse a parancsfájl-változókat, hogy a konfigurációs beállítások. Cserélje le a következő értékeket a megadott módon:

   * **\<tárfiók_neve\>**  a tárfiók nevére.
   * **\<storage_account_key\>**  a tárfiók elsődleges vagy másodlagos hozzáférési kulcsára.
   * **\<container_name\>**  egy nevet az új tárolót szeretne létrehozni, például az "azure-cli-példa-container".
   * **\<blob_name\>**  a cél blob tároló nevét.
   * **\<file_to_upload\>**  elérési útját a kis fájlt a helyi számítógépen, például a "~ / images/HelloWorld.png".
   * **\<destination_file\>**  a cél elérési út, például a "~ / downloadedImage.png".

3. A szükséges változók frissítése után mentse a parancsfájlt, és lépjen ki a saját szerkesztőben. A következő lépések azt feltételezik, hogy a parancsfájl nevű **my_storage_sample.sh**.

4. Megjelölés végrehajtható, mint a szkriptet, szükség esetén: `chmod +x my_storage_sample.sh`

5. A szkript végrehajtása. Ha például a Bash: `./my_storage_sample.sh`

A következőhöz hasonló kimenetnek kell megjelennie, és a **\<destination_file\>** az a parancsfájl meg kell jelennie a helyi számítógépen.

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
> A fenti kimeneti van **tábla** formátumban. Megadhatja, amely a kimeneti formátum használatára megadásával a `--output` argumentum a CLI-parancsok a, vagy állítsa be globálisan `az configure`.
>

## <a name="manage-storage-accounts"></a>Tárfiókok kezelése

### <a name="create-a-new-storage-account"></a>Új tárfiók létrehozása
Az Azure Storage használatához tárfiókra van szüksége. A számítógép konfigurálása után létrehozhat egy új Azure Storage-fiók [csatlakozzon az előfizetéséhez](#connect-to-your-azure-subscription).

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location` [Kötelező]: A hely. Például "West US".
* `--name` [Kötelező]: A tárfiók neve. A neve 3 – 24 karakter hosszúságú lehet, és csak kisbetűs alfanumerikus karaktereket használjon.
* `--resource-group` [Kötelező]: Erőforráscsoport neve.
* `--sku` [Kötelező]: A tárfiók SKU-JÁNAK. Megengedett értékek:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`

### <a name="set-default-azure-storage-account-environment-variables"></a>Az Azure storage-fiók alapértelmezett környezeti változók beállítása

Több tárfiók rendelkezhet az Azure-előfizetésében. Azokat az összes későbbi tárolási parancsra vonatkozó egyikét választhatja ki, ezeket a környezeti változókat állíthatja be:

Elsőként jelenítse meg a tárfiókkulcsokat az [az storage account keys list](/cli/azure/storage/account/keys) parancs segítségével:

```azurecli-interactive
az storage account keys list \
    --account-name <account_name> \
    --resource-group <resource_group> \
    --output table
```

Most, hogy a kulcs, környezeti változókként, és a fiók nevét adhatja meg:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_KEY=<key>
```

Egy alapértelmezett tárfiókot be egy másik úgy, hogy a kapcsolati karakterlánc használatával. Először kérje le a kapcsolati karakterláncot a `show-connection-string` parancsot:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Ezután másolja ki a kimeneti kapcsolati karakterláncot, és állítsa be a `AZURE_STORAGE_CONNECTION_STRING` (szüksége lehet a kapcsolati karakterlánc tegye idézőjelek között) környezeti változó:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> Ez a cikk a következő szakaszokban az összes példák azt feltételezik, hogy beállított a `AZURE_STORAGE_ACCOUNT` és `AZURE_STORAGE_KEY` környezeti változókat.

## <a name="create-and-manage-blobs"></a>Hozzon létre és kezelheti a blobokat
Az Azure Blob storage szolgáltatás nagy mennyiségű strukturálatlan adat, például szöveg vagy bináris adatot, amely segítségével bárhonnan elérhetők HTTP- vagy HTTPS keresztül a világon tárolásához. Ez a szakasz azt feltételezi, hogy már ismeri az Azure Blob-tárolóval kapcsolatos fogalmak. Részletes információkért lásd: [.NET használatával az Azure Blob storage használatának első lépései](../blobs/storage-dotnet-how-to-use-blobs.md) és [Blob szolgáltatással kapcsolatos fogalmak](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Tároló létrehozása
Az Azure BLOB storage összes blobjának egy tárolóban kell lennie. Használatával is létrehozhat egy tárolót a `az storage container create` parancsot:

```azurecli
az storage container create --name <container_name>
```

Beállíthat egy három szintje olvasási hozzáférés egy új tárolót megadásával a választható `--public-access` argumentum:

* `off` (alapértelmezett): Tároló adatok csak a fiók tulajdonosától.
* `blob`: Nyilvános olvasási hozzáférés blobok számára.
* `container`: A teljes tárolót nyilvános olvasási és a lista hozzáférést.

További információkért lás a [tárolók és blobok névtelen olvasási hozzáférésének kezelésével](../blobs/storage-manage-access-to-resources.md) foglalkozó témakört.

### <a name="upload-a-blob-to-a-container"></a>Blob feltöltése tárolóba
Az Azure Blob storage támogatja a blokk, hozzáfűzése, és a lapblobokat. Blobok feltöltése a tárolóba a `blob upload` parancsot:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

Ha közvetlenül feltöltése egy mappát a tárolóban, a storage-fiókban, szeretné, cserélje le a `--name <blob_name>` a `--name <folder/blob_name>`.

 Alapértelmezés szerint a `blob upload` parancs feltölti *.vhd fájlok lapblobok, illetve egyéb a blokkblobok használatát támogatják. Adjon meg egy másik típusú, ha feltölt egy blobot, használhatja a `--type` argumentum – engedélyezett értékek `append`, `block`, és `page`.

 A különböző blobtípusok további információkért lásd: [Understanding Block Blobs, hozzáfűző blobokat és Lapblobokat](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).


### <a name="download-a-blob-from-a-container"></a>Blob letöltése tárolóból
Ebben a példában a blob letöltése tárolóból mutatja be:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

A tárolóban lévő blobok listázása a [az storage blob list](/cli/azure/storage/blob#az_storage_blob_list) parancsot.

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

A fenti példában a céltároló már léteznie kell a cél tárfiók sikeres másolási művelet. Ezenkívül a `--source-uri` argumentumban megadott forrásblobnak tartalmaznia kell egy közös hozzáférésű jogosultságkód (SAS-) tokent, vagy nyilvánosnak kell lennie, ahogy ebben a példában is szerepel.

### <a name="delete-a-blob"></a>Blob törlése
A blob törléséhez használja a `blob delete` parancsot:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

## <a name="create-and-manage-file-shares"></a>Hozzon létre, és a fájlmegosztások felügyelete
Az Azure Files a Server Message Block (SMB) protokollt használó alkalmazások számára közös tárhelyet kínál. A Microsoft Azure virtuális gépek és felhőszolgáltatások, valamint a helyszíni alkalmazásokat, oszthatnak meg keresztül csatlakoztatott megosztások. Fájlmegosztások és a fájladatok az Azure CLI-n keresztül is kezelheti. További információ az Azure Files: [Bevezetés az Azure Files használatába](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Fájlmegosztás létrehozása
Azure-fájlmegosztások az SMB-fájlmegosztás az Azure-ban. Minden könyvtárnak és fájlnak léteznie kell egy fájlmegosztásban. Egy fiók korlátlan számú megosztást tartalmazhat, és a egy megosztás korlátlan számú fájl, egészen a tárfiók kapacitásának korlátjáig tárolhatja. A következő példában létrehozunk egy nevű fájlmegosztást **myshare**.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Könyvtár létrehozása
Egy címtárat az Azure-fájlmegosztások hierarchikus struktúrát biztosít. A következő példában létrehozunk egy nevű könyvtárat **könyvtárnév** a fájlmegosztásban.

```azurecli
az storage directory create --name myDir --share-name myshare
```

Az elérési út például több szinten is tartalmazhat **dir1/dir2**. Azonban biztosítania kell, hogy az összes szülőkönyvtár létezik alkönyvtárak létrehozása előtt. Elérési út esetében például **dir1/dir2**, először létre kell hoznia directory **dir1**, majd hozza létre a könyvtár **dir2**.

### <a name="upload-a-local-file-to-a-share"></a>Helyi fájl feltöltése a megosztásba
A következő példa feltölti a fájlt **~/temp/samplefile.txt** gyökérmappája a **myshare** fájlmegosztást. A `--source` argumentum, adja meg a meglévő helyi fájl feltöltése.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Igény szerint a címtár létrehozása, megadhat a megosztás feltölteni a fájlt a meglévő címtárhoz a megosztáson belüli belül az elérési út:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

A megosztásban található fájl akár 1 TB méretű is lehet.

### <a name="list-the-files-in-a-share"></a>Az olyan megosztáson található fájlok listázása
Listázhatja fájlok és könyvtárak egy megosztás használatával a `az storage file list` parancsot:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Fájlok másolása      
Fájl másolása másik fájlba, egy fájlt egy blobba vagy egy blobot egy fájlba. Például egy fájl másolása egy másik megosztást a könyvtárba:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Megosztási pillanatkép létrehozása
Megosztási pillanatképek használatával is létrehozhat a `az storage share snapshot` parancsot:

```cli
az storage share snapshot -n <share name>
```

Kimeneti példa
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

Megosztási pillanatképek egy adott megosztás használatával listázhatja `az storage share list --include-snapshots`

```cli
az storage share list --include-snapshots
```

**Kimeneti példa**
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
Egy bizonyos megosztási pillanatkép-tartalom használatával megtekintéséhez be is böngészhet `az storage file list`. Egy megosztás nevének megadására van `--share-name <snare name>` és az időbélyegző `--snapshot '2017-10-04T19:45:18.0000000Z'`

```azurecli-interactive
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

**Kimeneti példa**
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
### <a name="restore-from-share-snapshots"></a>Visszaállítása megosztási pillanatképekből

Visszaállíthatja a fájl másolásával, vagy egy pillanatkép segítségével megosztásban található fájl letöltése `az storage file download` parancs

```azurecli-interactive
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```
**Kimeneti példa**
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
Megosztási pillanatképek használatával törölheti az `az storage share delete` parancs megadásával `--snapshot` paraméterrel rendelkező megosztási pillanatkép időbélyeg:

```cli
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Kimeneti példa
```json
{
  "deleted": true
}
```

## <a name="next-steps"></a>További lépések
Az alábbiakban néhány további források további információra van szüksége az Azure CLI-vel kapcsolatban. 

* [Azure CLI használatának első lépései](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Az Azure CLI parancsdokumentációja](/cli/azure)
* [Az Azure CLI a Githubon](https://github.com/Azure/azure-cli)

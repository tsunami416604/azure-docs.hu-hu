---
title: "Műveletek elvégzése az Azure Blob Storage-ben (objektumtár) az Azure CLI-vel | Microsoft Docs"
description: "Megtudhatja, hogyan tölthet fel és le blobokat az Azure Blob Storage-ben, illetve hogyan hozhat létre közös hozzáférésű jogosultságkódot (SAS) a tárfiókban szereplő blobok hozzáférésének felügyeletéhez."
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: hero-article
ms.date: 06/15/2017
ms.author: tamram
ms.openlocfilehash: 4f781508652ee784fff61c8ecc76cb87ea47f0c2
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="perform-blob-storage-operations-with-azure-cli"></a>Blob Storage-műveletek elvégzése az Azure CLI-n

Az Azure Blob Storage szolgáltatás strukturálatlan adatok, például szövegek vagy bináris adatok nagy mennyiségben történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolaton keresztül a világon bárhonnan elérhetők. Ez az oktatóprogram az Azure Blog Storage olyan alapszintű műveleteit ismerteti, mint a feltöltés, a letöltés és a blobok törlése. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Tároló létrehozása
> * Fájl (blob) feltöltése tárolóba
> * A tárolóban lévő blobok listázása
> * Blob letöltése tárolóból
> * Blob másolása tárfiókok között
> * Blob törlése
> * A blob tulajdonságainak és metaadatainak megjelenítése és módosítása
> * Biztonságkezelés közös hozzáférésű jogosultságkód (SAS) használatával

Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Tároló létrehozása

A tárolók hasonlók a számítógép könyvtáraihoz, és a segítségükkel blobok csoportjait rendszerezheti egy tárolóban úgy, ahogy a fájlokat rendszerezi egy könyvtárban. Egy tárfiók tetszőleges számú tárolót tartalmazhat. Egy tárolóban 500 TB-nyi blobadatot tárolhat, amely a tárfiókban tárolható maximális adatmennyiségnek felel meg.

Hozzon létre blobok tárolására alkalmas tárolót az [az storage container create](/cli/azure/storage/container#az_storage_container_create) parancs segítségével.

```azurecli-interactive
az storage container create --name mystoragecontainer
```

A tároló nevének betűvel vagy számmal kell kezdődnie, és csak betűket, számokat és kötőjelet (-) tartalmazhat. A blobok és tárolók elnevezésével kapcsolatos szabályok teljes listáját lásd a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) foglalkozó cikket.

## <a name="enable-public-read-access-for-a-container"></a>Nyilvános olvasási hozzáférés engedélyezése egy tárolóhoz

Az újonnan létrehozott tároló alapértelmezés szerint magánjellegű. Ez azt jelenti, hogy senki sem férhet hozzá a tárolóhoz [közös hozzáférésű jogosultságkód](#create-a-shared-access-signature-sas) vagy a tárfiók hozzáférési kulcsai nélkül. Az Azure CLI használatával ezt megváltoztathatja, ha a tároló engedélyeit az alábbi három szint egyikére módosítja:

| | |
|---|---|
| `--public-access off` | (Alapértelmezett) Nyilvános olvasási hozzáférés letiltva |
| `--public-access blob` | Nyilvános olvasási hozzáférés csak a blobokhoz |
| `--public-access container` | Nyilvános olvasási hozzáférés a blobokhoz, tárolóban található blobok listájának lekérése |

Ha a nyilvános hozzáférésnél `blob` vagy `container` értéket ad meg, azzal az olvasási hozzáférést mindenki számára engedélyezi az interneten. Ha például blobként tárolt képeket szeretne megjeleníteni a weboldalán, engedélyeznie kell a nyilvános olvasási hozzáférést. Ha olvasási/írási hozzáférést szeretne engedélyezni, akkor [közös hozzáférésű jogosultságkódot (SAS)](#create-a-shared-access-signature-sas) kell használnia.

Engedélyezze a nyilvános olvasási hozzáférést a tárolóhoz az [az storage container set-permission](/cli/azure/storage/container#az_storage_container_create) paranccsal.

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access container
```

## <a name="upload-a-blob-to-a-container"></a>Blob feltöltése tárolóba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. A blokkblobok az Azure Storage-ban tárolt leggyakoribb blobtípusok. A hozzáfűző blobokat akkor használjuk, ha meglévő blobokhoz adatokat szeretnénk hozzáadni a meglévő tartalom módosítása nélkül (például naplózáshoz). A lapblobok az IaaS virtuális gépek VHD fájljait támogatják.

Ebben a példában egy blobot töltünk fel a legutóbbi lépésben, az [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) paranccsal létrehozott tárolóba.

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Ez az eljárás létrehozza a blobot, ha az még nem létezett, és felülírja, ha már igen. Töltsön fel több fájlt, így a következő lépésben, a blobok listázásakor több bejegyzés is látható lesz.

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

Listázza ki a tárolóban található blobokat az [az storage blob list](/cli/azure/storage/blob#az_storage_blob_list) paranccsal.

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

Példa a kimenetre:

```
Name            Blob Type      Length  Content Type              Last Modified
--------------  -----------  --------  ------------------------  -------------------------
ISSUE_TEMPLATE  BlockBlob         761  application/octet-stream  2017-04-21T18:29:50+00:00
LICENSE         BlockBlob       18653  application/octet-stream  2017-04-21T18:28:50+00:00
LICENSE-CODE    BlockBlob        1090  application/octet-stream  2017-04-21T18:29:02+00:00
README.md       BlockBlob        6700  application/octet-stream  2017-04-21T18:27:33+00:00
dir1/file1.txt  BlockBlob        6700  application/octet-stream  2017-04-21T18:32:51+00:00
```

## <a name="download-a-blob"></a>Blob letöltése

Töltse le az előző lépésben feltöltött blobot az [az storage blob download](/cli/azure/storage/blob#az_storage_blob_download) paranccsal.

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="copy-a-blob-between-storage-accounts"></a>Blob másolása tárfiókok között

Tárfiókokon és régiókon belül vagy azok között aszinkron módon másolhatja át a blobokat.

Az alábbi példa azt mutatja be, hogyan másolhat át blobokat egyik tárfiókból a másikba. Először a forrás tárfiókban hozunk létre tárolót, az abban lévő blobok számára pedig nyilvános olvasási hozzáférést adunk. Ezt követően feltöltünk egy fájlt a tárolóba, végül átmásoljuk a blobot ebből a tárolóból a céltárfiókban lévő tárolóba.

Ebben a példában ahhoz, hogy a másolás sikeresen megtörténhessen, a céltárolónak már léteznie kell a céltárfiókban. Ezenkívül a `--source-uri` argumentumban megadott forrásblobnak tartalmaznia kell egy közös hozzáférésű jogosultságkód (SAS-) tokent, vagy nyilvánosnak kell lennie, ahogy ebben a példában is szerepel.

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
    --file ~/path/to/sourcefile \
    --name sourcefile

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

## <a name="delete-a-blob"></a>Blob törlése

Törölje a blobot a tárolóból az [az storage blob delete](/cli/azure/storage/blob#az_storage_blob_delete) paranccsal.

```azurecli-interactive
az storage blob delete \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="set-the-content-type"></a>A tartalomtípus beállítása

A tartalomtípus (más néven MIME-típus) a blobban lévő adatok formátumát azonosítja. A böngészők és egyéb szoftverek a tartalomtípus segítségével állapítják meg az adatok feldolgozásának a módját. Az alábbi példa a tartalomtípust `image/png` értékűre állítja be.

```azurecli-interactive
# Set the content type
az storage blob update
    --container-name mystoragecontainer 
    --name blobName 
    --content-type image/png
```

## <a name="display-and-modify-blob-properties-and-metadata"></a>A blob tulajdonságainak és metaadatainak megjelenítése és módosítása

A blobok számos, szolgáltatás által definiált tulajdonsággal rendelkeznek, amelyeket az [az storage blob show](/cli/azure/storage/blob#az_storage_blob_show) paranccsal jeleníthet meg, így például a név, típus, hossz és egyebek. A blobokat saját tulajdonságok és azok értékei segítségével is konfigurálhatja az [az storage blob metadata update](/cli/azure/storage/blob/metadata#az_storage_blob_metadata_update) paranccsal.

Ebben a példában először megjelenítjük az adott blob szolgáltatás által definiált tulajdonságait, majd saját metaadat-tulajdonságaink közül kettővel frissítjük a blobot. Végül az [az storage blob metadata show](/cli/azure/storage/blob/metadata#az_storage_blob_metadata_show) paranccsal megjelenítjük a blob metaadat-tulajdonságait és azok értékeit.

```azurecli-interactive
# Show properties of a blob
az storage blob show \
    --container-name mystoragecontainer \
    --name blobName \
    --output table

# Set metadata properties of a blob (replaces all existing metadata)
az storage blob metadata update \
    --container-name mystoragecontainer \
    --name blobName \
    --metadata "key1=value1" "key2=value2"

# Show metadata properties of a blob
az storage blob metadata show \
    --container-name mystoragecontainer \
    --name blobName
```

## <a name="create-a-shared-access-signature-sas"></a>Közös hozzáférésű jogosultságkód (SAS) létrehozása

A közös hozzáférésű jogosultságkód (SAS) segítségével korlátozott hozzáférési lehetőséget biztosíthat a tárfiókjában található objektumokhoz anélkül, hogy ki kellene adnia a tárfiók hívóbetűjét. A személyes erőforrásokhoz való hozzáférést engedélyező URI előállításához létre kell hoznia egy, a kívánt engedélyekkel és érvényességi időkerettel (tényleges időtartam) rendelkező SAS-tokent, majd lekérdezési karakterláncként kell hozzáfűznie azt az adott erőforrás URL-címéhez, így megalkotva annak teljes SAS URI-ját. Az SAS URI (az erőforrás URL-címe és az SAS-token együttesének) birtokában ezt követően bárki hozzáférhet az adott erőforráshoz, az SAS-token érvényességi időkeretén belül. Előfordulhat például, hogy valakinek olvasási hozzáférést kíván adni két percre egy személyes blobhoz, annak megtekintéséhez.

A következő lépésekben letiltjuk a tároló nyilvános hozzáférését, tesztelni fogjuk a csak magánjellegű hozzáférést, majd pedig létrehozunk és tesztelünk egy SAS URI-t.

### <a name="disable-container-public-access"></a>A tároló nyilvános hozzáférésének letiltása

Először állítsuk a tároló hozzáférési szintjét `off` értékre. Ez azt jelzi, hogy közös hozzáférésű jogosultságkód vagy a tárfiók hívóbetűje nélkül nem lehet hozzáférni a tárolóhoz és annak blobjaihoz.

```azurecli-interactive
az storage container set-permission \
    --name mystoragecontainer \
    --public-access off
```

### <a name="verify-private-access"></a>Magánjellegű hozzáférés ellenőrzése

Annak ellenőrzéséhez, hogy az adott tárolóban lévő blobhoz nincs nyilvános olvasási hozzáférés rendelve, az [az storage blob url](/cli/azure/storage/blob#az_storage_blob_url) paranccsal kérje le a tároló egyik blobjának URL-címét.

```azurecli-interactive
az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv
```

Egy inkognitóablakban nyissa meg a blob URL-címét. Ha az adott blob magánjellegű, és nem adott meg közös hozzáférésű jogosultságkódot, akkor a rendszer `ResourceNotFound` hibát ad vissza.

### <a name="create-a-sas-uri"></a>SAS URI létrehozása

Most egy olyan SAS URI-t hozunk létre, amellyel hozzáférést adhat a blobhoz. Az alábbi példában az [az storage blob url](/cli/azure/storage/blob#az_storage_blob_url) paranccsal az egyik változónál a blob URL-címét, egy másik változónál pedig az [az storage blob generate-sas](/cli/azure/storage/blob#az_storage_blob_generate_sas) paranccsal létrehozott SAS-tokent adjuk meg. Végül a két, `?` lekérdezésikarakterlánc-elválasztóval elkülönített változó összefűzésével előállítjuk a blob teljes SAS URI-ját.

```azurecli-interactive
# Get UTC datetimes for SAS start and expiry (Example: 1994-11-05T13:15:30Z)
sas_start=`date -u +'%Y-%m-%dT%H:%M:%SZ'`
sas_expiry=`date -u +'%Y-%m-%dT%H:%M:%SZ' -d '+2 minute'`

# Get the URL for the blob
blob_url=`az storage blob url \
    --container-name mystoragecontainer \
    --name blobName \
    --output tsv`

# Obtain a SAS token granting read (r) access between the
# SAS start and expiry times
sas_token=`az storage blob generate-sas \
    --container-name mystoragecontainer \
    --name blobName \
    --start $sas_start \
    --expiry $sas_expiry \
    --permissions r \
    --output tsv`

# Display the full SAS URI for the blob
echo $blob_url?$sas_token
```

### <a name="test-the-sas-uri"></a>Az SAS URI tesztelése

Egy inkognitóablakban nyissa meg az `echo` paranccsal az előző kódrészletben megjelenített teljes SAS URI-t. Ekkor a rendszer már nem ad vissza hibát, és megtekintheti vagy letöltheti az adott blobot.

Várjon az URL-cím lejártáig (ebben a példában ez 2 perc), majd egy másik inkognitóablakban nyissa meg az URI-t. A rendszer ekkor `AuthenticationFailed` hibát ad vissza, mivel az SAS-token lejárt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportjában lévő egyik erőforrásra sem (beleértve a létrehozott tárfiókot és a jelen oktatóanyagban esetlegesen feltöltött blobokat is), törölje az erőforráscsoportot az [az group delete](/cli/azure/group#az_group_delete) paranccsal.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megismerhette a blobok Azure Storage-ban való használatának alapjait:

> [!div class="checklist"]
> * Tároló létrehozása
> * Fájl (blob) feltöltése tárolóba
> * A tárolóban lévő blobok listázása
> * Blob letöltése tárolóból
> * Blob másolása tárfiókok között
> * Blob törlése
> * A blob tulajdonságainak és metaadatainak megjelenítése és módosítása
> * Biztonságkezelés közös hozzáférésű jogosultságkód (SAS) használatával

Az alábbi erőforrásokban további információkat talál az Azure CLI, valamint a tárfiókban található erőforrások használatáról.

* Azure CLI
  * [Bejelentkezés az Azure CLI 2.0 használatával](/cli/azure/authenticate-azure-cli) – Ismerkedjen meg a parancssori felület használatával történő hitelesítés különböző módszereivel, például az [egyszerű szolgáltatáson](/cli/azure/authenticate-azure-cli#logging-in-with-a-service-principal) keresztül történő, nem interaktív bejelentkezéssel a felügyelet nélküli Azure CLI-szkriptek futtatásához.
  * [Az Azure CLI 2.0 parancsdokumentációja](/cli/azure/)
* Microsoft Azure Storage Explorer
  * A [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.

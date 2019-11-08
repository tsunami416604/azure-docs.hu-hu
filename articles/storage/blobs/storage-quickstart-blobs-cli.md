---
title: Azure rövid útmutató – Blob létrehozása objektumtárban Azure CLI használatával | Microsoft Docs
description: Ebből a rövid útmutatóból megtudhatja, hogyan tölthet fel blobokat az Azure Storage-ba az Azure CLI használatával, hogyan töltheti le a blobokat, és hogyan listázhatja a tárolóban lévő blobokat.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/06/2019
ms.author: tamram
ms.openlocfilehash: 1f3143eced90f97c090c0005375ef50fe48c5f5f
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747916"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>Rövid útmutató: Blobok feltöltése, letöltése és listázása az Azure CLI-vel

Az Azure CLI az Azure parancssori felülete, amely Azure-erőforrások kezelésére szolgál. Használhatja a böngészőjében az Azure Cloud Shell-lel. Vagy telepítheti macOS, Linux és Windows rendszeren, és futtathatja a parancssorból. Ebből a rövid útmutatóból megtudhatja, hogyan lehet az Azure CLI használatával adatokat fel- és letölteni az Azure Blob Storage-be.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. Futtassa az `az --version` parancsot a verzió meghatározásához. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Tároló létrehozása

A blobok minden esetben egy tárolóba lesznek feltöltve. A blobok csoportjait hasonló módon rendszerezheti, mint a fájlokat a számítógép mappáiban.

Hozzon létre blobok tárolására alkalmas tárolót az [az storage container create](/cli/azure/storage/container) parancs segítségével.

```azurecli-interactive
az storage container create --name sample-container
```

## <a name="upload-a-blob"></a>Blob feltöltése

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. Az ebben a rövid útmutatóban szereplő példák azt mutatják be, hogyan használhatók a blokkos Blobok.

Először hozzon létre egy fájlt, amelyet egy blokk blobba kíván feltölteni. Ha Azure Cloud Shell használ, a következő paranccsal hozhat létre egy fájlt:

```bash
vi helloworld
```

A fájl megnyitásakor nyomja le a **Beszúrás**gombot. Írja be a *Hello World*értéket, majd nyomja le az **ESC**billentyűt. Ezután írja be a következőt *: x*, majd nyomja le az **ENTER**billentyűt.

Ebben a példában egy blobot töltünk fel a legutóbbi lépésben, az [az storage blob upload](/cli/azure/storage/blob) paranccsal létrehozott tárolóba. A fájl elérési útját nem szükséges megadnia, mert a fájl a gyökérkönyvtárban lett létrehozva:

```azurecli-interactive
az storage blob upload \
    --container-name sample-container \
    --name helloworld \
    --file helloworld
```

Ez az eljárás létrehozza a blobot, ha az még nem létezett, és felülírja, ha már igen. Mielőtt továbblépne, töltsön fel annyi fájlt, amennyit csak szeretne.

Ha egyszerre több fájlt szeretne feltölteni, használhatja az [az storage blob upload-batch](/cli/azure/storage/blob) parancsot.

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

Listázza ki a tárolóban található blobokat az [az storage blob list](/cli/azure/storage/blob) paranccsal.

```azurecli-interactive
az storage blob list \
    --container-name sample-container \
    --output table
```

## <a name="download-a-blob"></a>Blob letöltése

Az [az storage blob download](/cli/azure/storage/blob) paranccsal letöltheti a korábban feltöltött blobot.

```azurecli-interactive
az storage blob download \
    --container-name sample-container \
    --name helloworld \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Adatátvitel az AzCopy használatával

Az [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) segédprogram az Azure Storage esetében egy további lehetőséget kínál az adatok nagy teljesítményű, parancsfájlalapú átvitelére. Az AzCopy segítségével blob, fájl és tábla típusú tárolókból és tárolókba vihet át adatokat.

A következő példa a AzCopy használatával tölt fel egy *sajat. txt* nevű fájlt a *minta-Container* tárolóba. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://<account-name>.blob.core.windows.net/sample-container \
    --dest-key <account-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoport egyik erőforrására sem, beleértve az ebben a rövid útmutatóban létrehozott Storage-fiókot, törölje az erőforráscsoportot az az [Group delete](/cli/azure/group) paranccsal. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```azurecli-interactive
az group delete --name <resource-group-name>
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan vihetők át fájlok egy helyi fájlrendszer és egy tároló az Azure Blob Storage szolgáltatásban. Ha bővebb információra van szüksége a blobok Azure Storage-beli használatával kapcsolatban, lépjen tovább az Azure Blob Storage használatáról szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Útmutató: Blob Storage-műveletek elvégzése az Azure CLI-vel](storage-how-to-use-blobs-cli.md)

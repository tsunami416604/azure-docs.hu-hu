---
title: "Azure gyors üzembe helyezés - átviteli objektumok az Azure Blob Storage tárolóban az Azure parancssori felülettel |} Microsoft Docs"
description: "Gyorsan további átvitele az objektumok és a Azure Blob Storage tárolóban az Azure parancssori felület használatával"
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/19/2017
ms.author: marsma
ms.openlocfilehash: c9b7e7a1fbc6b67821183ce31bdf2527de490c92
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-the-azure-cli"></a>Átviteli objektumok az Azure Blob Storage tárolóban az Azure parancssori felület használatával

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Az adatok le, és az Azure Blob storage- és feltöltése az Azure parancssori felület használatának gyors üzembe helyezés adatokat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Tároló létrehozása

Blobok egy tárolóba mindig feltöltése. Tárolók lehetővé teszik a csoportokba rendezheti a blobok például rendezheti a könyvtárakban található fájlokat a számítógépre.

Hozzon létre blobok tárolására alkalmas tárolót az [az storage container create](/cli/azure/storage/container#create) parancs segítségével.

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Egy blob feltöltése

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. A Blob storage-ban tárolt fájlok többsége blokkblobként tárolják. Hozzáfűző blobok használt adatokat hozzá kell adni egy meglévő blob nélkül módosítja a meglévő tartalom, mint például a naplózás. A lapblobok az IaaS virtuális gépek VHD fájljait támogatják.

Ebben a példában azt egy blob feltöltése a tárolóba és az előző lépésben létrehozott a [az tárolási blob feltöltése](/cli/azure/storage/blob#upload) parancsot.

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Ez az eljárás létrehozza a blobot, ha az még nem létezett, és felülírja, ha már igen. A folytatás előtt tetszés szerinti számú fájlok feltöltése.

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

Listázza ki a tárolóban található blobokat az [az storage blob list](/cli/azure/storage/blob#list) paranccsal.

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Blob letöltése

Használja a [az tárolási blob letöltési](/cli/azure/storage/blob#download) korábban feltöltött parancs blob letöltéséhez.

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Bejövő adatátvitel az AzCopy

A [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) segédprogram lehetősége, hogy az Azure Storage nagy teljesítményű parancsfájlok futtatására alkalmas adatok átvitelét. AzCopy vihet át adatokat, illetve onnan Blob, a fájl és a Table storage.

Gyors példaként, itt van az AzCopy parancs feltölteni a fájlt nevű *sajatfajl.txt* számára a *mystoragecontainer* tároló.

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://mystorageaccount.blob.core.windows.net/mystoragecontainer \
    --dest-key <storage-account-access-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforrásokat az erőforráscsoportban, beleértve a gyors üzembe helyezés létrehozott tárfiók törlése az erőforráscsoport a [az csoport törlése](/cli/azure/group#delete) parancsot.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés megtanulta, hogyan viheti át a fájlok helyi lemezre és az Azure Blob storage tárolója között. További információt az Azure Storage blobs használata, továbbra is az oktatóanyag az Azure Blob storage használata.

> [!div class="nextstepaction"]
> [Hogyan: Blob-tároló műveletek az Azure parancssori felülettel](storage-how-to-use-blobs-cli.md)

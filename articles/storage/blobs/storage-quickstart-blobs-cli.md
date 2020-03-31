---
title: Rövid útmutató – Blob létrehozása az Azure CLI-vel
titleSuffix: Azure Storage
description: Ebben a rövid útmutatóban megtudhatja, hogyan használhatja az Azure CLI-t egy blob feltöltésével az Azure Storage-ba, töltsön le egy blobot, és sorolja fel a blobokat egy tárolóban.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: tamram
ms.openlocfilehash: 2e1b1ac2ea315759b18dc882b98837bca0a84d46
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061440"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Rövid útmutató: Blobok létrehozása, letöltése és listázása az Azure CLI-vel

Az Azure CLI az Azure parancssori felülete, amely Azure-erőforrások kezelésére szolgál. Használhatja a böngészőjében az Azure Cloud Shell-lel. Vagy telepítheti macOS, Linux és Windows rendszeren, és futtathatja a parancssorból. Ebből a rövid útmutatóból megtudhatja, hogyan lehet az Azure CLI használatával adatokat fel- és letölteni az Azure Blob Storage-be.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>Az Azure CLI helyi telepítése

Ha úgy dönt, hogy telepíti és használja az Azure CLI helyileg, ez a rövid útmutató megköveteli, hogy az Azure CLI 2.0.46-os vagy újabb verzióját. Futtassa az `az --version` parancsot a verzió meghatározásához. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

Ha az Azure CLI helyileg fut, be kell jelentkeznie, és hitelesítenie kell. Ez a lépés nem szükséges, ha az Azure Cloud Shell használata. Az Azure CLI-be `az login` való bejelentkezéshez futtassa és hitelesítse magát a böngészőablakban:

```azurecli
az login
```

Az Azure CLI-vel való hitelesítésről további információt a [Bejelentkezés az Azure CLI-vel](/cli/azure/authenticate-azure-cli)című témakörben talál.

## <a name="authorize-access-to-blob-storage"></a>Hozzáférés engedélyezése a Blob-tárolóhoz

Engedélyezheti a Blob storage-hoz való hozzáférést az Azure CLI-ből az Azure AD hitelesítő adataival vagy a tárfiók hozzáférési kulcshasználatával. Az Azure AD hitelesítő adatok használata ajánlott. Ez a cikk bemutatja, hogyan engedélyezheti a Blob storage-műveleteket az Azure AD használatával.

Az Azure CLI-parancsok a Blob `--auth-mode` storage elleni adatműveletekhez támogatják a paramétert, amely lehetővé teszi egy adott művelet engedélyezésének módját. Állítsa `--auth-mode` be `login` a paramétert az Azure AD-hitelesítő adatokkal való engedélyezésre. További információ: [Hozzáférés engedélyezése blob- vagy várólista-adatokhoz az Azure CLI segítségével.](../common/authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Csak a Blob storage-adatműveletek támogatják a `--auth-mode` paramétert. Felügyeleti műveletek, például egy erőforráscsoport vagy tárfiók létrehozása, automatikusan használja az Azure AD hitelesítő adatok at engedélyezéshez.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Create a storage account

Az [az storage account create](/cli/azure/storage/account) paranccsal hozzon létre egy általános célú tárfiókot. Az általános célú tárfiók mind a négy szolgáltatással (blobok, fájlok, táblák és üzenetsorok) használható.

Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption blob
```

## <a name="create-a-container"></a>Tároló létrehozása

A blobok minden esetben egy tárolóba lesznek feltöltve. A blobcsoportokat a tárolókban is rendszerezheti, hasonlóan ahhoz, ahogyan a számítógépen lévő fájlokat mappákba rendezi.

Hozzon létre blobok tárolására alkalmas tárolót az [az storage container create](/cli/azure/storage/container) parancs segítségével. Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

## <a name="upload-a-blob"></a>Blob feltöltése

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. A rövid útmutató példái bemutatják, hogyan működik a blokkblobok.

Először hozzon létre egy fájlt feltölteni egy blokk blobba. Ha Az Azure Cloud Shellt használja, a következő paranccsal hozzon létre egy fájlt:

```bash
vi helloworld
```

Amikor a fájl megnyílik, nyomja le a **insert**. Írja be *a Hello world*, majd nyomja meg az **Esc**. Ezután írja be *az :x*, majd nyomja **le az Enter billentyűt.**

Ebben a példában egy blobot töltünk fel a legutóbbi lépésben, az [az storage blob upload](/cli/azure/storage/blob) paranccsal létrehozott tárolóba. Nem szükséges megadni a fájl elérési útját, mivel a fájl a gyökérkönyvtárban lett létrehozva. Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

Ez az eljárás létrehozza a blobot, ha az még nem létezett, és felülírja, ha már igen. Mielőtt továbblépne, töltsön fel annyi fájlt, amennyit csak szeretne.

Ha egyszerre több fájlt szeretne feltölteni, használhatja az [az storage blob upload-batch](/cli/azure/storage/blob) parancsot.

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása

Listázza ki a tárolóban található blobokat az [az storage blob list](/cli/azure/storage/blob) paranccsal. Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Blob letöltése

Az [az storage blob download](/cli/azure/storage/blob) paranccsal letöltheti a korábban feltöltött blobot. Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Adatátvitel az AzCopy használatával

Az AzCopy parancssori segédprogram nagy teljesítményű, parancsfájlalapú adatátvitelt kínál az Azure Storage számára. Az AzCopy segítségével adatokat vihet át a Blob storage és az Azure Files. Az AzCopy legújabb verziójáról, az AzCopy programmal kapcsolatos további tudnivalókért olvassa el az AzCopy – Első lépések az [AzCopy programot című témakört.](../common/storage-use-azcopy-v10.md) Az AzCopy v10 blobtármal való használatáról az [Adatok átvitele az AzCopy és a Blob storage használatával (Átvitel az AzCopy és a Blob storage) témakörben](../common/storage-use-azcopy-blobs.md)olvashat.

A következő példa az AzCopy segítségével feltölt egy helyi fájlt egy blobba. Ne felejtse el helyettesíteni a mintaértékeket a saját értékeivel:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a rövid útmutató részeként létrehozott erőforrásokat, beleértve a tárfiókot is, törölje az erőforráscsoportot az [az csoport törlése](/cli/azure/group) paranccsal. Ne felejtse el a szögletes zárójelekben lévő helyőrző értékeket a saját értékeire cserélni:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan vihet át fájlokat egy helyi fájlrendszer és egy tároló között az Azure Blob storage-ban. Ha bővebb információra van szüksége a blobok Azure Storage-beli használatával kapcsolatban, lépjen tovább az Azure Blob Storage használatáról szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Útmutató: Blob Storage-műveletek elvégzése az Azure CLI-vel](storage-how-to-use-blobs-cli.md)

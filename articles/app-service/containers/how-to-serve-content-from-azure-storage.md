---
title: Egyéni Storage-tároló csatolása Linux rendszeren
description: Megtudhatja, hogyan csatolhat egyéni hálózati megosztást a Linux-tárolóhoz a Azure App Serviceban. Fájlok megosztása az alkalmazások között, a statikus tartalmak távoli és helyileg elérhetővé való kezelése stb.
author: msangapu-msft
ms.topic: article
ms.date: 2/04/2019
ms.author: msangapu
ms.openlocfilehash: 00c60edeefa5fd8d1304aa5fc301a3b0304f5ca3
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671789"
---
# <a name="attach-azure-storage-containers-to-linux-containers"></a>Azure Storage-tárolók csatlakoztatása Linux-tárolóhoz

Ez az útmutató azt mutatja be, hogyan lehet hálózati megosztásokat csatlakoztatni a Linux-App Service az [Azure Storage](/azure/storage/common/storage-introduction)használatával. Az előnyök közé tartozik a biztonságos tartalom, a tartalom hordozhatósága, az állandó tárolás, a több alkalmazáshoz való hozzáférés és a több átadási módszer.

## <a name="prerequisites"></a>Előfeltételek

- Egy meglévő webalkalmazás (Linux vagy Web App for Containers App Service).
- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 vagy újabb).

## <a name="create-azure-storage"></a>Azure Storage létrehozása

> [!NOTE]
> Az Azure Storage nem alapértelmezett tárhely, és a webalkalmazás nem tartalmaz külön számlázást.
>
> A saját tároló használata az infrastruktúra korlátai miatt nem támogatja a tárolási tűzfal konfigurációját.
>

Hozzon létre egy Azure [Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli).

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>Fájlok feltöltése az Azure Storage-ba

Helyi könyvtár a Storage-fiókba való feltöltéséhez használja a [`az storage blob upload-batch`](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch) parancsot az alábbi példához hasonlóan:

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app-preview"></a>Tárterület csatolása a webalkalmazáshoz (előzetes verzió)

> [!CAUTION]
> Ha egy webalkalmazás egy meglévő könyvtárát egy Storage-fiókhoz csatolja, a rendszer törli a könyvtár tartalmát. Ha egy meglévő alkalmazás fájljainak áttelepítését végzi, a Kezdés előtt készítsen biztonsági másolatot az alkalmazásról és annak tartalmáról.
>

Ha Storage-fiókot szeretne csatlakoztatni a App Service alkalmazás egyik könyvtárához, használja a [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) parancsot. A tárolási típus lehet AzureBlob vagy AzureFiles. Ehhez a tárolóhoz a AzureBlob-t használja.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Ezt minden olyan címtárhoz el kell végeznie, amelyet hozzá szeretne kapcsolni egy Storage-fiókhoz.

## <a name="verify"></a>Ellenőrzés

Ha egy tároló egy webalkalmazáshoz van társítva, akkor a következő parancs futtatásával ellenőrizheti:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-custom-storage-in-docker-compose"></a>Egyéni tároló használata a Docker-összeállításban

Az Azure Storage a Custom-ID használatával több tárolós alkalmazással is csatlakoztatható. Az egyéni azonosító nevét a [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)futtatásával tekintheti meg.

A *Docker-compose. YML* fájlban rendelje hozzá a `custom-id`hoz a `volumes` lehetőséget. Példa:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>Következő lépések

- [Webalkalmazások konfigurálása Azure app Serviceban](../configure-common.md).

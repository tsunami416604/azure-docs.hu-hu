---
title: Tartalom továbbítása az Azure Storage linuxon – App Service-ben
description: Hogyan lehet konfigurálni, és a tartalom továbbítása az Azure Storage a linuxon futó Azure App Service-ben.
author: msangapu
manager: jeconnoc
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 2/04/2019
ms.author: msangapu-msft
ms.openlocfilehash: 6b4e145a693aabbf1a00d732e2fd602e7c887a03
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956012"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Tartalom továbbítása az Azure Storage a linuxon futó App Service-ben

Ez az útmutató bemutatja, hogyan statikus tartalmak továbbítása a Linuxon futó App Service használatával [Azure Storage](/azure/storage/common/storage-introduction). Értékelemek közé tartozik a védett tartalom, a tartalom hordozhatóságot, több alkalmazás és a több való továbbítása során a hozzáférést. 

## <a name="prerequisites"></a>Előfeltételek

- Egy meglévő web Apps (App Service Linux vagy a Web App for containers szolgáltatásban).
- [Az Azure CLI](/cli/azure/install-azure-cli) (2.0.46 vagy újabb).

## <a name="create-azure-storage"></a>Az Azure Storage létrehozása

> [!NOTE]
> Az Azure Storage nem alapértelmezett tároló, és külön kell fizetnie, nem találhatók meg a webalkalmazás.
>

Hozzon létre egy Azure [Azure storage-fiók](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli).

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>Fájlok feltöltése az Azure Storage

A storage-fiók egy helyi könyvtárba feltölteni, használhatja a [ `az storage blob upload-batch` ](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch) parancsot az alábbi példához hasonlóan:

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app-preview"></a>Hivatkozás tárolási a webalkalmazásban (előzetes verzió)

> [!CAUTION]
> Webes alkalmazás a meglévő directory összekapcsolása egy storage-fiók tartalmát törli. Ha egy meglévő alkalmazást fájljainak áttelepítése, megkezdése előtt készítsen biztonsági másolatot az alkalmazást és annak tartalmát.
>

Ahhoz a könyvtárhoz, az App Service-alkalmazást a storage-fiók csatlakoztatásához használhatja a [ `az webapp config storage-account add` ](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) parancsot. Tárolás típusa AzureBlob vagy az Azure filesba – lehet. Ez a tároló az Azure Blobba használhatja.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Akkor tegye ezt bármilyen egyéb tárfiókot társítani szeretné címtárak.

## <a name="verify"></a>Megerősítés

Miután egy storage-tárolót egy webalkalmazás van csatolva, ezt a következő parancs futtatásával ellenőrizheti:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-custom-storage-in-docker-compose"></a>Egyéni storage használata a Docker Compose

Az Azure Storage az egyéni-azonosító használatával többtárolós alkalmazások csatlakoztathatók. Az egyéni-azonosító neve megtekintéséhez futtassa [ `az webapp config storage-account list --name <app_name> --resource-group <resource_group>` ](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

Az a *docker-compose.yml* fájlt, és leképezheti a `volumes` beállítást `custom-id`. Példa:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>További lépések

- [Webalkalmazások konfigurálása az Azure App Service](../configure-common.md).

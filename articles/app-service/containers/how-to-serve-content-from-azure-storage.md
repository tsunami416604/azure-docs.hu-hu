---
title: Tartalom továbbítása az Azure Storage linuxon – App Service-ben
description: Hogyan lehet konfigurálni, és a tartalom továbbítása az Azure Storage a linuxon futó Azure App Service-ben.
author: msangapu
manager: jeconnoc
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 11/01/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 855df2820db6bba2b47d543ab671bee2193d7d9b
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250917"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Tartalom továbbítása az Azure Storage a linuxon futó App Service-ben

Ez az útmutató bemutatja, hogyan statikus tartalmak továbbítása a Linuxon futó App Service használatával [Azure Storage](/azure/storage/common/storage-introduction). Értékelemek közé tartozik a védett tartalom, a tartalom hordozhatóságot, több alkalmazás és a több való továbbítása során a hozzáférést. Ez az útmutató ismerteti a tartalom továbbítása az Azure Storage által [egyéni tároló konfigurálása](https://blogs.msdn.microsoft.com/appserviceteam/2018/09/24/announcing-bring-your-own-storage-to-app-service/).

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

## <a name="link-storage-to-your-web-app"></a>Hivatkozás tárolási a webalkalmazáshoz

> [!CAUTION]
> Webes alkalmazás a meglévő directory összekapcsolása egy storage-fiók tartalmát törli. Ha egy meglévő alkalmazást fájljainak áttelepítése, megkezdése előtt készítsen biztonsági másolatot az alkalmazást és annak tartalmát.
>

Ahhoz a könyvtárhoz, az App Service-alkalmazást a storage-fiók csatlakoztatásához használhatja a [ `az webapp config storage-account add` ](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) parancsot. Tárolás típusa AzureBlob vagy az Azure filesba – lehet. Ez a tároló az Azure Blobba használhatja.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Akkor tegye ezt bármilyen egyéb tárfiókot társítani szeretné címtárak.

## <a name="verify"></a>Ellenőrzés

Miután egy storage-tárolót egy webalkalmazás van csatolva, ezt a következő parancs futtatásával ellenőrizheti:

```azurecli
az webapp config storage-account list --resource-group <group_name> --name <app_name>
```

## <a name="next-steps"></a>További lépések

- [Webalkalmazások konfigurálása az Azure App Service](https://docs.microsoft.com/azure/app-service/web-sites-configure).

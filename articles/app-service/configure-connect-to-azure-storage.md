---
title: Egyéni tároló hozzáadása (Windows-tároló)
description: Megtudhatja, hogyan csatolhat egyéni hálózati megosztást egy egyéni Windows-tárolóban a Azure App Serviceban. Fájlok megosztása az alkalmazások között, a statikus tartalmak távoli és helyileg elérhetővé való kezelése stb.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: ad70bbe36369c03225079d1194043e6ceb109c6f
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671014"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Azure Files konfigurálása Windows-tárolóban App Service

> [!NOTE]
> Ez a cikk az egyéni Windows-tárolókat érinti. A _linuxon_app Service való üzembe helyezéssel kapcsolatban lásd: [tartalom kiszolgálása az Azure Storage-ból](./containers/how-to-serve-content-from-azure-storage.md).
>

Ez az útmutató bemutatja, hogyan érheti el az Azure Storage-t Windows-tárolókban. Csak [Azure Files megosztások](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) és [prémium fájlok megosztása](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) támogatott. Ebben az útmutatóban Azure Files megosztásokat használ. Az előnyök közé tartoznak a biztonságos tartalom, a tartalom hordozhatósága, a több alkalmazáshoz való hozzáférés és a több átadási módszer is.

## <a name="prerequisites"></a>Előfeltételek

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 vagy újabb).
- [Egy meglévő Windows-tároló alkalmazás a Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Azure-fájlmegosztás létrehozása](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Fájlok feltöltése az Azure-fájlmegosztásba](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> A Azure Files nem alapértelmezett tároló, és a webalkalmazásban nem szereplő külön számlázható. Az infrastruktúra-korlátozások miatt nem támogatja a tűzfal konfigurációját.
>

## <a name="link-storage-to-your-web-app-preview"></a>Tárterület csatolása a webalkalmazáshoz (előzetes verzió)

 Ha Azure Files-megosztást szeretne csatlakoztatni egy címtárhoz a App Service alkalmazásban, használja a [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) parancsot. A tárolási típusnak AzureFiles kell lennie.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Ezt minden olyan könyvtárhoz el kell végeznie, amelyet Azure Files-megosztáshoz szeretne csatolni.

## <a name="verify"></a>Ellenőrzés

Ha egy Azure Files-megosztás egy webalkalmazáshoz van társítva, akkor a következő parancs futtatásával ellenőrizheti:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>Következő lépések

- [ASP.NET-alkalmazás migrálása Azure app Servicere Windows-tároló (előzetes verzió) használatával](app-service-web-tutorial-windows-containers-custom-fonts.md).

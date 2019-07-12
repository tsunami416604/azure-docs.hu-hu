---
title: Az Azure Files segítségével konfigurálható a tárolás
description: Hogyan lehet konfigurálni, és csatlakozzon az Azure Files Windows-tárolóban az App Service-ben.
author: msangapu-msft
manager: gwallace
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu-msft
ms.openlocfilehash: ea6555e8459b8f372a73d7f943e9a96523d4c791
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789045"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Az Azure Files konfigurálása App Service-ben a Windows-tárolóban

> [!NOTE]
> Ez a cikk az egyéni Windows-tárolók vonatkozik. Az App Service-ben üzembe _Linux_, lásd: [tartalom szolgálja ki az Azure Storage-ból](./containers/how-to-serve-content-from-azure-storage.md).
>

Ez az útmutató bemutatja, hogyan Windows-tárolókban az Azure Storage eléréséhez. Csak [Azure-fájlmegosztások](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) és [prémium fájlmegosztásokat](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) támogatottak. Ebben az útmutatóban az Azure-fájlmegosztások használhatja. Értékelemek közé tartozik a védett tartalom, a tartalom hordozhatóságot, több alkalmazás és a több való továbbítása során a hozzáférést.

## <a name="prerequisites"></a>Előfeltételek

- [Az Azure CLI](/cli/azure/install-azure-cli) (2.0.46 vagy újabb).
- [Az Azure App Service-ben meglévő Windows-tároló alkalmazás](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Az Azure-fájlmegosztás létrehozása](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Fájlok feltöltése az Azure-fájlmegosztás](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Az Azure Files nem alapértelmezett tároló, és külön kell fizetnie, nem találhatók meg a webalkalmazás. Infrastruktúra-korlátozások miatt tűzfal-konfiguráció használata nem támogatott.
>

## <a name="link-storage-to-your-web-app-preview"></a>Hivatkozás tárolási a webalkalmazásban (előzetes verzió)

 Egy Azure-fájlmegosztást, ahhoz a könyvtárhoz, az App Service-alkalmazás csatlakoztatása, használja a [ `az webapp config storage-account add` ](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) parancsot. Tárolási típus az Azure filesba – kell lennie.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Akkor tegye ezt a bármely más címtárakban lehet kapcsolódni az Azure Files szeretné megosztani.

## <a name="verify"></a>Ellenőrzés

Miután az Azure-fájlmegosztási webalkalmazás van csatolva, ezt a következő parancs futtatásával ellenőrizheti:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>További lépések

- [ASP.NET-alkalmazás migrálása az Azure App Service-ben az olyan Windows-tárolók (előzetes verzió)](app-service-web-tutorial-windows-containers-custom-fonts.md).
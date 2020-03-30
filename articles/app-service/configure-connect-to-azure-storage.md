---
title: Egyéni tároló hozzáadása (Windows-tároló)
description: Megtudhatja, hogyan csatolhat egyéni hálózati megosztást egy egyéni Windows-tárolóban az Azure App Service-ben. Fájlokat oszthat meg az alkalmazások között, kezelheti a statikus tartalmakat távolról, és helyileg érheti el, stb.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 64ef4dfe81e6415f1285a74962e2123507715119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120670"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Azure-fájlok konfigurálása Windows-tárolóban az App Service szolgáltatásban

> [!NOTE]
> Ez a cikk az egyéni Windows-tárolókra vonatkozik. A _Linuxon_lévő App Service szolgáltatásban való üzembe helyezéshez olvassa el a [Tartalom kiszolgálása az Azure Storage-ból](./containers/how-to-serve-content-from-azure-storage.md).
>

Ez az útmutató bemutatja, hogyan érheti el az Azure Storage-t a Windows-tárolókban. Csak [az Azure-fájlmegosztások](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) és [a prémium fájlmegosztások](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) támogatottak. Ebben az útmutatóban az Azure-fájlmegosztásokat használja. Az előnyök közé tartozik a biztonságos tartalom, a tartalom hordozhatósága, a több alkalmazáshoz való hozzáférés és a többátviteli módszer.

## <a name="prerequisites"></a>Előfeltételek

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 vagy újabb).
- [Meglévő Windows Container-alkalmazás az Azure App Service-ben](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Azure-fájlmegosztás létrehozása](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Fájlok feltöltése az Azure-fájlmegosztásba](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Az Azure Files nem alapértelmezett tárterület, és külön számlázták, nem tartalmazza a webalkalmazás. Infrastruktúra-korlátozások miatt nem támogatja a tűzfal konfigurációjának használatát.
>

## <a name="limitations"></a>Korlátozások

- Az Azure Storage windowsos **tárolókban előzetes verzióban** érhető el, és **éles környezetben** **nem támogatott.**
- Az Azure Storage windowsos tárolókban csak az **Azure Files-tárolók** csatlakoztatását támogatja (olvasás/írás).
- Az Azure Storage windowsos tárolókban jelenleg **nem támogatott** a saját kódforgatókönyvek a Windows App Service-csomagok.
- Az Azure Storage windowsos tárolókban **nem támogatja** a **tárolási tűzfal** konfigurációját az infrastruktúra korlátai miatt.
- Az Azure Storage Windows-tárolókban lehetővé teszi, hogy alkalmazásonként **legfeljebb öt** csatlakoztatási pontot adjon meg.
- Az alkalmazáshoz csatlakoztatott Azure Storage nem érhető el az App Service FTP/FTPs végpontokon keresztül. Használja [az Azure Storage explorert.](https://azure.microsoft.com/features/storage-explorer/)
- Az Azure Storage számlázása függetlenül történik, és **nem tartalmazza** a webalkalmazás. További információ az [Azure Storage díjszabásáról.](https://azure.microsoft.com/pricing/details/storage)

## <a name="link-storage-to-your-web-app-preview"></a>Tárhely csatolása a webalkalmazáshoz (előzetes verzió)

 Az Azure Files Share csatlakoztatása az App Service-alkalmazás egy könyvtárba, használja a [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) parancsot. A tárolási típusnak AzureFiles típusúnak kell lennie.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Ezt minden más, Azure-fájlokhoz csatolni kívánt könyvtár esetén kell megtennie.

## <a name="verify"></a>Ellenőrzés

Miután egy Azure Files-megosztás tegy webalkalmazáshoz kapcsolódik, ezt a következő parancs futtatásával ellenőrizheti:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="next-steps"></a>További lépések

- [Áttelepíteni egy ASP.NET alkalmazást az Azure App Service-be egy Windows-tároló (előzetes verzió) használatával.](app-service-web-tutorial-windows-containers-custom-fonts.md)

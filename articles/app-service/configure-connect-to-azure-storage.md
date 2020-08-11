---
title: Azure Storage (tároló) hozzáadása
description: Megtudhatja, hogyan csatolhat egyéni hálózati megosztást egy tárolóban lévő alkalmazásban Azure App Serviceban. Fájlok megosztása az alkalmazások között, a statikus tartalmak távoli és helyileg elérhetővé való kezelése stb.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 8ced35f30966a96061792ad2171afe19599ed22c
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88077254"
---
# <a name="access-azure-storage-as-a-network-share-from-a-container-in-app-service"></a>Az Azure Storage hálózati megosztásként való elérése App Service-tárolóból

::: zone pivot="container-windows"

Ez az útmutató bemutatja, hogyan csatlakoztathatja az Azure Storage-fájlokat hálózati megosztásként egy App Service lévő Windows-tárolóhoz. Csak [Azure Files megosztások](../storage/files/storage-how-to-use-files-cli.md) és [prémium fájlok megosztása](../storage/files/storage-how-to-create-premium-fileshare.md) támogatott. Az előnyök közé tartoznak a biztonságos tartalom, a tartalom hordozhatósága, a több alkalmazáshoz való hozzáférés és a több átadási módszer is.

::: zone-end

::: zone pivot="container-linux"

Ez az útmutató bemutatja, hogyan csatlakoztathatja az Azure Storage-t egy Linux-tárolóhoz App Service. Az előnyök közé tartozik a biztonságos tartalom, a tartalom hordozhatósága, az állandó tárolás, a több alkalmazáshoz való hozzáférés és a több átadási módszer.

::: zone-end

## <a name="prerequisites"></a>Előfeltételek

::: zone pivot="container-windows"

- [Egy meglévő Windows-tároló alkalmazás a Azure App Service](quickstart-custom-container.md)
- [Azure-fájlmegosztás létrehozása](../storage/files/storage-how-to-use-files-cli.md)
- [Fájlok feltöltése az Azure-fájlmegosztásba](../storage/files/storage-files-deployment-guide.md)

::: zone-end

::: zone pivot="container-linux"

- Egy meglévő [app Service Linux-alkalmazásban](index.yml).
- [Azure Storage-fiók](../storage/common/storage-account-create.md?tabs=azure-cli)
- Egy [Azure-fájlmegosztás és-könyvtár](../storage/files/storage-how-to-use-files-cli.md).

::: zone-end

> [!NOTE]
> A Azure Files nem alapértelmezett tároló, és a webalkalmazásban nem szereplő külön számlázható. Az infrastruktúra-korlátozások miatt nem támogatja a tűzfal konfigurációját.
>

## <a name="limitations"></a>Korlátozások

::: zone pivot="container-windows"

- Az Azure Storage App Service **előzetes** verzióban érhető el, és **éles környezetben** **nem támogatott** .
- Az Azure Storage App Service jelenleg **nem támogatott** a saját programkód-forgatókönyvek (nem tároló Windows-alkalmazások) használata esetén.
- Az App Service Azure Storage szolgáltatás **nem támogatja** a **tárolási tűzfal** konfigurálását az infrastruktúra korlátai miatt.
- Az Azure Storage App Service lehetővé teszi, **hogy egy alkalmazás legfeljebb öt** csatlakoztatási pontot határozzon meg.
- Az alkalmazáshoz csatlakoztatott Azure Storage App Service FTP-/FTPs-végpontokon keresztül nem érhető el. Az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)használata.

::: zone-end

::: zone pivot="container-linux"

- Az Azure Storage-ban App Service **előzetes** verzióban érhető el a Linux és a Web App for containers app Service. **Éles környezetben** **nem támogatott** .
- Az Azure Storage App Service támogatja a **Azure Files tárolók** (írási/olvasási) és az **Azure Blob-tárolók** csatlakoztatását (csak olvasható)
- Az App Service Azure Storage szolgáltatás **nem támogatja** a **tárolási tűzfal** konfigurálását az infrastruktúra korlátai miatt.
- App Service Azure Storage-ban **legfeljebb öt** csatlakozási pontot adhat meg.
- Az alkalmazáshoz csatlakoztatott Azure Storage App Service FTP-/FTPs-végpontokon keresztül nem érhető el. Az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)használata.

::: zone-end

## <a name="link-storage-to-your-app"></a>Tároló csatolása az alkalmazáshoz

::: zone pivot="container-windows"

Miután létrehozta az [Azure Storage-fiókot, a fájlmegosztást és a könyvtárat](#prerequisites), mostantól konfigurálhatja az alkalmazást az Azure Storage használatával.

Ha Azure Files-megosztást szeretne csatlakoztatni egy címtárhoz a App Service alkalmazásban, használja az [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) parancsot. A tárolási típusnak AzureFiles kell lennie.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

Ezt minden olyan könyvtárhoz el kell végeznie, amelyet Azure Files-megosztáshoz szeretne csatolni.

::: zone-end

::: zone pivot="container-linux"

Miután létrehozta az [Azure Storage-fiókot, a fájlmegosztást és a könyvtárat](#prerequisites), mostantól konfigurálhatja az alkalmazást az Azure Storage használatával.

Ha Storage-fiókot szeretne csatlakoztatni a App Service alkalmazás egyik könyvtárába, használja az [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) parancsot. A tárolási típus lehet AzureBlob vagy AzureFiles. Ebben a példában a AzureFiles van használatban. A csatlakoztatási útvonal beállítása az Azure Storage-ból csatlakoztatni kívánt mappának felel meg. A "/" értékre állításával a teljes Azure-tárterületet csatlakoztathatja.


> [!CAUTION]
> A webalkalmazásban a csatlakoztatási útvonalként megadott könyvtárnak üresnek kell lennie. A címtárban tárolt bármely tartalmat egy külső csatlakoztatás hozzáadásakor törli a rendszer. Ha egy meglévő alkalmazás fájljainak áttelepítését végzi, a Kezdés előtt készítsen biztonsági másolatot az alkalmazásról és annak tartalmáról.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

Ezt minden olyan címtárhoz el kell végeznie, amelyet hozzá szeretne kapcsolni egy Storage-fiókhoz.

::: zone-end

## <a name="verify-linked-storage"></a>Csatolt tár ellenőrzése

Ha a megosztás az alkalmazáshoz van társítva, akkor a következő parancs futtatásával ellenőrizheti:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>További lépések

::: zone pivot="container-windows"

- [Egyéni szoftver Átmigrálása Azure app Service egy egyéni tároló használatával](tutorial-custom-container.md?pivots=container-windows).

::: zone-end

::: zone pivot="container-linux"

- [Egyéni tároló konfigurálása](configure-custom-container.md?pivots=platform-linux).

::: zone-end
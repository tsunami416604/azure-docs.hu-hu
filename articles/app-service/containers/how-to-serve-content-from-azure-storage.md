---
title: Tartalom kiszolgálása az Azure Storage-ból Linux-tárolók számára
description: Megtudhatja, hogyan csatolhat egyéni hálózati megosztást a Linux-tárolóhoz a Azure App Serviceban. Fájlok megosztása az alkalmazások között, a statikus tartalmak távoli és helyileg elérhetővé való kezelése stb.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 9a5a38ea32d927f50fb9ddbebe3e1c3533e6fcc0
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82625323"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Tartalom kiszolgálása az Azure Storage-ban App Service Linuxon

> [!NOTE]
> Ez a cikk a Linux-tárolók esetében érvényes. Egyéni Windows-tárolók üzembe helyezéséhez tekintse [meg a Azure Files konfigurálása a app Service Windows-tárolóban](../configure-connect-to-azure-storage.md)című témakört. A Linuxon futó Azure Storage szolgáltatás az **előzetes** verzióban elérhető App Service. Ez a funkció **éles környezetekben nem támogatott**.
>

Ez az útmutató bemutatja, hogyan csatlakoztathatja az Azure Storage-t a Linux App Serviceához. Az előnyök közé tartozik a biztonságos tartalom, a tartalom hordozhatósága, az állandó tárolás, a több alkalmazáshoz való hozzáférés és a több átadási módszer.

## <a name="prerequisites"></a>Előfeltételek

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 vagy újabb).
- Egy meglévő [app Service Linux-alkalmazásban](https://docs.microsoft.com/azure/app-service/containers/).
- [Azure Storage-fiók](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- Egy [Azure-fájlmegosztás és-könyvtár](../../storage/files/storage-how-to-use-files-cli.md).


## <a name="limitations-of-azure-storage-with-app-service"></a>Az Azure Storage és a App Service korlátozásai

- Az Azure Storage App Service **előzetes** verzióban érhető el a Linux és a Web App for containers app Service. **Éles környezetben** **nem támogatott** .
- Az Azure Storage App Service támogatja a **Azure Files tárolók** (írási/olvasási) és az **Azure Blob-tárolók** csatlakoztatását (csak olvasható)
- Az Azure Storage és a App Service **nem támogatja** a **tárolási tűzfal** konfigurációjának használatát az infrastruktúra korlátai miatt.
- Az Azure Storage App Service lehetővé teszi, **hogy egy alkalmazás legfeljebb öt** csatlakoztatási pontot határozzon meg.
- Az alkalmazáshoz csatlakoztatott Azure Storage App Service FTP-/FTPs-végpontokon keresztül nem érhető el. Az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)használata.
- Az Azure Storage szolgáltatás **nem része** a webalkalmazásnak, és külön számlázható. További információ az [Azure Storage díjszabásáról](https://azure.microsoft.com/pricing/details/storage).

> [!WARNING]
> Az Azure Blob Storage használatával App Service konfigurációk csak Feb 2020-ig lesznek olvashatók. [További információ](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Az alkalmazás konfigurálása az Azure Storage-ban

Miután létrehozta az [Azure Storage-fiókot, a fájlmegosztást és a könyvtárat](#prerequisites), mostantól konfigurálhatja az alkalmazást az Azure Storage használatával.

Ha Storage-fiókot szeretne csatlakoztatni a App Service alkalmazás egyik könyvtárába, használja az [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) parancsot. A tárolási típus lehet AzureBlob vagy AzureFiles. Ebben a példában a AzureFiles van használatban. A csatlakoztatási útvonal beállítása az Azure Storage-ból csatlakoztatni kívánt mappának felel meg. A "/" értékre állításával a teljes Azure-tárterületet csatlakoztathatja.


> [!CAUTION]
> A webalkalmazásban a csatlakoztatási útvonalként megadott könyvtárnak üresnek kell lennie. A címtárban tárolt bármely tartalmat egy külső csatlakoztatás hozzáadásakor törli a rendszer. Ha egy meglévő alkalmazás fájljainak áttelepítését végzi, a Kezdés előtt készítsen biztonsági másolatot az alkalmazásról és annak tartalmáról.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Ezt minden olyan címtárhoz el kell végeznie, amelyet hozzá szeretne kapcsolni egy Storage-fiókhoz.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Az Azure Storage-hivatkozás ellenőrzése a webalkalmazáshoz

Ha egy tároló egy webalkalmazáshoz van társítva, akkor a következő parancs futtatásával ellenőrizheti:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Az Azure Storage használata a Docker-összeállításban

Az Azure Storage a Custom-ID használatával több tárolós alkalmazással is csatlakoztatható. Az egyéni azonosító nevének megtekintéséhez futtassa a parancsot [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list).

A *Docker-compose. YML* fájlban rendelje hozzá `volumes` `custom-id`a () beállítást. Például:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>További lépések

- [Webalkalmazások konfigurálása Azure app Serviceban](../configure-common.md).


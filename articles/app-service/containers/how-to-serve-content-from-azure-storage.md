---
title: Tartalmak kiszolgálása az Azure Storage-ból Linux-tárolókba
description: Ismerje meg, hogyan csatolhat egyéni hálózati megosztást a Linux-tárolóhoz az Azure App Service-ben. Fájlokat oszthat meg az alkalmazások között, kezelheti a statikus tartalmakat távolról, és helyileg érheti el, stb.
author: msangapu-msft
ms.topic: article
ms.date: 01/02/2020
ms.author: msangapu
ms.openlocfilehash: 79a4e423f7a2b6570234c958ac833cdf5c6a75e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297917"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Az Azure Storage tartalmának kiszolgálása linuxos App Service-ben

> [!NOTE]
> Ez a cikk linuxos tárolókra vonatkozik. Az egyéni Windows-tárolókra való telepítésről az [Azure-fájlok konfigurálása Windows-tárolóban az App Service szolgáltatásban](../configure-connect-to-azure-storage.md)című témakörben található. Az Azure Storage az App Service Linux on Linux egy **előzetes verziójú** funkció. Ez a szolgáltatás **éles környezetben nem támogatott.**
>

Ez az útmutató bemutatja, hogyan csatolhat Azure Storage-t az App Service Linux-on. Az előnyök közé tartozik a biztonságos tartalom, a tartalom hordozhatósága, az állandó tárolás, a több alkalmazáshoz való hozzáférés és a többátviteli módszer.

## <a name="prerequisites"></a>Előfeltételek

- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 vagy újabb).
- Egy meglévő [App Service Linux-alkalmazás](https://docs.microsoft.com/azure/app-service/containers/).
- [Egy Azure storage-fiók](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli)
- Egy [Azure-fájlmegosztás és könyvtár](../../storage/files/storage-how-to-use-files-cli.md).


## <a name="limitations-of-azure-storage-with-app-service"></a>Az Azure Storage és az App Service korlátai

- Az Azure Storage és az App Service **előzetes verzióban** érhető el a Linux on App Service és a Web App for Containers. **Éles környezetben** **nem támogatott.**
- Az Azure Storage app szolgáltatással támogatja az **Azure Files-tárolók** (olvasás/írás) és **az Azure Blob-tárolók** csatlakoztatását (írásvédett)
- Az Azure Storage és az App Service **nem támogatja** a **storage tűzfal** konfigurációját az infrastruktúra korlátai miatt.
- Az Azure Storage és az App Service segítségével alkalmazásonként **legfeljebb öt** csatlakoztatási pontot adhat meg.
- Az alkalmazáshoz csatlakoztatott Azure Storage nem érhető el az App Service FTP/FTPs végpontokon keresztül. Használja [az Azure Storage explorert.](https://azure.microsoft.com/features/storage-explorer/)
- Az Azure Storage **nem része** a webalkalmazásnak, és külön számlázik. További információ az [Azure Storage díjszabásáról.](https://azure.microsoft.com/pricing/details/storage)

> [!WARNING]
> Az Azure Blob Storage használatával az App Service-konfigurációk csak 2020 februárában leszolvashatók lesznek. [További információ](https://github.com/Azure/app-service-linux-docs/blob/master/BringYourOwnStorage/mounting_azure_blob.md)
>

## <a name="configure-your-app-with-azure-storage"></a>Az alkalmazás konfigurálása az Azure Storage segítségével

Miután létrehozta az [Azure Storage-fiókot, a fájlmegosztást és a címtárat,](#prerequisites)most már konfigurálhatja az alkalmazást az Azure Storage-szal.

Ha egy tárfiókot szeretne csatlakoztatni az App Service-alkalmazás egy címtárához, használja a [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) parancsot. A storage típusa lehet AzureBlob vagy AzureFiles. Ebben a példában az AzureFiles használatos.


> [!CAUTION]
> A webalkalmazás csatlakoztatási útvonalaként megadott könyvtárnak üresnek kell lennie. Az ebben a könyvtárban tárolt tartalom a külső csatlakoztatás hozzáadásakor törlődik. Ha meglévő alkalmazáshoz telepít fájlokat, készítsen biztonsági másolatot az alkalmazásról és annak tartalmáról, mielőtt elkezdené.
>

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Ezt minden más könyvtár esetében meg kell tennie, amelyet egy tárfiókhoz szeretne kapcsolni.

## <a name="verify-azure-storage-link-to-the-web-app"></a>Az Azure Storage hivatkozásának ellenőrzése a webalkalmazásra

Miután egy tárolótároló t csatolt egy webalkalmazáshoz, ezt a következő parancs futtatásával ellenőrizheti:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```

## <a name="use-azure-storage-in-docker-compose"></a>Az Azure Storage használata a Docker Compose-ben

Az Azure Storage többtárolós alkalmazásokkal is csatlakoztatható az egyéni azonosító használatával. Az egyéni azonosító nevének megtekintéséhez futtassa a futtassa a futtassa a futtassa a programot. [`az webapp config storage-account list --name <app_name> --resource-group <resource_group>`](/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-list)

A *docker-compose.yml* fájlban `volumes` rendelje `custom-id`hozzá a lehetőséget a számára. Példa:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - <custom-id>:<path_in_container>
```

## <a name="next-steps"></a>További lépések

- [Webalkalmazások konfigurálása az Azure App Service szolgáltatásban.](../configure-common.md)


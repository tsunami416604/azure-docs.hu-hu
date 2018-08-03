---
title: Az IoT hubhoz) (verziójával az.py) az Azure CLI-vel fájlfeltöltés konfigurálása |} A Microsoft Docs
description: Hogyan fileuploads az Azure IoT hubba a platformfüggetlen Azure CLI 2.0) (verziójával az.py) használatával konfigurálható.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 0eac620d44967827f7703da9cf409703a123ab07
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39460197"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Az IoT Hub Azure CLI-vel a fájlfeltöltések konfigurálása

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Használatához a [fájlba feltöltésének működését az IoT Hub][lnk-upload], először társítania kell egy Azure Storage-fiókot az IoT hubbal. Használjon egy meglévő tárfiókot, vagy hozzon létre egy újat.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].
* [Az Azure CLI 2.0][lnk-CLI-install].
* Az Azure IoT hubra. Ha nem rendelkezik egy IoT hubot, használhatja a `az iot hub create` [parancs] [ lnk-cli-create-iothub] hozzon létre egyet, vagy a portál használatával [az IoT hub létrehozása] [lnk-portal-hub].
* Egy Azure Storage-fiók. Ha nem rendelkezik Azure Storage-fiókot, akkor használhatja a [Azure CLI 2.0 - storage-fiókok kezelése] [ lnk-manage-storage] hozzon létre egyet, vagy a portál használata [hozzon létre egy tárfiókot] [ lnk-portal-storage].

## <a name="sign-in-and-set-your-azure-account"></a>Jelentkezzen be, és állítsa be az Azure-fiókkal

Jelentkezzen be Azure-fiókjába, és válassza ki előfizetését.

1. A parancssorban futtassa a [login parancsot][lnk-login-command]:

    ```azurecli
    az login
    ```

    Kövesse az utasításokat a kóddal történő hitelesítéshez, és jelentkezzen be az Azure-fiókjába webböngészőből.

1. Ha több Azure-előfizetéssel rendelkezik, az Azure-ba történő bejelentkezéssel hozzáfér a hitelesítő adatokhoz tartozó összes Azure-fiókhoz. Az alábbi [paranccsal jelenítheti meg az elérhető Azure-fiókokat][lnk-az-account-command]:

    ```azurecli
    az account list
    ```

    Az alábbi parancs segítségével válassza ki azt az előfizetést, amelyet az IoT Hub létrehozásához szükséges parancsok futtatásához kíván használni. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>A storage-fiók adatainak beolvasása

A következő lépések azt feltételezik, hogy a tárfiók tárfiókkulcsait létrehozott a **Resource Manager** -alapú üzemi modellben, és nem a **klasszikus** üzemi modellt.

Fájlfeltöltés az eszközökről származó konfigurálásához szükség van a kapcsolati karakterláncot egy Azure storage-fiókot. A storage-fiókot az IoT hub az azonos előfizetésben kell lennie. A storage-fiókban található blob-tárolóra nevét is szükséges. A következő parancsot használja a storage-fiók kulcsok lekéréséhez:

```azurecli
az storage account show-connection-string --name {your storage account name} --resource-group {your storage account resource group}
```

Jegyezze fel a **connectionString** értéket. A következő lépésekben van szükség.

Meglévő blob tároló használata a fájlfeltöltési, vagy hozzon létre újat:

* A meglévő, a tárfiókban található blob-tárolók listájában, használja a következő parancsot:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Hozzon létre egy blobtárolót a tárfiókban található, használja a következő parancsot:

    ```azurecli
    az storage container create --name {container name} --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Fájl feltöltése

Mostantól konfigurálhatja az IoT hub engedélyezéséhez [fájlba feltöltésének működését] [ lnk-upload] használatával a tárfiók részleteit.

A konfigurációs van szükség a következő értékeket:

**A tároló**: az aktuális Azure-előfizetés társítása az IoT hub az Azure storage-fiókban lévő blobtárolóba. A szükséges tárolási fiók adatait az előző szakaszban lekért. Az IoT Hub automatikusan létrehozza az SAS URI-k eszközöket használja, ha azok a fájlok feltöltése a blob-tárolóba írási engedéllyel rendelkező.

**A feltöltött fájlokkra vonatkozó értesítések fogadása**: fájl feltöltése értesítések engedélyezése vagy letiltása.

**SAS-élettartam**: Ez a beállítás akkor a time-to-live, az eszközt az IoT Hub által visszaadott SAS URI-azonosítókat. Alapértelmezés szerint egy óra beállítva.

**Értesítési beállítások alapértelmezett TTL fájl**: A time-to-live-fájl feltöltése értesítési, mielőtt lejár. Alapértelmezés szerint egy nap beállítva.

**A fájl értesítési kézbesítések maximális száma**: hányszor az IoT Hub fájlt próbál meg feltölteni értesítés. Alapértelmezés szerint a 10-re állítva.

A következő Azure CLI-parancsok használatával adja meg a fájl feltöltése beállításait az IoT hubnak:

A bash rendszerhéj használja:

```azurecli
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"
az iot hub update --name {your iot hub name} --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

A Windows-parancssort használja:

```azurecli
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.connectionString="{your storage account connection string}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.containerName="{your storage container name}""
az iot hub update --name {your iot hub name} --set "properties.storageEndpoints.$default.sasTtlAsIso8601=PT1H0M0S"

az iot hub update --name {your iot hub name} --set properties.enableFileUploadNotifications=true
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10
az iot hub update --name {your iot hub name} --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

A fájlok feltöltése konfigurálása az IoT hub a következő paranccsal tekintheti meg:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>További lépések

Az IoT Hub a fájl feltöltése képességeivel kapcsolatos további információkért lásd: [fájlfeltöltés az eszközökről][lnk-upload].

Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub kezelése:

* [Tömeges IoT-eszközök kezelése][lnk-bulk]
* [Az IoT Hub-metrikák][lnk-metrics]
* [Műveletek figyelése][lnk-monitor]

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Az IoT Hub fejlesztői útmutató][lnk-devguide]
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]
* [Az IoT-megoldás az alapoktól biztonságos mentése][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: /azure/iot-fundamentals/iot-security-ground-up


[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-manage-storage]:../storage/common/storage-azure-cli.md#manage-storage-accounts
[lnk-portal-storage]:../storage/common/storage-create-storage-account.md
[lnk-cli-create-iothub]: https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create
---
title: "Fájl feltöltése az Azure CLI-vel (az.py) IoT-központ konfigurálása |} Microsoft Docs"
description: "Hogyan fileuploads az Azure IoT hubhoz a platformok közötti Azure CLI 2.0 (az.py) használatával konfigurálható."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 6b100e65aba604fd8becb02c3a205b3348872bc4
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurálja az IoT-központ fájlfeltöltések Azure parancssori felület használatával

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Használatához a [feltöltés funkció fájlt az IoT-központ][lnk-upload], először társítania kell egy Azure Storage-fiókot az IoT hub. Meglévő tárfiók használata, vagy hozzon létre egy újat.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].
* [Az Azure CLI 2.0][lnk-CLI-install].
* Az Azure IoT-központ. Ha még nem rendelkezik az IoT-központ, használhatja a `az iot hub create` [parancs] [ lnk-cli-create-iothub] hozzon létre egyet, vagy a portál használatával [létrehoz egy IoT-központot] [lnk-portal-hub].
* Egy Azure Storage-fiókot. Ha egy Azure Storage-fiók nem rendelkezik, használhatja a [Azure CLI 2.0 - storage-fiókok kezelése] [ lnk-manage-storage] hozzon létre egyet, vagy használja a portál [hozzon létre egy tárfiókot][lnk-portal-storage].

## <a name="sign-in-and-set-your-azure-account"></a>Jelentkezzen be, és állítsa be az Azure-fiókjával

Jelentkezzen be az Azure-fiókjával, és jelölje ki az előfizetését.

1. A parancssorban futtassa a [bejelentkezési parancs][lnk-login-command]:

    ```azurecli
    az login
    ```

    Kövesse az utasításokat a hitelesítést a kódot, és jelentkezzen be az Azure-fiókjával webböngészőn keresztül.

1. Ha több Azure-előfizetéssel rendelkezik, az Azure-bA bejelentkezés engedélyezi a hozzáférést az Azure fiókokhoz tartozó hitelesítő adatait. Használja a következő [paranccsal listát készíthet az Azure-fiókra] [ lnk-az-account-command] elérhető lesz szükség:

    ```azurecli
    az account list
    ```

    Az alábbi parancs segítségével válassza ki, hogy az IoT hub létrehozására szolgáló parancsok futtatásához használni kívánt előfizetést. Az előfizetés neve vagy azonosítója is használhatja, ha az előző parancs kimenetében:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>A tárfiókadatok beolvasása

A következő lépések feltételezik, hogy a tárolási fiók használata a **erőforrás-kezelő** telepítési modell, és nem a **klasszikus** üzembe helyezési modellben.

Fájlfeltöltéseket adott területen működő eszközök konfigurálásához szükséges a kapcsolati karakterlánc egy Azure storage-fiókot. A tárfiók ugyanahhoz az előfizetéshez, mint az IoT hub kell lennie. A tárfiók a blob-tároló neve is kell. A következő paranccsal lekérni a tárfiók kulcsait:

```azurecli
az storage account show-connection-string --name {your storage account name} --resource-group {your storage account resource group}
```

Jegyezze fel a **connectionString** érték. A következő lépésekben van szükség.

Meglévő blob tároló használata a fájl feltöltéshez, vagy hozzon létre újat:

* A meglévő blob tárolók a tárfiókban lévő listájában, használja a következő parancsot:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* A tárfiók a blob-tároló létrehozásához használja a következő parancsot:

    ```azurecli
    az storage container create --name {container name} --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Fájl feltöltése

Mostantól konfigurálhatja az IoT hub engedélyezése [fájl feltöltése funkció] [ lnk-upload] a tárfiókadatok használatával.

A konfiguráció szükséges a következő értékeket:

**A tároló**: egy blob tároló, az Azure-tárfiók az IoT hub társítja a jelenlegi Azure-előfizetésben. A szükséges tárfiók adatait az előző szakaszban leírt lekért. Az IoT-központ automatikusan létrehozza a SAS URI-azonosítók eszközöket használja, ha ezek a fájlok feltöltése a blob tároló írási engedéllyel rendelkező.

**A feltöltött fájlok értesítéseket**: engedélyezheti vagy tilthatja le a fájl feltöltése értesítések.

**SAS-élettartam**: Ez a beállítás akkor a idő élettartamát az az eszközt az IoT-központ által visszaadott SAS URI-azonosítók. Alapértelmezés szerint egyórás beállítva.

**Az értesítési beállítások alapértelmezett élettartam**: az idő TTL-fájl feltöltése értesítési, előtt lejárt. Alapértelmezés szerint egy nap beállítva.

**Értesítési maximális száma fájl**: A szám, ahányszor az IoT Hub megpróbál egy fájl feltöltése értesítést. Alapértelmezés szerint 10-re állítva.

A következő Azure CLI-parancsok segítségével beállításainak megadása a fájl feltöltése az IoT hub:

A bash rendszerhéjat használja:

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

A fájl feltöltése konfigurációs az IoT hub, a következő parancsot a tekintheti meg:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Következő lépések

Az IoT-központ a fájl feltöltése képességeivel kapcsolatos további információk: [egy eszközről tölt fel][lnk-upload].

Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub kezelése:

* [Tömeges az IoT-eszközök kezelése][lnk-bulk]
* [Az IoT-központ metrikák][lnk-metrics]
* [Figyelési műveletek][lnk-monitor]

Az IoT-központ képességeit további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató][lnk-devguide]
* [Az Azure IoT peremhálózati peremeszközök AI központi telepítése][lnk-iotedge]
* [Az IoT-megoldásból az alapoktól biztonságos mentése][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md


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
[lnk-cli-create-iothub]: https://docs.microsoft.com/cli/azure/iot/hub#az_iot_hub_create
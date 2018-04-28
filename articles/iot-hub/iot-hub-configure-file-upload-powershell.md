---
title: Fájl feltöltése konfigurálása az Azure PowerShell használatával |} Microsoft Docs
description: Az IoT hub fájl engedélyezéséhez konfigurálja az Azure PowerShell-parancsmagok segítségével feltölti a csatlakoztatott eszközökről. A cél Azure storage-fiók konfigurálásával kapcsolatos információkat tartalmazza.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 22d331342504aa7cc3d4fe86777ef9c2595a27ac
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Konfigurálja az IoT-központ fájlfeltöltések a PowerShell használatával

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Használatához a [feltöltés funkció fájlt az IoT-központ][lnk-upload], először társítania kell egy Azure storage-fiókot az IoT hub. Meglévő tárfiók használata, vagy hozzon létre egy újat.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].
* [Az Azure PowerShell-parancsmagok][lnk-powershell-install].
* Az Azure IoT-központ. Ha még nem rendelkezik az IoT-központ, használhatja a [New-AzureRmIoTHub parancsmag] [ lnk-powershell-iothub] hozzon létre egyet, vagy használja a portál [létrehoz egy IoT-központot][lnk-portal-hub].
* Egy Azure-tárfiók. Ha egy Azure storage-fiók nem rendelkezik, használhatja a [Azure Storage PowerShell parancsmagjainak] [ lnk-powershell-storage] hozzon létre egyet, vagy használja a portál [hozzon létre egy tárfiókot][lnk-portal-storage].

## <a name="sign-in-and-set-your-azure-account"></a>Jelentkezzen be, és állítsa be az Azure-fiókjával

Jelentkezzen be Azure-fiókjába, és válassza ki előfizetését.

1. A PowerShell parancssorból futtassa a **Connect-AzureRmAccount** parancsmagot:

    ```powershell
    Connect-AzureRmAccount
    ```

1. Ha több Azure-előfizetéssel rendelkezik, a jelentkezik be az Azure ad hozzáférést az összes Azure-előfizetést a hitelesítő adatok társított. Használja a következő parancsot a rendelkezésre álló használata Azure-előfizetések listázásához:

    ```powershell
    Get-AzureRMSubscription
    ```

    A következő parancs segítségével válassza ki, hogy az IoT hub kezelésére szolgáló parancsok futtatásához használni kívánt előfizetést. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

    ```powershell
    Select-AzureRMSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>A tárfiókadatok beolvasása

A következő lépések feltételezik, hogy a tárolási fiók használata a **erőforrás-kezelő** telepítési modell, és nem a **klasszikus** üzembe helyezési modellben.

Fájlfeltöltéseket adott területen működő eszközök konfigurálásához szükséges a kapcsolati karakterlánc egy Azure storage-fiókot. A tárfiók ugyanahhoz az előfizetéshez, mint az IoT hub kell lennie. A tárfiók a blob-tároló neve is kell. A következő paranccsal lekérni a tárfiók kulcsait:

```powershell
Get-AzureRmStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Jegyezze fel a **key1** tárolási fiók kulcs értékét. A következő lépésekben van szükség.

Meglévő blob tároló használata a fájl feltöltéshez, vagy hozzon létre újat:

* A meglévő blob tárolók a tárfiókban lévő listájában, használja a következő parancsokat:

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzureStorageContainer -Context $ctx
    ```

* A tárfiók a blob-tároló létrehozásához használja a következő parancsokat:

    ```powershell
    $ctx = New-AzureStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzureStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Az IoT hub konfigurálása

Mostantól konfigurálhatja az IoT hub engedélyezése [fájl feltöltése funkció] [ lnk-upload] a tárfiókadatok használatával.

A konfiguráció szükséges a következő értékeket:

**A tároló**: egy blob tároló, az Azure-tárfiók az IoT hub társítja a jelenlegi Azure-előfizetésben. A szükséges tárfiók adatait az előző szakaszban leírt lekért. Az IoT-központ automatikusan létrehozza a SAS URI-azonosítók eszközöket használja, ha ezek a fájlok feltöltése a blob tároló írási engedéllyel rendelkező.

**A feltöltött fájlok értesítéseket**: engedélyezheti vagy tilthatja le a fájl feltöltése értesítések.

**SAS-élettartam**: Ez a beállítás akkor a idő élettartamát az az eszközt az IoT-központ által visszaadott SAS URI-azonosítók. Alapértelmezés szerint egyórás beállítva.

**Az értesítési beállítások alapértelmezett élettartam**: az idő TTL-fájl feltöltése értesítési, előtt lejárt. Alapértelmezés szerint egy nap beállítva.

**Értesítési maximális száma fájl**: A szám, ahányszor az IoT Hub megpróbál egy fájl feltöltése értesítést. Alapértelmezés szerint 10-re állítva.

Használja a következő PowerShell-parancsmag segítségével konfigurálhatja a fájl feltöltése az IoT hub beállításainak:

```powershell
Set-AzureRmIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

## <a name="next-steps"></a>További lépések

Az IoT-központ a fájl feltöltése képességeivel kapcsolatos további információk: [egy eszközről tölt fel][lnk-upload].

Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub kezelése:

* [Tömeges az IoT-eszközök kezelése][lnk-bulk]
* [Az IoT-központ metrikák][lnk-metrics]
* [Figyelési műveletek][lnk-monitor]

Az IoT-központ képességeit további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató][lnk-devguide]
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]
* [Az IoT-megoldásból az alapoktól biztonságos mentése][lnk-securing]

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-powershell-storage]: https://docs.microsoft.com/powershell/module/azurerm.storage/
[lnk-powershell-iothub]: https://docs.microsoft.com/powershell/module/azurerm.iothub/new-azurermiothub
[lnk-portal-hub]: iot-hub-create-through-portal.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal-storage]:../storage/common/storage-create-storage-account.md

---
title: Fájlfeltöltés konfigurálása az Azure PowerShell használatával | Microsoft dokumentumok
description: Az Azure PowerShell-parancsmagok használata az IoT hub konfigurálásához a csatlakoztatott eszközökről származó fájlfeltöltések engedélyezéséhez. A cél Azure-tárfiók konfigurálásával kapcsolatos információkat tartalmazza.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: c8fc0393e0961b46fbb8031d735f27e9ad785031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60318460"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>IoT Hub-fájlfeltöltések konfigurálása a PowerShell használatával

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

A [fájlfeltöltési funkció az IoT Hubban](iot-hub-devguide-file-upload.md)használatához először hozzá kell rendelnie egy Azure-tárfiókot az IoT-központhoz. Használhatja a meglévő tárfiókot, vagy hozzon létre egy újat.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, néhány perc alatt létrehozhat egy [ingyenes fiókot.](https://azure.microsoft.com/pricing/free-trial/)

* [Az Azure PowerShell-parancsmagok.](https://docs.microsoft.com/powershell/azure/install-Az-ps)

* Egy Azure IoT-központ. Ha nem rendelkezik IoT-központtal, a [New-AzIoTHub-parancsmag](https://docs.microsoft.com/powershell/module/az.iothub/new-aziothub) használatával létrehozhat egyet, vagy a portál használatával [hozzon létre egy IoT hubot.](iot-hub-create-through-portal.md)

* Egy Azure-tárfiók. Ha nem rendelkezik Azure-tárfiókkal, az [Azure Storage PowerShell-parancsmagokkal](https://docs.microsoft.com/powershell/module/az.storage/) létrehozhat egyet, vagy használhatja a [portált egy tárfiók létrehozásához](../storage/common/storage-create-storage-account.md)

## <a name="sign-in-and-set-your-azure-account"></a>Jelentkezzen be és állítsa be Azure-fiókját

Jelentkezzen be Azure-fiókjába, és válassza ki előfizetését.

1. A PowerShell-parancssorban futtassa a **Connect-AzAccount** parancsmamot:

    ```powershell
    Connect-AzAccount
    ```

2. Ha több Azure-előfizetéssel rendelkezik, az Azure-ba való bejelentkezés hozzáférést biztosít a hitelesítő adataihoz társított összes Azure-előfizetéshez. Az alábbi paranccsal felsorolhatja a használható Azure-előfizetéseket:

    ```powershell
    Get-AzSubscription
    ```

    A következő paranccsal válassza ki az ioT hub kezeléséhez a parancsok futtatásához használni kívánt előfizetést. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>A tárfiók adatainak lekérése

A következő lépések feltételezik, hogy a tárfiókot az **Erőforrás-kezelő** központi telepítési modell, és nem a **klasszikus** központi telepítési modell használatával hozta létre.

A fájlok feltöltésének konfigurálásához az eszközökről egy Azure-tárfiók csatlakozási karakterláncára van szükség. A tárfióknak ugyanabban az előfizetésben kell lennie, mint az IoT hubnak. Szüksége van egy blobtároló nevére is a tárfiókban. A tárfiók kulcsainak lekéréséhez használja a következő parancsot:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Jegyezze fel a **key1** tárfiók kulcsértékét. Szüksége van rá a következő lépésekben.

Használhat egy meglévő blobtárolót a fájlfeltöltéshez, vagy létrehozhat újat:

* A meglévő blobtárolók listázásához a tárfiókban a következő parancsokat használja:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Blob-tároló létrehozásához a tárfiókban a következő parancsokat használja:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Az IoT hub konfigurálása

Most már konfigurálhatja az IoT hub [fájlok feltöltése az IoT hub](iot-hub-devguide-file-upload.md) a tárfiók adatait.

A konfigurációhoz a következő értékek szükségesek:

* **Storage container:** Egy blob tároló egy Azure-tárfiókban a jelenlegi Azure-előfizetés az IoT hub társítására. Az előző szakaszban lekérte a szükséges tárfiók-adatokat. Az IoT Hub automatikusan létrehozza a SAS URI-k írási engedélyekkel ezt a blob tárolót az eszközök számára, amelyeket a fájlok feltöltésekor használni.

* **Értesítések fogadása a feltöltött fájlokról**: Fájlfeltöltési értesítések engedélyezése vagy letiltása.

* **SAS TTL**: Ez a beállítás az IoT Hub által az eszközre visszaadott SAS URI-k élő ideje. Alapértelmezés szerint egy órára van beállítva.

* **Fájl értesítési beállítások alapértelmezett TTL**: A fájlfeltöltési értesítés lejárta előtti ideje. Alapértelmezés szerint egy napra van beállítva.

* **A fájlok kézbesítésének maximális kézbesítési száma:** Az IoT Hub hányszor kísérel meg fájlfeltöltési értesítést kézbesíteni. Alapértelmezés szerint 10-re van állítva.

A következő PowerShell-parancsmag segítségével konfigurálhatja a fájlfeltöltési beállításokat az IoT hubon:

```powershell
Set-AzIotHub `
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

Az IoT Hub fájlfeltöltési lehetőségeiről további információt a [Fájlok feltöltése eszközről](iot-hub-devguide-file-upload.md)című témakörben talál.

Az Azure IoT Hub kezeléséről az alábbi hivatkozásokra kattintva olvashat bővebben:

* [IoT-eszközök tömeges felügyelete](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub-metrikák](iot-hub-metrics.md)
* [Műveletek figyelése](iot-hub-operations-monitoring.md)

Az IoT Hub képességeinek további megismeréséhez lásd:

* [Az IoT Hub fejlesztői útmutatója](iot-hub-devguide.md)
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
* [Biztosítsa IoT-megoldását az alapoktól kezdve](../iot-fundamentals/iot-security-ground-up.md)

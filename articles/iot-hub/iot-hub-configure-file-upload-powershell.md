---
title: Fájlfeltöltés konfigurálása az Azure PowerShell használatával |} A Microsoft Docs
description: Hogyan állítsa be IoT hubját fájl engedélyezése az Azure PowerShell-parancsmagok használatával feltölti a csatlakoztatott eszközökről. A cél az Azure storage-fiók konfigurálásával kapcsolatos információkat tartalmaz.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: 4f1a5d59c340a02dcdc0291046ef6361c6f41c86
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044912"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Konfigurálja az IoT Hub fájlfeltöltések PowerShell-lel

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Használatához a [fájlba feltöltésének működését az IoT Hub](iot-hub-devguide-file-upload.md), először társítania kell egy Azure storage-fiókot az IoT hubbal. Használjon egy meglévő tárfiókot, vagy hozzon létre egy újat.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.

* [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/install-Az-ps).

* Az Azure IoT hubra. Ha nem rendelkezik egy IoT hubot, használhatja a [New-AzIoTHub parancsmag](https://docs.microsoft.com/powershell/module/az.iothub/new-aziothub) hozzon létre egyet, vagy a portál használata [hozzon létre egy IoT hubot](iot-hub-create-through-portal.md).

* Egy Azure-tárfiók. Ha nem rendelkezik Azure storage-fiókkal, használhatja a [Azure Storage PowerShell parancsmagjainak](https://docs.microsoft.com/powershell/module/az.storage/) hozzon létre egyet, vagy a portál használata [storage-fiók létrehozása](../storage/common/storage-create-storage-account.md)

## <a name="sign-in-and-set-your-azure-account"></a>Jelentkezzen be, és állítsa be az Azure-fiókkal

Jelentkezzen be Azure-fiókjába, és válassza ki előfizetését.

1. A PowerShell-parancssorban futtassa a következő a **Connect-AzAccount** parancsmagot:

    ```powershell
    Connect-AzAccount
    ```

2. Ha több Azure-előfizetéssel rendelkezik, az Azure-bA bejelentkezik hozzáférést, az összes Azure-előfizetések a hitelesítő adatokhoz tartozó. Használja a következő parancs használható elérhető Azure-előfizetések listázásához:

    ```powershell
    Get-AzSubscription
    ```

    A következő parancs használata kezelheti az IoT hub-parancsok futtatásához használni kívánt előfizetés kiválasztásához. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>A storage-fiók adatainak beolvasása

A következő lépések azt feltételezik, hogy a tárfiók tárfiókkulcsait létrehozott a **Resource Manager** -alapú üzemi modellben, és nem a **klasszikus** üzemi modellt.

Fájlfeltöltés az eszközökről származó konfigurálásához szükség van a kapcsolati karakterláncot egy Azure storage-fiókot. A storage-fiókot az IoT hub az azonos előfizetésben kell lennie. A storage-fiókban található blob-tárolóra nevét is szükséges. A következő parancsot használja a storage-fiók kulcsok lekéréséhez:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Jegyezze fel a **key1** storage-fiók kulcs értékét. A következő lépésekben van szükség.

Meglévő blob tároló használata a fájlfeltöltési, vagy hozzon létre újat:

* A meglévő, a tárfiókban található blob-tárolók listájában, használja a következő parancsokat:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Hozzon létre egy blobtárolót a tárfiókban található, használja a következő parancsokat:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Állítsa be IoT hubját

Mostantól konfigurálhatja az IoT hub [fájlok feltöltése az IoT hub](iot-hub-devguide-file-upload.md) használatával a tárfiók részleteit.

A konfigurációs van szükség a következő értékeket:

* **A tároló**: Az aktuális Azure-előfizetés társítása az IoT hub az Azure storage-fiókban lévő blobtárolóba. A szükséges tárolási fiók adatait az előző szakaszban lekért. Az IoT Hub automatikusan létrehozza az SAS URI-k eszközöket használja, ha azok a fájlok feltöltése a blob-tárolóba írási engedéllyel rendelkező.

* **A feltöltött fájlokkra vonatkozó értesítések fogadása**: Értesítések engedélyezése vagy letiltása fájl feltöltése.

* **SAS-ÉLETTARTAM**: Ez a beállítás akkor a time-to-live, az eszközt az IoT Hub által visszaadott SAS URI-azonosítókat. Alapértelmezés szerint egy óra beállítva.

* **Értesítési beállítások alapértelmezett TTL fájl**: Értesítés a time-to-live-fájl feltöltése, mielőtt lejár. Alapértelmezés szerint egy nap beállítva.

* **A fájl értesítési kézbesítések maximális száma**: Az IoT Hub próbál fájlt hányszor értesítési töltse fel. Alapértelmezés szerint a 10-re állítva.

Használja az alábbi PowerShell-parancsmagot a fájl konfigurálása töltse fel az IoT hub beállításai:

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

Az IoT Hub a fájl feltöltése képességeivel kapcsolatos további információkért lásd: [fájlfeltöltés az eszközökről](iot-hub-devguide-file-upload.md).

Az alábbi hivatkozásokból tudhat meg többet az Azure IoT Hub kezelése:

* [IoT-eszközök tömeges felügyelete](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub metrics](iot-hub-metrics.md)
* [Műveletek figyelése](iot-hub-operations-monitoring.md)

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Az IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
* [Edge-eszközök mesterséges Intelligencia telepítése az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
* [Az IoT-megoldás az alapoktól biztonságos mentése](../iot-fundamentals/iot-security-ground-up.md)

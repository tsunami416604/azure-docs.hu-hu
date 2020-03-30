---
title: Az Azure IoT Hub-fájlok feltöltésének megismerése | Microsoft dokumentumok
description: Fejlesztői útmutató – az IoT Hub fájlfeltöltési funkciójával kezelheti a fájlok feltöltését az eszközről egy Azure storage blobtárolóba.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.openlocfilehash: 35e10c0f9babca7719ff496e7068ad1564670fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209154"
---
# <a name="upload-files-with-iot-hub"></a>Fájlok feltöltése az IoT Hubbal

Az [IoT Hub végpontok](iot-hub-devguide-endpoints.md) cikkében részletezett módon az eszköz elindíthatja a fájlfeltöltést egy eszközfelé néző végponton (**/devices/{deviceId}/files)** keresztül küldött értesítéssel. Amikor egy eszköz értesíti az IoT Hubot, hogy a feltöltés befejeződött, az IoT Hub fájlfeltöltési értesítési üzenetet küld a **/messages/servicebound/filenotifications** szolgáltatással szembeni végponton keresztül.

Ahelyett, hogy az IoT Hubon keresztül közvetítene az üzeneteket, az IoT Hub ehelyett egy társított Azure Storage-fiók diszpécserjeként működik. Egy eszköz egy tárolási jogkivonatot kér az IoT Hubtól, amely az eszköz által feltölteni kívánt fájlra jellemző. Az eszköz a SAS URI-t használja a fájl feltöltéséhez a tárolóba, és amikor a feltöltés befejeződött, az eszköz értesítést küld az IoT Hubnak a befejezésről. Az IoT Hub ellenőrzi, hogy a fájlfeltöltés befejeződött-e, majd hozzáad egy fájlfeltöltési értesítési üzenetet a szolgáltatás felé irányuló fájlértesítési végponthoz.

Mielőtt feltölt egy fájlt az IoT Hubegy eszközről, konfigurálnia kell a hub [ot egy Azure Storage-fiók társításával.](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub)

Az eszköz ezután [inicializálhatja a feltöltést,](iot-hub-devguide-file-upload.md#initialize-a-file-upload) majd értesítheti az [IoT hubot,](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) amikor a feltöltés befejeződik. Ha egy eszköz értesíti az IoT Hubot, hogy a feltöltés befejeződött, a szolgáltatás [értesítési üzenetet](iot-hub-devguide-file-upload.md#file-upload-notifications)hozhat létre.

### <a name="when-to-use"></a>A következő esetekben használja

A fájlfeltöltéssel médiafájlokat és nagy telemetriai kötegeket küldhet az időszakosan csatlakoztatott eszközök által feltöltött vagy tömörített fájloknak a sávszélesség megtakarítása érdekében.

Ha kétségei vannak a jelentett tulajdonságok, az eszközről a felhőbe irányuló üzenetek vagy a fájlok feltöltése között, olvassa el [az eszközről a felhőbe irányuló kommunikációra vonatkozó útmutatást.](iot-hub-devguide-d2c-guidance.md)

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Azure Storage-fiók társítása az IoT Hubbal

A fájlfeltöltési funkció használatához először csatolnia kell egy Azure Storage-fiókot az IoT Hubhoz. Ezt a feladatot az Azure Portalon keresztül vagy programozott módon az [IoT Hub-erőforrás-szolgáltató REST API-kon](/rest/api/iothub/iothubresource)keresztül végezheti el. Miután társított egy Azure Storage-fiókot az IoT Hub, a szolgáltatás visszaad egy SAS URI-t az eszköznek, amikor az eszköz elindítja a fájlfeltöltési kérelmet.

A [fájlok feltöltése az eszközről a felhőbe az IoT Hub](iot-hub-csharp-csharp-file-upload.md) útmutatóival teljes körű útmutatót biztosít a fájlfeltöltési folyamatról. Ezek az útmutató útmutatók bemutatják, hogyan használhatja az Azure Portalon egy tárfiókot egy IoT-központhoz.

> [!NOTE]
> Az [Azure IoT SDK-k](iot-hub-devguide-sdks.md) automatikusan kezeli a SAS URI-k lekérését, a fájl feltöltését és az IoT Hub értesítését egy befejezett feltöltésről.

## <a name="initialize-a-file-upload"></a>Fájlfeltöltés inicializálása
Az IoT Hub rendelkezik egy végpontkifejezetten az eszközök számára, hogy egy SAS URI-t kérjen a fájl feltöltéséhez. A fájlfeltöltési folyamat elindításához az `{iot hub}.azure-devices.net/devices/{deviceId}/files` eszköz postai kérelmet küld a következő JSON-törzsnek:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

Az IoT Hub a következő adatokat adja vissza, amelyeket az eszköz a fájl feltöltésére használ:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Elavult: fájlfeltöltés inicializálása GET-vel

> [!NOTE]
> Ez a szakasz elavult funkciókat ismerteti, amelyek segítségével az IoT Hubs-ból kaphat SAS-URI-t. Használja a korábban leírt POST metódust.

Az IoT Hub két REST-végpontot támogat a fájlfeltöltés, az egyik a SAS URI-tároláshoz, a másik, hogy értesítse az IoT hub egy befejezett feltöltés. Az eszköz elindítja a fájlfeltöltési folyamatot `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`úgy, hogy get-t küld az IoT hubnak a rendszerhez. Az IoT hub a következőket adja vissza:

* A feltöltendő fájlra jellemző SAS-URI.

* A feltöltés befejezése után használandó korrelációs azonosító.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Az IoT Hub értesítése egy befejezett fájlfeltöltésről

Az eszköz feltölti a fájlt a tárolóba az Azure Storage SDK-k használatával. Amikor a feltöltés befejeződött, a `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` készülék postakérést küld a következő JSON-törzsnek:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Az érték `isSuccess` egy logikai érték, amely azt jelzi, hogy a fájl feltöltése sikeresvolt-e. Az állapotkód `statusCode` a fájl tárolóba való feltöltésének `statusDescription` állapota, és `statusCode`a megfelel a .

## <a name="reference-topics"></a>Referenciatémakörök:

Az alábbi referenciatémakörök további információt nyújtanak a fájlok eszközről történő feltöltéséről.

## <a name="file-upload-notifications"></a>Fájlfeltöltési értesítések

Ha egy eszköz értesíti az IoT Hubot, hogy a feltöltés befejeződött, az IoT Hub értesítési üzenetet hoz létre. Ez az üzenet a fájl nevét és tárolási helyét tartalmazza.

[Avégpontok](iot-hub-devguide-endpoints.md)magyarázata szerint az IoT Hub fájlfeltöltési értesítéseket küld egy szolgáltatás felé néző végponton (**/messages/servicebound/fileuploadnotifications)** üzenetként. A fájlfeltöltési értesítések fogadási szemantikája megegyezik a felhőből az eszközre küldött üzenetekéből, és [életciklusa](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle)megegyezik. A fájlfeltöltési értesítés végpontjáról beolvasott minden üzenet egy JSON-rekord, amelynek tulajdonságai a következők:

| Tulajdonság | Leírás |
| --- | --- |
| VárólistánTimeUtc |Az értesítés létrehozásának idejét jelző időbélyegző. |
| DeviceId |A fájlt feltöltő eszköz **eszközazonosítója.** |
| Pacuri |A feltöltött fájl URI-ja. |
| BlobName (BlobName) |A feltöltött fájl neve. |
| LastUpdatedTime |Időbélyeg, amely jelzi, hogy mikor frissítették utoljára a fájlt. |
| BlobSizeInBájt |A feltöltött fájl mérete. |

**Példa**. Ez a példa egy fájlfeltöltési értesítési üzenet törzsét mutatja be.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Fájlfeltöltési értesítés konfigurációs beállításai

Minden IoT-központ a következő konfigurációs beállításokkal rendelkezik a fájlfeltöltési értesítésekhez:

| Tulajdonság | Leírás | Tartomány és alapértelmezett |
| --- | --- | --- |
| **enableFileUploadNotifications** |Azt szabályozza, hogy a fájlfeltöltési értesítések a fájlértesítések végpontjára íródjanak-e. |Bool. Alapértelmezett: Igaz. |
| **fileNotifications.ttlAsIso8601** |Alapértelmezett TTL a fájlfeltöltési értesítésekhez. |ISO_8601 időközt 48 H-ig (legalább 1 perc). Alapértelmezett: 1 óra. |
| **fileNotifications.lockDuration** |A fájlfeltöltési értesítések várólistájának zárolási időtartama. |5-300 másodperc (minimum 5 másodperc). Alapértelmezett: 60 másodperc. |
| **fileNotifications.maxDeliveryCount fájl** |A fájlfeltöltési értesítési várólistához való maximális kézbesítési szám. |1-ről 100-ra. Alapértelmezett: 100. |

Ezeket a tulajdonságokat az IoT-központ az Azure Portalon, az Azure CLI-n vagy a PowerShellen keresztül állíthatja be. Ennek módjáról a [Fájlfeltöltés konfigurálása](iot-hub-configure-file-upload.md)című témakörben olvashat.

## <a name="additional-reference-material"></a>További referenciaanyagok

Az IoT Hub fejlesztői útmutatójának további referenciatémakörei a következők:

* [Az IoT Hub végpontjai](iot-hub-devguide-endpoints.md) a futásidejű és felügyeleti műveletek különböző IoT-hub-végpontjait ismertetik.

* [A sávszélesség-szabályozás és](iot-hub-devguide-quotas-throttling.md) a kvóták az IoT Hub-szolgáltatásra vonatkozó kvótákat és szabályozási viselkedéseket ismertetik.

* [Az Azure IoT-eszközök és szolgáltatások SDK-k](iot-hub-devguide-sdks.md) felsorolja a különböző nyelvi SDK-k segítségével, ha az IoT Hub-szolgáltatást használó eszköz- és szolgáltatásalkalmazásokat is fejleszt.

* [Az IoT Hub lekérdezési nyelve](iot-hub-devguide-query-language.md) leírja a lekérdezési nyelvet, amelysegítségével információkat kérhet le az IoT Hubról az eszköztwins és a feladatok használatával.

* [Az IoT Hub MQTT-támogatása](iot-hub-mqtt-support.md) további információt nyújt az MQTT protokoll IoT Hub-támogatásáról.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan tölthet fel fájlokat az IoT Hubot használó eszközökről, érdekelhetik az Alábbi IoT Hub fejlesztői útmutatótémakörök:

* [Eszközidentitások kezelése az IoT Hubban](iot-hub-devguide-identity-registry.md)

* [IoT Hub-hozzáférés szabályozása](iot-hub-devguide-security.md)

* [Az állapot és a konfigurációk szinkronizálása eszköztwins használatával](iot-hub-devguide-device-twins.md)

* [Közvetlen metódus meghívása eszközön](iot-hub-devguide-direct-methods.md)

* [Feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md)

A cikkben ismertetett fogalmak némelyikének kipróbálásához tekintse meg az IoT Hub következő oktatóanyagát:

* [Fájlok feltöltése az eszközökről a felhőbe az IoT Hub segítségével](iot-hub-csharp-csharp-file-upload.md)
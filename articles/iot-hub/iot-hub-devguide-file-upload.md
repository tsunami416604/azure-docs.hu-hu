---
title: Megismerheti az Azure IoT Hub-fájl feltöltése |} A Microsoft Docs
description: Fejlesztői útmutató – eszközök fájljainak a fájlfeltöltési funkcióról az IoT Hub kezelése feltöltése egy Azure storage blob-tároló használata.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.openlocfilehash: 217d348eacab30b90e06fe805d9cdb0cf32349ac
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59050730"
---
# <a name="upload-files-with-iot-hub"></a>Fájlok feltöltése az IoT Hub

A részletes a [IoT Hub-végpontok](iot-hub-devguide-endpoints.md) a cikkben egy eszköz első lépésként feltöltés küld értesítést egy eszközre irányuló végponton keresztül (**/devices/ {deviceId} / fájlok**). Amikor egy eszköz értesíti, hogy a feltöltés befejeződése az IoT Hub, IoT Hub keresztül fájl feltöltése értesítő üzenetet küld a **/messages/servicebound/filenotifications** szolgáltatás felé néző végpont.

Helyett kereskedelmi üzeneteket keresztül magát az IoT Hub IoT Hub inkább funkcionál egy dispatcher társított Azure Storage-fiókba. Egy eszközt az IoT Hub, az eszköz felhasználja a feltöltendő fájl adott tárolási tokent kér. Az eszköz az SAS URI-t használja a fájl feltöltése a tárolóba, és a feltöltés befejeztével az eszköz befejezéséről értesítést küld az IoT hubnak. Az IoT Hub ellenőrzi a fájl feltöltése befejeződött, és a szolgáltatás által használt értesítési végpont feltöltési fájlértesítési üzenetek hozzáadja.

Mielőtt egy eszközről az IoT hub feltölt egy fájlt, konfigurálnia kell a hub által [társítása egy Azure Storage](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub) azt a fiókot.

Az eszköz lehet majd [inicializálása egy feltöltési](iot-hub-devguide-file-upload.md#initialize-a-file-upload) , majd [értesítése az IoT hub](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) a feltöltés befejezését. Ha szükséges, amikor egy eszköz értesítést küld az IoT Hub a feltöltés befejeződése, a szolgáltatás hozhat létre egy [értesítési üzenet](iot-hub-devguide-file-upload.md#file-upload-notifications).

### <a name="when-to-use"></a>A következő esetekben használja

Fájl feltöltése használatával médiafájlokhoz, és csak időszakosan kapcsolódó eszközök által feltöltött vagy menteni a sávszélesség tömörített nagy telemetriai kötegek küldése.

Tekintse meg [eszközről a felhőbe való kommunikáció útmutatást](iot-hub-devguide-d2c-guidance.md) Ha bizonytalan, jelentett tulajdonságok, az eszköz – felhő üzeneteket és a fájl feltöltése használata között.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Egy Azure Storage-fiókot társítja az IoT hubbal

A fájlfeltöltési funkciókhoz használatához először az IoT hub kell kapcsolni egy Azure Storage-fiókot. Ez a feladat az Azure Portalon vagy programozott módon keresztül is elvégezheti a [az IoT Hub erőforrás-szolgáltató REST API-k](/rest/api/iothub/iothubresource). Azure Storage-fiókhoz társított az IoT hubhoz, ha a a szolgáltatás a egy fájl feltöltési kérés indításakor az eszközön a SAS URI-t egy eszközhöz adja vissza.

A [töltse fel a fájlokat az eszközről a felhőbe, az IoT Hub](iot-hub-csharp-csharp-file-upload.md) útmutatókat, adja meg a fájl feltöltési folyamat teljes leírását. Ezek az útmutatók bemutatják, hogyan storage-fiókot társítson egy IoT hubra az Azure portal használatával.

> [!NOTE]
> A [Azure IoT SDK-k](iot-hub-devguide-sdks.md) automatikusan kezelik az SAS URI lekérdezése, a fájl feltöltése és a egy befejezett feltöltésről az IoT Hub értesítése.

## <a name="initialize-a-file-upload"></a>A fájlfeltöltés inicializálása
Az IoT Hub a kifejezetten a kérelem a fájl feltöltése Storage SAS URI-eszközök egy végponttal rendelkezik. A feltöltési folyamat elindításához az eszköz egy POST kérést küld `{iot hub}.azure-devices.net/devices/{deviceId}/files` az alábbi JSON-törzse:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

Az IoT Hub adja vissza az eszköz a fájl feltöltéséhez használja az alábbi adatokat:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Elavult: inicializálása egy a GET-fájl feltöltése

> [!NOTE]
> Ez a szakasz ismerteti az elavult funkciók a SAS URI fogadása az IoT hubról. Használja a korábban leírt POST metódust.

Az IoT Hub két REST-végpontokat támogatja a feltöltés, egy get értesíteni az IoT hub egy befejezett feltöltésről, tárolás és a másik a SAS URI-rendelkezik. Az eszköz a feltöltési folyamat elindul, ha egy GET küld az IoT hub, `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. Az IoT hub adja vissza:

* A SAS URI-t adott-e a fájl fel kell tölteni.

* A korrelációs Azonosítót kell használni, a feltöltés befejeződése után.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Az IoT Hub a befejezett fájlfeltöltés értesítése

Az eszköz feltölti a fájlt az Azure Storage SDK-k használatával. A feltöltés befejeződése után az eszköz egy POST kérést küld `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` az alábbi JSON-törzse:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Az érték `isSuccess` egy olyan logikai, amely azt jelzi, hogy a fájl feltöltése sikeresen befejeződött. Állapotkódja `statusCode` a Storage, a fájl feltöltésének állapota és a `statusDescription` felel meg a `statusCode`.

## <a name="reference-topics"></a>Referencia-témaköreit:

A következő referencia-témakörök nyújtanak további információt az eszközről fájlok feltöltése.

## <a name="file-upload-notifications"></a>Fájl feltöltése értesítések

Igény szerint amikor egy eszköz értesíti, hogy a feltöltés befejeződése az IoT Hub, IoT Hub állít elő, egy értesítési üzenetet. Ez az üzenet tartalmazza a fájl nevét és a tárolási helyét.

A [végpontok](iot-hub-devguide-endpoints.md), az IoT Hub kínál a fájl feltöltése értesítést a szolgáltatás felé néző végpont (**/messages/servicebound/fileuploadnotifications**) üzenetekként. A fájl feltöltése értesítések fogadása szemantikát megegyeznek a felhőből az eszközre irányuló üzenetek, és azonos [üzenet életciklus](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-lifecycle). Minden a fájl feltöltése értesítési végpont lekért üzenet egy JSON-rekord a következő tulajdonságokkal:

| Tulajdonság | Leírás |
| --- | --- |
| EnqueuedTimeUtc |Az értesítés létrehozásának jelző időbélyeg. |
| Eszközazonosító |**DeviceId** az eszköz, amely a fájl feltöltése. |
| BlobUri |URI-ját a feltöltött fájl. |
| BlobName |A feltöltött fájl neve. |
| LastUpdatedTime |Jelzi, ha a fájl utolsó frissítés időbélyege. |
| BlobSizeInBytes |A feltöltött fájl mérete. |

**Példa**. Ez a példa bemutatja, hogy a szervezet egy fájl feltöltése értesítő üzenetet.

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

## <a name="file-upload-notification-configuration-options"></a>Fájl feltöltése értesítési konfigurációs beállítások

Minden IoT-központ rendelkezik az alábbi konfigurációs lehetőségeket a fájl feltöltése értesítéseket:

| Tulajdonság | Leírás | Tartomány és az alapértelmezett |
| --- | --- | --- |
| **enableFileUploadNotifications** |Azt szabályozza, hogy e fájl feltöltése értesítések kerüljenek-e az értesítések végpont. |Logikai. Alapértelmezett: Értéke TRUE. |
| **fileNotifications.ttlAsIso8601** |Alapértelmezett TTL-fájl feltöltése az értesítésekhez. |ISO_8601 időköz legfeljebb 48 óra (legalább 1 perc). Alapértelmezett: 1 óra. |
| **fileNotifications.lockDuration** |A fájl feltöltése értesítések várólista Zárolás időtartama. |5 és 300 másodperc (legalább 5 másodperces). Alapértelmezett: 60 másodperc. |
| **fileNotifications.maxDeliveryCount** |Kézbesítések maximális száma a fájl feltöltése az értesítési várólista. |1 és 100 között. Alapértelmezett: 100. |

## <a name="additional-reference-material"></a>További – referenciaanyag

Az IoT Hub fejlesztői útmutató más referencia témakörei a következők:

* [IoT Hub-végpontok](iot-hub-devguide-endpoints.md) ismerteti a különböző IoT hub-végpontok futásidejű és felügyeleti műveletekhez.

* [Sávszélesség-szabályozási és kvóták](iot-hub-devguide-quotas-throttling.md) ismerteti a kvóták és szabályozás viselkedéseket, amelyek érvényesek az IoT Hub szolgáltatást.

* [Az Azure IoT eszköz- és szolgáltatásspecifikus SDK-k](iot-hub-devguide-sdks.md) felsorolja a különböző nyelvű SDK-ban is használhatja az IoT Hub szolgáltatással kommunikáló eszközt és szolgáltatást is alkalmazások fejlesztése során.

* [IoT Hub lekérdezési nyelv](iot-hub-devguide-query-language.md) ismerteti a lekérdezési nyelv az ikereszközökhöz és feladatokhoz kapcsolatos adatok lekérését az IoT Hub segítségével.

* [IoT Hub MQTT-támogatás](iot-hub-mqtt-support.md) további információ az IoT Hub-támogatásról nyújt az MQTT protokoll.

## <a name="next-steps"></a>További lépések

Most már bemutattuk, hogyan lehet a fájlok feltöltése eszközökről, az IoT Hub használatával, érdekelheti, az alábbi az IoT Hub fejlesztői útmutató témakörök:

* [Az IoT Hub eszközidentitások kezelése](iot-hub-devguide-identity-registry.md)

* [IoT Hub-hozzáférés szabályozása](iot-hub-devguide-security.md)

* [Ikereszközök használata az állapot és a konfiguráció szinkronizálása](iot-hub-devguide-device-twins.md)

* [Az eszközön közvetlen metódus meghívása](iot-hub-devguide-direct-methods.md)

* [Feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md)

Próbálja ki a jelen cikkben ismertetett fogalmakat, tekintse meg a következő IoT Hub-oktatóanyag:

* [Fájlok feltöltése eszközökről a felhőbe, az IoT Hub hogyan](iot-hub-csharp-csharp-file-upload.md)
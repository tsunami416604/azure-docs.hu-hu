---
title: "Azure IoT Hub-fájl feltöltése megértése |} Microsoft Docs"
description: "Fejlesztői útmutató - eszköz fájljainak a fájlfeltöltési szolgáltatása az IoT-központ kezeléséhez feltöltése az Azure blob tároló használja."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a0427925-3e40-4fcd-96c1-2a31d1ddc14b
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 75a6b9bc3ecfe6d6901bb38e312d62333f38daf1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="upload-files-with-iot-hub"></a>Az IoT hubbal fájlok feltöltése

A részletes a [IoT-központok végpontjai] [ lnk-endpoints] a cikkben egy eszköz is kezdeményezhető a fájlfeltöltés úgy, hogy egy eszköz felé néző végpont keresztül értesítést küld (**/devices/ {deviceId} / fájlok**). Amikor egy eszköz értesíti, hogy befejeződött-e a feltöltés IoT Hub, az IoT-központ keresztül fájl feltöltése értesítő üzenetet küld a **/messages/servicebound/filenotifications** szolgáltatás felé néző végpont.

Helyett kereskedelmi üzeneteket maga az IoT-központ keresztül az IoT-központ helyette úgy működik, mint egy kézbesítő társított Azure Storage-fiók számára. Egy eszköz tárolási jogkivonat kéri le, amely csak az eszköz kíván feltölteni a fájlt az IoT-központ. Az eszköz feltölteni a fájlt az tárolási a SAS URI-t használ, és a feltöltés befejeződése az eszköz az IoT hubhoz befejezéséről értesítést küld. Az IoT-központ ellenőrzi a fájl feltöltése befejeződött, és hozzáadja a fájl feltöltése értesítési üzenetet a szolgáltatás felé néző fájl értesítési végpont.

Mielőtt IoT-központot egy fájlt feltölteni egy eszközről, konfigurálnia kell a a központ által [társítása egy Azure Storage] [ lnk-associate-storage] azt a fiókot.

Az eszköz lehet majd [feltöltés inicializálása] [ lnk-initialize] , majd [értesíteni az IoT-központ] [ lnk-notify] a feltöltés befejezését. Ha szükséges, amikor egy eszköz értesíti az IoT-központ, hogy helyesek-e a feltöltés, a szolgáltatás hozhat létre egy [értesítési üzenet][lnk-service-notification].

### <a name="when-to-use"></a>A következő esetekben használja

A fájl feltöltése is elküldhetik a médiafájlokat és nagy telemetriai kötegek időnként csatlakoztatott eszközök által feltöltött, illetve sávszélességet tömörítve.

Tekintse meg [eszközről a felhőbe kommunikációs útmutatást] [ lnk-d2c-guidance] Ha bizonytalan jelentett tulajdonságok, az eszköz a felhőbe küldött üzeneteket vagy a fájl feltöltése használata között.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Egy Azure Storage-fiók társítása az IoT-központ

A fájl feltöltése funkciók használatához először egy Azure Storage-fiókot hozzá kell rendelnie az IoT-központ. Ez a feladat keresztül hajthatja végre a [Azure-portálon][lnk-management-portal], vagy programozott módon a [IoT-központ erőforrás-szolgáltató REST API-k][lnk-resource-provider-apis]. Egy Azure Storage-fiókot az IoT Hub vannak társítva, ha a szolgáltatás SAS URI eszköz ad vissza, ha az eszköz kezdeményezi a fájl feltöltési kérést.

> [!NOTE]
> A [Azure IoT SDK-k] [ lnk-sdks] automatikusan kezeli a SAS URI-JÁNAK beolvasása, feltölteni a fájlt, és a feltöltött az IoT-központ, amely értesíti.


## <a name="initialize-a-file-upload"></a>A fájlfeltöltés inicializálása
Az IoT-központ végpont kifejezetten eszközök feltölteni a fájlt a tároló SAS URI tartozik. A fájl feltöltési folyamat elindításához az eszköz egy POST kérést küld `{iot hub}.azure-devices.net/devices/{deviceId}/files` , a következő JSON-törzsére:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

Az IoT-központ adja vissza a következő adatait, amely az eszköz lehet feltölteni a fájlt használja:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Elavult: a GET fájlfeltöltés inicializálása

> [!NOTE]
> Ez a szakasz ismerteti a SAS URI fogadása az IoT-központ elavult funkció. A POST metódussal a fentiekben ismertetett használja.

Az IoT-központ rendelkezik két REST-végpontok fájlfeltöltés, tárolási és egyéb értesíteni az IoT-központ, a feltöltött SAS URI segítségével egy támogatásához. Az eszköz a feltöltési folyamat indít el egy GET küld az IoT hub, `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. Az IoT hub adja vissza:

* SAS URI jellemző a fájlt fel kell tölteni.
* Ha befejeződött a feltöltés használandó Korrelációazonosító.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Az IoT-központ a befejezett fájlfeltöltés értesítése

Az eszköz feltölteni a fájlt az Azure Storage SDK-k segítségével történő felelős. A feltöltés befejeződése után az eszköz egy POST kérést küld `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` , a következő JSON-törzsére:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Értékének `isSuccess` van egy logikai jelző, hogy a fájl feltöltése sikeresen befejeződött. Állapotkódja `statusCode` tárhelyre, a fájl feltöltése a állapota és a `statusDescription` megfelel-e a `statusCode`.

## <a name="reference-topics"></a>Referencia-témaköreit:

A következő témaköröket nyújtanak további információt az eszközről fájlok feltöltése.

## <a name="file-upload-notifications"></a>Fájl feltöltése értesítések

Nem kötelező amikor egy eszköz értesíti az IoT-központ, hogy befejeződött-e a feltöltés, IoT-központ hozhat létre egy értesítési üzenetet, amely tartalmazza a fájl nevét és a tárolási helyét.

A [végpontok][lnk-endpoints], IoT-központ biztosítja a fájl feltöltése értesítések keresztül a szolgáltatás felé néző végpont (**/messages/servicebound/fileuploadnotifications**) üzeneteihez. A fájl feltöltése értesítések receive szemantikája ugyanúgy felhő-eszközre küldött üzenetek, és az [üzenet életciklus][lnk-lifecycle]. Minden egyes a fájl feltöltése értesítési végpont lekért üzenet egy JSON-rekord a következő tulajdonságokkal:

| Tulajdonság | Leírás |
| --- | --- |
| EnqueuedTimeUtc |Az értesítés létrehozásának jelző időbélyegző. |
| Eszközazonosító |**DeviceId** az eszköz, amely a fájl feltöltése. |
| BlobUri |A feltöltött fájl URI Azonosítóját. |
| Blobnév |A feltöltött fájl neve. |
| LastUpdatedTime |Timestamp típusú, amely azt jelzi, ha a fájl utolsó módosításának. |
| BlobSizeInBytes |A feltöltött fájl méretét. |

**Példa**. Ez a példa bemutatja, hogy a szervezet egy fájl feltöltése értesítési üzenetet.

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

Minden egyes IoT-központ mutatja meg az alábbi konfigurációs lehetőségeket a fájl feltöltése értesítések:

| Tulajdonság | Leírás | Tartomány- és alapértelmezett |
| --- | --- | --- |
| **enableFileUploadNotifications** |Meghatározza, hogy fájl feltöltése értesítések írja a rendszer a fájl értesítések végpont. |Logikai érték. Alapértelmezett: igaz. |
| **fileNotifications.ttlAsIso8601** |Alapértelmezett élettartam-fájl feltöltése értesítéseket. |ISO_8601 időköz legfeljebb 48 H (legalább 1 perc). Alapértelmezett: 1 óra. |
| **fileNotifications.lockDuration** |A fájl feltöltése értesítések várólista zárolási időtartama. |5 és 300 másodperc (legalább 5 másodperces). Alapértelmezett: 60 másodperc. |
| **fileNotifications.maxDeliveryCount** |A fájlok maximális száma töltse fel az értesítési várólista. |1 és 100 közötti. Alapértelmezett: 100. |

## <a name="additional-reference-material"></a>További referenciaanyag

Az IoT Hub fejlesztői útmutató más hivatkozás témaköröket tartalmazza:

* [IoT-központok végpontjai] [ lnk-endpoints] ismerteti a különböző végpontok, amelyek minden egyes IoT-központ elérhetővé teszi a futásidejű és felügyeleti műveletek.
* [Sávszélesség-szabályozási és kvóták] [ lnk-quotas] ismerteti a kvóták és a szabályozás viselkedéseket, amelyek az IoT-központ szolgáltatás vonatkozik.
* [Az Azure IoT eszköz és a szolgáltatás SDK-k] [ lnk-sdks] felsorolja a különböző nyelvi használhatja az eszköz és a szolgáltatás alkalmazások gondoskodnak az IoT hubbal fejlesztésekor SDK-k.
* [Az IoT-központ lekérdezési nyelv] [ lnk-query] a lekérdezési nyelv segítségével adatok lekérését az IoT-központ az eszköz twins és feladatokat ismerteti.
* [Az IoT Hub MQTT támogatási] [ lnk-devguide-mqtt] IoT-központ támogatásával kapcsolatos további információkat biztosít a MQTT protokoll.

## <a name="next-steps"></a>Következő lépések

Most már rendelkezik megtudta, hogyan eszközök IoT-központ a fájlok feltöltéséhez, esetleg a következő IoT Hub fejlesztői útmutató témakörei iránt érdeklődik:

* [Az IoT Hub eszköz identitásainak kezelése][lnk-devguide-identities]
* [Elérés az IoT hubhoz][lnk-devguide-security]
* [Eszköz twins segítségével szinkronizálja az állapotot és konfigurációk][lnk-devguide-device-twins]
* [Az eszközön közvetlen metódus][lnk-devguide-directmethods]
* [Több eszközön feladatok ütemezése][lnk-devguide-jobs]

Ha azt szeretné, hogy próbálja ki azokat a jelen cikkben ismertetett fogalmakat, esetleg megváltozása a következő IoT Hub-oktatóanyag:

* [A felhőhöz, az IoT-központ eszközökről fájlok feltöltéséről][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-lifecycle
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

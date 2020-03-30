---
title: Ismerje meg az Azure IoT Hub identitásbeállítási nyilvántartását | Microsoft dokumentumok
description: Fejlesztői útmutató – az IoT Hub identitásbeállítás-jegyzékének leírása, valamint az eszközök kezeléséhez való használata. Az eszközidentitások tömeges importálásával és exportálásával kapcsolatos információkat tartalmazza.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.openlocfilehash: ccb840caea5d28975daaf8cbf6f0d4985bdf006d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499141"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Ismerje meg az IoT hub identitásbeállítás-beállításjegyzékét

Minden IoT hub rendelkezik egy identitás-beállításjegyzék, amely tárolja az adatokat az eszközök és modulok számára engedélyezett csatlakozni az IoT hub. Ahhoz, hogy egy eszköz vagy modul csatlakozhatna egy IoT-központhoz, az adott eszközhöz vagy modulhoz bejegyzést kell lennie az IoT hub identitásbeállítási jegyzékében. Egy eszköz vagy modul is hitelesítenie kell az IoT hub az identitás-beállításjegyzékben tárolt hitelesítő adatok alapján.

Az identitás-beállításjegyzékben tárolt eszköz- vagy modulazonosító nem különérel el a kis- és nagybetűket.

Magas szinten az identitás-beállításjegyzék eszköz- vagy modulidentitás-erőforrások REST-képes gyűjteménye. Amikor hozzáad egy bejegyzést az identitás-beállításjegyzékben, az IoT Hub létrehoz egy eszközenkénti erőforrásokat, például a várólistát, amely a repülés közbeni felhőből az eszközre irányuló üzeneteket tartalmazza.

Használja az identitásjegyzéket, ha a következőkre van szüksége:

* Az IoT-központhoz kapcsolódó eszközök vagy modulok kiépítése.
* Eszközenként/modulonkénti hozzáférés vezérlése a hub eszközéhez vagy modulnéző végpontjaihoz.

> [!NOTE]
> * Az identitásjegyzék nem tartalmaz alkalmazásspecifikus metaadatokat.
> * A modul identitása és az ikermodul nyilvános előzetes verzióban érhető el. Az alábbiakban a funkció támogatja a modul identitását, ha általánosan elérhető.
>

## <a name="identity-registry-operations"></a>Identitásjegyzék műveletei

Az IoT Hub identitásjegyzéke a következő műveleteket teszi elérhetővé:

* Eszköz- vagy modulidentitás létrehozása
* Eszköz- vagy modulidentitás frissítése
* Eszköz- vagy modulidentitás lekérése azonosítóval
* Eszköz- vagy modulidentitás törlése
* Akár 1000 identitás listázása
* Eszközidentitások exportálása az Azure blobstorageba
* Eszközidentitások importálása az Azure blob storage-ból

Mindezek a műveletek az [RFC7232-ben](https://tools.ietf.org/html/rfc7232)meghatározott optimista egyidejűséget használhatnak.

> [!IMPORTANT]
> Az IoT-központ identitásbeállítási jegyzékében lévő összes identitás lekérésének egyetlen módja az [Exportálás](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) funkció használata.

IoT Hub-identitásbeállításjegyzék:

* Nem tartalmaz alkalmazásmetaadatokat.
* Az **eszközazonosító** vagy **a moduleId** kulcsként való használatával elérhető, mint egy szótár.
* Nem támogatja a kifejező lekérdezéseket.

Az IoT-megoldás általában rendelkezik egy külön megoldás-specifikus tároló, amely tartalmazza az alkalmazás-specifikus metaadatokat. Például a megoldás-specifikus tárolja egy intelligens épület megoldás rögzíti a helyiségben, ahol a hőmérséklet-érzékelő telepítve van.

> [!IMPORTANT]
> Csak eszközkezelési és kiépítési műveletekhez használja az identitás-beállításjegyzéket. A nagy átviteli sebességű műveletek futási időben nem függhet az identitásjegyzékben végzett műveletek től. Például egy eszköz kapcsolati állapotának ellenőrzése a parancs elküldése előtt nem támogatott minta. Győződjön meg arról, hogy ellenőrizze az identitás-beállításjegyzék [szabályozási díjait](iot-hub-devguide-quotas-throttling.md) és az [eszköz szívverési](iot-hub-devguide-identity-registry.md#device-heartbeat) mintáját.

## <a name="disable-devices"></a>Eszközök letiltása

Az eszközök letiltásához frissítse az identitás **állapottulajdonságát** az identitásjegyzékben. Ezt a tulajdonságot általában két esetben használja:

* Egy kiépítési vezénylési folyamat során. További információ: [Device Provisioning](iot-hub-devguide-identity-registry.md#device-provisioning).

* Ha bármilyen okból úgy gondolja, hogy egy eszköz biztonsága sérült vagy illetéktelenné vált.

Ez a funkció modulok esetén nem érhető el.

## <a name="import-and-export-device-identities"></a>Eszközidentitások importálása és exportálása

Aszinkron műveletek az [IoT Hub erőforrás-szolgáltató végponton](iot-hub-devguide-endpoints.md) az eszközök identitások tömeges exportálása az IoT hub identitás-beállításjegyzék. Az exportálás olyan hosszú ideig futó feladat, amely az ügyfél által megadott blobtárolót használja az identitásjegyzékből beolvasott eszközidentitás-adatok mentéséhez.

Aszinkron műveletek az [IoT Hub erőforrás-szolgáltató végponton](iot-hub-devguide-endpoints.md) az eszközök identitásainak importálása tömegesen egy IoT hub identitás-beállításjegyzékbe. Az importálás olyan hosszú ideig futó feladatok, amelyek az ügyfél által megadott blobtárolóban lévő adatokat használják az eszközidentitás-adatok írásához az identitásjegyzékbe.

Az importálási és exportálási API-król további információt az [IoT Hub erőforrás-szolgáltató REST API-jai című](/rest/api/iothub/iothubresource)témakörben talál. Az importálási és exportálási feladatok futtatásáról az [IoT Hub-eszközidentitások tömeges kezelése.](iot-hub-bulk-identity-mgmt.md)

Az eszközidentitások exportálhatók és importálhatók egy IoT Hubról a Service API-n keresztül a [REST API-n](/rest/api/iothub/service/jobclient/createimportexportjob) vagy az IoT [Hub-szolgáltatás SDK-n](/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-hub-service-sdks)keresztül.

## <a name="device-provisioning"></a>Eszközkiépítés

Az adott IoT-megoldás által tárolott eszközadatok a megoldás konkrét követelményeitől függenek. De a megoldásnak legalább az eszközidentitásokat és a hitelesítési kulcsokat kell tárolnia. Az Azure IoT Hub tartalmaz egy identitás-beállításjegyzéket, amely képes tárolni az egyes eszközök értékeit, például azonosítók, hitelesítési kulcsok és állapotkódok. A megoldások más Azure-szolgáltatások, például a table storage, blob storage vagy a Cosmos DB használatával további eszközadatok tárolására.

*Az eszközkiépítés* a kezdeti eszközadatok hozzáadása a megoldás tárolóihoz. Ahhoz, hogy egy új eszköz csatlakozzon a hubhoz, hozzá kell adnia egy eszközazonosítót és kulcsokat az IoT Hub identitásbeállításjegyzékhez. A kiépítési folyamat részeként előfordulhat, hogy más megoldástárolókban inicializálnia kell az eszközspecifikus adatokat. Az Azure IoT Hub-eszközkiépítési szolgáltatás sal is engedélyezheti a nulla érintéses, just-in-time kiépítést egy vagy több IoT-központba emberi beavatkozás nélkül. További információ: [a kiépítési szolgáltatás dokumentációja.](https://azure.microsoft.com/documentation/services/iot-dps)

## <a name="device-heartbeat"></a>Eszköz szívverése

Az IoT Hub identitásjegyzéke egy **connectionState**nevű mezőt tartalmaz. Csak a **connectionState** mezőt használja a fejlesztés és a hibakeresés során. Az IoT-megoldások nem kell lekérdezni a mezőt futási időben. Például ne kérdezze le a **connectionState** mezőt, hogy ellenőrizze, hogy egy eszköz csatlakoztatva van-e, mielőtt felhőből eszközre irányuló üzenetet vagy SMS-t küldene. Javasoljuk, hogy az [ **eseményhálózaton az eszközleválasztott** eseményre](iot-hub-event-grid.md#event-types) való feliratkozással értesítéseket kapjon, és figyelje az eszköz kapcsolatának állapotát. Ebből az [oktatóanyagból](iot-hub-how-to-order-connection-state-events.md) megtudhatja, hogyan integrálhatja az IT Hubról az Eszközkapcsolt és az eszközleválasztott eseményeket az IoT-megoldásban.

Ha az IoT-megoldásnak tudnia kell, hogy egy eszköz csatlakoztatva van-e, megvalósíthatja a *szívverésmintát.*
A szívverésmintában az eszköz minden meghatározott idő alatt legalább egyszer (például óránként legalább egyszer) küld idáig az eszközről a felhőbe irányuló üzeneteket. Ezért még akkor is, ha egy eszköz nem rendelkezik adatokkal, hogy küldjön, akkor is küld egy üres eszköz-felhő üzenet (általában egy tulajdonság, amely azonosítja azt a szívverés). A szolgáltatási oldalon a megoldás egy térképet tart fenn az egyes eszközökhöz fogadott utolsó szívveréssel. Ha a megoldás nem kap szívverési üzenetet az eszköztől várt időn belül, azt feltételezi, hogy probléma van az eszközzel.

Egy összetettebb megvalósítási tartalmazhat adatokat az [Azure Monitor](../azure-monitor/index.yml) és az Azure [Resource Health](../service-health/resource-health-overview.md) azonosítására eszközök, amelyek próbálnak csatlakozni, vagy kommunikálni, de nem sikerül, ellenőrizze a [figyelő diagnosztikai](iot-hub-monitor-resource-health.md) útmutató. A szívveréses minta megvalósításakor győződjön meg arról, hogy ellenőrizze az [IoT Hub-kvóták és a szabályozások.](iot-hub-devguide-quotas-throttling.md)

> [!NOTE]
> Ha egy IoT-megoldás a kapcsolat állapotát kizárólag annak meghatározására használja, hogy küldjön felhőből az eszközre üzeneteket, és az üzenetek et nem sugározzák nagy eszközcsoportoknak, fontolja meg az egyszerűbb *rövid lejárati idő* minta használatát. Ez a minta ugyanazt az eredményt éri el, mint az eszközkapcsolati állapot beállításjegyzékének karbantartása a szívverésminta használatával, miközben hatékonyabb. Ha üzenetnyugtázást kér, az IoT Hub értesítheti, hogy mely eszközök fogadhatnak üzeneteket, és melyek nem.

## <a name="device-and-module-lifecycle-notifications"></a>Eszköz- és moduléletciklus-értesítések

Az IoT Hub értesítheti az IoT-megoldást, ha egy identitást hoz létre vagy töröl életciklus-értesítések küldésével. Ehhez az IoT-megoldásnak létre kell hoznia egy útvonalat, és az adatforrást a *DeviceLifecycleEvents* vagy *a ModuleLifecycleEvents*értékűnek kell beállítania. Alapértelmezés szerint nem küld életciklus-értesítéseket, azaz nincsenek ilyen útvonalak előre léteznek. Az értesítési üzenet tulajdonságokat és törzset tartalmaz.

Tulajdonságok: Az üzenetrendszer tulajdonságai `$` a szimbólummal vannak előrögzítve.

Értesítési üzenet az eszközhöz:

| Név | Érték |
| --- | --- |
|$content típusú | application/json |
|$iothub-enqueuedtime |  Az értesítés elküldésének időpontja |
|$iothub-üzenet-forrás | eszközLifecycleEvents |
|$content kódolás | utf-8 |
|opType (típus) | **createDeviceIdentity** vagy **deleteDeviceIdentity** |
|hubName | Az IoT Hub neve |
|deviceId | Az eszköz azonosítója |
|operationTimestamp művelet | ISO8601 működési időbélyeg |
|iothub-üzenet-séma | eszközÉletciklusÉrtesítés |

Törzs: Ez a szakasz JSON formátumban, és a létrehozott eszközidentitás ikertestvérét jelöli. Például:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```
Értesítési üzenet a modulhoz:

| Név | Érték |
| --- | --- |
$content típusú | application/json |
$iothub-enqueuedtime |  Az értesítés elküldésének időpontja |
$iothub-üzenet-forrás | modulLifecycleEvents |
$content kódolás | utf-8 |
opType (típus) | **createModuleIdentity** vagy **deleteModuleIdentity** |
hubName | Az IoT Hub neve |
moduleId | A modul azonosítója |
operationTimestamp művelet | ISO8601 működési időbélyeg |
iothub-üzenet-séma | modulÉletciklusÉrtesítés |

Törzs: Ez a szakasz JSON formátumban, és képviseli a két létrehozott modul identitás. Például:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "moduleId":"tempSensor",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>Eszközidentitás-tulajdonságok

Az eszközidentitások JSON-dokumentumokként jelennek meg a következő tulajdonságokkal:

| Tulajdonság | Beállítások | Leírás |
| --- | --- | --- |
| deviceId |szükséges, csak olvasható a frissítések |Az ASCII 7 bites alfanumerikus karakterek ből és bizonyos speciális karakterekből álló `- . + % _ # * ? ! ( ) , = @ $ '`kis- és nagybetűket megkülönböztető karakterlánc (legfeljebb 128 karakter hosszú) . |
| generationId |szükséges, csak olvasható |Egy IoT hub által generált, kis- és nagybetűket megkülönböztető karakterlánc legfeljebb 128 karakter hosszú. Ez az érték az azonos **eszközazonosítóval**rendelkező eszközök megkülönböztetésére szolgál, ha törölték és újra létrehozták őket. |
| Etag |szükséges, csak olvasható |Az eszköz identitásának gyenge ETag-jét képviselő karakterlánc az [RFC7232](https://tools.ietf.org/html/rfc7232)szerint. |
| Auth |választható |Hitelesítési információkat és biztonsági anyagokat tartalmazó összetett objektum. |
| auth.symkey |választható |Elsődleges és másodlagos kulcsot tartalmazó összetett objektum, base64 formátumban tárolva. |
| status |kötelező |Egy hozzáférési jelző. **Engedélyezhető** vagy **letiltható.** Ha **engedélyezve**van, az eszköz csatlakozhat. Ha **le van tiltva,** az eszköz nem tud hozzáférni egyetlen eszközfelé néző végponthoz sem. |
| statusReason (ok) |választható |Egy 128 karakterhosszú karakterlánc, amely tárolja az eszköz identitásállapotának okát. Minden UTF-8 karakter megengedett. |
| statusUpdateTime |írásvédett |Egy időbeli mutató, amely az utolsó állapotfrissítés dátumát és időpontját mutatja. |
| connectionState |írásvédett |A kapcsolat állapotát jelző mező: **csatlakoztatva** vagy **leválasztva.** Ez a mező az eszközkapcsolat állapotának IoT Hub nézetét jelöli. **Fontos:** Ez a mező csak fejlesztési/hibakeresési célokra használható. A kapcsolat állapota csak az MQTT vagy AMQP-t használó eszközök esetében frissül. Emellett protokollszintű pingeken (MQTT pingek en vagy AMQP-pingeken) is alapul, és legfeljebb 5 perces késleltetéssel rendelkezhet. Ezen okok miatt lehetnek hamis pozitív, például csatlakoztatottként jelentett, de leválasztott eszközök. |
| connectionStateUpdatedTime |írásvédett |Időbeli jelző, amely a kapcsolat állapotának dátumát és utolsó dátumát mutatja. |
| lastActivityTime |írásvédett |Időbeli jelző, amely az eszköz csatlakoztatásának, fogadásának vagy üzenetküldésének dátumát és utolsó időpontját mutatja. |

> [!NOTE]
> A kapcsolat állapota csak a kapcsolat állapotának IoT Hub nézetét képviselheti. A hálózati feltételektől és konfigurációktól függően az állapot frissítései késhetnek.

> [!NOTE]
> Jelenleg az eszköz SDK-k `+` nem `#` támogatják a és a karakterek a **deviceId**.

## <a name="module-identity-properties"></a>Modul identitástulajdonságai

A modulidentitások JSON-dokumentumokként jelennek meg a következő tulajdonságokkal:

| Tulajdonság | Beállítások | Leírás |
| --- | --- | --- |
| deviceId |szükséges, csak olvasható a frissítések |Az ASCII 7 bites alfanumerikus karakterek ből és bizonyos speciális karakterekből álló `- . + % _ # * ? ! ( ) , = @ $ '`kis- és nagybetűket megkülönböztető karakterlánc (legfeljebb 128 karakter hosszú) . |
| moduleId |szükséges, csak olvasható a frissítések |Az ASCII 7 bites alfanumerikus karakterek ből és bizonyos speciális karakterekből álló `- . + % _ # * ? ! ( ) , = @ $ '`kis- és nagybetűket megkülönböztető karakterlánc (legfeljebb 128 karakter hosszú) . |
| generationId |szükséges, csak olvasható |Egy IoT hub által generált, kis- és nagybetűket megkülönböztető karakterlánc legfeljebb 128 karakter hosszú. Ez az érték az azonos **eszközazonosítóval**rendelkező eszközök megkülönböztetésére szolgál, ha törölték és újra létrehozták őket. |
| Etag |szükséges, csak olvasható |Az eszköz identitásának gyenge ETag-jét képviselő karakterlánc az [RFC7232](https://tools.ietf.org/html/rfc7232)szerint. |
| Auth |választható |Hitelesítési információkat és biztonsági anyagokat tartalmazó összetett objektum. |
| auth.symkey |választható |Elsődleges és másodlagos kulcsot tartalmazó összetett objektum, base64 formátumban tárolva. |
| status |kötelező |Egy hozzáférési jelző. **Engedélyezhető** vagy **letiltható.** Ha **engedélyezve**van, az eszköz csatlakozhat. Ha **le van tiltva,** az eszköz nem tud hozzáférni egyetlen eszközfelé néző végponthoz sem. |
| statusReason (ok) |választható |Egy 128 karakterhosszú karakterlánc, amely tárolja az eszköz identitásállapotának okát. Minden UTF-8 karakter megengedett. |
| statusUpdateTime |írásvédett |Egy időbeli mutató, amely az utolsó állapotfrissítés dátumát és időpontját mutatja. |
| connectionState |írásvédett |A kapcsolat állapotát jelző mező: **csatlakoztatva** vagy **leválasztva.** Ez a mező az eszközkapcsolat állapotának IoT Hub nézetét jelöli. **Fontos:** Ez a mező csak fejlesztési/hibakeresési célokra használható. A kapcsolat állapota csak az MQTT vagy AMQP-t használó eszközök esetében frissül. Emellett protokollszintű pingeken (MQTT pingek en vagy AMQP-pingeken) is alapul, és legfeljebb 5 perces késleltetéssel rendelkezhet. Ezen okok miatt lehetnek hamis pozitív, például csatlakoztatottként jelentett, de leválasztott eszközök. |
| connectionStateUpdatedTime |írásvédett |Időbeli jelző, amely a kapcsolat állapotának dátumát és utolsó dátumát mutatja. |
| lastActivityTime |írásvédett |Időbeli jelző, amely az eszköz csatlakoztatásának, fogadásának vagy üzenetküldésének dátumát és utolsó időpontját mutatja. |

> [!NOTE]
> Jelenleg az eszköz SDK-k `+` nem `#` támogatják a és a karakterek a **deviceId** és **moduleId**.

## <a name="additional-reference-material"></a>További referenciaanyagok

Az IoT Hub fejlesztői útmutatójának további referenciatémakörei a következők:

* [Az IoT Hub-végpontok](iot-hub-devguide-endpoints.md) ismerteti a különböző végpontok, amelyek az egyes IoT-központok elérhetővé teszi a futásidejű és felügyeleti műveletek.

* [A sávszélesség-szabályozás és](iot-hub-devguide-quotas-throttling.md) a kvóták az IoT Hub-szolgáltatásra vonatkozó kvótákat és szabályozási viselkedéseket ismertetik.

* [Az Azure IoT-eszközök és szolgáltatások SDK-k](iot-hub-devguide-sdks.md) felsorolja a különböző nyelvi SDK-k segítségével, ha az IoT Hub-szolgáltatást használó eszköz- és szolgáltatásalkalmazásokat is fejleszt.

* [Az IoT Hub lekérdezési nyelve](iot-hub-devguide-query-language.md) leírja a lekérdezési nyelvet, amelysegítségével információkat kérhet le az IoT Hubról az eszköztwins és a feladatok használatával.

* [Az IoT Hub MQTT-támogatása](iot-hub-mqtt-support.md) további információt nyújt az MQTT protokoll IoT Hub-támogatásáról.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta az IoT Hub identitásbeállítás-beállításjegyzékének használatát, érdekelheti képpen az IoT Hub következő fejlesztői útmutatótémaköreit:

* [IoT Hub-hozzáférés szabályozása](iot-hub-devguide-security.md)

* [Az állapot és a konfigurációk szinkronizálása eszköztwins használatával](iot-hub-devguide-device-twins.md)

* [Közvetlen metódus meghívása eszközön](iot-hub-devguide-direct-methods.md)

* [Feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md)

A cikkben ismertetett fogalmak némelyikének kipróbálásához tekintse meg az IoT Hub következő oktatóanyagát:

* [Ismerkedés az Azure IoT Hub szolgáltatással](quickstart-send-telemetry-dotnet.md)

Az IoT Hub-eszközkiépítési szolgáltatás használatával a nulla érintéses, just-in-time kiépítés engedélyezéséről a következő témakört kell ismertennie: 

* [Azure IoT Hub Device Provisioning Service](https://azure.microsoft.com/documentation/services/iot-dps)

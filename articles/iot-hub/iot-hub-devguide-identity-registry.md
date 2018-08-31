---
title: Megismerheti az Azure IoT Hub-identitásjegyzék |} A Microsoft Docs
description: Fejlesztői útmutató – a leírását az IoT Hub eszközidentitás-jegyzék és hogyan használhatja az eszközök kezelésére. Tömeges importálása és exportálása eszközidentitások kapcsolatos információkat tartalmazza.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 4e23b70c8dc5fdacfd609fb4664a78293b9e2362
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247645"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Az IoT hub eszközidentitás-jegyzék ismertetése

Minden IoT hub tartalmaz egy identitásjegyzéket, amely az eszközök és az IoT hubhoz való csatlakozáshoz engedélyezett modulok kapcsolatos információkat tárolja. Egy eszköz vagy a modul csatlakozhat egy IoT hubot, mielőtt az adott eszköz vagy az IoT hub eszközidentitás-jegyzékben lévő modul bejegyzése kell. Egy eszköz vagy a modul is hitelesítsék magukat, és az IoT hub eszközidentitás-jegyzékben lévő tárolt hitelesítő adatok alapján.

Az eszköz vagy a modul az eszközidentitás-jegyzékben lévő tárolt csomagazonosítója kis-és nagybetűket.

Magas szintű az eszközidentitás-jegyzék egy REST-kompatibilis erőforrások gyűjteménye, eszköz vagy a modul identitás. Amikor hozzáad egy bejegyzés az eszközidentitás-jegyzékben lévő, az IoT Hub eszközönkénti erőforrások, például az üzenetsor, amely tartalmazza az átvitel alatt felhőből az eszközre irányuló üzenetek készletét hoz létre.

Használja az eszközidentitás-jegyzék, ha szüksége:

* Eszközök kiépítése, vagy az IoT hubhoz csatlakozó modulok.
* A hub eszköz vagy a modul felé néző végpontok száma – eszköz / /-modul hozzáférés szabályozása.

> [!NOTE]
> Az eszközidentitás-jegyzék nem tartalmaz minden olyan alkalmazás – is szolgáltatásspecifikus metaadatokat.

## <a name="identity-registry-operations"></a>Identitásjegyzék műveletei

Az IoT Hub-identitásjegyzék tünteti fel a következő műveleteket:

* Eszköz vagy a modul identitás létrehozása
* Eszköz vagy a modul identitás frissítése
* Beolvasása azonosító alapján eszköz vagy a modul identitás
* Törli az eszköz vagy a modul identitás
* Legfeljebb 1000 identitások listázása
> Identitás- és modul ikermodul jelenleg nyilvános előzetes verzióban. A szolgáltatás alatt fog támogatni modul identitás általános esetén érhető el.
* Az Azure blob storage-eszközidentitások exportálása
* Eszközidentitások importálása az Azure blob storage-ból

Ezeket a műveleteket használhatja az optimista egyidejűséget, megadott [RFC7232][lnk-rfc7232].

> [!IMPORTANT]
> Csak a legegyszerűbb módja, az IoT hub eszközidentitás-jegyzékben lévő összes identitások beolvasni a [exportálása] [ lnk-export] funkciót.

Az IoT Hub-identitásjegyzék:

* Bármely alkalmazás metaadat nem tartalmaz.
* Egy szótárhoz hasonlóan használatával is elérhető a **deviceId** vagy **moduleId** kulcsként.
* Nem támogatja a kifejező lekérdezéseket.

IoT-megoldások általában egy külön megoldásspecifikus áruházbeli alkalmazás – is szolgáltatásspecifikus metaadatokat tartalmazó rendelkezik. Például a megoldásspecifikus tároló intelligens épület-megoldásban rögzíti, amelyek egy hőmérséklet-érzékelő üzembe lesz helyezve a helyiségben.

> [!IMPORTANT]
> Csak az eszközidentitás-jegyzék használja eszköz kezelés és üzembe helyezés műveleteket. Nagy adatátviteli műveletek futási időben nem szabad függenie az eszközidentitás-jegyzékben lévő műveleteket végez. Például egy parancs küldése előtt a kapcsolat állapota ellenőrzése nem támogatott mintát. Mindenképp tekintse át a [díjak szabályozás] [ lnk-quotas] az eszközidentitás-jegyzék, és a [eszköz szívverés] [ lnk-guidance-heartbeat] minta.

## <a name="disable-devices"></a>Eszközök letiltása

Eszközök módosításával letilthatja a **állapot** az identitás, az eszközidentitás-jegyzékben lévő tulajdonságát. Ezt a tulajdonságot általában két esetben használhatja:

* A kiépítési vezénylési során. További információkért lásd: [Device Provisioning][lnk-guidance-provisioning].
* Ha bármilyen okból úgy gondolja, hogy az eszköz biztonsága sérült, vagy jogosulatlan elérhetetlenné válik.

Ez a funkció nem érhető el. a modulok.

## <a name="import-and-export-device-identities"></a>Eszközidentitások importálása és exportálása

Az aszinkron műveletek használata a [az IoT Hub erőforrás-szolgáltatói végpont] [ lnk-endpoints] eszközidentitások tömeges exportálása egy IoT hub identitásjegyzékében. Export eszköz identitása adatokat olvasni az eszközidentitás-jegyzék mentése egy ügyfél által megadott blob-tárolóba használó hosszú ideig futó feladatok.

Az aszinkron műveletek használata a [az IoT Hub erőforrás-szolgáltatói végpont] [ lnk-endpoints] eszközidentitások tömeges importálása egy IoT hub identitásjegyzékében. Import eszközt identitás adatokat írjon az eszközidentitás-jegyzék be adatok egy ügyfél által megadott blob-tárolóban használó hosszú ideig futó feladatok.

Az importálás és exportálás API-kkal kapcsolatos további információkért lásd: [az IoT Hub erőforrás-szolgáltató REST API-k][lnk-resource-provider-apis]. Feladatok exportálása és importálása futtatásával kapcsolatos további: [tömeges kezelése IoT Hub-eszközidentitásokat][lnk-bulk-identity].

## <a name="device-provisioning"></a>Eszköz kiépítése

Az eszközön lévő adatokat, amely egy adott IoT-megoldás tárolja, hogy a megoldás egyéni követelményei függ. De legalább egy megoldást kell tárolnia, eszközidentitások és a hitelesítési kulcsok. Az Azure IoT Hub tartalmaz egy identitásjegyzéket, amely az egyes eszközök, például az azonosítók, a hitelesítési kulcsokat és a állapotkódok tárolására is. A megoldás például a table storage, blob storage-bA vagy Cosmos DB más Azure-szolgáltatások használatával minden olyan további eszközadatok tárolásához.

*Eszközkiépítési* azt a folyamatot, a kezdeti adatok hozzáadása a tárolók a megoldásban. Ahhoz, hogy az új eszköz a hubhoz való csatlakozáshoz, hozzá kell adnia egy Eszközazonosítót és a kulcsok az IoT Hub identitásjegyzékében. A kiépítési folyamat részeként szükség lehet inicializálni az eszközre vonatkozó adatokat más megoldás tárolja. Az Azure IoT Hub Device Provisioning Service használatával engedélyezze a beavatkozás nélküli, just-in-time egy vagy több IoT hubon való üzembe helyezést, emberi beavatkozás nélkül. További tudnivalókért tekintse meg a [provisioning service dokumentációja][lnk-dps].

## <a name="device-heartbeat"></a>Eszköz szívverés

Az IoT Hub eszközidentitás-jegyzék neve mezőt tartalmaz **connectionState**. Csak a **connectionState** mezőt a fejlesztés és hibakeresés során. IoT-megoldások nem kell lekérdezni a mező futásidőben. Például nem lekérdezni a **connectionState** mezőt, ellenőrizze, hogy ha egy eszköz csatlakoztatva van, a felhőből az eszközre üzenetet vagy SMS küldése előtt. Javasoljuk, hogy feliratkozik a [ **eszköz leválasztva** esemény](https://docs.microsoft.com/azure/iot-hub/iot-hub-event-grid#event-types) az Event Grid értesítéseket kaphat, és figyelheti az eszköz kapcsolati állapotát. Ezzel [oktatóanyag](https://docs.microsoft.com/azure/event-grid/publish-iot-hub-events-to-logic-apps) megtudhatja, hogyan integrálható az események az IoT Hub az IoT-megoldás.

Ha az IoT-megoldás tudnia kell, ha egy eszköz csatlakozik, valósítható meg a *szívverés minta*.
A szívverés a mintában az eszköz eszköz – felhő üzeneteket küld legalább egyszer minden rögzített időn (például óránként legalább egyszer). Ezért akkor is, ha egy eszköz nem rendelkezik küldendő adatok, továbbra is üzenetet küld az egy üres eszközről a felhőbe (általában a Ez a tulajdonság azt állapítja meg szívverés). Szolgáltatás oldalán a megoldás létrehoz egy térkép a minden egyes eszközhöz kapott utolsó szívverés. Ha a megoldás az eszközről a várt időn belül nem kap szívverésüzenet, azt feltételezi, hogy nincs-e az eszköz egy hibás.

Egy összetettebb végrehajtása sikerült tartalmazzák kapcsolatos információt [műveletek figyelése] [ lnk-devguide-opmon] próbál csatlakozni, vagy a kommunikációhoz, de sikertelen eszközök azonosítása. Ellenőrizze, hogy a szívverést a minta megvalósításakor [IoT Hub kvótái és szabályozások][lnk-quotas].

> [!NOTE]
> Ha egy IoT-megoldás használ a kapcsolat állapota kizárólag határozza meg, hogy a felhőből az eszközre irányuló üzenetek küldéséhez, és üzeneteket a rendszer nem közvetítést nagyszámú eszközön, fontolja meg az egyszerűbb *rövid lejárati idő* mintát. Ez a minta egy kapcsolat állapota eszközjegyzékében mintával a szívverés, ugyanakkor hatékonyabban karbantartása ugyanazt az eredményt éri el. Ha üzenetet a nyugtázás kér, az IoT Hub értesíti a felhasználót, mely eszközök tudnak üzeneteket fogadni, és amelyek nem állnak.

## <a name="device-and-module-lifecycle-notifications"></a>Eszköz- és modul életciklusára vonatkozó értesítések

Az IoT Hub értesítheti az IoT-megoldás, ha az identitás létrehozása vagy törlése életciklusára vonatkozó értesítések küldésével. Ehhez az IoT-megoldás van szüksége, hozzon létre egy útvonalat és beállíthatja az adatforrás egyenlő *DeviceLifecycleEvents* vagy *ModuleLifecycleEvents*. Alapértelmezés szerint nincs életciklus-értesítést kapnak, azt jelenti, nincs ilyen útvonal már léteznie kell. Üzenet tulajdonságai és törzs tartalmaz.

Tulajdonságok: Üzenet Rendszertulajdonságok van fűzve előtagként a `'$'` szimbólum.

Értesítési üzenet eszköz:

| Name (Név) | Érték |
| --- | --- |
|$content-típus | application/json |
|$iothub-enqueuedtime |  Idő, amikor az értesítés küldése |
|$iothub-üzenet-forrás | deviceLifecycleEvents |
|$content-kódolás | utf-8 |
|opType | **createDeviceIdentity** vagy **deleteDeviceIdentity** |
|HubName | IoT Hub nevét |
|deviceId | Az eszköz azonosítója |
|operationTimestamp | ISO8601 időbélyegző-művelet |
|iothub-message-schema | deviceLifecycleNotification |

Szervezet: Ebben a szakaszban JSON formátumban vannak, és a iker, a létrehozott eszközidentitással jelöli. Például:

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
Értesítési üzenet modul:

| Name (Név) | Érték |
| --- | --- |
$content-típus | application/json |
$iothub-enqueuedtime |  Idő, amikor az értesítés küldése |
$iothub-üzenet-forrás | moduleLifecycleEvents |
$content-kódolás | utf-8 |
opType | **createModuleIdentity** vagy **deleteModuleIdentity** |
HubName | IoT Hub nevét |
moduleId | A modul azonosítója |
operationTimestamp | ISO8601 időbélyegző-művelet |
iothub-message-schema | moduleLifecycleNotification |

Törzse: Ez a szakasz JSON formátumban vannak, és az ikereszköz az létrehozott modul identitás jelöli. Például:

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

## <a name="device-identity-properties"></a>Eszköztulajdonságok identitás

Eszközidentitások jelentésekként jelennek meg az alábbi tulajdonságokkal rendelkező JSON-dokumentumok:

| Tulajdonság | Beállítások | Leírás |
| --- | --- | --- |
| deviceId |szükség esetén a frissítések csak olvasható |A kis-és nagybetűket karakterlánc (legfeljebb 128 karakter hosszú) ASCII 7 bites alfanumerikus karaktereket, valamint bizonyos speciális karaktereket: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| generationId |kötelező, csak olvasható |Az IoT hub által létrehozott, a kis-és nagybetűket karakterlánc legfeljebb 128 karakter hosszúságú. Ez az érték azonos eszközök megkülönböztetésére használható **deviceId**, amikor törli és újból létrehozza. |
| Az ETag |kötelező, csak olvasható |Egy gyenge ETag az eszközidentitást képviselő megfelelően karakterláncot [RFC7232][lnk-rfc7232]. |
| hitelesítés |választható |A hitelesítési adatokat, és biztonsági anyagokat tartalmazó összetett objektum. |
| auth.symkey |választható |Egy összetett objektum egy elsődleges és a egy másodlagos kulcsot tartalmazó base64 formátumban tárolja. |
| status |szükséges |Az access kijelző. Lehet **engedélyezve** vagy **letiltott**. Ha **engedélyezve**, az eszköz csatlakozhat. Ha **letiltott**, az eszköz nem fér hozzá a bármely eszköz által használt végpont. |
| statusReason |választható |128 karakter hosszú karakterlánc, amely tárolja az Eszközállapot-identitás az az oka. Az összes UTF-8 karakterek használhatók. |
| statusUpdateTime |csak olvasható |A historikus mutató, dátuma és időpontja, utolsó frissítésének állapota látható. |
| connectionState |csak olvasható |A mező jelzi a kapcsolat állapota: vagy **csatlakoztatva** vagy **leválasztott**. Ez a mező képviseli az IoT Hub nézetben, az eszköz kapcsolati állapotát. **Fontos**: Ez a mező csak fejlesztési és hibakeresési célokra használandó. A kapcsolat állapota úgy frissül, csak a MQTT, AMQP vagy használó eszközök esetében. Ezenkívül protokollszintű pingelésre (MQTT pingelésre vagy AMQP pingelésre.) alapján, és veheti fel a maximális késleltetés csak 5 perc. Ebből kifolyólag lehet téves, például a csatlakoztatott eszközök jelentve, de, amelyek le vannak választva. |
| connectionStateUpdatedTime |csak olvasható |A historikus mutató, a dátum és idő utolsó megjelenítése a kapcsolati állapot frissítése megtörtént. |
| lastActivityTime |csak olvasható |Historikus mutató, a dátum és idő utolsó: az eszköz csatlakoztatva, fogadott vagy elküldött egy üzenetet. |

> [!NOTE]
> Kapcsolat állapota csak hozhat létre az IoT Hub nézetben, a kapcsolat állapotát. Hálózati körülmények és konfigurációk függően ez az állapot frissítése késhet.

## <a name="module-identity-properties"></a>A modul identitás tulajdonságai

A modul identitások jelentésekként jelennek meg az alábbi tulajdonságokkal rendelkező JSON-dokumentumok:

| Tulajdonság | Beállítások | Leírás |
| --- | --- | --- |
| deviceId |szükség esetén a frissítések csak olvasható |A kis-és nagybetűket karakterlánc (legfeljebb 128 karakter hosszú) ASCII 7 bites alfanumerikus karaktereket, valamint bizonyos speciális karaktereket: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| moduleId |szükség esetén a frissítések csak olvasható |A kis-és nagybetűket karakterlánc (legfeljebb 128 karakter hosszú) ASCII 7 bites alfanumerikus karaktereket, valamint bizonyos speciális karaktereket: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| generationId |kötelező, csak olvasható |Az IoT hub által létrehozott, a kis-és nagybetűket karakterlánc legfeljebb 128 karakter hosszúságú. Ez az érték azonos eszközök megkülönböztetésére használható **deviceId**, amikor törli és újból létrehozza. |
| Az ETag |kötelező, csak olvasható |Egy gyenge ETag az eszközidentitást képviselő megfelelően karakterláncot [RFC7232][lnk-rfc7232]. |
| hitelesítés |választható |A hitelesítési adatokat, és biztonsági anyagokat tartalmazó összetett objektum. |
| auth.symkey |választható |Egy összetett objektum egy elsődleges és a egy másodlagos kulcsot tartalmazó base64 formátumban tárolja. |
| status |szükséges |Az access kijelző. Lehet **engedélyezve** vagy **letiltott**. Ha **engedélyezve**, az eszköz csatlakozhat. Ha **letiltott**, az eszköz nem fér hozzá a bármely eszköz által használt végpont. |
| statusReason |választható |128 karakter hosszú karakterlánc, amely tárolja az Eszközállapot-identitás az az oka. Az összes UTF-8 karakterek használhatók. |
| statusUpdateTime |csak olvasható |A historikus mutató, dátuma és időpontja, utolsó frissítésének állapota látható. |
| connectionState |csak olvasható |A mező jelzi a kapcsolat állapota: vagy **csatlakoztatva** vagy **leválasztott**. Ez a mező képviseli az IoT Hub nézetben, az eszköz kapcsolati állapotát. **Fontos**: Ez a mező csak fejlesztési és hibakeresési célokra használandó. A kapcsolat állapota úgy frissül, csak a MQTT, AMQP vagy használó eszközök esetében. Ezenkívül protokollszintű pingelésre (MQTT pingelésre vagy AMQP pingelésre.) alapján, és veheti fel a maximális késleltetés csak 5 perc. Ebből kifolyólag lehet téves, például a csatlakoztatott eszközök jelentve, de, amelyek le vannak választva. |
| connectionStateUpdatedTime |csak olvasható |A historikus mutató, a dátum és idő utolsó megjelenítése a kapcsolati állapot frissítése megtörtént. |
| lastActivityTime |csak olvasható |Historikus mutató, a dátum és idő utolsó: az eszköz csatlakoztatva, fogadott vagy elküldött egy üzenetet. |

## <a name="additional-reference-material"></a>További – referenciaanyag

Az IoT Hub fejlesztői útmutató más referencia témakörei a következők:

* [IoT Hub-végpontok] [ lnk-endpoints] ismerteti a különféle végpontok, amely minden IoT-központ közzéteszi a futásidejű és felügyeleti műveletekhez.
* [Sávszélesség-szabályozási és kvóták] [ lnk-quotas] ismerteti a kvóták és szabályozás viselkedéseket, amelyek érvényesek az IoT Hub szolgáltatást.
* [Az Azure IoT eszköz- és szolgáltatásspecifikus SDK-k] [ lnk-sdks] felsorolja a különböző nyelvű SDK-ban is használhatja az IoT Hub szolgáltatással kommunikáló eszközt és szolgáltatást is alkalmazások fejlesztése során.
* [IoT Hub lekérdezési nyelv] [ lnk-query] ismerteti a lekérdezési nyelv az ikereszközökhöz és feladatokhoz kapcsolatos adatok lekérését az IoT Hub segítségével.
* [IoT Hub MQTT-támogatás] [ lnk-devguide-mqtt] további információ az IoT Hub-támogatásról nyújt az MQTT protokoll.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan használhatja az IoT Hub eszközidentitás-jegyzék, érdekelheti, az alábbi az IoT Hub fejlesztői útmutató témakörök:

* [Az IoT Hub az adathozzáférés szabályozása][lnk-devguide-security]
* [Ikereszközök használata az állapot és a konfiguráció szinkronizálása][lnk-devguide-device-twins]
* [Az eszközön közvetlen metódus meghívása][lnk-devguide-directmethods]
* [Feladatok ütemezése több eszközön][lnk-devguide-jobs]

Próbálja ki a jelen cikkben ismertetett fogalmakat, tekintse meg a következő IoT Hub-oktatóanyag:

* [Az Azure IoT Hub használatának első lépései][lnk-getstarted-tutorial]

Böngészhet a beavatkozás nélküli, just-in-time kiépítését lehetővé tevő, olvassa el az IoT Hub Device Provisioning Service használatával: 

* [Az Azure IoT Hub Device Provisioning Service][lnk-dps]


<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: quickstart-send-telemetry-dotnet.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps

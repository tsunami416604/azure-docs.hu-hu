---
title: Az Azure IoT Hub identitásjegyzékhez megértése |} Microsoft Docs
description: Fejlesztői útmutató - leírását az IoT-központ identitásjegyzékhez és használata az eszközök kezeléséhez. Tömeges az importálás és exportálás eszköz identitások kapcsolatos információkat tartalmazza.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 9a3d3d1c93ce0c8bc782a2634eb7be9b95fcf4b4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633570"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Az IoT hub a az identitásjegyzékhez ismertetése

Minden IoT-központ rendelkezik, amely az eszközök és az IoT hub való csatlakozáshoz engedélyezett modulok kapcsolatos információkat tárolja az identitásjegyzékhez. Egy eszköz vagy modul akkor is csatlakozhatnak az IoT-központ, az adott eszköz vagy az IoT-központ identitásjegyzékhez modul bejegyzése kell. Egy eszköz vagy modul is kell hitelesítenie és az IoT hub az identitásjegyzékhez tárolt hitelesítő adatok alapján.

Az eszköz vagy modul azonosító tárolja az identitásjegyzékhez: kis-és nagybetűket.

Magas szinten a identitásjegyzékhez egy REST-kompatibilis erőforrások gyűjteménye, eszköz vagy modul identitás. Az identitásjegyzékhez adjon hozzá egy bejegyzést IoT-központ létrehoz egy eszköz erőforrások, például a várólista, amely tartalmazza az üzenetsoroktól felhő eszközre üzenetek készletét.

Ha szeretné, használja az identitásjegyzékhez:

* Eszközök beállításához vagy modulokat, az IoT hub-hez.
* /-Eszköz / /-modul való hozzáférést a hub eszköz vagy modul felé néző végpontok.

> [!NOTE]
> Az identitásjegyzékhez nem minden alkalmazás-specifikus metaadatot tartalmaz.

## <a name="identity-registry-operations"></a>Identitásjegyzék műveletei

Az IoT-központ identitásjegyzékhez mutatja meg a következő műveleteket:

* Eszköz vagy modul identitás
* Frissítse az eszköz vagy modul identitás
* Eszköz vagy modul identitás-azonosító szerint beolvasása
* Törli az eszköz vagy modul identitás
* Legfeljebb 1000 identitások felsorolása
> Modul identitás- és modul két nyilvános előzetes verziójában van. A szolgáltatás alatt sem fog támogatni modul identitás általános esetén érhető el.
* Az Azure blob storage eszköz identitások exportálása
* Az Azure blob storage eszköz identitások importálása

Ezeket a műveleteket egyidejű hozzáférések optimista, használhatja a [RFC7232][lnk-rfc7232].

> [!IMPORTANT]
> Csak az IoT-központ identitásjegyzékhez összes identitását beolvasni, használatához a [exportálása] [ lnk-export] funkciót.

Az IoT-központ identitásjegyzékhez:

* Nem minden alkalmazás metaadatot tartalmaz.
* A szótár, például elérhető a **deviceId** vagy **moduleId** kulcsként.
* Nem támogatja a kifejező lekérdezések.

Az IoT-megoldás jellemzően rendelkezik, amely tartalmazza az alkalmazás-specifikus metaadatok külön Megoldásfüggő tárolhatják. Például a megoldás-specifikus tárolási intelligens épület-megoldásban rögzíti a helyiségben hőmérséklet-érzékelő van telepítve.

> [!IMPORTANT]
> Eszközkezelés és a üzembe helyezési műveletek csak használja az identitásjegyzékhez. Magas teljesítmény műveletek futási időben függhetnek műveletek végrehajtása a az identitásjegyzékhez. Például egy eszköz kapcsolat állapotának ellenőrzése előtt parancsot nincs támogatott mintát. Ellenőrizze, hogy a [díjszabás szabályozás] [ lnk-quotas] az identitásjegyzékhez, és a [eszköz szívverés] [ lnk-guidance-heartbeat] mintát.

## <a name="disable-devices"></a>Eszközök letiltása

Eszközök frissítésével letilthatja a **állapot** identitás az identity beállításjegyzék tulajdonság. Általában akkor használják ezt a tulajdonságot két esetben:

* A létesítési vezénylési során. További információkért lásd: [eszköz kiépítése][lnk-guidance-provisioning].
* Ha bármilyen okból úgy gondolja, hogy az eszköz biztonsága sérül, vagy jogosulatlan vált.

Ez a funkció nincs mavenen modulok.

## <a name="import-and-export-device-identities"></a>Importálás és exportálás eszköz identitások

Az aszinkron műveletek használatát a [IoT-központ erőforrás-szolgáltató végpont] [ lnk-endpoints] eszköz identitások tömeges exportálása az IoT-központ identitásjegyzékhez. Export eszköz identitási adatokat olvasni az identitásjegyzékhez menteni egy ügyfél által megadott blob tároló használó hosszan futó feladatokat.

Az aszinkron műveletek használatát a [IoT-központ erőforrás-szolgáltató végpont] [ lnk-endpoints] eszköz identitások tömegesen importálhatja az IoT-központ identitásjegyzékhez. Importálja olyan ügyfél által megadott blob tároló adatokat írjon az eszköz identitásadatok be az identitásjegyzékhez használó hosszan futó feladatokat.

Az importálás és exportálás API-k kapcsolatos további információkért lásd: [IoT-központ erőforrás-szolgáltató REST API-k][lnk-resource-provider-apis]. További információk a importálás fut, és exportálni a feladatokat, [IoT Hub eszköz identitások kezelését tömeges][lnk-bulk-identity].

## <a name="device-provisioning"></a>Eszköz kiépítése

Az eszköz adatok, amelyek egy adott IoT-megoldás tárolja, hogy a megoldás a meghatározott követelmények függ. De legalább egy megoldást kell tárolnia, eszköz identitások és a hitelesítési kulcsokat. Azure IoT Hub tartalmaz egy, az egyes eszközök, például az azonosítók, a hitelesítési kulcsokat, és a állapotkódok értékeket tárolhat identitásjegyzékhez. A megoldás például a table storage, a blob-tároló vagy a Cosmos DB más Azure-szolgáltatásokkal segítségével további adatok tárolására.

*Eszköz kiépítése* folyamata a kezdeti adatok hozzáadása a tároló a megoldásban. Ahhoz, hogy egy új eszközt szeretne az elosztóhoz csatakoztatni, hozzá kell adnia egy Eszközazonosító és a kulcsok az IoT-központ identitásjegyzékhez. A telepítési folyamat részeként szükség lehet inicializálni az eszközre vonatkozó adatokat más megoldás tárolja. Az Azure IoT Hub eszköz kiépítése szolgáltatás segítségével is nulla-érintés, közvetlenül az időponthoz kötött emberi beavatkozás nélkül egy vagy több IoT-központok kiépítés engedélyezése. További tudnivalókért tekintse meg a [szolgáltatás dokumentációja kiépítés][lnk-dps].

## <a name="device-heartbeat"></a>Eszköz szívverés

Az IoT-központ identitásjegyzékhez tartalmaz egy nevű mező **connectionState**. Csak a **connectionState** mező fejlesztési és hibakeresés során. Az IoT-megoldások nem kell lekérdezni a mező futási időben. Például nem lekérdezni a **connectionState** mezőben ellenőrizze, hogy egy eszköz csatlakoztatva, egy felhő-eszköz vagy az SMS küldése előtt.

Ha az IoT-megoldásból tudnia kell, ha egy eszköz csatlakozik, meg kell valósítani a *szívverés mintát*.

A szívverés a mintában az eszköz eszközről a felhőbe üzeneteket küld legalább egyszer minden rögzített időn (például óránként legalább egyszer). Ezért még akkor is, ha egy eszköz nem rendelkezik adatokat küldeni, továbbra is küldené egy üres eszközről a felhőbe (általában egy tulajdonsággal rendelkező üzenet azt állapítja meg, a szívverés). A szolgáltatás oldalon a megoldás az egyes eszközök kapott utolsó szívverésének a térképre tart fenn. A megoldás nem az eszközről a várt időn belül szívverés hibaüzenetet kap, ha azt feltételezi, hogy nincs-e az eszköz egy hibás.

Egy összetettebb megvalósítási tartalmazhatnak információival [figyelési műveletek] [ lnk-devguide-opmon] eszközüket, amelyek próbál csatlakozni, vagy a kommunikációhoz, de sikertelen. Amikor alkalmazza a szívverés mintát, ellenőrizze, hogy [IoT Hub kvóták és szabályozások][lnk-quotas].

> [!NOTE]
> Ha egy IoT-megoldás használ a kapcsolat állapota kizárólag szükség van-e a felhő-eszközre küldött üzenetek küldéséhez, és az üzenet nem kiküldése az eszközök nagy mennyiségű, fontolja meg az egyszerűbb *rövid lejárati idejének* mintát. Ebben a mintában a szívverés minta használatával ugyanakkor nem sokkal hatékonyabb eszköz kapcsolati állapotát beállításjegyzék karbantartása ugyanazt az eredményt éri el. Üzenet nyugták kér le, ha az IoT-központ értesítheti, mely eszközök fogadhat üzeneteket, és amelyek nincsenek.

## <a name="device-and-module-lifecycle-notifications"></a>Eszköz- és modul életciklus-értesítést

Az IoT-központ az IoT-megoldásból értesítheti, ha identitás létrehozása vagy törlése úgy, hogy az életciklus-értesítést küld. Ehhez az IoT-megoldásból egy útvonal létrehozása és kell beállítani az adatforrás egyenlő *DeviceLifecycleEvents* vagy *ModuleLifecycleEvents*. Alapértelmezés szerint nincs életciklus-értesítést kapnak, ez azt jelenti, hogy, hogy nincs ilyen útvonal létezik-e előre. Az üzenet tulajdonságait és a szövegtörzs tartalmaz.

Tulajdonságai: Üzenet Rendszertulajdonságok fűzve előtagként a `'$'` szimbólum.

Értesítési üzenet eszközhöz:

| Name (Név) | Érték |
| --- | --- |
|$content-típus | application/json |
|$iothub-enqueuedtime |  Ha az értesítés küldése idő |
|$iothub-üzenet-forrás | deviceLifecycleEvents |
|$content-kódolás | utf-8 |
|opType | **createDeviceIdentity** vagy **deleteDeviceIdentity** |
|hubName | Az IoT-központ nevét |
|deviceId | Az eszköz azonosítója |
|operationTimestamp | A művelet ISO8601 időbélyegzője |
|iothub-message-schema | deviceLifecycleNotification |

Törzs: Ez a szakasz JSON formátumú, és a kettős az létrehozott eszközt identitás jelöli. Például:

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
$iothub-enqueuedtime |  Ha az értesítés küldése idő |
$iothub-üzenet-forrás | moduleLifecycleEvents |
$content-kódolás | utf-8 |
opType | **createModuleIdentity** vagy **deleteModuleIdentity** |
hubName | Az IoT-központ nevét |
moduleId | A modul azonosítója |
operationTimestamp | A művelet ISO8601 időbélyegzője |
iothub-message-schema | moduleLifecycleNotification |

Törzs: Ebben a szakaszban JSON formátumban van, és a két létrehozott modul identitási jelöli. Például:

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

Eszköz identitások helyettesítik JSON-dokumentumokat az alábbi tulajdonságokkal:

| Tulajdonság | Beállítások | Leírás |
| --- | --- | --- |
| deviceId |szükség esetén a frissítések csak olvasható |A kis-és nagybetűket karakterlánc (legfeljebb 128 karakter hosszú) ASCII 7 bites alfanumerikus karaktereket, valamint bizonyos speciális karaktereket: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| generationId |szükség esetén csak olvasható |Az IoT hub által létrehozott, a kis-és nagybetűket karakterlánc legfeljebb 128 karakter hosszúságú lehet. Ez az érték azonos eszközök megkülönböztetésére használható **deviceId**, törölt és újból létrehozza. |
| ETag |szükség esetén csak olvasható |Egy karakterlánc, amely az eszköz identitásának gyenge ETag értékre megfelelően [RFC7232][lnk-rfc7232]. |
| hitelesítés |választható |A hitelesítési adatokat és biztonsági anyagokat tartalmazó összetett objektum. |
| auth.symkey |választható |Egy elsődleges és másodlagos kulcsot, amely egy összetett objektum base64 formátumban tárolja. |
| status |szükséges |Az access mutató. Lehet **engedélyezve** vagy **letiltott**. Ha **engedélyezve**, az eszköz csatlakozhat. Ha **letiltott**, az eszköz minden eszköz felé néző végpont nem érhető el. |
| statusReason |választható |128 karakter hosszú karakterlánc, amely tárolja az eszköz identitása állapotát az az oka. Minden UTF-8 karakterek használhatók. |
| statusUpdateTime |csak olvasható |A historikus mutató, a dátum és az utolsó frissítésének idejét. |
| connectionState |csak olvasható |A kapcsolat állapotát jelző mező: vagy **csatlakoztatva** vagy **Disconnected**. Ez a mező képviseli az IoT Hub nézetben, az eszköz kapcsolati állapotát. **Fontos**: Ez a mező csak fejlesztési/hibakeresési célokra lehetne felhasználni. A kapcsolat állapota csak az eszközök MQTT vagy AMQP frissül. Is (MQTT ping-üzenetek, illetve AMQP ping-üzenetek) protokoll szintű ping-üzenetek alapul, és csak 5 perces késéssel veheti fel. Ezen okok miatt lehet téves, például a csatlakoztatott eszközöket jelentett, de, amely nem kapcsolódik. |
| connectionStateUpdatedTime |csak olvasható |A historikus mutató, a dátum és az utolsó idő megjelenítése a kapcsolat állapota frissítve lett. |
| lastActivityTime |csak olvasható |Historikus mutató, megjeleníti a dátum és az utolsó idő az eszköz csatlakoztatva, fogadott vagy elküldött egy üzenetet. |

> [!NOTE]
> Kapcsolat állapota csak jelenthet az IoT Hub nézetben, a kapcsolat állapotát. Ebben az állapotban frissítések később, attól függően, hogy hálózati körülmények és konfigurációkat.

## <a name="module-identity-properties"></a>A modul identitás tulajdonságai

Modul identitások helyettesítik JSON-dokumentumokat az alábbi tulajdonságokkal:

| Tulajdonság | Beállítások | Leírás |
| --- | --- | --- |
| deviceId |szükség esetén a frissítések csak olvasható |A kis-és nagybetűket karakterlánc (legfeljebb 128 karakter hosszú) ASCII 7 bites alfanumerikus karaktereket, valamint bizonyos speciális karaktereket: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| moduleId |szükség esetén a frissítések csak olvasható |A kis-és nagybetűket karakterlánc (legfeljebb 128 karakter hosszú) ASCII 7 bites alfanumerikus karaktereket, valamint bizonyos speciális karaktereket: `- . + % _ # * ? ! ( ) , = @ $ '`. |
| generationId |szükség esetén csak olvasható |Az IoT hub által létrehozott, a kis-és nagybetűket karakterlánc legfeljebb 128 karakter hosszúságú lehet. Ez az érték azonos eszközök megkülönböztetésére használható **deviceId**, törölt és újból létrehozza. |
| ETag |szükség esetén csak olvasható |Egy karakterlánc, amely az eszköz identitásának gyenge ETag értékre megfelelően [RFC7232][lnk-rfc7232]. |
| hitelesítés |választható |A hitelesítési adatokat és biztonsági anyagokat tartalmazó összetett objektum. |
| auth.symkey |választható |Egy elsődleges és másodlagos kulcsot, amely egy összetett objektum base64 formátumban tárolja. |
| status |szükséges |Az access mutató. Lehet **engedélyezve** vagy **letiltott**. Ha **engedélyezve**, az eszköz csatlakozhat. Ha **letiltott**, az eszköz minden eszköz felé néző végpont nem érhető el. |
| statusReason |választható |128 karakter hosszú karakterlánc, amely tárolja az eszköz identitása állapotát az az oka. Minden UTF-8 karakterek használhatók. |
| statusUpdateTime |csak olvasható |A historikus mutató, a dátum és az utolsó frissítésének idejét. |
| connectionState |csak olvasható |A kapcsolat állapotát jelző mező: vagy **csatlakoztatva** vagy **Disconnected**. Ez a mező képviseli az IoT Hub nézetben, az eszköz kapcsolati állapotát. **Fontos**: Ez a mező csak fejlesztési/hibakeresési célokra lehetne felhasználni. A kapcsolat állapota csak az eszközök MQTT vagy AMQP frissül. Is (MQTT ping-üzenetek, illetve AMQP ping-üzenetek) protokoll szintű ping-üzenetek alapul, és csak 5 perces késéssel veheti fel. Ezen okok miatt lehet téves, például a csatlakoztatott eszközöket jelentett, de, amely nem kapcsolódik. |
| connectionStateUpdatedTime |csak olvasható |A historikus mutató, a dátum és az utolsó idő megjelenítése a kapcsolat állapota frissítve lett. |
| lastActivityTime |csak olvasható |Historikus mutató, megjeleníti a dátum és az utolsó idő az eszköz csatlakoztatva, fogadott vagy elküldött egy üzenetet. |

## <a name="additional-reference-material"></a>További referenciaanyag

Az IoT Hub fejlesztői útmutató más hivatkozás témaköröket tartalmazza:

* [IoT-központok végpontjai] [ lnk-endpoints] ismerteti a különböző végpontok, amelyek minden egyes IoT-központ elérhetővé teszi a futásidejű és felügyeleti műveletek.
* [Sávszélesség-szabályozási és kvóták] [ lnk-quotas] ismerteti a kvóták és a szabályozás viselkedéseket, amelyek az IoT-központ szolgáltatás vonatkozik.
* [Az Azure IoT eszköz és a szolgáltatás SDK-k] [ lnk-sdks] felsorolja a különböző nyelvi használhatja az eszköz és a szolgáltatás alkalmazások gondoskodnak az IoT hubbal fejlesztésekor SDK-k.
* [Az IoT-központ lekérdezési nyelv] [ lnk-query] a lekérdezési nyelv segítségével adatok lekérését az IoT-központ az eszköz twins és feladatokat ismerteti.
* [Az IoT Hub MQTT támogatási] [ lnk-devguide-mqtt] IoT-központ támogatásával kapcsolatos további információkat biztosít a MQTT protokoll.

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik megtudta, hogyan használható az IoT-központ identitásjegyzékhez, esetleg a következő IoT Hub fejlesztői útmutató témakörei iránt érdeklődik:

* [Elérés az IoT hubhoz][lnk-devguide-security]
* [Eszköz twins segítségével szinkronizálja az állapotot és konfigurációk][lnk-devguide-device-twins]
* [Az eszközön közvetlen metódus][lnk-devguide-directmethods]
* [Több eszközön feladatok ütemezése][lnk-devguide-jobs]

Próbálja ki azokat a jelen cikkben ismertetett fogalmakat, olvassa el a következő IoT Hub-oktatóanyag:

* [Ismerkedés az Azure IoT Hub][lnk-getstarted-tutorial]

Az IoT Hub eszköz kiépítése szolgáltatás segítségével nulla-érintés engedélyezése megismeréséhez just-in-time kiépítés, lásd: 

* [Az Azure IoT Hub eszköz-üzembehelyezési szolgáltatás][lnk-dps]


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

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps

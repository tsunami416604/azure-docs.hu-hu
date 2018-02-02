---
title: "Azure IoT Hub eszköz twins megértése |} Microsoft Docs"
description: "Fejlesztői útmutató - használata eszköz twins állapotot és a konfigurációs adatokat az IoT-központ és az eszközök közötti szinkronizálása"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 8a3da072-a5bf-46e5-8de4-24cdbb2a03fa
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5bf2d24d0d5eadfea5ec8fd239a115c05a54fe99
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Ismertetés és az IoT Hub eszköz twins használata

*Eszköz twins* JSON-dokumentumokat tároló eszköz állapotának adatai, beleértve a metaadatok, a konfiguráció és a feltételek vannak. Azure IoT Hub fenntartja az egyes eszközök, amikor csatlakozik az IoT-központot egy eszköz iker. Ez a cikk ismerteti:


* Az eszköz iker szerkezete: *címkék*, *kívánt* és *tulajdonságok jelentett*.
* Eszköz twins eszközeinek alkalmazásait és hátsó akkor ér véget végezhető műveletek.

Az eszköz twins használja:

* A felhőben tárolt eszközre vonatkozó metaadatok. Például a központi telepítési helye a Eladóautomata.
* A jelentés aktuális állapotadatokat például a rendelkezésre álló lehetőségek és az eszköz alkalmazás állapotának. Például egy eszköz csatlakozik az IoT hub cellás over vagy Wi-Fi.
* Hosszan futó munkafolyamatok közötti eszközalkalmazás és háttér-alkalmazás állapotának szinkronizálása. Például a megoldás biztonsági end határozza meg az új belső vezérlőprogram verziójának telepítéséhez, és az eszköz alkalmazás jelentés a frissítési folyamat különböző szakaszaiban.
* Az eszköz metaadatait, konfigurációs vagy az állapot lekérdezése.

Tekintse meg [eszközről a felhőbe kommunikációs útmutatást] [ lnk-d2c-guidance] jelentett tulajdonságok, az eszköz a felhőbe küldött üzeneteket vagy a fájl feltöltése útmutatót.
Tekintse meg [felhő eszközre kommunikációs útmutatást] [ lnk-c2d-guidance] kívánt tulajdonságokkal, a közvetlen módszerek és a felhő-eszközre küldött üzenetek útmutatót.

## <a name="device-twins"></a>Eszköz twins
Eszköz twins eszközzel kapcsolatos adatok tárolására, amelyek:

* Eszköz- és biztonsági végpontok használatával szinkronizálhatja a eszköz feltételek és a konfiguráció.
* A megoldás háttérrendszeréhez segítségével lekérdezés és a cél hosszú futású műveleteket.

Egy eszköz iker életciklusát kapcsolódik a megfelelő [eszközidentitás][lnk-identity]. Eszköz twins implicit létrehozása és törlése, amikor egy eszközidentitás jön létre, vagy az IoT-központ törölt.

Egy eszköz iker tartalmazó JSON-dokumentumhoz:

* **Címkék**. A JSON-dokumentum, amely a megoldás háttérrendszeréhez olvasni és írni egy része. Címkék nem láthatók az eszközön futó alkalmazások.
* **Szükségeskonfiguráció-tulajdonságok**. Eszközök konfigurálása és a feltételek szinkronizálásához használt jelentett tulajdonságok együtt. A megoldás háttérrendszeréhez is beállíthatja a kívánt tulajdonságokat, és az eszköz alkalmazás is olvashatja őket. Az eszköz alkalmazás akkor is jelentkezhet változások értesítések kívánt tulajdonságai.
* **Tulajdonságok jelentett**. Eszközök konfigurálása és feltételek szinkronizálásához használni kívánt tulajdonságokkal együtt. Az eszköz alkalmazás állíthatja be a jelentésben szereplő tulajdonságok, és a megoldás háttérrendszeréhez, olvassa el, és lekérdezheti.
* **Eszköztulajdonságok identitás**. Az eszköz iker JSON-dokumentum gyökerébe tartalmazza a megfelelő eszközidentitás tárolja a csak olvasható tulajdonságok a [identitásjegyzékhez][lnk-identity].

![][img-twin]

A következő példa bemutatja egy eszköz iker JSON-dokumentum:

```json
{
    "deviceId": "devA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

A legfelső szintű objektum az eszköz azonosító tulajdonságainak, és a tárolóobjektumok `tags` és mindkét `reported` és `desired` tulajdonságok. A `properties` tároló néhány írásvédett elemet tartalmaz (`$metadata`, `$etag`, és `$version`) ismertetett a [eszköz iker metaadatok] [ lnk-twin-metadata] és [egyidejű hozzáférések optimista] [ lnk-concurrency] szakaszok.

### <a name="reported-property-example"></a>Jelentett tulajdonság – példa
Az előző példában az eszköz iker tartalmaz egy `batteryLevel` az eszköz alkalmazás által jelentett tulajdonság. Ez a tulajdonság lekérdezése, és az utolsó jelentett akkumulátor szint alapján eszközökön működik lehetővé teszi. További példák lehetnek az alkalmazás jelentéskészítési eszköz eszközképességek vagy kapcsolati lehetőségek.

> [!NOTE]
> Jelentett tulajdonságok forgatókönyvek, amelyben az utolsó ismert tulajdonság értéke a megoldás háttérrendszeréhez iránt érdeklődik egyszerűsítése érdekében. Használjon [eszköz a felhőbe küldött üzeneteket] [ lnk-d2c] hogy kell-e a megoldás háttérrendszeréhez telemetriát eseménysorozat időbélyegzővel, például a time series formájában feldolgozni.

### <a name="desired-property-example"></a>Kívánt tulajdonság – példa
Az előző példában a `telemetryConfig` eszköz iker szükséges, és a jelentett tulajdonságok szinkronizálni a telemetria-konfigurációt, az eszköz a megoldás háttérrendszeréhez, és az eszköz alkalmazás által használt. Példa:

1. A megoldás háttérrendszeréhez állítja be a kívánt tulajdonságot a szükségeskonfiguráció-értékkel. Ez a dokumentum a kívánt tulajdonság értéke:

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. Az eszköz alkalmazásának a változás azonnal, ha létrejött a kapcsolat, vagy első újbóli értesítést kap. Az eszköz alkalmazás majd jelentést készít a frissített konfigurációt (vagy egy hiba feltétel használatával a `status` tulajdonság). A jelentésben szereplő tulajdonságok része a következő:

    ```json
    ...
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ...
    ```

3. A megoldás háttérrendszeréhez nyomon követheti a konfigurációs művelet eredménye számos eszközön, az [lekérdezése] [ lnk-query] eszköz twins.

> [!NOTE]
> Az előző kódtöredékek példák, olvashatóságát, egyik módja egy eszköz konfigurálásának és annak állapotát kódolása optimalizálva. Az IoT-központ nem ír elő egy adott séma, az eszköz iker szükséges, és az eszköz twins tulajdonságok jelentett.
> 
> 

Twins segítségével szinkronizálása hosszú ideig futó műveletek, például a belső vezérlőprogram-frissítésekre. Szinkronizálja, és nyomon követéséhez egy hosszú ideig futó művelet az eszközön tulajdonságok használatával további információkért lásd: [használata szükséges eszközök tulajdonságok][lnk-twin-properties].

## <a name="back-end-operations"></a>Háttér-műveletek
A megoldás háttérrendszeréhez végez műveleteket ezeken az eszköz a két a következő atomi műveletek közzétéve az HTTPS használatával:

* **Eszköz iker azonosítójú beolvasása**. Ez a művelet eszköz iker dokumentumát, beleértve a címkék és a kívánt és jelentett rendszer tulajdonságai adja vissza.
* **Részlegesen frissítse az eszköz iker**. Ez a művelet lehetővé teszi, hogy a megoldás háttérrendszeréhez részben frissíteni a címkék vagy egy eszköz iker kívánt tulajdonságokat. A részleges frissítés, amely ad hozzá vagy bármilyen tulajdonságot JSON-dokumentumként van kifejezve. Tulajdonságok beállítása `null` törlődnek. Az alábbi példa hoz létre egy új kívánt tulajdonság értéke `{"newProperty": "newValue"}`, felülírja a meglévő értéket az `existingProperty` rendelkező `"otherNewValue"`, és eltávolítja a `otherOldProperty`. Nincs más módosításai meglévő kívánt tulajdonságok vagy a címkék:

    ```json
    {
        "properties": {
            "desired": {
                "newProperty": {
                    "nestedProperty": "newValue"
                },
                "existingProperty": "otherNewValue",
                "otherOldProperty": null
            }
        }
    }
    ```

* **Cserélje ki a kívánt tulajdonságokkal**. Ez a művelet lehetővé teszi, hogy a megoldás háttérrendszeréhez teljesen felülírja az összes meglévő kívánt tulajdonságokat, és helyettesítse be az új JSON-dokumentum `properties/desired`.
* **Cserélje le a címkék**. Ez a művelet lehetővé teszi, hogy a megoldás háttérrendszeréhez teljesen felülírja az összes meglévő címkék és helyettesítse be az új JSON-dokumentum `tags`.
* **A két értesítéseket**. Ez a művelet lehetővé teszi, hogy a megoldás háttérrendszeréhez értesíti, ha a két módosul. Ehhez az IoT-megoldásból egy útvonal létrehozása és kell beállítani az adatforrás egyenlő *twinChangeEvents*. Alapértelmezés szerint nincs iker értesítések küldése általában olyan, ez azt jelenti, hogy, hogy nincs ilyen útvonal létezik-e előre. Ha a módosítási aránya túl magas, vagy más okokból belső hibák, az IoT Hub el tudja küldeni csak egy értesítést, amely tartalmazza az összes módosítást. Ezért ha az alkalmazásnak megbízható vizsgálati és naplózási az összes köztes állapotok, használjon eszköz a felhőbe küldött üzeneteket. A két üzenet tulajdonságait és a szervezet tartalmaz.

    - Tulajdonságok

    | Name (Név) | Érték |
    | --- | --- |
    $content-típus | application/json |
    $iothub-enqueuedtime |  Ha az értesítés küldése idő |
    $iothub-üzenet-forrás | twinChangeEvents |
    $content-kódolás | utf-8 |
    deviceId | Az eszköz azonosítója |
    hubName | Az IoT-központ nevét |
    operationTimestamp | [ISO8601] művelet időbélyegzője |
    iothub-message-schema | deviceLifecycleNotification |
    opType | "replaceTwin" vagy "updateTwin" |

    Üzenet Rendszertulajdonságok fűzve előtagként a `'$'` szimbólum.

    - Törzs
        
    Ez a szakasz a kettős módosításokat tartalmazza JSON formátumban. Ugyanazt a formátumot használja, a javítás, azzal a különbséggel, hogy minden iker szakasz tartalmazhat: címkék, properties.reported, properties.desired és, hogy az tartalmazza-e a "$metadata" elemek. Például:

    ```json
    {
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

Az előző műveletei támogatják [egyidejű hozzáférések optimista] [ lnk-concurrency] , és van szüksége a **ServiceConnect** engedéllyel, akkor az a [biztonsági] [ lnk-security] cikk.

Ezek a műveletek mellett a megoldás háttérrendszeréhez is:

* Az eszköz twins használata az SQL-szerű lekérdezése [IoT-központ lekérdezési nyelv][lnk-query].
* Műveletek végzése eszköz twins használatával nagy mennyiségű [feladatok][lnk-jobs].

## <a name="device-operations"></a>Eszköz műveletek
Az eszköz alkalmazás végez műveleteket ezeken az eszköz a két használatával a következő atomi műveletek:

* **A két eszköz beolvasása**. Ez a művelet a jelenleg csatlakoztatott eszköz (beleértve a címkék és a kívánt és jelentett Rendszertulajdonságok) eszköz iker dokumentumát adja vissza.
* **Részlegesen a jelentett tulajdonságainak frissítése**. Ez a művelet lehetővé teszi, hogy a jelenleg csatlakoztatott eszköz jelentett tulajdonságait részleges frissítése. Ez a művelet formátuma JSON frissítés, hogy a megoldás biztonsági egy részleges frissítés kívánt tulajdonságok felhasználását.
* **Figyelje meg a kívánt tulajdonságokkal**. A jelenleg csatlakoztatott eszközre választhat, ha értesítést szeretne kapni a kívánt tulajdonságokkal frissítések akkor fordulhat elő. Az eszköz megkapja a frissítés (teljes vagy részleges csere) hajtja végre a megoldás háttérrendszeréhez ugyanolyan formában.

Az előző működéséhez szükségesek a **DeviceConnect** engedéllyel, akkor az a [biztonsági] [ lnk-security] cikk.

A [Azure IoT-eszközök SDK-k] [ lnk-sdks] megkönnyítik a fenti műveleteket az sok nyelvekhez és platformokhoz használatára. Az IoT-központ primitívek kívánt tulajdonságokkal szinkronizálás részleteit további információkért lásd: [eszköz újracsatlakozás folyamat][lnk-reconnection].

## <a name="tags-and-properties-format"></a>Címkék és a Tulajdonságok
Címkék, a kívánt tulajdonságokat és a jelentésben szereplő tulajdonságok nem a JSON-objektumok a következő korlátozásokkal:

* A JSON-objektumok összes kulcsai kis-és nagybetűket 64 bájt UTF-8 UNICODE karakterláncokat. Engedélyezett karakterek kizárása UNICODE vezérlőkaraktereket (szegmensek C0 és C1), és `'.'`, `' '`, és `'$'`.
* JSON-objektumok összes értéke lehet a következő JSON-típusok: boolean, számot, karakterlánc, objektum. Tömbök használata nem megengedett. Egész számok maximális értéke 4503599627370495, és az egész számok legkisebb értéke-4503599627370496.
* Címkék, kívánt és jelentett tulajdonságok minden JSON-objektumok maximális mélysége 5 rendelkezhet. Például a következő objektum érvénytelen:

    ```json
    {
        ...
        "tags": {
            "one": {
                "two": {
                    "three": {
                        "four": {
                            "five": {
                                "property": "value"
                            }
                        }
                    }
                }
            }
        },
        ...
    }
    ```

* Az összes karakterlánc-értékek legfeljebb 4 KB-os hossza.

## <a name="device-twin-size"></a>Eszköz iker mérete
Az IoT-központ kikényszeríti egy 8 KB-os méret korlátozás a megfelelő teljes értékek az egyes `tags`, `properties/desired`, és `properties/reported`, kivéve a csak olvasható elemeket.
A méret UNICODE vezérlőkaraktereket (szegmensek C0 és C1) kivételével minden karakter alapján számított és szóközöket a karakterlánckonstansokat kívül esnek.
Az IoT-központ hibával elutasítja a összes művelet lenne növelje a mérete meghaladja a közzétett dokumentumokat.

## <a name="device-twin-metadata"></a>Eszköz iker metaadatok
Az IoT-központ tárolja a minden JSON-objektumból, az eszköz a két utolsó frissítésének időbélyeg szükséges tulajdonságok jelentett. Az időbélyeg helyi időre UTC és kódolású a [ISO8601] formátum `YYYY-MM-DDTHH:MM:SS.mmmZ`.
Példa:

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                }
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

Ezek az információk megőrzése érdekében távolítsa el a objektum kulcsok frissítések tartják minden szinten (nem csak a JSON struktúrában leaves).

## <a name="optimistic-concurrency"></a>Egyidejű hozzáférések optimista
Címkék, szükséges, és a Tulajdonságok jelentett összes támogatási hozzáférések optimista.
Címkék rendelkezik egy ETag megfelelően [RFC7232], amely jelzi, hogy a címke JSON-megjelenítés. A megoldás háttérrendszeréhez feltételes frissítési műveletek ETag-EK használatával biztosítják a konzisztenciát.

Eszköz iker szükséges, és a Tulajdonságok jelentett ETag-EK nem rendelkezik, de rendelkezik egy `$version` érték, amely garantáltan növekményes. Ehhez hasonlóan az egy ETag-gel, a verziója használható a frissítési entitás frissítések konzisztencia érvényesítéséhez. Például egy eszköz alkalmazás jelentett tulajdonság vagy a megoldás háttérrendszeréhez kívánt tulajdonság.

Verziók is hasznosak, ha (például az eszköz alkalmazás tartják be a kívánt tulajdonságokkal) observing ügynök fajok között egy lekérése művelet eredményét, és a frissítési értesítést kell feloldani. A szakasz [eszköz újracsatlakozás folyamat] [ lnk-reconnection] nyújt részletesebb információt.

## <a name="device-reconnection-flow"></a>Eszköz újracsatlakozás folyamata
Az IoT-központ nem őrzi meg a kívánt tulajdonságokkal frissítési értesítések leválasztott eszközökhöz. Ez azt jelenti, hogy egy eszköz, amely kapcsolódik a teljes kívánt tulajdonságokat tartalmazó dokumentum, frissítési értesítések való előfizetés mellett kell olvasni. Frissítési értesítések és a teljes visszaállításhoz közötti fajok lehetőséget biztosítani a következő folyamatot kell biztosítani:

1. Eszköz alkalmazásának csatlakozik az IoT-központ.
2. Eszköz alkalmazás frissítési értesítések előfizet a kívánt tulajdonságokat.
3. Eszköz alkalmazás lekéri a teljes dokumentumot a kívánt tulajdonságokat.

Az eszköz alkalmazás figyelmen kívül hagyhatja az összes értesítésben `$version` kisebb vagy egyenlő, mint a teljes lekérdezése dokumentum verziója. Ez a megközelítés lehetőség, mert az IoT-központ biztosítja, hogy verziók mindig növelhető.

> [!NOTE]
> Ezt a tagot már megvalósította a [Azure IoT-eszközök SDK-k][lnk-sdks]. Ez a megnevezés akkor hasznos, csak akkor, ha az eszköz alkalmazás az Azure IoT-eszközök SDK-k nem használható, és programot kell közvetlenül a MQTT felületet.
> 
> 

## <a name="additional-reference-material"></a>További referenciaanyag
Az IoT Hub fejlesztői útmutató más hivatkozás témaköröket tartalmazza:

* A [IoT-központok végpontjai] [ lnk-endpoints] a cikk ismerteti a különböző végpontok, amelyek minden egyes IoT-központ elérhetővé teszi a futásidejű és felügyeleti műveletek.
* A [sávszélesség-szabályozási és kvóták] [ lnk-quotas] a cikk ismerteti a kvótákat, az IoT-központ szolgáltatás és a sávszélesség-szabályozási viselkedését történik, ha a szolgáltatás használatához.
* A [Azure IoT-eszközök és szolgáltatások SDK] [ lnk-sdks] cikk felsorolja a különböző nyelvi használhatja az eszköz és a szolgáltatás alkalmazások gondoskodnak az IoT hubbal fejlesztésekor SDK-k.
* A [IoT-központ lekérdezési nyelv eszköz twins, feladatok és üzenet útválasztási] [ lnk-query] a cikk ismerteti az IoT-központ lekérdezési nyelv segítségével IoT Hub eszköz twins és feladatok kapcsolatos adatok lekérését.
* A [IoT Hub MQTT támogatási] [ lnk-devguide-mqtt] cikkben olvashat az IoT-Központ támogatja a MQTT protokollt.

## <a name="next-steps"></a>További lépések
Most már rendelkezik megismerte eszköz twins, előfordulhat, hogy érdeklődik a következő IoT Hub fejlesztői útmutató témakörei:

* [Az eszközön közvetlen metódus][lnk-methods]
* [Több eszközön feladatok ütemezése][lnk-jobs]

Próbálja ki azokat a jelen cikkben ismertetett fogalmakat, olvassa el az alábbi IoT Hub-oktatóanyagok:

* [A két eszköz használata][lnk-twin-tutorial]
* [A két eszköztulajdonságok használata][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#device-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png

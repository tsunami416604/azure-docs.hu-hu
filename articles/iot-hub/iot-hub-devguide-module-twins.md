---
title: Azure IoT Hub modul twins megértése |} Microsoft Docs
description: Fejlesztői útmutató - használata modul twins állapotot és a konfigurációs adatokat az IoT-központ és az eszközök közötti szinkronizálása
services: iot-hub
documentationcenter: .net
author: chrissie926
manager: timlt
editor: ''
ms.assetid: 8a3da072-a5bf-46e5-8de4-24cdbb2a03fa
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: menchi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e6f52da6f7524828cef928c2ee5d5cd92a1de1e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Ismertetés és modul twins használja az IoT hubon

Ez a cikk feltételezi, hogy elolvasta a [megértéséhez, valamint eszköz twins használja az IoT-központ] [ lnk-devguide-device-twins] első. Az IoT-központot minden eszközt identitás hozhat létre legfeljebb 20 modul identitásokat. Minden modul identitás implicit módon egy modul iker állít elő. Eszköz twins nagyon hasonló, modul twins olyan JSON-dokumentumok tárolni a metaadatokat, a konfigurációkat és a feltételek modul állapotadatait. Az Azure IoT Hub egy modul iker csatlakozik az IoT-központ modulokhoz tartozó tart fenn. 

Az eszköz oldalán az IoT Hub eszköz SDK-k lehetővé teszik modulok, amely minden megnyit egy IoT-központ független kapcsolat létrehozása. Ez lehetővé teszi, hogy különálló névterek használ a különböző összetevőket az eszközön. Például hogy egy Eladóautomata, amely három különböző érzékelők rendelkezik. Minden egyes érzékelő a vállalat különböző részlegei vezérli. Minden egyes érzékelő modul hozhat létre. Így minden részleg értéke csak-feladat vagy közvetlen módszerek az érzékelő szabályozó azokat, szükségtelenné téve az ütközések és felhasználói hibákat kell küldenie.

 Modul identitás- és modul iker eszközidentitás és eszköz iker, de egyeztetését részletességű ugyanazokat a képességeket biztosít. Ez nagyobb részletességgel lehetővé teszi, hogy használatára képes eszközök, például az operációs rendszer eszközök vagy a belső vezérlőprogram eszközök kezelése több összetevőjét, különítheti el a konfigurációs és az egyes összetevők feltételek alapján. Modul identitás- és modul twins adja meg a kérdések elkülönítése az IoT-eszközök, amelyek moduláris szoftverösszetevőket használatakor. A Microsoft célja modul iker szinten összes eszköz iker funkciót támogató modul iker általános rendelkezésre állás. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez a cikk ismerteti:

* A modul iker szerkezete: *címkék*, *kívánt* és *tulajdonságok jelentett*.
* A modulok és hátsó befejezésének modul twins végezhető műveletek.

Tekintse meg [eszközről a felhőbe kommunikációs útmutatást] [ lnk-d2c-guidance] jelentett tulajdonságok, az eszköz a felhőbe küldött üzeneteket vagy a fájl feltöltése útmutatót.
Tekintse meg [felhő eszközre kommunikációs útmutatást] [ lnk-c2d-guidance] kívánt tulajdonságokkal, a közvetlen módszerek és a felhő-eszközre küldött üzenetek útmutatót.

## <a name="module-twins"></a>A modul twins
A modul twins modul kapcsolatos információk tárolására, amelyek:

* Az eszközön, és az IoT-központ modulok használatával szinkronizálhatja a modul feltételek és a konfiguráció.
* A megoldás háttérrendszeréhez segítségével lekérdezés és a cél hosszú futású műveleteket.

A modul iker életciklusát kapcsolódik a megfelelő [modul identitás][lnk-identity]. Modulok twins implicit létrehozása és törlése, amikor egy modul identitás jön létre, vagy az IoT-központ törölt.

Egy modul iker tartalmazó JSON-dokumentumhoz:

* **Címkék**. A JSON-dokumentum, amely a megoldás háttérrendszeréhez olvasni és írni egy része. Címkék nem láthatók a modulokhoz az eszközön. Címkék vannak beállítva, a cél lekérdezése.
* **Szükségeskonfiguráció-tulajdonságok**. Modul konfigurációjának és feltételek szinkronizálásához használt jelentett tulajdonságok együtt. A megoldás háttérrendszeréhez is beállíthatja a kívánt tulajdonságokat, és a modul alkalmazást is olvashatja őket. A modul az alkalmazás is fogadhat el változások értesítések kívánt tulajdonságai.
* **Tulajdonságok jelentett**. Modulok konfigurációjának módosításai vagy feltételek szinkronizálásához használni kívánt tulajdonságokkal együtt. A modul alkalmazás állíthatja be a jelentésben szereplő tulajdonságok, és a megoldás háttérrendszeréhez, olvassa el, és lekérdezheti.
* **A modul azonosító tulajdonságainak**. A modul iker JSON-dokumentum gyökerébe tartalmazza a csak olvasható tulajdonságok identitásból a megfelelő modul tárolja a [identitásjegyzékhez][lnk-identity].

![][img-module-twin]

A következő példa bemutatja egy modul iker JSON-dokumentum:

```json
{
    "deviceId": "devA",
    "moduleId": "moduleA",
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

A legfelső szintű objektum a modul azonosító tulajdonságainak, és a tárolóobjektumok `tags` és mindkét `reported` és `desired` tulajdonságok. A `properties` tároló néhány írásvédett elemet tartalmaz (`$metadata`, `$etag`, és `$version`) ismertetett a [modul iker metaadatai] [ lnk-module-twin-metadata] és [ Egyidejű hozzáférések optimista] [ lnk-concurrency] szakaszok.

### <a name="reported-property-example"></a>Jelentett tulajdonság – példa
Az előző példában a modul iker tartalmaz egy `batteryLevel` a modul alkalmazás által jelentett tulajdonság. Ez a tulajdonság lekérdezése és működhet a modulok utolsó jelentett akkumulátor szint alapján lehetővé teszi. További példák lehetnek a modul jelentéskészítési modul képességeit vagy kapcsolati lehetőségek.

> [!NOTE]
> Jelentett tulajdonságok forgatókönyvek, amelyben az utolsó ismert tulajdonság értéke a megoldás háttérrendszeréhez iránt érdeklődik egyszerűsítése érdekében. Használjon [eszköz a felhőbe küldött üzeneteket] [ lnk-d2c] hogy kell-e a megoldás háttérrendszeréhez modul telemetriai eseménysorozat időbélyegzővel, például a time series formájában feldolgozni.

### <a name="desired-property-example"></a>Kívánt tulajdonság – példa
Az előző példában a `telemetryConfig` modul iker szükséges, és a jelentett tulajdonságok szinkronizálni a telemetria-konfigurációt a modulhoz tartozó a megoldás háttérrendszeréhez, és a modul alkalmazás által használt. Példa:

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

2. A modul alkalmazást a változás azonnal, ha létrejött a kapcsolat, vagy első újbóli értesítést kap. A modul app majd jelentést készít a frissített konfigurációt (vagy egy hiba feltétel használatával a `status` tulajdonság). A jelentésben szereplő tulajdonságok része a következő:

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

3. A megoldás háttérrendszeréhez nyomon követheti a konfigurációs művelet eredményei között számos modult az [lekérdezése] [ lnk-query] modul twins.

> [!NOTE]
> Az előző kódtöredékek példák, olvashatóságát, egyik módja egy modul konfigurációjának és állapotának kódolása optimalizálva. Az IoT-központ nem ír elő egy adott séma, a modul iker szükséges, és a modul twins tulajdonságok jelentett.
> 
> 

## <a name="back-end-operations"></a>Háttér-műveletek
A megoldás háttérrendszeréhez a használatával a következő atomi műveletek HTTPS keresztül közzétett modul iker működik:

* **Beolvasni a modul a két azonosító szerint**. Ez a művelet a modul a két dokumentumot, beleértve a címkék és a kívánt és jelentett rendszer tulajdonságai adja vissza.
* **Részlegesen frissíteni a modul iker**. Ez a művelet lehetővé teszi, hogy a megoldás háttérrendszeréhez részben frissíteni a címkék vagy egy modul iker kívánt tulajdonságokat. A részleges frissítés, amely ad hozzá vagy bármilyen tulajdonságot JSON-dokumentumként van kifejezve. Tulajdonságok beállítása `null` törlődnek. Az alábbi példa hoz létre egy új kívánt tulajdonság értéke `{"newProperty": "newValue"}`, felülírja a meglévő értéket az `existingProperty` rendelkező `"otherNewValue"`, és eltávolítja a `otherOldProperty`. Nincs más módosításai meglévő kívánt tulajdonságok vagy a címkék:

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
    moduleId | A modul azonosítója |
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

* A modul twins használata az SQL-szerű lekérdezése [IoT-központ lekérdezési nyelv][lnk-query].

## <a name="module-operations"></a>Modulműveletek
A modul alkalmazás működik a modul iker használatával a következő atomi műveletek:

* **Beolvasni a modul iker**. Ez a művelet a jelenleg csatlakoztatott modul (beleértve a címkék és a kívánt és jelentett Rendszertulajdonságok) modul két dokumentumát adja vissza.
* **Részlegesen a jelentett tulajdonságainak frissítése**. Ez a művelet lehetővé teszi, hogy a jelenleg csatlakoztatott modul jelentett tulajdonságainak részleges frissítés. Ez a művelet formátuma JSON frissítés, hogy a megoldás biztonsági egy részleges frissítés kívánt tulajdonságok felhasználását.
* **Figyelje meg a kívánt tulajdonságokkal**. A jelenleg csatlakoztatott modul választhat, ha értesítést szeretne kapni a kívánt tulajdonságokkal frissítések akkor fordulhat elő. A modul megkapja a frissítés (teljes vagy részleges csere) hajtja végre a megoldás háttérrendszeréhez ugyanolyan formában.

Az előző működéséhez szükségesek a **ModuleConnect** engedéllyel, akkor az a [biztonsági] [ lnk-security] cikk.

A [Azure IoT-eszközök SDK-k] [ lnk-sdks] megkönnyítik a fenti műveleteket az sok nyelvekhez és platformokhoz használatára.

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

## <a name="module-twin-size"></a>Modul iker mérete
Az IoT-központ kikényszeríti egy 8 KB-os méret korlátozás a megfelelő teljes értékek az egyes `tags`, `properties/desired`, és `properties/reported`, kivéve a csak olvasható elemeket.
A méret UNICODE vezérlőkaraktereket (szegmensek C0 és C1) kivételével minden karakter alapján számított és szóközöket a karakterlánckonstansokat kívül esnek.
Az IoT-központ hibával elutasítja a összes művelet lenne növelje a mérete meghaladja a közzétett dokumentumokat.

## <a name="module-twin-metadata"></a>Modul iker metaadatai
Az IoT-központ tárolja a minden JSON-objektum, a modul a két utolsó frissítésének időbélyeg szükséges tulajdonságok jelentett. Az időbélyeg helyi időre UTC és kódolású a [ISO8601] formátum `YYYY-MM-DDTHH:MM:SS.mmmZ`.
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

## <a name="optimistic-concurrency"></a>Optimista párhuzamosság
Címkék, szükséges, és a Tulajdonságok jelentett összes támogatási hozzáférések optimista.
Címkék rendelkezik egy ETag megfelelően [RFC7232], amely jelzi, hogy a címke JSON-megjelenítés. A megoldás háttérrendszeréhez feltételes frissítési műveletek ETag-EK használatával biztosítják a konzisztenciát.

Modul iker szükséges, és a Tulajdonságok jelentett ETag-EK nem rendelkezik, de rendelkezik egy `$version` érték, amely garantáltan növekményes. Ehhez hasonlóan az egy ETag-gel, a verziója használható a frissítési entitás frissítések konzisztencia érvényesítéséhez. Például egy modul alkalmazás jelentett tulajdonság vagy a megoldás háttérrendszeréhez kívánt tulajdonság.

Verziók is hasznosak, ha (például a modul az alkalmazás tartják be a kívánt tulajdonságokkal) observing ügynök fajok között egy lekérése művelet eredményét, és a frissítési értesítést kell feloldani. A szakasz [eszköz újracsatlakozás folyamata] [lnk-újracsatlakozást] nyújt részletesebb információt. 

## <a name="next-steps"></a>További lépések
Próbálja ki azokat a jelen cikkben ismertetett fogalmakat, olvassa el az alábbi IoT Hub-oktatóanyagok:

* [Ismerkedés az IoT-központ modul identitás- és modul iker .NET biztonsági mentési és a .NET-eszköz használata][lnk-module-twin-tutorial]

<!-- links and images -->

[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232

[lnk-module-twin-tutorial]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-module-twin-metadata]: iot-hub-devguide-module-twins.md#module-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[img-module-twin]: media/iot-hub-devguide-device-twins/module-twin.jpg

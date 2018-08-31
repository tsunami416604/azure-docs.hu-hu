---
title: Azure IoT Hub az ikereszközök |} A Microsoft Docs
description: Fejlesztői útmutató – ikereszközök használata állapotot és a konfigurációs adatokat az IoT Hub és az eszközök közötti szinkronizálása
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: b5e0a0caacab2413b640c58575673e555eeb76a4
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43288278"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Megismerheti és ikereszközök használata az IoT hubban

*Ikereszközök* JSON-dokumentumok, amelyek tárolják az eszközök állapotinformációit metaadatokat, konfigurációkat és állapotokat többek között. Az Azure IoT Hub fenntart egy ikereszközt, egyes eszközök csatlakoztatása az IoT hubhoz. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez a cikk ismerteti:

* Az ikereszközök szerkezete: *címkék*, *kívánt* és *jelentett tulajdonságokként*.
* Eszközalkalmazások és a háttérrendszerek ikereszközök hajthatnak végre műveleteket.

Az ikereszközök használata:

* Store eszközspecifikus metaadatokat a felhőben. Például a központi telepítési helye a Eladóautomata.
* A jelentés aktuális állapot információkat, például elérhető képességek és feltételeket az eszközalkalmazáshoz. Ha például egy eszköz csatlakozik az IoT hub over mobilhálózati vagy Wi-Fi.
* A hosszan futó munkafolyamatok közötti eszközalkalmazás és a háttéralkalmazás állapot szinkronizálásához. Például amikor a megoldás biztonsági célból adja meg a új belső vezérlőprogram verziójának telepítéséhez, és az eszközfelügyeleti alkalmazás jelenti a frissítési folyamat különböző szakaszaiban.
* Az eszközök metaadatait, konfigurációs vagy állapot lekérdezése.

Tekintse meg [eszközről a felhőbe való kommunikáció útmutatást] [ lnk-d2c-guidance] útmutató használatával a jelentett tulajdonságok, az eszköz – felhő üzeneteket vagy a fájl feltöltése.
Tekintse meg [felhőből az eszközre irányuló kommunikáció útmutatást] [ lnk-c2d-guidance] a kívánt tulajdonságok, a közvetlen metódusok vagy a felhőből az eszközre irányuló üzenetek útmutatást.

## <a name="device-twins"></a>Ikereszközök
Ikereszközök eszközzel kapcsolatos információk tárolására, amelyek:

* Eszköz- és biztonsági vége használatával eszköz feltételek és a konfiguráció szinkronizálása.
* A megoldás háttérrendszere segítségével a lekérdezés és a cél hosszú ideig futó műveleteket.

Az ikereszközök életciklusának kapcsolódik a megfelelő [eszközidentitás][lnk-identity]. Ikereszközök implicit módon létrehozni és törölni a létrehozásakor vagy törölve az IoT Hub eszközidentitás.

Az ikereszközök JSON-dokumentumok, amely tartalmazza az:

* **A címkék**. A JSON-dokumentum, amely a megoldás háttérrendszere olvasni és írni egy része. Címkék nem láthatók az alkalmazások a eszközt.
* **Kívánt tulajdonságok**. Eszközkonfiguráció vagy feltételeket szinkronizálására használt jelentett tulajdonságokkal együtt. A megoldás háttérrendszere beállíthatja a kívánt tulajdonságok, és az eszközfelügyeleti alkalmazás olvashatja őket. Az eszköz alkalmazás is fogadhat értesítéseket a változások a kívánt tulajdonságait.
* **Jelentett tulajdonságokként**. Eszközkonfiguráció vagy feltételeket szinkronizálásához használni kívánt tulajdonságokkal együtt. Az eszközalkalmazás be, a jelentett tulajdonságokat, és a megoldás háttérrendszere, olvassa el, és lekérdezheti, ha.
* **Eszköztulajdonságok identitás**. A device twin JSON-dokumentum gyökere a megfelelő eszközidentitással tárolja a csak olvasható tulajdonságokat tartalmazza a [eszközidentitás-jegyzék][lnk-identity].

![][img-twin]

Az alábbi példa bemutatja az ikereszközök JSON-dokumentum:

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

A legfelső szintű objektumban az eszköz azonosítótulajdonságokat, és a tárolóobjektumok `tags` és mindkét `reported` és `desired` tulajdonságait. A `properties` tároló egyes csak olvasható elemeket tartalmaz (`$metadata`, `$etag`, és `$version`) leírtak a [Device twin metaadatok] [ lnk-twin-metadata] és [ Optimista párhuzamosság] [ lnk-concurrency] szakaszokat.

### <a name="reported-property-example"></a>A példában a jelentett tulajdonságok
Az előző példában az ikereszköz tartalmaz egy `batteryLevel` az eszközalkalmazás által jelentett tulajdonság. Ez a tulajdonság lekérdezése és a művelethez használt eszközök, az utolsó jelentett töltöttségi szint alapján lehetővé teszi. További példák lehetnek az alkalmazás jelentéskészítési eszköz eszközképességek vagy kapcsolati lehetőségek.

> [!NOTE]
> Jelentett tulajdonságok egyszerűbbé teszik a forgatókönyvek, ahol a megoldás háttérrendszere az utolsó ismert tulajdonság értéke érdekli. Használat [eszköz – felhő üzeneteket] [ lnk-d2c] Ha a megoldás háttérrendszere kell feldolgoznia eszköztelemetria időbélyegzővel eseményekről, például a time series sorrendje formájában.

### <a name="desired-property-example"></a>Kívánt tulajdonság példa
Az előző példában a `telemetryConfig` ikereszköz kívánt és a jelentett tulajdonságok szinkronizálni a telemetria-konfigurációt, az eszköz a megoldás háttérrendszere és az eszközalkalmazás által használt. Példa:

1. A megoldás háttérrendszere állítja be a kívánt tulajdonságot a szükségeskonfiguráció-értékkel. Íme a dokumentum a kívánt tulajdonság értéke:

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

2. Az eszközalkalmazás a változás azonnal, ha csatlakozik, vagy első újbóli értesítést kap. Az eszközalkalmazás majd jelenti a frissített konfigurációt (vagy egy hiba feltétel használatával a `status` tulajdonság). Íme a jelentett tulajdonságok részéhez:

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

3. A megoldás háttérrendszere nyomon követheti a konfigurálási művelet eredményeinek számos eszközön, a [lekérdezése] [ lnk-query] ikereszközök.

> [!NOTE]
> Az előző kódrészletek példák, optimalizálva az olvashatóság érdekében bemutatjuk, hogyan kódolása egy eszköz konfigurálásának és annak állapotát. Az IoT Hub nem kötelezi egy adott séma az ikereszköz kívánt, és jelentett tulajdonságokként az ikereszközök a.
> 
> 

A párok segítségével szinkronizálása hosszú ideig futó műveletek, például a lemezfirmware-frissítések. A tulajdonságok használatát szinkronizálását, és a egy hosszú ideig futó művelet nyomon követheti a különböző eszközökön további információkért lásd: [használata kívánt tulajdonságok konfigurálhatja az eszközöket][lnk-twin-properties].

## <a name="back-end-operations"></a>Háttér-műveletek
A megoldás háttérrendszere az ikereszközök használatával a következő atomi műveletek, a HTTPS-en keresztül elérhetővé tett működik:

* **Beolvasása azonosító alapján ikereszköz**. A művelet a eszköz ikereszköz dokumentumot, beleértve a címkék és a rendszer kívánt és a jelentett tulajdonságokat adja vissza.
* **Részlegesen frissítése az ikereszköz**. Ez a művelet lehetővé teszi, hogy a megoldás háttérrendszere részben a címkék vagy az eszközök ikereszköze a kívánt tulajdonságok frissítése. A részleges frissítési fejezzük ki, amely bármely vlastnost ad hozzá vagy JSON-dokumentumként. Tulajdonságok beállítása `null` el lesznek távolítva. A következő példában létrehozunk egy új kívánt tulajdonság értékkel `{"newProperty": "newValue"}`, felülírja a meglévő értékét `existingProperty` a `"otherNewValue"`, és eltávolítja a `otherOldProperty`. Nem más változnak meglévő kívánt tulajdonságok vagy címkék:

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

* **Cserélje le a kívánt tulajdonságok**. Ez a művelet lehetővé teszi, hogy a megoldás háttérrendszere teljesen felülírja az összes kívánt tulajdonságokkal, és helyettesítse be az új JSON-dokumentumok `properties/desired`.
* **Cserélje le a címkék**. Ez a művelet lehetővé teszi, hogy a megoldás háttérrendszere teljesen felülírja az összes meglévő címkék, és helyettesítse be az új JSON-dokumentumok `tags`.
* **Ikereszköz-értesítések fogadása**. Ez a művelet lehetővé teszi, hogy a megoldás háttérrendszere az ikereszköz módosításakor értesülni kívánnak. Ehhez az IoT-megoldás van szüksége, hozzon létre egy útvonalat és beállíthatja az adatforrás egyenlő *twinChangeEvents*. Alapértelmezés szerint nincs ikereszköz értesítések lesznek küldve, ez azt jelenti, hogy nincs ilyen útvonal már léteznie kell. Ha túl magas a változási gyakoriság, vagy más okból, például a belső hibákat, az IoT Hub küldhet csak egy értesítést, amely tartalmazza az összes módosítást. Ezért ha az alkalmazásnak megbízható vizsgálati és naplózási összes köztes állapotok, használjon eszköz – felhő üzeneteket. Ikereszköz üzenet tulajdonságait és a törzs tartalmaz.

    - Tulajdonságok

    | Name (Név) | Érték |
    | --- | --- |
    $content-típus | application/json |
    $iothub-enqueuedtime |  Idő, amikor az értesítés küldése |
    $iothub-üzenet-forrás | twinChangeEvents |
    $content-kódolás | utf-8 |
    deviceId | Az eszköz azonosítója |
    HubName | IoT Hub nevét |
    operationTimestamp | [ISO8601] időbélyegző-művelet |
    iothub-message-schema | deviceLifecycleNotification |
    opType | "replaceTwin" vagy "updateTwin" |

    Üzenet Rendszertulajdonságok van fűzve előtagként a `'$'` szimbólum.

    - Törzs
        
    Ebben a szakaszban egy JSON-formátumban ikereszköz módosításokat tartalmazza. Ugyanazt a formátumot használja a javítások, azzal a különbséggel, hogy minden ikereszköz szakasz tartalmazhat: címkék, properties.reported, properties.desired és, hogy a "$metadata" elemeket tartalmazza. Például:

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

A fenti műveletek támogatásához [optimista egyidejűséget] [ lnk-concurrency] és szükséges a **ServiceConnect** engedéllyel, ahogyan az a [biztonsági] [ lnk-security] cikk.

Ezek a műveletek mellett a megoldás háttérrendszere is:

* Az ikereszközök használatával az SQL-szerű lekérdezési [IoT Hub lekérdezési nyelv][lnk-query].
* Műveletek végrehajtása az ikereszközök használatával nagy méretű adatcsoportokat [feladatok][lnk-jobs].

## <a name="device-operations"></a>Eszközművelet
Az eszközalkalmazás működik az ikereszközök használatával a következő atomi műveletek:

* **Ikereszköz beolvasása**. Ez a művelet az eszköz ikereszköz dokumentum (beleértve a címkék és a rendszer kívánt és a jelentett Tulajdonságok) a jelenleg csatlakoztatott eszközre adja vissza.
* **Részben a jelentett tulajdonságok frissítésére**. Ez a művelet lehetővé teszi, hogy a jelenleg csatlakoztatott eszköz a jelentett tulajdonságok részleges frissítéséhez. Ez a művelet, hogy a megoldás biztonsági célból használ egy részleges kívánt tulajdonságok frissítése ugyanazon JSON frissítés formátumot használja.
* **Figyelje meg a kívánt tulajdonságok**. A jelenleg csatlakoztatott eszközre lehet váltani, ha azok a frissítések a kívánt tulajdonságok értesítést. Az eszköz megkaphatná a megoldás háttérrendszere által végrehajtott frissítés (részleges vagy teljes csere) ugyanazon az űrlapon.

A fenti műveletekhez szükség van a **DeviceConnect** engedéllyel, ahogyan az a [biztonsági] [ lnk-security] cikk.

A [Azure IoT eszközoldali SDK-k] [ lnk-sdks] megkönnyítik a fenti műveletek számos nyelven és platformon használja. Az IoT Hub primitívek kívánt tulajdonságok szinkronizálás talál további információkért lásd: [eszköz újrakapcsolódási folyamat][lnk-reconnection].

## <a name="tags-and-properties-format"></a>Címkék és tulajdonságok formátum
Címkék, kívánt tulajdonságok, jelentett tulajdonságok jsou JSON-objektumok a következő korlátozásokkal:

* A JSON-objektumok összes kulcsokat a kis-és nagybetűket 64 bájt UTF-8 UNICODE karakterláncokat. Engedélyezett karakterek kizárása UNICODE vezérlőkaraktereket (szegmensek C0 és C1), és `'.'`, `' '`, és `'$'`.
* JSON-objektumok összes értéke lehet a következő JSON-típusok: boolean, szám, karakterlánc, objektum. Tömbök használata nem engedélyezett. Egész számok maximális értéke 4503599627370495, az egész számok minimális értéke pedig-4503599627370496.
* A címkék és a kívánt, és a jelentett tulajdonságok minden JSON-objektumok rendelkezhet 5 maximális mélységét. Például a következő objektum érvénytelen:

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

* Az összes karakterlánc-értékek legfeljebb 512 bájt hosszúságú lehet.

## <a name="device-twin-size"></a>Device twin mérete
Az IoT Hub kényszerít egy 8 KB-os korlátozás megfelelő a Összértékek az egyes `tags`, `properties/desired`, és `properties/reported`, kivéve a csak olvasható elemeket.
Összes karakter lehet UNICODE vezérlőkaraktereket (szegmensek C0 és C1) alapján számított méretét és a szóközöket a karakterlánc-állandókat kívüli felhasználókhoz tartozik.
Az IoT Hub elutasítja egy hiba miatt minden művelet, amely növelné mérete meghaladja a közzétett dokumentumokat.

## <a name="device-twin-metadata"></a>Device twin metaadatok
Az IoT Hub fenntartja az időbélyeg az ikereszközön minden JSON-objektum utolsó frissítése szükséges, és jelentett tulajdonságokként. Az időbélyegek (UTC), és a kódolt a [ISO8601] formátum `YYYY-MM-DDTHH:MM:SS.mmmZ`.
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

Ezek az információk segítségével megőrizheti a frissítéseket, amelyek objektum bejegyzéseinek eltávolítása tárolt minden szinten (nem csak a JSON-struktúrát levelek).

## <a name="optimistic-concurrency"></a>Optimista párhuzamosság
Címkék, szükséges, és jelentett tulajdonságokként minden támogatási optimista egyidejűséget.
A címkék rendelkezik egy ETag megfelelően [RFC7232], amely JSON-reprezentációval a címke jelöli. Feltételes frissítési műveleteket a megoldás háttérrendszere az ETag használatával biztosítani a konzisztenciát.

Ikereszköz kívánt, és jelentett tulajdonságokként ETag nem rendelkezik, de van egy `$version` érték, amely garantáltan növekményes. Hasonlóképpen, az ETag címke, a verzió segítségével a frissítési fél konzisztenciájuk frissítések. Például egy eszköz alkalmazás a jelentett tulajdonságok vagy kívánt tulajdonság a megoldás háttérrendszeréhez.

Verziók akkor is hasznos, ha observing ügynök (például az eszköz alkalmazás figyelje a kívánt tulajdonságok) meg kell szüntetnie végigszaladsz beolvasása művelet eredményének és a egy frissítési értesítés között. A szakasz [eszköz újrakapcsolódási folyamat] [ lnk-reconnection] további információkat biztosít.

## <a name="device-reconnection-flow"></a>Eszköz újrakapcsolódási folyamat
Az IoT Hub nem őrzi meg a kívánt tulajdonságait frissítési értesítések a leválasztott eszközöket. Ebből az következik, hogy egy eszköz, amely csatlakozik, le kell kérnie a teljes kívánt tulajdonságok dokumentum mellett frissítési értesítések való előfizetés. Adja meg a frissítési értesítések és a teljes lekéréséhez közötti végigszaladsz lehetőségét, a következő folyamattal kell biztosítani:

1. Eszköz alkalmazás csatlakozik az IoT hubra.
2. Eszköz alkalmazást feliratkozik a kívánt tulajdonságok frissítési értesítések.
3. Eszközalkalmazás kérdezi le a teljes dokumentumot a kívánt tulajdonságok.

Az eszközalkalmazás figyelmen kívül hagyhatja az összes értesítésben `$version` kisebb vagy egyenlő, mint a teljes lekért dokumentumot. Ez a módszer azért lehetséges, mert az IoT Hub garantálja, hogy a verziója mindig növekszik.

> [!NOTE]
> Ez a logika már implementálva van a [Azure IoT eszközoldali SDK-k][lnk-sdks]. Ez a leírás akkor hasznos, csak ha az eszköz alkalmazás nem használja az Azure IoT eszközoldali SDK-k valamelyikét, és program kell közvetlenül a MQTT felület.
> 
> 

## <a name="additional-reference-material"></a>További – referenciaanyag
Az IoT Hub fejlesztői útmutató más referencia témakörei a következők:

* A [IoT Hub-végpontok] [ lnk-endpoints] a cikk ismerteti a különféle végpontok, amely minden IoT-központ közzéteszi a futásidejű és felügyeleti műveletekhez.
* A [sávszélesség-szabályozási és kvóták] [ lnk-quotas] a cikk ismerteti a kvótákat, az IoT Hub szolgáltatás és a szabályozás működésék, számítson, ha a szolgáltatás használatához.
* A [Azure IoT-eszköz és szolgáltatás SDK] [ lnk-sdks] a cikk felsorolja a különböző nyelvű SDK-ban is használhatja az IoT Hub szolgáltatással kommunikáló eszközt és szolgáltatást is alkalmazások fejlesztése során.
* A [az IoT Hub lekérdezési nyelv az ikereszközökhöz, feladatokkal és üzenet-útválasztása] [ lnk-query] a cikk ismerteti az IoT Hub lekérdezési nyelv az ikereszközökhöz és feladatokhoz kapcsolatos adatok lekérését az IoT Hub segítségével.
* A [IoT Hub MQTT-támogatás] [ lnk-devguide-mqtt] cikk további információt az MQTT protokoll támogatása az IoT Hub tartalmaz.

## <a name="next-steps"></a>További lépések
Most már rendelkezik megismerkedett ikereszközök, érdekelheti az alábbi az IoT Hub fejlesztői útmutató témakörök:

* [Ismertetése és használati ikermodulokkal az IoT hubon][lnk-module-twins]
* [Az eszközön közvetlen metódus meghívása][lnk-methods]
* [Feladatok ütemezése több eszközön][lnk-jobs]

Próbálja ki a jelen cikkben ismertetett fogalmakat, tekintse meg az IoT Hub anyagra:

* [Az eszközpárok használata][lnk-twin-tutorial]
* [Eszköz-ikertulajdonságok használata][lnk-twin-properties]
* [Eszközfelügyelet a VS Code az Azure IoT-eszközkészlet][lnk-twin-vscode]

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
[lnk-twin-properties]: tutorial-device-twins.md
[lnk-twin-vscode]: iot-hub-device-management-iot-toolkit.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#device-twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-module-twins]:iot-hub-devguide-module-twins.md

[img-twin]: media/iot-hub-devguide-device-twins/twin.png

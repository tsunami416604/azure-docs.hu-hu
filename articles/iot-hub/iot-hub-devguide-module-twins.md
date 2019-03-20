---
title: Azure IoT Hub ikermodulok áttekintése |} A Microsoft Docs
description: Fejlesztői útmutató – használata ikermodulokkal állapotot és a konfigurációs adatokat az IoT Hub és az eszközök közötti szinkronizálása
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 789657e53f8575b4e001fd3ec2629aaefe1a2d8b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082007"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>Ismertetése és használati ikermodulokkal az IoT hubon

Ez a cikk azt feltételezi, hogy elolvasta [ikereszközök megismerése és használata az IoT Hub](iot-hub-devguide-device-twins.md) első. Az IoT Hub minden egyes eszköz identitás alatt hozhat létre legfeljebb 20 modul identitásokat. Minden modul identitás implicit módon hoz létre egy ikermodul. Ikereszközök hasonlóan ikermodulokkal olyan JSON-dokumentumok, beleértve a metaadatokat, konfigurációkat és állapotokat modul állapot információkat tároló. Az Azure IoT Hub fenntart egy ikermodulja az egyes modulok, csatlakozik az IoT hubnak. 

Az eszköz oldalán az IoT Hub eszközoldali SDK-k lehetővé teszik modulok, ahol mindegyik megnyílik egy független az IoT Hub-kapcsolat létrehozása. Ez a funkció lehetővé teszi, hogy az önálló névterek különböző összetevőket az eszközön. Például hogy egy Eladóautomata, amely három különböző érzékelők rendelkezik. A vállalat különböző részlegei minden érzékelő vezérli. A modul minden egyes érzékelő hozhat létre. Így minden részleg csak akkor feladatok vagy közvetlen metódusokat küldhet az érzékelő, amelyek szabályozzák, elkerülve az ütközések és a felhasználói hibáinak száma.

 A modul identitás- és ikermodul adja meg az eszközidentitást, valamint az ikereszköz, de egy kifinomultabb granularitási ugyanazokat a lehetőségeket. A kifinomultabb granularitási lehetővé teszi a kompatibilis eszközök, például az operációs rendszer-alapú eszközök vagy a belső vezérlőprogrammal rendelkező eszközök kezelése több összetevőből állnak, konfigurációja és az egyes összetevők feltételek elkülönítésére. A modul identitás- és ikermodulokkal adjon meg egy felügyeleti kockázatok elkülönítése az IoT-eszközök, amelyek moduláris szoftverösszetevőket használatakor. A Microsoft célja, a modul ikereszköz szinten az összes eszköz ikereszköz funkciót támogató modul ikereszköz általános rendelkezésre állás szerint. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez a cikk ismerteti:

* Az ikermodul szerkezete: *címkék*, *kívánt* és *jelentett tulajdonságokként*.
* A modulok és a háttérrendszerek ikermodulokkal hajthatnak végre műveleteket.

Tekintse meg [eszközről a felhőbe való kommunikáció útmutatást](iot-hub-devguide-d2c-guidance.md) útmutató használatával a jelentett tulajdonságok, az eszköz – felhő üzeneteket vagy a fájl feltöltése.

Tekintse meg [felhőből az eszközre irányuló kommunikáció útmutatást](iot-hub-devguide-c2d-guidance.md) a kívánt tulajdonságok, a közvetlen metódusok vagy a felhőből az eszközre irányuló üzenetek útmutatást.

## <a name="module-twins"></a>Ikermodulokkal

Ikermodulokkal modul kapcsolatos információk tárolására, amelyek:

* Az eszközön, és az IoT Hub modulok segítségével modul feltételek és a konfiguráció szinkronizálása.

* A megoldás háttérrendszere segítségével a lekérdezés és a cél hosszú ideig futó műveleteket.

Egy ikermodul életciklusának kapcsolódik a megfelelő [modul identitás](iot-hub-devguide-identity-registry.md). Modulok twins implicit módon létrehozva, és törlődnek, ha egy modul identitás létrehozása vagy törlése az IoT Hub.

Egy ikermodul tartalmazó JSON-dokumentumok:

* **A címkék**. A JSON-dokumentum, amely a megoldás háttérrendszere olvasni és írni egy része. A címkék nem láthatók el modulokat az eszközön. A címkék lekérdezése célú vannak beállítva.

* **Kívánt tulajdonságok**. Modul konfigurációjának vagy feltételeket szinkronizálására használt jelentett tulajdonságokkal együtt. A megoldás háttérrendszere beállíthatja a kívánt tulajdonságok, és a modul alkalmazás olvashatja őket. A modul alkalmazás is fogadhat értesítéseket a módosításokat a kívánt tulajdonságai.

* **Jelentett tulajdonságokként**. Modul konfigurációjának vagy feltételeket szinkronizálásához használni kívánt tulajdonságokkal együtt. A modul app be, a jelentett tulajdonságokat, és a megoldás háttérrendszere, olvassa el, és lekérdezheti, ha.

* **A modul azonosítótulajdonságokat**. A modul ikereszköz JSON-dokumentum gyökerében tárolt megfelelő modul identitásból írásvédett tulajdonságokat tartalmazza a [eszközidentitás-jegyzék](iot-hub-devguide-identity-registry.md).

![Ikereszköz architekturális ábrázolása](./media/iot-hub-devguide-device-twins/module-twin.jpg)

A következő példában egy JSON-dokumentumok ikermodulja:

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

A legfelső szintű objektumban a modul azonosítótulajdonságokat, és a tárolóobjektumok `tags` és mindkét `reported` és `desired` tulajdonságait. A `properties` tároló egyes csak olvasható elemeket tartalmaz (`$metadata`, `$etag`, és `$version`) leírtak a [modul ikereszköz metaadatok](iot-hub-devguide-module-twins.md#module-twin-metadata) és [optimista egyidejűséget](iot-hub-devguide-device-twins.md#optimistic-concurrency) szakaszokat.

### <a name="reported-property-example"></a>A példában a jelentett tulajdonságok

Az előző példában az ikermodul tartalmaz egy `batteryLevel` modul alkalmazás által jelentett tulajdonság. Ez a tulajdonság lekérdezése és a művelethez használandó modulok az utolsó jelentett töltöttségi szint alapján lehetővé teszi. Más ilyenek például a modul alkalmazás jelentéskészítési modul képességek vagy kapcsolati lehetőségek.

> [!NOTE]
> Jelentett tulajdonságok egyszerűbbé teszik a forgatókönyvek, ahol a megoldás háttérrendszere az utolsó ismert tulajdonság értéke érdekli. Használat [eszköz – felhő üzeneteket](iot-hub-devguide-messages-d2c.md) Ha a megoldás háttérrendszere kell feldolgoznia modul telemetriai időbélyegzővel eseményekről, például a time series sorrendje formájában.

### <a name="desired-property-example"></a>Kívánt tulajdonság példa

Az előző példában a `telemetryConfig` ikermodul kívánt és a jelentett tulajdonságok szinkronizálni a telemetria-konfigurációt a modulhoz tartozó a megoldás háttérrendszere és a modul alkalmazás által használt. Példa:

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

2. A modul alkalmazást a változás azonnal, ha csatlakozik, vagy első újbóli értesítést kap. A modul alkalmazást, majd jelentést a frissített konfigurációt (vagy egy hiba feltétel használatával a `status` tulajdonság). Íme a jelentett tulajdonságok részéhez:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. A megoldás háttérrendszere nyomon követheti a konfigurálási művelet eredményeinek között számos modulok által [lekérdezése](iot-hub-devguide-query-language.md) ikermodulokkal.

> [!NOTE]
> Az előző kódrészletek példák, optimalizálva az olvashatóság érdekében bemutatjuk, hogyan kódolása a modul konfigurációja és állapota. Az IoT Hub az ikermodul kívánt és a jelentett tulajdonságok az ikermodulokkal nem kötelezi egy adott séma.
> 
> 

## <a name="back-end-operations"></a>Háttér-műveletek
A megoldás háttérrendszere az ikermodul használatával a következő atomi műveletek, a HTTPS-en keresztül elérhetővé tett működik:

* **Beolvasása azonosító alapján ikermodul**. A művelet a modul ikereszköz dokumentumot, beleértve a címkék és a rendszer kívánt és a jelentett tulajdonságokat adja vissza.

* **Részlegesen frissítése az ikermodul**. Ez a művelet lehetővé teszi, hogy a megoldás háttérrendszere a címkék és a egy ikermodul kívánt tulajdonságai részben frissíteni. A részleges frissítési fejezzük ki, amely bármely vlastnost ad hozzá vagy JSON-dokumentumként. Tulajdonságok beállítása `null` el lesznek távolítva. A következő példában létrehozunk egy új kívánt tulajdonság értékkel `{"newProperty": "newValue"}`, felülírja a meglévő értékét `existingProperty` a `"otherNewValue"`, és eltávolítja a `otherOldProperty`. Nem más változnak meglévő kívánt tulajdonságok vagy címkék:

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
    $content-type | application/json |
    $iothub-enqueuedtime |  Idő, amikor az értesítés küldése |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | Az eszköz azonosítója |
    moduleId | A modul azonosítója |
    HubName | Name of IoT Hub |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) időbélyegző-művelet |
    iothub-message-schema | deviceLifecycleNotification |
    opType | "replaceTwin" vagy "updateTwin" |

    Üzenet Rendszertulajdonságok van fűzve előtagként a `$` szimbólum.

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

A fenti műveletek támogatásához [optimista egyidejűséget](iot-hub-devguide-device-twins.md#optimistic-concurrency) és szükséges a **ServiceConnect** engedéllyel, ahogyan az az a [hozzáférés vezérlése az IoT hub](iot-hub-devguide-security.md) cikk.

Ezek a műveletek mellett a megoldás háttérrendszere le tudja kérdezni az ikermodulokkal használatával az SQL-szerű [IoT Hub lekérdezési nyelv](iot-hub-devguide-query-language.md).

## <a name="module-operations"></a>Modulműveletek

A modul alkalmazás használatával a következő atomi műveletek ikermodulja működik:

* **Ikermodul lekéréséhez**. A művelet a modul ikereszköz dokumentum (beleértve a címkék és a rendszer kívánt és a jelentett Tulajdonságok) a jelenleg csatlakoztatott modul adja vissza.

* **Részben a jelentett tulajdonságok frissítésére**. Ez a művelet lehetővé teszi, hogy a jelenleg csatlakoztatott modul az a jelentett tulajdonságok részleges frissítéséhez. Ez a művelet, hogy a megoldás biztonsági célból használ egy részleges kívánt tulajdonságok frissítése ugyanazon JSON frissítés formátumot használja.

* **Figyelje meg a kívánt tulajdonságok**. A jelenleg csatlakoztatott modul lehet váltani, ha azok a frissítések a kívánt tulajdonságok értesítést. A modul megkapja a megoldás háttérrendszere által végrehajtott frissítés (részleges vagy teljes csere) ugyanazon az űrlapon.

A fenti műveletekhez szükség van a **ModuleConnect** engedéllyel, ahogyan az a [hozzáférés vezérlése az IoT hub](iot-hub-devguide-security.md) cikk.

A [Azure IoT eszközoldali SDK-k](iot-hub-devguide-sdks.md) megkönnyítik a fenti műveletek számos nyelven és platformon használja.

## <a name="tags-and-properties-format"></a>Címkék és tulajdonságok formátum

Címkék, kívánt tulajdonságok, jelentett tulajdonságok jsou JSON-objektumok a következő korlátozásokkal:

* A JSON-objektumok összes kulcsokat a kis-és nagybetűket 64 bájt UTF-8 UNICODE karakterláncokat. Engedélyezett karakterek kizárása UNICODE vezérlőkaraktereket (szegmensek C0 és C1), és `.`, SP és `$`.

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

## <a name="module-twin-size"></a>Modul ikereszköz mérete

Az IoT Hub kényszerít egy 8 KB-os korlátozás megfelelő a Összértékek az egyes `tags`, `properties/desired`, és `properties/reported`, kivéve a csak olvasható elemeket.

Összes karakter lehet UNICODE vezérlőkaraktereket (szegmensek C0 és C1) alapján számított méretét és a szóközöket a karakterlánc-állandókat kívüli felhasználókhoz tartozik.

Az IoT Hub elutasítja egy hiba miatt minden művelet, amely növelné mérete meghaladja a közzétett dokumentumokat.

## <a name="module-twin-metadata"></a>A modul ikereszköz metaadatok

Az IoT Hub fenntartja az időbélyeget utolsó frissítésének minden JSON-objektum az ikermodul kívánt, és jelentett tulajdonságokként. Az időbélyegek (UTC), és a kódolt a [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) formátum `YYYY-MM-DDTHH:MM:SS.mmmZ`.
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
A címkék rendelkezik egy ETag megfelelően [RFC7232](https://tools.ietf.org/html/rfc7232), amely JSON-reprezentációval a címke jelöli. Feltételes frissítési műveleteket a megoldás háttérrendszere az ETag használatával biztosítani a konzisztenciát.

Ikermodul kívánt, és jelentett tulajdonságokként ETag nem rendelkezik, de van egy `$version` érték, amely garantáltan növekményes. Hasonlóképpen, az ETag címke, a verzió segítségével a frissítési fél konzisztenciájuk frissítések. Például egy modul alkalmazás a jelentett tulajdonságok vagy kívánt tulajdonság a megoldás háttérrendszeréhez.

Verziók akkor is hasznos, ha observing ügynök (például a modul az alkalmazás figyelje a kívánt tulajdonságok) meg kell szüntetnie végigszaladsz beolvasása művelet eredményének és a egy frissítési értesítés között. A szakasz [eszköz újrakapcsolódási folyamat](iot-hub-devguide-device-twins.md#device-reconnection-flow) további információkat biztosít. 

## <a name="next-steps"></a>További lépések

Próbálja ki a jelen cikkben ismertetett fogalmakat, tekintse meg az IoT Hub anyagra:

* [Ismerkedés az IoT Hub identitás- és modul ikermodul .NET-háttérrendszer és a .NET-eszköz használata](iot-hub-csharp-csharp-module-twin-getstarted.md)

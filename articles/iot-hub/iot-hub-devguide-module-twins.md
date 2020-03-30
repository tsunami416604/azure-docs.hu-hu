---
title: Az Azure IoT Hub-modul twins megismerése | Microsoft dokumentumok
description: Fejlesztői útmutató – a modultwins használatával szinkronizálhatja az állapot- és konfigurációs adatokat az IoT Hub és az eszközök között
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: menchi
ms.openlocfilehash: 5ef6c4de288a764abbe434c5d84fc99e154f7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303596"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>A modulikai ikrek megértése és használata az IoT Hubban

Ez a cikk feltételezi, hogy először az [IoT Hubban olvassa az eszközök ikreinek megismerése és használata](iot-hub-devguide-device-twins.md) című olvasnivalót. Az IoT Hubban az egyes eszközidentitások alatt legfeljebb 20 modulidentitást hozhat létre. Minden modulidentitás implicit módon létrehoz egy ikermodult. Az ikereszközökhöz hasonlóan a modulikrek is json-dokumentumok, amelyek a modul állapotadatait, beleértve a metaadatokat, konfigurációkat és feltételeket is tárolnak. Az Azure IoT Hub egy ikermodult tart fenn minden olyan modulhoz, amelyhez csatlakozik az IoT Hubhoz. 

Az eszköz oldalán az IoT Hub-eszköz SDK-k lehetővé teszik, hogy olyan modulokat hozzon létre, ahol mindegyik önálló kapcsolatot nyit meg az IoT Hubhoz. Ez a funkció lehetővé teszi, hogy külön névtereket használjon az eszköz különböző összetevőihez. Például van egy automata, amely három különböző érzékelővel rendelkezik. Minden érzékelőt a vállalat különböző részlegei irányítanak. Minden érzékelőhöz létrehozhat egy modult. Így minden részleg csak feladatokat vagy közvetlen metódusokat küldhet az általa irányított érzékelőnek, elkerülve az ütközéseket és a felhasználói hibákat.

 A modulidentitás és az ikermodul ugyanazokat a képességeket biztosítja, mint az eszközidentitás és az ikereszköz, de finomabb részletességgel. Ez a finomabb részletesség lehetővé teszi, hogy a képes eszközök, például az operációs rendszer alapú eszközök vagy a több összetevőt kezelő belső vezérlőprogram-eszközök elkülönítsék az egyes összetevők konfigurációját és feltételeit. A modulidentitás és a moduliker-ikrek a moduláris szoftverösszetevőkkel rendelkező IoT-eszközökkel végzett problémák felügyeleti elkülönítését biztosítják. Célunk, hogy támogassa az összes eszköz iker funkció modul iker szinten modul iker általános elérhetősége. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez a cikk a következőket ismerteti:

* A modul szerkezete iker: *címkék*, *kívánt* és *jelentett tulajdonságok*.
* A modulok és a háttér-végek által végrehajtható műveletek et a modul twins.

Tekintse meg [az eszközről a felhőbe irányuló kommunikáció útmutatást](iot-hub-devguide-d2c-guidance.md) a jelentett tulajdonságok, eszköz-felhő üzenetek vagy fájlfeltöltés.

A [felhőből az eszközre irányuló kommunikációról szóló útmutatásban](iot-hub-devguide-c2d-guidance.md) útmutatást talál a kívánt tulajdonságok, a közvetlen módszerek vagy a felhőből az eszközre irányuló üzenetek használatával kapcsolatban.

## <a name="module-twins"></a>Modul ikrek

A twins modulmodulokkal kapcsolatos információkat tárolnak, amelyek:

* Az eszközön lévő modulok és az IoT Hub segítségével szinkronizálhatja a modul feltételeit és a konfigurációt.

* A megoldás háttérrendszeréből lekérdezheti és megcélozhatja a hosszú ideig futó műveleteket.

Az ikermodul életciklusa a megfelelő [modulidentitáshoz](iot-hub-devguide-identity-registry.md)kapcsolódik. A modulok ikrei implicit módon jönnek létre és törlődnek, amikor egy modulidentitást hoznak létre vagy törölnek az IoT Hubban.

A modul iker egy JSON dokumentum, amely tartalmazza:

* **Címkék**. A JSON-dokumentum azon szakasza, amelyből a megoldás háttértagja olvashat és írhat. A címkék nem láthatók az eszközön lévő modulok számára. A címkék lekérdezési célra vannak beállítva.

* **A kívánt tulajdonságokat.** A jelentett tulajdonságokkal együtt szinkronizálja a modul konfigurációját vagy feltételeit. A megoldás háttér-kezelője beállíthatja a kívánt tulajdonságokat, és a modulalkalmazás el tudja olvasni őket. A modulalkalmazás értesítéseket is kaphat a kívánt tulajdonságok változásairól.

* **Jelentett tulajdonságok**. A kívánt tulajdonságokkal együtt szinkronizálja a modul konfigurációját vagy feltételeit. A modulalkalmazás beállíthatja a jelentett tulajdonságokat, és a megoldás háttérrendszerénkedő rendszer leolvashatja és lekérdezheti azokat.

* **A modul identitásának tulajdonságai**. A modul iker JSON-dokumentumának gyökere az [identitásjegyzékben](iot-hub-devguide-identity-registry.md)tárolt megfelelő modulidentitás csak olvasható tulajdonságait tartalmazza.

![Az ikereszköz építészeti ábrázolása](./media/iot-hub-devguide-device-twins/module-twin.jpg)

A következő példa egy iker JSON-dokumentumot mutat be:

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
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

A gyökérobjektumban a modul identitástulajdonságai és `tags` a `reported` tárolóobjektumok, valamint a `desired` tulajdonságok. A `properties` tároló tartalmaz néhány csak`$metadata` `$etag`olvasható `$version`elemeket ( , és ) a [modul iker metaadatok](iot-hub-devguide-module-twins.md#module-twin-metadata) és [optimista egyidejűség](iot-hub-devguide-device-twins.md#optimistic-concurrency) szakaszokban ismertetett.

### <a name="reported-property-example"></a>Jelentett tulajdonság példa

Az előző példában az ikermodul tartalmaz egy `batteryLevel` tulajdonságot, amely a modul alkalmazás által jelentett. Ez a tulajdonság lehetővé teszi a modulok lekérdezését és működtetését az utolsó jelentett akkumulátorszint alapján. További példák a modul alkalmazásjelentési modul képességei vagy kapcsolódási lehetőségek.

> [!NOTE]
> A jelentett tulajdonságok leegyszerűsítik azokat a forgatókönyveket, ahol a megoldás háttérrendszerének érdeklődése a tulajdonság utolsó ismert értéke. Használjon [eszközről felhőre irányuló üzeneteket,](iot-hub-devguide-messages-d2c.md) ha a megoldás háttérrendszerének időbélyeggel ellátott események, például idősorozatok sorozatok formájában kell feldolgoznia a modultelemetriai adatokat.

### <a name="desired-property-example"></a>Kívánt tulajdonság példa

Az előző példában `telemetryConfig` a modul iker kívánt és jelentett tulajdonságokat a megoldás háttér-és a modul alkalmazás a modul telemetriai konfigurációjának szinkronizálásához. Példa:

1. A megoldás háttérvége beállítja a kívánt tulajdonságot a kívánt konfigurációs értékkel. Itt van a dokumentum nak a kívánt tulajdonságkészlettel rendelkező része:

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

2. A modulalkalmazás azonnal értesítést kap a változásról, ha csatlakoztatva van, vagy az első újracsatlakozáskor. A modulalkalmazás ezután jelenti a frissített konfigurációt (vagy a `status` tulajdonságot használó hibafeltételt). Itt van a jelentett tulajdonságok része:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. A megoldás háttér-rendszerén nyomon követheti a konfigurációs művelet eredményeit számos modul, a modul twins [lekérdezésével.](iot-hub-devguide-query-language.md)

> [!NOTE]
> Az előző kódrészletek példák, olvashatóságra optimalizálva, a modulkonfiguráció és állapotának kódolásának egyik módja. Az IoT Hub nem ír elő egy adott sémát a modul iker kívánt és jelentett tulajdonságokat a modul twins.
> 
> 

## <a name="back-end-operations"></a>Háttérműveletek
A megoldás háttérvége a modul ikermodulján működik a következő atomi műveletek használatával, https-en keresztül kitéve:

* **A modul ikermodul lekérése azonosítóval.** Ez a művelet a modul ikerdokumentumát adja vissza, beleértve a címkéket és a kívánt és jelentett rendszertulajdonságokat.

* **Részlegesfrissítési modul iker**. Ez a művelet lehetővé teszi, hogy a megoldás háttérrendszere részben frissítse a címkéket vagy a kívánt tulajdonságokat egy ikermodulban. A részleges frissítés JSON-dokumentumként van kifejezve, amely bármely tulajdonságot hozzáad vagy frissít. A tulajdonságai törlődnek. `null` A következő példa egy új `{"newProperty": "newValue"}`kívánt tulajdonságot hoz létre `existingProperty` `"otherNewValue"`értékkel, `otherOldProperty`felülírja a meglévő értékét a alkalmazással, és eltávolítja a kívánt tulajdonságot . A meglévő kívánt tulajdonságokat vagy címkéket nem módosítják:

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

* **Cserélje ki a kívánt tulajdonságokat**. Ez a művelet lehetővé teszi, hogy a megoldás háttérrendszerének teljes `properties/desired`mértékben felülírja az összes meglévő kívánt tulajdonságot, és helyettesítsen egy új JSON-dokumentumot a programmal.

* **Címkék cseréje**. Ez a művelet lehetővé teszi, hogy a megoldás háttérrendszerének teljes `tags`mértékben felülírja az összes meglévő címkét, és helyettesítse az új JSON-dokumentumot.

* **Két értesítés fogadása**. Ez a művelet lehetővé teszi, hogy a megoldás háttér-tartalék adandó értesítést, ha az iker módosításra kerül. Ehhez az IoT-megoldásnak létre kell hoznia egy útvonalat, és az adatforrást *a twinChangeEvents-nek kell beállítania.* Alapértelmezés szerint a rendszer nem küld ikerértesítéseket, azaz nincsenek előre ilyen útvonalak. Ha a változás mértéke túl magas, vagy más okok miatt, például a belső hibák, az IoT Hub előfordulhat, hogy csak egy értesítést, amely tartalmazza az összes módosítást. Ezért ha az alkalmazás nak szüksége van az összes köztes állapot megbízható naplózására és naplózására, használjon eszközről felhőbe irányuló üzeneteket. A kettős értesítési üzenet tulajdonságokat és törzset tartalmaz.

  - Tulajdonságok

    | Név | Érték |
    | --- | --- |
    $content típusú | application/json |
    $iothub-enqueuedtime |  Az értesítés elküldésének időpontja |
    $iothub-üzenet-forrás | twinChangeEvents |
    $content kódolás | utf-8 |
    deviceId | Az eszköz azonosítója |
    moduleId | A modul azonosítója |
    hubName | Az IoT Hub neve |
    operationTimestamp művelet | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) működési időbélyeg |
    iothub-üzenet-séma | twinChangeNotification |
    opType (típus) | "replaceTwin" vagy "updateTwin" |

    Az üzenetrendszer tulajdonságai a `$` szimbólummal vannak előrögzítve.

  - Törzs
        
    Ez a szakasz a JSON formátum összes ikermódosítását tartalmazza. Ugyanazt a formátumot használja, mint egy javítás, azzal a különbséggel, hogy az összes ikerszakaszt tartalmazhatja: címkék, properties.reported, properties.desired, és hogy tartalmazza a "$metadata" elemeket. Például:

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

Az összes előző művelet támogatja az [optimista egyidejűséget,](iot-hub-devguide-device-twins.md#optimistic-concurrency) és az [IoT Hubhoz való hozzáférés szabályozása](iot-hub-devguide-security.md) cikkben meghatározott **ServiceConnect-engedélyre** van szükség.

Ezeken a műveleteken kívül a megoldás háttérrendszer-kezelőja lekérdezheti a modul ikrek et az SQL-szerű [IoT Hub lekérdezési nyelv](iot-hub-devguide-query-language.md)használatával.

## <a name="module-operations"></a>Modulműveletek

A modul alkalmazás működik a modul iker segítségével a következő atomi műveletek:

* **Az ikermodul beolvasása**. Ez a művelet a modul ikerdokumentumát adja vissza (beleértve a címkéket és a kívánt és jelentett rendszertulajdonságokat) az aktuálisan csatlakoztatott modulhoz.

* **Részben frissíti a jelentett tulajdonságokat**. Ez a művelet lehetővé teszi az aktuálisan csatlakoztatott modul jelentett tulajdonságainak részleges frissítését. Ez a művelet ugyanazt a JSON frissítési formátumot használja, amelyet a megoldás háttérrendszerének a kívánt tulajdonságok részleges frissítésére használ.

* **Figyelje meg a kívánt tulajdonságokat**. A jelenleg csatlakoztatott modul dönthet úgy, hogy értesítést kap a kívánt tulajdonságok frissítéseiről, amikor azok megtörténnek. A modul megkapja a megoldás háttéráltal végrehajtott frissítési (részleges vagy teljes csere) formáját.

Az összes előző művelethez a **ModuleConnect** engedély szükséges, az [IoT Hubhoz való hozzáférés szabályozása](iot-hub-devguide-security.md) cikkben meghatározottak szerint.

Az [Azure IoT-eszköz SDK-k](iot-hub-devguide-sdks.md) megkönnyítik az előző műveletek használatát számos nyelven és platformon.

## <a name="tags-and-properties-format"></a>Címkék és tulajdonságok formátuma

A címkék, a kívánt tulajdonságok és a jelentett tulajdonságok JSON-objektumok, amelyek a következő korlátozásokkal rendelkeznek:

* **Billentyűk**: A JSON-objektumokban lévő összes kulcs kis- és nagybetűket megkülönböztető 64 bájtos UTF-8 UNICODE karakterlánc. Az engedélyezett karakterek nem tartalmazzák a UNICODE vezérlőkaraktereket (C0 és C1 szegmens), valamint `.`a , SP és `$`.

* **Értékek**: A JSON-objektumok ban lévő összes érték a következő JSON-típusok lehetnek: logikai, szám, karakterlánc, objektum. A tömbök nem engedélyezettek.

    * Az egész számok minimális értéke -4503599627370496, maximális értéke 45035999627370495.

    * A karakterlánc-értékek UTF-8 kódolásúak, és legfeljebb 512 bájt hosszúságúak lehetnek.

* **Mélység:** A címkékben lévő összes JSON-objektum, a kívánt és a jelentett tulajdonságok maximális mélysége 5 lehet. Például a következő objektum érvényes:

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

## <a name="module-twin-size"></a>Ikermodul mérete

Az IoT Hub 8 KB-os méretkorlátot kényszerít ki a értékére, `tags` `properties/desired` és `properties/reported`32 KB-os méretkorlátot a és a értékére. Ezek az összegek nem tartalmazzák `$etag`az `$version`írásvédett elemeket, mint például a , és `$metadata/$lastUpdated`a .

Az ikerméret kiszámítása a következőképpen történik:

* A JSON-dokumentum minden tulajdonsága esetében az IoT Hub összesítve számítja ki és adja hozzá a tulajdonság kulcsának és értékének hosszát.

* A tulajdonságkulcsok UTF8 kódolású karakterláncoknak minősülnek.

* Az egyszerű tulajdonságértékek UTF8 kódolású karakterláncoknak, numerikus értékeknek (8 bájt) vagy logikai értékeknek (4 bájt) számítanak.

* Az UTF8 kódolású karakterláncok méretét az összes karakter megszámlálásával számítja ki, kivéve a UNICODE vezérlőkaraktereket (C0 és C1 szegmens).

* Az összetett tulajdonságértékek (beágyazott objektumok) számítása a tulajdonságkulcsok és a tulajdonságértékek összesített mérete alapján van kiszámítva.

Az IoT Hub elutasítja egy hiba minden olyan műveletet, amely növelné a dokumentumok méretét a korlát felett.

## <a name="module-twin-metadata"></a>Ikermodul metaadatai

Az IoT Hub az utolsó frissítés időbélyegét tartja karban minden Egyes JSON-objektumhoz az ikermodul által kívánt és jelentett tulajdonságokban. Az időbélyegek UTC formátumban vannak, és [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) formátumban `YYYY-MM-DDTHH:MM:SS.mmmZ`vannak kódolva.
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
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
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

Ez az információ minden szinten (nem csak a JSON-struktúra levelein) tárolható az objektumkulcsokat eltávolító frissítések megőrzése érdekében.

## <a name="optimistic-concurrency"></a>Optimista egyidejűség

Címkék, kívánt, és a jelentett tulajdonságok minden támogatja az optimista egyidejűség.
Címkék egy ETag, mint egy [RFC7232](https://tools.ietf.org/html/rfc7232), amely képviseli a tag JSON ábrázolása. Az ETag-ek a megoldás háttérrendszeréből származó feltételes frissítési műveletekben is használhatja a konzisztencia biztosítása érdekében.

A modul iker kívánt és jelentett tulajdonságai nem `$version` rendelkeznek ETag-ek, de van egy érték, amely garantáltan növekményes. Az ETag-hez hasonlóan a verziót a frissítő fél is használhatja a frissítések konzisztenciájának érvényesítésére. Például egy modul alkalmazás egy jelentett tulajdonság, vagy a megoldás háttéra a kívánt tulajdonsághoz.

A verziók akkor is hasznosak, ha egy megfigyelő ügynöknek (például a kívánt tulajdonságokat megfigyelő modulalkalmazásnak) össze kell egyeztetnie a lekérési művelet és a frissítési értesítés közötti versenyeket. Az [Eszköz-újrakapcsolódási folyamat](iot-hub-devguide-device-twins.md#device-reconnection-flow) szakasz további információkat tartalmaz. 

## <a name="next-steps"></a>További lépések

A cikkben ismertetett fogalmak némelyikének kipróbálásához tekintse meg az Alábbi IoT Hub-oktatóanyagokat:

* [Ismerkedés az IoT Hub modulidentitásával és az ikermodulmodullal a .NET háttér- és .NET-eszköz használatával](iot-hub-csharp-csharp-module-twin-getstarted.md)

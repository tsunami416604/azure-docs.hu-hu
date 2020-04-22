---
title: Az Azure IoT Hub-eszközök ikreinek megismerése | Microsoft dokumentumok
description: Fejlesztői útmutató – az eszközök iktatóinak használata az Állapot- és konfigurációs adatok szinkronizálásához az IoT Hub és az eszközök között
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/01/2020
ms.custom: mqtt
ms.openlocfilehash: 3bec3d19ed68b7eb8bb50baa8f6c11135ef778cc
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731463"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Az eszközök ikreinek megértése és használata az IoT Hubban

*Az ikereszközök* olyan JSON-dokumentumok, amelyek az eszközállapot-adatokat, beleértve a metaadatokat, konfigurációkat és feltételeket is tárolnak. Az IoT Hub minden egyes rácsatlakoztatott eszközhöz fenntart egy ikereszközt. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez a cikk a következőket ismerteti:

* Az eszköz szerkezete iker: *címkék*, *kívánt* és *jelentett tulajdonságok*.
* Az eszközalkalmazások és a háttérrendszerek által végrehajtható műveletek az ikereszközökön is végrehajthatók.

Az eszközikrek használata:

* Eszközspecifikus metaadatok tárolása a felhőben. Például egy automaták üzembe helyezésének helye.

* Jelentse az aktuális állapotinformációkat, például az eszközalkalmazásból rendelkezésre álló képességeket és feltételeket. Például egy eszköz mobilhálózaton vagy Wi-Fi-n keresztül csatlakozik az IoT hubhoz.

* Szinkronizálja a hosszú ideig futó munkafolyamatok állapotát az eszközalkalmazás és a háttéralkalmazás között. Ha például a megoldás háttérrendszerénbelül megadja a telepítendő új belső vezérlőprogram-verziót, és az eszközalkalmazás jelenti a frissítési folyamat különböző szakaszait.

* Az eszköz metaadatainak, konfigurációjának vagy állapotának lekérdezése.

Tekintse meg [az eszközről a felhőbe irányuló kommunikáció útmutatást](iot-hub-devguide-d2c-guidance.md) a jelentett tulajdonságok, eszköz-felhő üzenetek vagy fájlfeltöltés.

A [felhőből az eszközre irányuló kommunikációról szóló útmutatásban](iot-hub-devguide-c2d-guidance.md) útmutatást talál a kívánt tulajdonságok, a közvetlen módszerek vagy a felhőből az eszközre irányuló üzenetek használatával kapcsolatban.

## <a name="device-twins"></a>Eszköz ikrek

Az ikereszközök az eszközzel kapcsolatos információkat tárolják, amelyek:

* Az eszköz- és háttérrendszerek az eszköz állapotának és konfigurációjának szinkronizálására használhatók.

* A megoldás háttérrendszeréből lekérdezheti és megcélozhatja a hosszú ideig futó műveleteket.

Az ikereszköz életciklusa a megfelelő [eszközidentitáshoz](iot-hub-devguide-identity-registry.md)kapcsolódik. Az eszköztwins implicit módon jön létre, és törlődik, ha egy eszköz identitását hoz létre vagy törli az IoT Hub.

Az ikereszköz egy JSON-dokumentum, amely a következőket tartalmazza:

* **Címkék**. A JSON-dokumentum azon szakasza, amelyből a megoldás háttértagja olvashat és írhat. A címkék nem láthatók az eszközalkalmazások számára.

* **A kívánt tulajdonságokat.** A jelentett tulajdonságokkal együtt szinkronizálja az eszköz konfigurációját vagy feltételeit. A megoldás háttér-kezelője beállíthatja a kívánt tulajdonságokat, és az eszközalkalmazás képes olvasni őket. Az eszközalkalmazás értesítéseket is kaphat a kívánt tulajdonságok változásairól.

* **Jelentett tulajdonságok**. Az eszköz konfigurációjának és feltételeinek szinkronizálásához a kívánt tulajdonságokkal együtt használható. Az eszközalkalmazás beállíthatja a jelentett tulajdonságokat, és a megoldás háttérrendszerének olvashatják és lekérdezhetik azokat.

* **Az eszköz identitásának tulajdonságai**. Az ikerJSON-dokumentum gyökere az [identitásjegyzékben](iot-hub-devguide-identity-registry.md)tárolt megfelelő eszközidentitás csak olvasható tulajdonságait tartalmazza. tulajdonságokat, `connectionStateUpdatedTime` és `generationId` nem fog szerepelni.

![Képernyőkép az ikereszköz tulajdonságairól](./media/iot-hub-devguide-device-twins/twin.png)

A következő példa egy iker JSON-dokumentumot mutat be:

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
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

A gyökérobjektumban az eszköz identitásának tulajdonságai, `tags` valamint `reported` `desired` a tárolóobjektumok, valamint a tulajdonságok. A `properties` tároló tartalmaz néhány írásvédett `$etag`elemet `$version`(`$metadata`, és ) az [Eszköz iker metaadat-](iot-hub-devguide-device-twins.md#device-twin-metadata) és [optimista egyidejűségi](iot-hub-devguide-device-twins.md#optimistic-concurrency) szakaszában ismertetett.

### <a name="reported-property-example"></a>Jelentett tulajdonság példa

Az előző példában az ikereszköz tartalmaz egy `batteryLevel` tulajdonságot, amely az eszköz alkalmazás által jelentett. Ez a tulajdonság lehetővé teszi az eszközök lekérdezését és működtetését az utolsó jelentett akkumulátorszint alapján. További példák az eszközalkalmazás-jelentési eszköz képességei vagy a kapcsolódási lehetőségek.

> [!NOTE]
> A jelentett tulajdonságok leegyszerűsítik azokat a forgatókönyveket, ahol a megoldás háttérrendszerének érdeklődése a tulajdonság utolsó ismert értéke. Használjon [eszközről felhőre irányuló üzeneteket,](iot-hub-devguide-messages-d2c.md) ha a megoldás háttérrendszerének időbélyeggel ellátott események, például idősorozatok sorozatok formájában kell feldolgoznia az eszköz telemetriai adatokat.

### <a name="desired-property-example"></a>Kívánt tulajdonság példa

Az előző példában `telemetryConfig` az eszköz iker kívánt és jelentett tulajdonságok at a megoldás háttér-és az eszköz alkalmazás az eszköz telemetriai konfigurációjának szinkronizálásához. Például:

1. A megoldás háttérvége beállítja a kívánt tulajdonságot a kívánt konfigurációs értékkel. Itt van a dokumentum nak a kívánt tulajdonságkészlettel rendelkező része:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. Az eszközalkalmazás azonnal értesítést kap a változásról, ha csatlakoztatva van, vagy az első újracsatlakozáskor. Az eszközalkalmazás ezután jelenti a frissített konfigurációt (vagy a `status` tulajdonságot használó hibaállapotot). Itt van a jelentett tulajdonságok része:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. A megoldás háttér-rendszerén nyomon követheti a konfigurációs művelet eredményeit számos eszközön az eszközök twins [lekérdezésével.](iot-hub-devguide-query-language.md)

> [!NOTE]
> Az előző kódrészletek az eszközkonfiguráció és állapotának egy módon való kódolásának egyik módja az olvashatóságra optimalizálva. Az IoT Hub nem ír elő egy adott sémát az ikereszköz által kívánt és jelentett tulajdonságokhoz az ikereszközökben.
> 

Az ikrek segítségével szinkronizálhatja a hosszú ideig futó műveleteket, például a belső vezérlőprogram frissítéseit. Ha többet szeretne tudni arról, hogy miként használhatja a tulajdonságokat egy hosszú ideig futó művelet szinkronizálására és nyomon követésére az eszközök között, olvassa el a Kívánt tulajdonságok használata eszközök konfigurálásához című [témakört.](tutorial-device-twins.md)

## <a name="back-end-operations"></a>Háttérműveletek

A megoldás háttérvége a következő atomi műveleteket használva működik az ikereszközön, https-en keresztül kitéve:

* **Az ikereszköz lekérése azonosítóval.** Ez a művelet az eszköz ikerdokumentumát adja vissza, beleértve a címkéket és a kívánt és jelentett rendszertulajdonságokat.

* **Részlegesen frissíti az ikereszközt**. Ez a művelet lehetővé teszi, hogy a megoldás háttérrendszere részben frissítse a címkéket vagy a kívánt tulajdonságokat egy ikereszközben. A részleges frissítés JSON-dokumentumként van kifejezve, amely bármely tulajdonságot hozzáad vagy frissít. A tulajdonságai törlődnek. `null` A következő példa egy új `{"newProperty": "newValue"}`kívánt tulajdonságot hoz létre `existingProperty` `"otherNewValue"`értékkel, `otherOldProperty`felülírja a meglévő értékét a alkalmazással, és eltávolítja a kívánt tulajdonságot . A meglévő kívánt tulajdonságokat vagy címkéket nem módosítják:

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

* **Két értesítés fogadása**. Ez a művelet lehetővé teszi, hogy a megoldás háttér-tartalék adandó értesítést, ha az iker módosításra kerül. Ehhez az IoT-megoldásnak létre kell hoznia egy útvonalat, és az adatforrást *a twinChangeEvents-nek kell beállítania.* Alapértelmezés szerint nincsenek ilyen útvonalak előre, így nem küld ikerértesítéseket. Ha a változás mértéke túl magas, vagy más okok miatt, például a belső hibák, az IoT Hub előfordulhat, hogy csak egy értesítést, amely tartalmazza az összes módosítást. Ezért ha az alkalmazás nak szüksége van az összes köztes állapot megbízható naplózására és naplózására, használjon eszközről felhőbe irányuló üzeneteket. A kettős értesítési üzenet tulajdonságokat és törzset tartalmaz.

  - Tulajdonságok

    | Name (Név) | Érték |
    | --- | --- |
    $content típusú | application/json |
    $iothub-enqueuedtime |  Az értesítés elküldésének időpontja |
    $iothub-üzenet-forrás | twinChangeEvents |
    $content kódolás | utf-8 |
    deviceId | Az eszköz azonosítója |
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

Az összes előző művelet támogatja az [Optimista egyidejűséget,](iot-hub-devguide-device-twins.md#optimistic-concurrency) és az [IoT Hubhoz való hozzáférés szabályozása](iot-hub-devguide-security.md)című részben meghatározott **ServiceConnect-engedélyre** van szükség.

Ezeken a műveleteken kívül a megoldás háttérvége:

* Az sql-szerű IoT Hub lekérdezési nyelv használatával lekérdezi az eszköz twins.Query the device twins using the SQL-like [IoT Hub query language.](iot-hub-devguide-query-language.md)

* Műveletek végrehajtása nagy eszköz ikrek feladatok [használatával.](iot-hub-devguide-jobs.md)

## <a name="device-operations"></a>Eszközműveletek

Az eszközalkalmazás a következő atomi műveletekkel működik az ikereszközön:

* **Az ikereszköz beolvasása**. Ez a művelet az eszköz ikerdokumentumát adja vissza (beleértve a kívánt és jelentett rendszertulajdonságokat) az aktuálisan csatlakoztatott eszközhöz. (A címkék nem láthatók az eszközalkalmazások számára.)

* **Részben frissíti a jelentett tulajdonságokat**. Ez a művelet lehetővé teszi az aktuálisan csatlakoztatott eszköz jelentett tulajdonságainak részleges frissítését. Ez a művelet ugyanazt a JSON frissítési formátumot használja, amelyet a megoldás háttérrendszerének a kívánt tulajdonságok részleges frissítésére használ.

* **Figyelje meg a kívánt tulajdonságokat**. A jelenleg csatlakoztatott eszköz dönthet úgy, hogy értesítést kap a kívánt tulajdonságok frissítéseiről, amikor azok megtörténnek. Az eszköz megkapja a megoldás háttéráltal végrehajtott frissítési (részleges vagy teljes csere) formáját.

Az összes előző művelethez a **DeviceConnect** engedély szükséges az [IoT Hubhoz való hozzáférés vezérlése](iot-hub-devguide-security.md)című részben meghatározottak szerint.

Az [Azure IoT-eszköz SDK-k](iot-hub-devguide-sdks.md) megkönnyítik az előző műveletek használatát számos nyelven és platformon. Az IoT Hub primitívek a kívánt tulajdonságok szinkronizálásának részleteiről az [Eszköz-újrakapcsolódási folyamat](iot-hub-devguide-device-twins.md#device-reconnection-flow)című témakörben talál további információt.

## <a name="tags-and-properties-format"></a>Címkék és tulajdonságok formátuma

A címkék, a kívánt tulajdonságok és a jelentett tulajdonságok JSON-objektumok, amelyek a következő korlátozásokkal rendelkeznek:

* **Billentyűk**: A JSON-objektumok ban lévő összes kulcs UTF-8 kódolású, kis- és nagybetűket megkülönböztető és legfeljebb 1 KB hosszúságú. Az engedélyezett karakterek kizárják a UNICODE vezérlőkaraktereket `.` `$`(C0 és C1 szegmens), valamint a , és az SP karaktereket.

* **Értékek**: A JSON-objektumok ban lévő összes érték a következő JSON-típusok lehetnek: logikai, szám, karakterlánc, objektum. A tömbök nem engedélyezettek.

    * Az egész számok minimális értéke -4503599627370496, maximális értéke 45035999627370495.

    * A karakterlánc-értékek UTF-8 kódolásúak, és legfeljebb 4 KB hosszúságúak lehetnek.

* **Mélység:** A JSON-objektumok maximális mélysége a címkékben, a kívánt tulajdonságokban és a jelentett tulajdonságokban 10. Például a következő objektum érvényes:

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "six": {
                                   "seven": {
                                       "eight": {
                                           "nine": {
                                               "ten": {
                                                   "property": "value"
                                               }
                                           }
                                       }
                                   }
                               }
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

## <a name="device-twin-size"></a>Az ikereszköz mérete

Az IoT Hub 8 KB-os méretkorlátot kényszerít ki a értékére, `tags` `properties/desired` és `properties/reported`32 KB-os méretkorlátot a és a értékére. Ezek az összegek nem tartalmazzák `$etag`az `$version`írásvédett elemeket, mint például a , és `$metadata/$lastUpdated`a .

Az ikerméret kiszámítása a következőképpen történik:

* A JSON-dokumentum minden tulajdonsága esetében az IoT Hub összesítve számítja ki és adja hozzá a tulajdonság kulcsának és értékének hosszát.

* A tulajdonságkulcsok UTF8 kódolású karakterláncoknak minősülnek.

* Az egyszerű tulajdonságértékek UTF8 kódolású karakterláncoknak, numerikus értékeknek (8 bájt) vagy logikai értékeknek (4 bájt) számítanak.

* Az UTF8 kódolású karakterláncok méretét az összes karakter megszámlálásával számítja ki, kivéve a UNICODE vezérlőkaraktereket (C0 és C1 szegmens).

* Az összetett tulajdonságértékek (beágyazott objektumok) számítása a tulajdonságkulcsok és a tulajdonságértékek összesített mérete alapján van kiszámítva.

Az IoT Hub hibát elválaszt minden olyan művelettel, `properties/desired`amely `properties/reported` növelné a `tags`, vagy a korlát feletti dokumentumok méretét.

## <a name="device-twin-metadata"></a>Az ikereszköz metaadatai

Az IoT Hub az utolsó frissítés időbélyegét tartja karban minden Egyes JSON-objektumhoz az ikereszköz által kívánt és jelentett tulajdonságokban. Az időbélyegek UTC formátumban vannak, és [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) formátumban `YYYY-MM-DDTHH:MM:SS.mmmZ`vannak kódolva.

Például:

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

Az ikereszköz kívánt és jelentett tulajdonságai nem `$version` rendelkeznek ETag-ek, de olyan értékkel rendelkeznek, amely garantáltan növekményes. Az ETag-hez hasonlóan a verziót a frissítő fél is használhatja a frissítések konzisztenciájának érvényesítésére. Például egy eszköz alkalmazás egy jelentett tulajdonság, vagy a megoldás háttéra a kívánt tulajdonsághoz.

A verziók akkor is hasznosak, ha egy megfigyelő ügynöknek (például a kívánt tulajdonságokat megfigyelő eszközalkalmazásnak) össze kell egyeztetnie a lekérési művelet és a frissítési értesítés közötti versenyeket. Az [Eszköz-újrakapcsolódási folyamat szakasz](iot-hub-devguide-device-twins.md#device-reconnection-flow) további információkat tartalmaz.

## <a name="device-reconnection-flow"></a>Eszköz-újrakapcsolódási folyamat

Az IoT Hub nem őrzi meg a leválasztott eszközök kívánt tulajdonságfrissítési értesítéseit. Ebből következik, hogy a csatlakozó eszköznek le kell kérnie a teljes kívánt tulajdonságdokumentumot, a frissítési értesítésekre való feliratkozáson kívül. Tekintettel arra, hogy a frissítési értesítések és a teljes visszakeresés között versenyek vannak, a következő áramlást kell biztosítani:

1. Az eszközalkalmazás egy IoT-központhoz csatlakozik.
2. Az eszközalkalmazás előfizet a kívánt tulajdonságok frissítési értesítéseire.
3. Az eszközalkalmazás lekéri a kívánt tulajdonságok teljes dokumentumát.

Az eszközalkalmazás figyelmen `$version` kívül hagyhatja az összes olyan értesítést, amely nek kevesebb vagy egyenlő a teljes beolvasott dokumentum verziója. Ez a megközelítés azért lehetséges, mert az IoT Hub garantálja, hogy a verziók mindig növekszik.

> [!NOTE]
> Ez a logika már megvalósítva van az [Azure IoT-eszköz SDK-kban.](iot-hub-devguide-sdks.md) Ez a leírás csak akkor hasznos, ha az eszközalkalmazás nem tudja használni az Azure IoT-eszköz SDK-k egyikét sem, és közvetlenül kell programoznia az MQTT-felületet.
> 

## <a name="additional-reference-material"></a>További referenciaanyagok

Az IoT Hub fejlesztői útmutatójának további referenciatémakörei a következők:

* Az [IoT Hub végpontok](iot-hub-devguide-endpoints.md) cikk ismerteti a különböző végpontok, amelyek az egyes IoT-központok elérhetővé teszi a futásidejű és felügyeleti műveletek.

* A [sávszélesség-szabályozási és kvóták](iot-hub-devguide-quotas-throttling.md) cikk ismerteti az IoT Hub szolgáltatásra vonatkozó kvótákat és a szolgáltatás használatakor várható szabályozási viselkedést.

* Az [Azure IoT-eszköz- és szolgáltatásSDK-k](iot-hub-devguide-sdks.md) cikk felsorolja a különböző nyelvi SDK-k, amelyek et használhat, ha az IoT Hub-szolgáltatással interakcióba lépő eszköz- és szolgáltatásalkalmazásokat is fejleszt.

* Az [IoT Hub lekérdezési nyelv e twins, feladatok és üzenet-útválasztási](iot-hub-devguide-query-language.md) cikk ismerteti az IoT Hub lekérdezési nyelv segítségével lekérheti az adatokat az IoT Hub az eszköz twins és feladatok.

* Az [IoT Hub MQTT támogatási](iot-hub-mqtt-support.md) cikke további információt nyújt az MQTT protokoll IoTHub-támogatásáról.

## <a name="next-steps"></a>További lépések

Most, hogy értesült az ikereszközökről, az Alábbi IoT Hub-fejlesztői útmutatótémakörök érdekelhetik:

* [A modulikai ikrek megértése és használata az IoT Hubban](iot-hub-devguide-module-twins.md)
* [Közvetlen metódus meghívása eszközön](iot-hub-devguide-direct-methods.md)
* [Feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md)

A cikkben ismertetett fogalmak némelyikének kipróbálásához tekintse meg az Alábbi IoT Hub-oktatóanyagokat:

* [A készülék használata iker](iot-hub-node-node-twin-getstarted.md)
* [Az ikereszköz tulajdonságainak használata](tutorial-device-twins.md)
* [Eszközfelügyelet VS Code-hoz készült Azure IoT-eszközökkel](iot-hub-device-management-iot-toolkit.md)

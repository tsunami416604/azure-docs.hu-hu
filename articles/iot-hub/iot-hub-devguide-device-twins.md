---
title: Az Azure IoT Hub-eszközök ikrek ismertetése | Microsoft Docs
description: Fejlesztői útmutató – az eszközök ikrek használatával szinkronizálhatók az állapot-és konfigurációs adatokat IoT Hub és az eszközei között
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 406f6f7a3db5f63fb50242a93f021c481631adaa
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209711"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Az IoT Hub eszközön található ikrek megismerése és használata

Az *ikrek* olyan JSON-dokumentumok, amelyek az eszköz állapotával kapcsolatos információkat tárolnak, beleértve a metaadatokat, a konfigurációkat és a feltételeket. Az Azure IoT Hub a IoT Hubhoz csatlakoztatott összes eszközhöz fenntart egy eszközt. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez a cikk ismerteti:

* A két eszköz szerkezete: *címkék*, *kívánt* és *jelentett tulajdonságok*.
* Az eszközön futó alkalmazások és a háttérbeli műveletek az eszközökön is elvégezhetők.

Az eszközökhöz tartozó ikrek használata:

* Az eszközre jellemző metaadatok tárolása a felhőben. Például egy árusító gép üzembe helyezési helye.

* A jelenlegi állapotadatok, például a rendelkezésre álló képességek és kikötések jelentése az eszköz alkalmazásában. Egy eszköz például a IoT hubhoz csatlakozik a mobil-vagy WiFi-kapcsolaton keresztül.

* Szinkronizálja a hosszan futó munkafolyamatok állapotát az eszköz alkalmazás és a háttérbeli alkalmazás között. Ha például a megoldás háttérbe állítása meghatározza a telepítendő új belső vezérlőprogram-verziót, és az eszköz alkalmazás a frissítési folyamat különböző szakaszait jelenti.

* Az eszköz metaadatait, konfigurációját vagy állapotát kérdezheti le.

Tekintse meg az [eszközről a felhőbe irányuló kommunikációs útmutatót](iot-hub-devguide-d2c-guidance.md) a jelentett tulajdonságok, az eszközről a felhőbe irányuló üzenetek vagy a fájlfeltöltés használatával kapcsolatos útmutatásért.

Tekintse át a [felhőből az eszközre irányuló kommunikációs útmutatót](iot-hub-devguide-c2d-guidance.md) , amely útmutatást nyújt a kívánt tulajdonságok, közvetlen metódusok vagy a felhőből az eszközre irányuló üzenetek használatához.

## <a name="device-twins"></a>Eszköz ikrek

A Device Twins az eszközhöz kapcsolódó információkat tárolja:

* Az eszköz és a háttér végpontja az eszközre vonatkozó feltételek és konfiguráció szinkronizálására használható.

* A megoldás háttérbeli futtatása a hosszú ideig futó műveletek lekérdezésére és megcélzására használható.

Az eszközök különálló életciklusa a megfelelő [eszköz identitásához](iot-hub-devguide-identity-registry.md)van csatolva. Az eszköz-ikrek implicit módon jönnek létre, és törlődnek az eszköz identitásának létrehozásakor vagy törlésekor IoT Hub.

Az eszköz Twin egy JSON-dokumentum, amely a következőket tartalmazza:

* **Címkék**. A megoldás hátterében lévő JSON-dokumentum egy szakasza, amelyből beolvasható és írható. A címkék nem láthatók az eszköz alkalmazásaiban.

* **Kívánt tulajdonságok**. A jelentett tulajdonságokkal együtt használható az eszköz konfigurációjának vagy feltételeinek szinkronizálásához. A megoldás háttérbe állítása megadhatja a kívánt tulajdonságokat, és az eszköz alkalmazás is elolvashatja őket. Az alkalmazás a kívánt tulajdonságok változásairól is fogadhat értesítéseket.

* **Jelentett tulajdonságok**. A kívánt tulajdonságokkal együtt használható az eszköz konfigurációjának vagy feltételeinek szinkronizálásához. Az alkalmazás beállíthatja a jelentett tulajdonságokat, és a megoldás hátterében olvashat és lekérdezheti azokat.

* **Eszköz identitásának tulajdonságai** Az eszköz kettős JSON-dokumentumának gyökere a [személyazonossági beállításjegyzékben](iot-hub-devguide-identity-registry.md)tárolt megfelelő eszköz identitásának írásvédett tulajdonságait tartalmazza.

![Képernyőfelvétel az eszközök Twin tulajdonságairól](./media/iot-hub-devguide-device-twins/twin.png)

Az alábbi példa egy eszköz kettős JSON-dokumentumot mutat be:

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

A root objektumban az eszköz identitásának tulajdonságai, valamint a `tags` és a `reported` és `desired` tulajdonságok tároló objektumai. A `properties` tároló tartalmaz néhány írásvédett elemet (`$metadata`, `$etag`és `$version`) az [eszköz kettős metaadatai](iot-hub-devguide-device-twins.md#device-twin-metadata) és az [optimista Egyidejűség](iot-hub-devguide-device-twins.md#optimistic-concurrency) szakaszokban.

### <a name="reported-property-example"></a>Jelentett tulajdonság – példa

Az előző példában az eszköz Twin `batteryLevel` tulajdonságot tartalmaz, amelyet az eszköz alkalmazás jelentett. Ez a tulajdonság lehetővé teszi az eszközök lekérdezését és működését az utolsó jelentett akkumulátor szintjének megfelelően. Egyéb példák például az eszköz alkalmazás-jelentési eszköz képességei vagy kapcsolódási lehetőségei.

> [!NOTE]
> A jelentett tulajdonságok leegyszerűsítik az olyan forgatókönyveket, amelyekben a megoldás hátterét egy tulajdonság utolsó ismert értéke érdekli. Az [eszközről a felhőbe](iot-hub-devguide-messages-d2c.md) irányuló üzenetek használata, ha a megoldás hátterének időbélyeg-események (például idősorozat) formájában kell feldolgoznia az eszköz telemetria.

### <a name="desired-property-example"></a>Példa a kívánt tulajdonságra

Az előző példában a megoldás háttérbe állítása és a jelentett tulajdonságok a `telemetryConfig` Device Twin kívánt és jelentett tulajdonságokat használják az eszköz telemetria-konfigurációjának szinkronizálásához. Például:

1. A megoldás háttérbe állítása a kívánt tulajdonságot a kívánt konfigurációs értékkel állítja be. Itt látható a dokumentum azon része, amely a kívánt tulajdonságot beállítja:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. Az eszköz alkalmazás azonnal értesítést kap a változásról, ha csatlakozik, vagy az első újracsatlakozáskor. Az alkalmazás ezután jelentést készít a frissített konfigurációról (vagy a `status` tulajdonságot használó hiba feltételéről). Itt látható a jelentett tulajdonságok része:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. A megoldás háttérbe állításával több eszközön is nyomon követheti a konfigurációs művelet eredményeit az eszközök [lekérésével](iot-hub-devguide-query-language.md) .

> [!NOTE]
> Az előző kódrészletek az olvashatóságra optimalizált példák, amelyek az eszköz konfigurációjának és állapotának az egyik módszerét jelentik. A IoT Hub nem határoz meg konkrét sémát az eszköz Twin kívánt és jelentett tulajdonságaihoz az eszközök ikrekben.
> 

Az ikrek használatával a hosszan futó műveleteket, például a belső vezérlőprogram frissítéseit is szinkronizálhatja. Ha további információra van szükség arról, hogyan használhatók a tulajdonságok a hosszú ideig futó műveletek szinkronizálásához és nyomon követéséhez az eszközök között, tekintse meg a [kívánt tulajdonságok használata az eszközök konfigurálásához](tutorial-device-twins.md)

## <a name="back-end-operations"></a>Háttérbeli műveletek

A megoldás háttérrendszer a következő, HTTPS protokollon keresztül elérhető atomi műveletek használatával működik az eszközön:

* **A Twin eszköz beolvasása azonosító alapján**. Ez a művelet visszaadja az eszköz kettős dokumentumát, beleértve a címkéket és a kívánt és jelentett rendszertulajdonságokat.

* **Részben frissítse az eszköz ikerét**. Ez a művelet lehetővé teszi, hogy a megoldás háttérbe kerüljön, hogy részben frissítse a címkéket vagy a kívánt tulajdonságokat egy különálló eszközön. A részleges frissítés JSON-dokumentumként van megadva, amely bármilyen tulajdonságot feltesz vagy frissít. A `null`ra beállított tulajdonságok el lesznek távolítva. Az alábbi példa egy új kívánt tulajdonságot hoz létre `{"newProperty": "newValue"}`értékkel, felülírja a `existingProperty` meglévő értékét a `"otherNewValue"`, és eltávolítja `otherOldProperty`. A meglévő kívánt tulajdonságok vagy címkék nem módosulnak:

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

* A **kívánt tulajdonságok cseréje**. Ez a művelet lehetővé teszi a megoldás háttérbe lépését, hogy teljesen felülírja az összes meglévő kívánt tulajdonságot, és új JSON-dokumentumot cseréljen a `properties/desired`re.

* **Címkék cseréje** Ez a művelet lehetővé teszi, hogy a megoldás háttérrendszer teljesen felülírja az összes meglévő címkét, és új JSON-dokumentumot cseréljen a `tags`.

* **Kettős értesítések fogadása**. Ez a művelet lehetővé teszi a megoldás háttérbeli értesítését, ha a Twin módosítva van. Ehhez a IoT-megoldásnak létre kell hoznia egy útvonalat, és az adatforrást meg kell egyeznie a *twinChangeEvents*értékkel. Alapértelmezés szerint nem léteznek ilyen útvonalak, ezért a rendszer nem küld külön értesítéseket. Ha a változás sebessége túl magas, vagy más okokból, például belső hibák esetén, a IoT Hub csak egy értesítést küldhet, amely az összes módosítást tartalmazza. Ezért, ha az alkalmazásnak az összes közbenső állapot megbízható naplózására és naplózására van szüksége, az eszközről a felhőbe irányuló üzeneteket kell használnia. A kettős értesítési üzenet tartalmazza a tulajdonságokat és a törzset.

  - Tulajdonságok

    | Name (Név) | Érték |
    | --- | --- |
    $content típusa | application/json |
    $iothub-enqueuedtime |  Az értesítés elküldésének ideje |
    $iothub-message-source | twinChangeEvents |
    $content – kódolás | utf-8 |
    deviceId | Az eszköz azonosítója |
    hubName | IoT Hub neve |
    operationTimestamp | A művelet [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) időbélyege |
    iothub-message-schema | deviceLifecycleNotification |
    opType | "replaceTwin" vagy "updateTwin" |

    Az üzenetrendszer tulajdonságai előtaggal vannak ellátva a `$` szimbólummal.

  - Törzs
        
    Ez a szakasz a JSON-formátum összes kettős módosítását tartalmazza. Ugyanazt a formátumot használja, mint a javítás, a különbséggel, hogy az összes különálló szakaszt tartalmazhatja: címkék, tulajdonságok. jelentett, Properties. desired, és hogy tartalmazza a "$metadata" elemeket. Például:

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

Az összes korábbi művelet támogatja az [optimista párhuzamosságot](iot-hub-devguide-device-twins.md#optimistic-concurrency) , és megköveteli a **ServiceConnect** engedélyt a [IoT hub hozzáférés-vezérlésének szabályozása alapján](iot-hub-devguide-security.md).

Ezen műveletek mellett a megoldás hátterében a következőket teheti:

* Az eszköz-ikrek lekérdezése az SQL-szerű [IoT hub lekérdezési nyelv](iot-hub-devguide-query-language.md)használatával.

* Műveleteket hajthat végre nagy mennyiségű eszközön a [feladatok](iot-hub-devguide-jobs.md)használatával.

## <a name="device-operations"></a>Eszköz műveletei

Az eszköz-alkalmazás a következő atomi műveletek használatával működik az eszközön:

* A **Twin eszköz beolvasása**. A művelet visszaadja az eszköz kettős dokumentumát (beleértve a kívánt és jelentett rendszertulajdonságokat is) a jelenleg csatlakoztatott eszközhöz. (A címkék nem láthatók az eszköz alkalmazásai számára.)

* **Jelentett tulajdonságok részleges frissítése**. Ez a művelet lehetővé teszi a jelenleg csatlakoztatott eszköz jelentett tulajdonságainak részleges frissítését. Ez a művelet ugyanazt a JSON-frissítési formátumot használja, amelyet a megoldás hátterében a kívánt tulajdonságok részleges frissítése használ.

* A **kívánt tulajdonságok megfigyelése**. A jelenleg csatlakoztatott eszköz dönthet úgy, hogy értesítést küld a kívánt tulajdonságok frissítéseiről, amikor azok történnek. Az eszköz ugyanazt a frissítést (részleges vagy teljes cserét) kapja, amelyet a megoldás hátterében hajt végre.

Az összes fenti művelethez szükség van a **DeviceConnect** engedélyre, ahogy az a [IoT hub hozzáférés-vezérlési szolgáltatásban](iot-hub-devguide-security.md)van meghatározva.

Az [Azure IoT-eszközök SDK](iot-hub-devguide-sdks.md) -k megkönnyítik az előző műveletek használatát számos nyelvről és platformról. További információ a kívánt tulajdonságok szinkronizálásához IoT Hub primitívek részleteiről: [eszköz újrakapcsolási folyamata](iot-hub-devguide-device-twins.md#device-reconnection-flow).

## <a name="tags-and-properties-format"></a>Címkék és tulajdonságok formátuma

A címkék, a kívánt tulajdonságok és a jelentett tulajdonságok a JSON-objektumok a következő korlátozásokkal:

* A JSON-objektumokban lévő összes kulcs UTF-8 kódolású, kis-és nagybetűket megkülönböztető, és legfeljebb 1 KB hosszúságú lehet. Az engedélyezett karakterek kizárják a UNICODE vezérlőkarakterek (szegmensek C0 és C1), valamint `.`, `$`és SP.

* A JSON-objektumokban lévő összes érték a következő JSON-típusokkal rendelkezhet: logikai, szám, karakterlánc, objektum. Tömbök használata nem engedélyezett. Az egész számok maximális értéke 4503599627370495, az egész számok minimális értéke pedig-4503599627370496.

* A címkék, a kívánt és a jelentett tulajdonságok összes JSON-objektuma legfeljebb 10 mélységet tartalmazhat. Például a következő objektum érvényes:

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

* Az összes karakterlánc-érték legfeljebb 4 KB hosszúságú lehet.

## <a name="device-twin-size"></a>Eszköz kettős mérete

IoT Hub kényszeríti a 8 kb korlátozását a `tags`, `properties/desired`és `properties/reported`megfelelő teljes értékeire, kivéve a csak olvasható elemeket.

A méret kiszámítása az összes karakter számlálásával történik, kivéve a UNICODE vezérlő karaktereket (szegmensek C0 és C1), valamint a karakterlánc-konstansokon kívüli szóközöket.

IoT Hub elutasítja az összes olyan műveletet, amely a határértéknél nagyobb mértékben növelné a dokumentumok méretét.

## <a name="device-twin-metadata"></a>Eszköz – Twin metaadatok

IoT Hub karbantartja az összes JSON-objektum utolsó frissítésének időbélyegét az eszközök Twin-beli kívánt és jelentett tulajdonságaiban. Az időbélyegek UTC szerint vannak kódolva, és a [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) formátuma `YYYY-MM-DDTHH:MM:SS.mmmZ`.

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

Ezek az információk minden szinten megmaradnak (nem csak a JSON-struktúra levelei) az objektumok kulcsait eltávolító frissítések megőrzése érdekében.

## <a name="optimistic-concurrency"></a>Optimista párhuzamosság

Címkék, kívánt és jelentett tulajdonságok az optimista párhuzamosságok támogatásával.
A címkékhez ETag ( [RFC7232](https://tools.ietf.org/html/rfc7232)) tartozik, amely a címke JSON-ábrázolását jelöli. A konzisztencia biztosításához használhatja a megoldás Etagek a feltételes frissítési műveletekben.

A kívánt eszközök és a jelentett tulajdonságok nem rendelkeznek Etagek, de egy `$version` érték, amely garantáltan növekményes. A ETag hasonlóan a frissítési fél is használhatja a verziót a frissítések konzisztenciájának betartatására. Tegyük fel például, hogy egy alkalmazás egy jelentett tulajdonsághoz vagy egy kívánt tulajdonsághoz a megoldás hátteréhez tartozó eszköz.

A verziók akkor is hasznosak, ha egy megfigyelő ügynöknek (például a kívánt tulajdonságokat megfigyelő eszköz alkalmazásnak) össze kell egyeztetni a versenyeket egy lekérési művelet és egy frissítési értesítés eredménye között. Az [eszköz újrakapcsolási folyamat szakasza](iot-hub-devguide-device-twins.md#device-reconnection-flow) további információkat tartalmaz.

## <a name="device-reconnection-flow"></a>Eszköz újrakapcsolási folyamata

IoT Hub nem őrzi meg a leválasztott eszközökhöz tartozó, a kívánt tulajdonságok frissítési értesítéseit. Ez azt eredményezi, hogy egy csatlakozó eszköznek le kell kérnie a teljes kívánt tulajdonságokat tartalmazó dokumentumot a frissítési értesítések előfizetése mellett. Tekintettel arra, hogy a verseny a frissítési értesítések és a teljes lekérések között is fennáll, a következő folyamatot kell biztosítani:

1. Az eszközbeállítások egy IoT hubhoz csatlakozik.
2. Az eszköz-alkalmazás előfizet a kívánt tulajdonságok frissítési értesítéseire.
3. Az eszköz beolvassa a teljes dokumentumot a kívánt tulajdonságokhoz.

Az alkalmazás figyelmen kívül hagyhatja az összes olyan értesítést, amelynek `$version` kisebb vagy egyenlő, mint a teljes beolvasott dokumentum verziószáma. Ez a megközelítés azért lehetséges, mert IoT Hub garantálja, hogy a verziók mindig növekményt biztosítanak.

> [!NOTE]
> Ez a logika már implementálva van az [Azure IoT Device SDK](iot-hub-devguide-sdks.md)-ban. Ez a leírás csak akkor hasznos, ha az eszköz nem tudja használni az Azure IoT Device SDK-kat, és a MQTT felületét közvetlenül kell programoznia.
> 

## <a name="additional-reference-material"></a>További referenciaanyagok

A IoT Hub Fejlesztői útmutatóban található további témakörök a következők:

* A [IoT hub endpoints](iot-hub-devguide-endpoints.md) cikk a különböző végpontokat ismerteti, amelyeket az egyes IoT hub a futásidejű és a felügyeleti műveletek számára tesz elérhetővé.

* A [szabályozás és kvóták](iot-hub-devguide-quotas-throttling.md) cikk a IoT hub szolgáltatásra vonatkozó kvótákat és a szolgáltatás használatakor várható szabályozási viselkedést ismerteti.

* Az [Azure IoT eszköz és szolgáltatás SDK-](iot-hub-devguide-sdks.md) k című cikk felsorolja azokat a különböző nyelvi SDK-kat, amelyek a IoT hub használatával kommunikáló eszköz-és szolgáltatás-alkalmazások fejlesztéséhez használhatók.

* Az [ikrek, feladatok és üzenet-útválasztási cikk IoT hub lekérdezési nyelve](iot-hub-devguide-query-language.md) a IoT hub lekérdezési nyelvet írja le, amellyel lekérheti az adatokat az eszközökről az ikrekről és a feladatokról IoT hub.

* A [IOT hub MQTT-támogatási](iot-hub-mqtt-support.md) cikk további információkat nyújt a MQTT protokoll IoT hub támogatásáról.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az eszközökről szóló ikreket, az alábbi IoT Hub fejlesztői útmutató témaköreiben találhat további információkat:

* [Az ikrek megismerése és használata IoT Hub](iot-hub-devguide-module-twins.md)
* [Közvetlen metódus meghívása egy eszközön](iot-hub-devguide-direct-methods.md)
* [Feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md)

A cikkben ismertetett fogalmak némelyikének kipróbálásához tekintse meg a következő IoT Hub oktatóanyagokat:

* [Az eszköz Twin használata](iot-hub-node-node-twin-getstarted.md)
* [Az eszköz Twin tulajdonságainak használata](tutorial-device-twins.md)
* [Eszközkezelés a VS Code-hoz készült Azure IoT-eszközökkel](iot-hub-device-management-iot-toolkit.md)

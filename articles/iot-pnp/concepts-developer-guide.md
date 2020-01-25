---
title: Fejlesztői útmutató – IoT Plug and Play előzetes verzió | Microsoft Docs
description: A IoT Plug and Play fejlesztők számára készült eszközök modellezésének leírása
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 61fe4af2d40d7252aa2b0dffb373a81162b9f67a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722509"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>IoT Plug and Play előzetes verzió modellezése – fejlesztői útmutató

A IoT Plug and Play Preview lehetővé teszi, hogy olyan eszközöket hozzon létre, amelyek az Azure IoT alkalmazásaiban hirdetik ki képességeiket. A IoT Plug and Play-eszközökhöz nincs szükség manuális konfigurálásra, ha az ügyfél a Plug and Play-kompatibilis alkalmazások IoT csatlakoztatja őket. IoT Central példa egy IoT Plug and Play-kompatibilis alkalmazásra.

IoT Plug and Play eszköz létrehozásához létre kell hoznia egy eszköz leírását. A leírás a digitális Twins Definition Language (DTDL) nevű egyszerű definíciós nyelvvel történik.

## <a name="device-capability-model"></a>Eszköz képességeinek modellje

A DTDL-mel létrehoz egy _eszköz-képesség modellt_ az eszköz részeinek leírásához. Egy tipikus IoT-eszköz a következőket alkotja:

- Egyéni részek, amelyek az eszköz egyedivé tételét teszik elérhetővé.
- Szabványos részek, amelyek az összes eszközre jellemző dolgok.

Ezeket a részeket _illesztőfelületeknek_ nevezzük az eszköz képességeinek modelljében. A felületek határozzák meg az eszköz által megvalósított egyes részek részleteit.

A következő példa egy termosztátos eszköz eszköz-képesség modelljét mutatja be:

```json
{
  "@id": "urn:example:Thermostat_T_1000:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "name": "thermostat",
      "schema": "urn:example:Thermostat:1"
    },
    {
      "name": "urn_azureiot_deviceManagement_DeviceInformation",
      "schema": "urn:azureiot:deviceManagement:DeviceInformation:1"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

A képesség-modellnek van néhány kötelező mezője:

- `@id`: egyedi azonosító egyszerű egységes erőforrás-név formájában.
- `@type`: kijelenti, hogy ez az objektum egy képesség modell.
- `@context`: a képesség modellhez használt DTDL-verziót adja meg.
- `implements`: az eszköz által megvalósított felületek felsorolása.

A megvalósítások szakaszban található felületek listájának minden bejegyzése a következőket tartalmazza:

- `name`: az interfész programozási neve.
- `schema`: a képesség modell által megvalósított felület.

További választható mezőket is használhat, amelyekkel további részleteket adhat hozzá a képesség modelljéhez, például a megjelenítendő név és a Leírás mezőben. A képességi modellben deklarált felületek az eszköz összetevőiként is megtekinthetők. A nyilvános előzetes verzióban az illesztőfelületek listájának sémája csak egy bejegyzéssel rendelkezhet.

## <a name="interface"></a>Felület

A DTDL segítségével az eszköz képességeit a felületek használatával írhatja le. A felületek leírják, hogy a _Tulajdonságok_, a _telemetria_és a _parancsok_ hogyan implementálják az eszköz részét:

- `Properties` kérdésre adott válaszban foglalt lépéseket. A tulajdonságok olyan adatmezők, amelyek az eszköz állapotát jelölik. A tulajdonságok használatával az eszköz tartós állapotát, például egy Hűtőfolyadék-szivattyú on-off állapotát jelölheti ki. A tulajdonságok az alapszintű eszköz tulajdonságait is jelezhetik, például az eszköz belső vezérlőprogram-verzióját. A tulajdonságokat csak olvasható vagy írható módon deklarálhatja.
- `Telemetry` kérdésre adott válaszban foglalt lépéseket. A telemetria mezők az érzékelők méréseit jelölik. Minden alkalommal, amikor az eszköz érzékelőt használ, egy telemetria eseményt kell küldenie, amely az érzékelő adatait tartalmazza.
- `Commands` kérdésre adott válaszban foglalt lépéseket. A parancsok olyan metódusokat jelölnek, amelyeket az eszköz felhasználói futtathatnak az eszközön. Például egy alaphelyzetbe állítási parancs vagy egy, a ventilátor be-és kikapcsolására szolgáló parancs.

A következő példa egy termosztátos eszköz felületét szemlélteti:

```json
{
  "@id": "urn:example:Thermostat:1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Telemetry",
      "name": "temperature",
      "schema": "double"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Az illesztőfelületnek van néhány kötelező mezője:

- `@id`: egyedi azonosító egyszerű egységes erőforrás-név formájában.
- `@type`: kijelenti, hogy ez az objektum illesztőfelület.
- `@context`: a csatolóhoz használt DTDL-verziót adja meg.
- `contents`: az eszközt alkotó tulajdonságok, telemetria és parancsok felsorolása.

Ebben az egyszerű példában csak egyetlen telemetria mező van. A mező minimális leírása a következőket tartalmazhatja:

- `@type`: a képesség típusát adja meg: `Telemetry`, `Property`vagy `Command`.
- `name`: megadja a telemetria értékének nevét.
- `schema`: a telemetria adattípusát adja meg. Ez az érték egyszerű típus lehet, például dupla, Integer, Boolean vagy string. Az összetett objektumtípusok, tömbök és leképezések is támogatottak.

További választható mezők, például a megjelenítendő név és a leírás, lehetővé teszik további részletek hozzáadását az interfészhez és a képességekhez.

### <a name="properties"></a>Tulajdonságok

Alapértelmezés szerint a tulajdonságok csak olvashatók. A csak olvasható tulajdonságok azt jelentik, hogy az eszköz a IoT-hubhoz frissíti a tulajdonság értékét. Az IoT hub nem tudja beállítani a csak olvasható tulajdonság értékét.

Egy tulajdonságot egy felületen írhatóként is megadhat. Az eszköz a IoT hub-ból származó írható tulajdonságra, valamint a központhoz tartozó tulajdonságértékek frissítéseire vonatkozó frissítést is képes fogadni.

Az eszközöknek nem kell csatlakozniuk a tulajdonságértékek beállításához. A frissített értékek akkor lesznek átadva, amikor az eszköz a következőhöz csatlakozik a hubhoz. Ez a viselkedés a csak olvasható és írható tulajdonságokra is vonatkozik.

Ne használjon tulajdonságokat a telemetria az eszközről való küldéséhez. Például egy írásvédett tulajdonság, például `temperatureSetting=80` azt jelenti, hogy az eszköz hőmérséklete 80 értékre van állítva, és az eszköz megpróbál bejutni vagy maradni, ez a hőmérséklet.

Az írható tulajdonságok esetében az eszköz a kívánt állapot kódját, verzióját és leírását adja vissza annak jelzésére, hogy a kapott és alkalmazta-e a tulajdonság értékét.

### <a name="telemetry"></a>Telemetria

Alapértelmezés szerint a IoT Hub az eszközökről érkező összes telemetria-üzenetet a [beépített szolgáltatáshoz kapcsolódó végpontra (**üzenetek/események**)](../iot-hub/iot-hub-devguide-messages-read-builtin.md) irányítja, amely kompatibilis a [Event Hubsval](https://azure.microsoft.com/documentation/services/event-hubs/).

A [IoT hub egyéni végpontok és útválasztási szabályok](../iot-hub/iot-hub-devguide-messages-d2c.md) segítségével telemetria küldhet más célhelyekre, például a blob Storage-ba vagy más esemény-hubokba. Az útválasztási szabályok az üzenet tulajdonságait használják az üzenetek kiválasztásához.

### <a name="commands"></a>Parancsok

A parancsok szinkron vagy aszinkron jellegűek. A szinkron parancsoknak alapértelmezés szerint 30 másodpercen belül kell futniuk, és az eszköznek csatlakoztatva kell lennie a parancs megérkezése után. Ha az eszköz időben válaszol, vagy az eszköz nincs csatlakoztatva, akkor a parancs sikertelen lesz.

Használjon aszinkron parancsokat a hosszan futó műveletekhez. Az eszköz telemetria-üzenetek használatával küldi el a végrehajtási adatokat. Ezek az állapotjelző üzenetek a következő fejléc-tulajdonságokkal rendelkeznek:

- `iothub-command-name`: a parancs neve, például `UpdateFirmware`.
- `iothub-command-request-id`: a kiszolgáló oldalán generált kérelem-azonosító, amelyet a kezdeti hívásban az eszköznek küldtek.
- `iothub-interface-id`: a parancs által definiált interfész azonosítója (például `urn:example:AssetTracker:1`).
 `iothub-interface-name`: az interfész példányának neve, például `myAssetTracker`.
- `iothub-command-statuscode`: az eszközről visszaadott állapotkód, például `202`.

## <a name="register-a-device"></a>Eszköz regisztrálása

A IoT Plug and Play megkönnyíti az eszköz képességeinek reklámozását. Ha az eszköz IoT Hubhoz való csatlakozása után a IoT Plug and Play, regisztrálnia kell az eszköz képességeinek modelljét. A regisztráció lehetővé teszi az ügyfelek számára az eszköz IoT Plug and Play funkciójának használatát.

Ez az útmutató bemutatja, hogyan regisztrálhat egy eszközt a C-hez készült Azure IoT Device SDK-val.

Az eszköz által megvalósított minden egyes csatolóhoz létre kell hoznia egy felületet, és hozzá kell kötnie a megvalósításához.

A korábban, a C SDK használatával bemutatott termosztát-interfészhez a termosztát-felületet a megvalósításához kell létrehoznia és összekapcsolásához:

```c
DIGITALTWIN_INTERFACE_HANDLE thermostatInterfaceHandle;

DIGITALTWIN_CLIENT_RESULT result = DigitalTwin_InterfaceClient_Create(
    "thermostat",
    "urn:example:Thermostat:1",
    null, null,
    &thermostatInterfaceHandle);

result = DigitalTwin_Interface_SetCommandsCallbacks(
    thermostatInterfaceHandle,
    commandsCallbackTable);

result = DigitalTwin_Interface_SetPropertiesUpdatedCallbacks(
    thermostatInterfaceHandle,
    propertiesCallbackTable);

```

Ismételje meg ezt a kódot minden, az eszköz által megvalósított csatolón.

Miután létrehozta a felületet, regisztrálja az eszköz képességeinek modelljét és felületeit az IoT hub-ban:

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaceHandleList, 2,
    null, null);
```

## <a name="use-a-device"></a>Eszköz használata

A IoT Plug and Play lehetővé teszi olyan eszközök használatát, amelyek regisztrálták képességeiket az IoT hub-ban. Az eszköz tulajdonságait és parancsait például közvetlenül elérheti.

Ha az IoT hubhoz csatlakoztatott IoT Plug and Play eszközt kíván használni, használja a IoT Hub REST API vagy az egyik IoT nyelvi SDK-t. Az alábbi példák a IoT Hub REST API használják.

Az eszköz tulajdonságainak (például a belső vezérlőprogram verziója (`fwVersion`) a termosztátban található `DeviceInformation` felületen való beszerzéséhez használja a digitális ikrek REST API.

Ha a termosztátos eszközt `t-123`nevezik, az eszköz által megvalósított összes tulajdonságot egy REST API GET hívással érheti el:

```REST
GET /digitalTwins/t-123/interfaces
```

Általánosságban az összes tulajdonság a REST API sablonnal érhető el, ahol `{device-id}` az eszköz azonosítója:

```REST
GET /digitalTwins/{device-id}/interfaces
```

Ha ismeri a csatoló nevét, és az adott csatoló tulajdonságait szeretné lekérni, a hatókört a megadott csatolóra szűkítheti név szerint:

```REST
GET /digitalTwins/t-123/interfaces/info
```

Általánosságban az adott interfész tulajdonságai a REST API sablonon keresztül érhetők el, ahol `device-id` az eszköz azonosítója, és `{interface-name}` a csatoló neve:

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

Közvetlenül is meghívhatja a IoT Plug and Play eszköz parancsait. Ha a `t-123` eszköz `Thermostat` felületének `restart` parancsa van, meghívhatja egy REST API POST hívással:

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

Általánosabban a parancsok a REST API sablonnal is meghívhatók:

- `device-id`: az eszköz azonosítója.
- `interface-name`: az eszköz képességei modellének megvalósítások szakaszában található felület neve.
- `command-name`: a parancs neve.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az eszközök modellezését, íme néhány további erőforrás:

- [Digital Twin Definition Language (DTDL)](https://aka.ms/DTDL)
- [C eszköz-SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)

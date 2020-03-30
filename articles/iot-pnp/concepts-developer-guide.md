---
title: Fejlesztői útmutató - IoT Plug and Play preview | Microsoft dokumentumok
description: Az IoT Plug and Play fejlesztői eszközmodellezésének ismertetése
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5fda51e6d2f62b9cbef0fcac22d5bb2ea0df905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605222"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>Az IoT Plug and Play előzetes modellezési útmutatója

Az IoT Plug and Play preview lehetővé teszi olyan eszközök készítését, amelyek az Azure IoT-alkalmazásokban hirdetik képességeiket. Az IoT Plug and Play eszközök nem igényelnek manuális konfigurációt, amikor az ügyfél összeköti őket az IoT Plug és Play-kompatibilis alkalmazásokkal. Az IoT Central egy példa egy IoT Plug and Play-kompatibilis alkalmazásra.

IoT Plug and Play eszköz létrehozásához létre kell hoznia egy eszköz leírását. A leírás történik egy egyszerű definíciós nyelv nevű Digitális Twins Definition Language (DTDL).

## <a name="device-capability-model"></a>Eszközképességi modell

A DTDL segítségével _eszközképesség-modellt_ hozhat létre az eszköz részeinek leírására. Egy tipikus IoT-eszköz a következőkből áll:

- Egyéni alkatrészek, amelyek a készülék egyedivé teszik.
- Standard alkatrészek, amelyek olyan dolgok, amelyek közösek minden eszközön.

Ezeket a részeket egy eszközképességi modell _interfészének nevezzük._ Az interfészek határozzák meg az eszköz által megvalósított egyes részek részleteit.

A következő példa egy termosztáteszköz eszközképességi modelljét mutatja be:

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

A képességmodell nek van néhány szükséges mezője:

- `@id`: egyedi azonosító egyszerű egységes erőforrásnév formájában.
- `@type`: deklarálja, hogy az objektum képességmodell.
- `@context`: a képességmodellhez használt DTDL verziót adja meg.
- `implements`: felsorolja az eszköz által megvalósított felületeket.

A munkaeszközök szakaszban az interfészek listájának minden egyes bejegyzése rendelkezik a következőkkal:

- `name`: a kapcsolat programozási neve.
- `schema`: a képességmodell által megvalósított felület.

További választható mezőket is használhat, amelyekkel további részleteket adhat hozzá a képességmodellhez, például a megjelenítendő nevet és a leírást. A képességmodellen belül deklarált felületek az eszköz összetevőinek tekinthetők. A nyilvános előzetes verzióban a kapcsolati felületek listája sémánként csak egy bejegyzéssel rendelkezhet.

## <a name="interface"></a>Felület

A DTDL segítségével az eszköz képességeit felületek segítségével írhatja le. Az összeköttetések leírják a _tulajdonságokat_, _telemetriai adatokat_, és _az_ eszköz egy részét implementálja:

- `Properties`. A tulajdonságok olyan adatmezők, amelyek az eszköz állapotát jelölik. A tulajdonságok segítségével az eszköz tartós állapotát jelölheti, például a hűtőfolyadék-szivattyú ki-be kapcsolott állapotát. A tulajdonságok az eszköz alapvető tulajdonságait is képviselhetik, például az eszköz belső vezérlőprogram-verzióját. A tulajdonságokat írásvédettként vagy írhatóként deklarálhatja.
- `Telemetry`. A telemetriai mezők az érzékelők ből származó méréseket jelölik. Amikor az eszköz érzékelőmérést végez, telemetriai eseményt kell küldenie, amely tartalmazza az érzékelő adatait.
- `Commands`. A parancsok olyan metódusokat jelölnek, amelyeket az eszköz felhasználói végrehajthatnak az eszközön. Például egy alaphelyzetbe állítási parancs vagy egy ventilátor be- és kikapcsolásához.

A következő példa egy termosztáteszköz felületét mutatja be:

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

A kapcsolatnak van néhány szükséges mezője:

- `@id`: egyedi azonosító egyszerű egységes erőforrásnév formájában.
- `@type`: deklarálja, hogy az objektum egy felület.
- `@context`: megadja a kapcsolathoz használt DTDL verziót.
- `contents`: felsorolja az eszközt használó tulajdonságokat, telemetriai adatokat és parancsokat.

Ebben az egyszerű példában csak egyetlen telemetriai mező van. A minimális mezőleírásnak a következők vannak:

- `@type`: meghatározza a képesség `Telemetry`típusát: `Property` `Command`, vagy .
- `name`: megadja a telemetriai érték nevét.
- `schema`: megadja a telemetriai adatok adattípusát. Ez az érték lehet primitív típus, például dupla, egész, logikai vagy karakterlánc. Összetett objektumtípusok, tömbök és leképezések is támogatottak.

Más választható mezők, például a megjelenítendő név és a leírás lehetővé teszik további részletek hozzáadását a felülethez és a képességekhez.

### <a name="properties"></a>Tulajdonságok

Alapértelmezés szerint a tulajdonságok írásvédettek. Az írásvédett tulajdonságok azt jelentik, hogy az eszköz jelenti a tulajdonságérték-frissítéseket az IoT hubhoz. Az IoT hub nem állíthatja be egy csak olvasható tulajdonság értékét.

Egy tulajdonságot írhatóként is megjelölhet egy felületen. Egy eszköz kaphat egy írható tulajdonság frissítését az IoT hubról, valamint a tulajdonságérték-frissítések jelentése a hub.

Az eszközöket nem kell csatlakoztatni a tulajdonságértékek beállításához. A frissített értékek átvitele akkor történik meg, amikor a következő eszköz csatlakozik a hubhoz. Ez a viselkedés írásvédett és írható tulajdonságokra egyaránt vonatkozik.

Ne használjon tulajdonságokat telemetriai adatok küldésére az eszközről. Például egy csak olvasható `temperatureSetting=80` tulajdonság, például azt jelenti, hogy az eszköz hőmérséklete 80-ra van állítva, és az eszköz megpróbálja elérni vagy ott maradni ezt a hőmérsékletet.

Írható tulajdonságok esetén az eszközalkalmazás a kívánt állapotállapotkódot, verziót és leírást adja vissza annak jelzésére, hogy megkapta és alkalmazta-e a tulajdonság értékét.

### <a name="telemetry"></a>Telemetria

Alapértelmezés szerint az IoT Hub az eszközökről érkező összes telemetriai üzenetet az Event Hubs rendszerrel [kompatibilis, beépített szolgáltatásnéző végpontra **(üzenetekre/eseményekre)**](../iot-hub/iot-hub-devguide-messages-read-builtin.md) [irányítja.](https://azure.microsoft.com/documentation/services/event-hubs/)

Az [IoT Hub egyéni végpontjai és útválasztási szabályai](../iot-hub/iot-hub-devguide-messages-d2c.md) segítségével telemetriai adatokat küldhet más célhelyekre, például a blobtárolóba vagy más eseményközpontokba. Az útválasztási szabályok az üzenetek kijelöléséhez az üzeneteket használják az üzenetek kijelöléséhez.

### <a name="commands"></a>Parancsok

A parancsok szinkronok vagy aszinkronak. A szinkron parancsoknak alapértelmezés szerint 30 másodpercen belül kell végrehajtaniuk, és az eszközt csatlakoztatni kell, amikor a parancs megérkezik. Ha az eszköz időben válaszol, vagy az eszköz nincs csatlakoztatva, akkor a parancs sikertelen lesz.

Hosszú ideig futó műveletekhez használjon aszinkron parancsokat. Az eszköz telemetriai üzenetek használatával küldi a folyamatadatokat. Ezek a végrehajtási üzenetek a következő fejléctulajdonságokkal rendelkeznek:

- `iothub-command-name`: a parancs neve, például `UpdateFirmware`.
- `iothub-command-request-id`: a kiszolgálóoldalon generált és az eszközre az első hívás során küldött kérelemazonosító.
- `iothub-interface-id`: Annak a kapcsolatnak az azonosítója, amelyen a parancs definiálva van, például `urn:example:AssetTracker:1`.
 `iothub-interface-name`: a kapcsolat példányneve, `myAssetTracker`például .
- `iothub-command-statuscode`: az eszközről visszaadott állapotkód, például `202`.

## <a name="register-a-device"></a>Eszköz regisztrálása

Az IoT Plug and Play megkönnyíti az eszköz képességeinek hirdetését. Az IoT Plug and Play segítségével, miután az eszköz csatlakozik az IoT Hubhoz, regisztrálnia kell az eszköz képességmodelljét. A regisztráció lehetővé teszi az ügyfelek számára, hogy az eszköz IoT Plug and Play funkcióit használják.

Ez az útmutató bemutatja, hogyan regisztrálhat egy eszközt az Azure IoT Device SDK C használatával.

Az eszköz által megvalósított minden egyes felülethez létre kell hoznia egy felületet, és csatlakoztatnia kell azt a megvalósításhoz.

A korábban bemutatott termosztát felülethez a C SDK használatával hozza létre és csatlakoztatja a termosztát felületet annak megvalósításához:

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

Ismételje meg ezt a kódot minden olyan illesztőfelülethez, amelyet az eszköz végrehajt.

Miután létrehozott egy felületet, regisztrálja az eszköz képességmodelljét és interfészeit az IoT hubbal:

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaces, 2,
    null, null);
```

## <a name="use-a-device"></a>Eszköz használata

Az IoT Plug and Play lehetővé teszi, hogy olyan eszközöket használjon, amelyek regisztrálták a képességeiket az IoT hubon. Például közvetlenül elérheti egy eszköz tulajdonságait és parancsait.

Az IoT Plug and Play-eszköz, amely az IoT hubhoz csatlakozik, használja az IoT Hub REST API-t vagy az IoT nyelvi SDK-k egyikét. Az alábbi példák az IoT Hub REST API-t használják. Az `2019-07-01-preview`API jelenlegi verziója . Fűzz `?api-version=2019-07-01-preview` hozzá a REST PI-hívásokhoz.

Egy eszköztulajdonság, például a belső vezérlőprogram`fwVersion`verziója `DeviceInformation` ( ) értékének lehívásához a termosztát felületén a digitális twins REST API-t használja.

Ha a termosztát eszköz `t-123`neve, akkor az összes tulajdonságot megkapja az eszköz által megvalósított összes felületen egy REST API GET hívással:

```REST
GET /digitalTwins/t-123/interfaces
```

Általánosabban fogalmazva, az összes kapcsolat összes tulajdonsága `{device-id}` ezzel a REST API-sablonnal érhető el, ahol az eszköz azonosítója található:

```REST
GET /digitalTwins/{device-id}/interfaces
```

Ha ismeri a kapcsolat nevét, `deviceInformation`például a , és az adott kapcsolat tulajdonságait szeretné lekérni, a kérelmet név szerint egy adott felületre kell bevinnie:

```REST
GET /digitalTwins/t-123/interfaces/deviceInformation
```

Általánosabban fogalmazva, egy adott kapcsolat tulajdonságai ezen a `device-id` REST API-sablonon `{interface-name}` keresztül érhetők el, ahol az eszköz azonosítója, és a kapcsolat neve:

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

Az IoT Plug and Play eszközparancsokat közvetlenül is meghívhatja. Ha `Thermostat` az eszköz `t-123` ben `restart` lévő kapcsolat rendelkezik egy paranccsal, meghívhatja REST API POST-hívással:

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

Általánosabban, a parancsok ezen a REST API-sablonon keresztül hívhatók meg:

- `device-id`: az eszköz azonosítója.
- `interface-name`: az interfész neve az eszközképességi modell munkaeszköz-szakaszából.
- `command-name`: a parancs neve.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>További lépések

Most, hogy már értesült az eszközmodellezésről, az alábbiakban további forrásokat olvashat:

- [Digitális ikerdefiníciós nyelv (DTDL)](https://aka.ms/DTDL)
- [C eszköz-SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)

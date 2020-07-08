---
title: Mik az Azure IoT Centralban található eszközök sablonjai | Microsoft Docs
description: Az Azure IoT Central-sablonjai lehetővé teszik az alkalmazáshoz csatlakoztatott eszközök viselkedésének megadását.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2d5009086a24a54c9a2ec4734d3c4dcbebb04475
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84418798"
---
# <a name="what-are-device-templates"></a>Mik azok az eszközsablonok?

_Ez a cikk az eszközök fejlesztőire és megoldás-építői vonatkozik._

Az Azure IoT Central egy olyan tervrajz, amely az alkalmazáshoz csatlakozó eszközök jellemzőit és viselkedését határozza meg. Az eszköz sablonja például meghatározza azt a telemetria, amelyet az eszköz küld, így a IoT Central a megfelelő egységeket és adattípusokat használó vizualizációkat hozhat létre.

A megoldás-szerkesztő egy IoT Central alkalmazáshoz adja hozzá az eszközök sablonjait. Az eszközök fejlesztője az eszköz kódját írja le, amely megvalósítja az eszköz sablonjában definiált viselkedéseket.

Az eszköz sablonjai a következő szakaszt tartalmazzák:

- _Egy eszköz-képesség modell (DCM)_. Az eszköz ezen része határozza meg, hogy az eszköz hogyan kommunikál az alkalmazással. Az eszközök fejlesztői implementálják a DCM-ben meghatározott viselkedéseket.
- _Felhő tulajdonságai_ A sablon ezen része lehetővé teszi, hogy a megoldás fejlesztője a tárolni kívánt eszköz-metaadatokat határozza meg. A felhő tulajdonságai soha nem szinkronizálhatók az eszközökkel, és csak az alkalmazásban léteznek. A felhő tulajdonságai nem érintik azt a kódot, amelyet az eszköz fejlesztője a DCM megvalósítására ír.
- _Testreszabások_. A sablon ezen része lehetővé teszi, hogy a megoldás fejlesztője felülbírálja a DCM egyes definícióit. A testreszabások akkor hasznosak, ha a megoldás fejlesztője szeretné megszabni, hogy az alkalmazás hogyan kezelje az értéket, például egy tulajdonság megjelenített nevének vagy a telemetria értékének megjelenítéséhez használt színnek a módosítását. A testreszabások nem érintik azt a kódot, amelyet az eszköz fejlesztője a DCM megvalósítására ír.
- _Nézetek_. Az eszközbeállítások ezen része lehetővé teszi, hogy a megoldás fejlesztője a vizualizációkat definiálja az eszköz adatainak megtekintéséhez, valamint az eszközök felügyeletére és vezérlésére szolgáló űrlapokat. A nézetek a DCM, a Cloud Properties és a testreszabásokat használják. A nézetek nem érintik azt a kódot, amelyet az eszköz fejlesztője a DCM megvalósítására ír.

## <a name="device-capability-models"></a>Eszköz képességeinek modelljei

A DCM azt határozza meg, hogy egy eszköz hogyan kommunikál a IoT Central alkalmazással. Az eszköz fejlesztőinek meg kell győződnie arról, hogy az eszköz megvalósítja a DCM-ben meghatározott viselkedéseket, hogy IoT Central tudja figyelni és kezelni az eszközt. A DCM egy vagy több _illesztőfelületből_áll, és mindegyik csatoló _telemetria_ -típusok, _eszköz-tulajdonságok_és- _parancsok_gyűjteményét is meghatározhatja. A megoldás fejlesztője importálhat egy olyan JSON-fájlt, amely definiálja a DCM-et egy eszköz sablonjában, vagy a IoT Central webes FELÜLETén a DCM létrehozásához vagy szerkesztéséhez használja. A webes felület használatával készített DCM-re való váltáshoz szükség van az [eszköz sablonjának verziószámozására](./howto-version-device-template.md).

A megoldás fejlesztője a DCM-et tartalmazó JSON-fájl exportálását is elvégezheti. Egy eszköz fejlesztője ezt a JSON-dokumentumot arra használhatja, hogy megtudja, hogyan kommunikál az eszköz a IoT Central alkalmazással.

A DCM-et definiáló JSON-fájl a [Digital Twin Definition Language (DTDL) v1](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)protokollt használja. IoT Central elvárja, hogy a JSON-fájl tartalmazza a DCM-et a beágyazott illesztőfelületekkel, nem pedig külön fájlokban.

Egy tipikus IoT-eszköz a következőket alkotja:

- Egyéni részek, amelyek az eszköz egyedivé tételét teszik elérhetővé.
- Szabványos részek, amelyek az összes eszközre jellemző dolgok.

Ezeket a részeket egy DCM-ben lévő _illesztőfelületnek_ nevezzük. A felületek határozzák meg az eszköz által megvalósított egyes részek részleteit. A felületek újrafelhasználhatók a DCMs között.

Az alábbi példa egy környezeti érzékelő eszköz eszköz-képesség modelljének körvonalát mutatja be két interfésszel:

```json
{
  "@id": "urn:contoso:sensor_device:1",
  "@type": "CapabilityModel",
  "displayName": "Environment Sensor Capability Model",
  "implements": [
    {
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": "Device Information",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    },
    {
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:1",
        "@type": "Interface",
        "displayName": "Environmental Sensor",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

A képesség-modellnek van néhány kötelező mezője:

- `@id`: egyedi azonosító egyszerű egységes erőforrás-név formájában.
- `@type`: kijelenti, hogy ez az objektum egy képességi modell.
- `@context`: a képesség modellhez használt DTDL-verziót adja meg.
- `implements`: az eszköz által megvalósított felületek felsorolása.

A megvalósítások szakaszban található felületek listájának minden bejegyzése a következőket tartalmazza:

- `name`: az interfész programozási neve.
- `schema`: az a felület, amelyet a képesség modell implementál.

Az illesztőfelületnek van néhány kötelező mezője:

- `@id`: egyedi azonosító egyszerű egységes erőforrás-név formájában.
- `@type`: kijelenti, hogy ez az objektum illesztőfelület.
- `@context`: az illesztőfelülethez használt DTDL-verziót adja meg.
- `contents`: az eszközt alkotó tulajdonságok, telemetria és parancsok felsorolása.

Vannak olyan választható mezők, amelyek segítségével további részleteket adhat hozzá a képesség modelljéhez, például a megjelenítendő név és a Leírás lehetőséghez.

### <a name="interface"></a>Interfész

A DTDL lehetővé teszi az eszköz képességeinek leírását. A kapcsolódó képességek felületekbe vannak csoportosítva. A felületek leírják, hogy a tulajdonságok, a telemetria és a parancsok hogyan implementálják az eszköz részét:

- `Properties`. A tulajdonságok olyan adatmezők, amelyek az eszköz állapotát jelölik. A tulajdonságok használatával az eszköz tartós állapotát, például egy Hűtőfolyadék-szivattyú on-off állapotát jelölheti ki. A tulajdonságok az alapszintű eszköz tulajdonságait is jelezhetik, például az eszköz belső vezérlőprogram-verzióját. A tulajdonságokat csak olvasható vagy írható módon deklarálhatja.
- `Telemetry`. A telemetria mezők az érzékelők méréseit jelölik. Minden alkalommal, amikor az eszköz érzékelőt használ, egy telemetria eseményt kell küldenie, amely az érzékelő adatait tartalmazza.
- `Commands`. A parancsok olyan metódusokat jelölnek, amelyeket az eszköz felhasználói futtathatnak az eszközön. Például egy alaphelyzetbe állítási parancs vagy egy, a ventilátor be-és kikapcsolására szolgáló parancs.

A következő példa a környezeti érzékelő felületének definícióját mutatja be:

```json
{
  "@type": "Property",
  "displayName": "Device State",
  "description": "The state of the device. Two states online/offline are available.",
  "name": "state",
  "schema": "boolean"
},
{
  "@type": "Property",
  "displayName": "Customer Name",
  "description": "The name of the customer currently operating the device.",
  "name": "name",
  "schema": "string",
  "writable": true
},
{
  "@type": [
    "Telemetry",
    "SemanticType/Temperature"
  ],
  "description": "Current temperature on the device",
  "displayName": "Temperature",
  "name": "temp",
  "schema": "double",
  "unit": "Units/Temperature/fahrenheit"
},
{
  "@type": "Command",
  "name": "turnon",
  "comment": "This Commands will turn-on the LED light on the device.",
  "commandType": "synchronous"
},
{
  "@type": "Command",
  "name": "turnoff",
  "comment": "This Commands will turn-off the LED light on the device.",
  "commandType": "synchronous"
}
```

Ez a példa két tulajdonságot, egy telemetria és két parancsot mutat be. A mező minimális leírása a következőket tartalmazhatja:

- `@type`a képesség típusának megadása: `Telemetry` , `Property` , vagy `Command` .  Bizonyos esetekben a típus egy szemantikai típust tartalmaz, amely lehetővé teszi, hogy IoT Central az érték kezelésével kapcsolatos feltételezések elvégzéséhez.
- `name`a telemetria értékhez.
- `schema`a telemetria vagy a tulajdonság adattípusának megadása. Ez az érték egyszerű típus lehet, például dupla, Integer, Boolean vagy string. Az összetett objektumtípusok, tömbök és leképezések is támogatottak.
- `commandType`a parancs kezelésének megadásához.

Az opcionális mezők, például a megjelenítendő név és a Leírás lehetővé teszik, hogy további részleteket adjon hozzá az interfészhez és a képességekhez.

### <a name="properties"></a>Tulajdonságok

Alapértelmezés szerint a tulajdonságok csak olvashatók. A csak olvasható tulajdonságok érték azt jelenti, hogy az eszköz a IoT Central alkalmazásra frissíti a tulajdonság értékét. A IoT Central alkalmazás nem állíthatja be a csak olvasható tulajdonság értékét.

Egy tulajdonságot egy felületen írhatóként is megadhat. Az eszközök frissíthetik a IoT Central alkalmazásból származó írható tulajdonságot, valamint az alkalmazásra vonatkozó jelentéskészítési tulajdonságértékek frissítését is.

Az eszközöknek nem kell csatlakozniuk a tulajdonságértékek beállításához. A frissített értékek akkor lesznek átadva, amikor az eszköz a következőhöz csatlakozik az alkalmazáshoz. Ez a viselkedés a csak olvasható és írható tulajdonságokra is vonatkozik.

Ne használjon tulajdonságokat a telemetria az eszközről való küldéséhez. Például egy írásvédett tulajdonság például `temperatureSetting=80` azt jelenti, hogy az eszköz hőmérséklete 80 értékre van állítva, és az eszköz megpróbál bejutni vagy maradni, ez a hőmérséklet.

Az írható tulajdonságok esetében az eszköz a kívánt állapot kódját, verzióját és leírását adja vissza annak jelzésére, hogy a kapott és alkalmazta-e a tulajdonság értékét.

### <a name="telemetry"></a>Telemetria

IoT Central segítségével megtekintheti az irányítópultokon és diagramokon lévő telemetria, és a szabályok segítségével elindíthatja a műveleteket, ha elérik a küszöbértékeket. A IoT Central a DCM-ben található információkat, például adattípusokat, egységeket és megjelenítendő neveket használ a telemetria-értékek megjelenítésének meghatározásához.

A IoT Central adatexportálási szolgáltatással továbbíthatja a telemetria más célhelyekre, például a Storage-ba vagy a Event Hubsba.

### <a name="commands"></a>Parancsok

A parancsok szinkron vagy aszinkron jellegűek. A szinkron parancsoknak alapértelmezés szerint 30 másodpercen belül kell futniuk, és az eszköznek csatlakoztatva kell lennie a parancs megérkezése után. Ha az eszköz időben válaszol, vagy az eszköz nincs csatlakoztatva, akkor a parancs sikertelen lesz.

Használjon aszinkron parancsokat a hosszan futó műveletekhez. Az eszköz telemetria-üzenetek használatával küldi el a végrehajtási adatokat. Ezek az állapotjelző üzenetek a következő fejléc-tulajdonságokkal rendelkeznek:

- `iothub-command-name`: a parancs neve, például `UpdateFirmware` .
- `iothub-command-request-id`: a kiszolgáló oldalán generált kérelem-azonosító, amelyet a rendszer a kezdeti hívásban az eszköznek küld.
- `iothub-interface-id`: Annak az adapternek az azonosítója, amelyhez ez a parancs van meghatározva (például `urn:example:AssetTracker:1` ).
 `iothub-interface-name`: az interfész példányának neve, például `myAssetTracker` .
- `iothub-command-statuscode`: az eszközről visszaadott állapotkód, például `202` .

## <a name="cloud-properties"></a>Felhő tulajdonságai

A felhő tulajdonságai az eszköz sablon részét képezik, de nem részei a DCM-nek. A felhő tulajdonságai lehetővé teszik a megoldás fejlesztője számára a IoT Central alkalmazásban tárolni kívánt eszközök metaadatainak megadását. A felhő tulajdonságai nem érintik azt a kódot, amelyet az eszköz fejlesztője a DCM megvalósítására ír.

A megoldás fejlesztője hozzáadhat Felhőbeli tulajdonságokat az irányítópultokhoz és nézetekhez az eszköz tulajdonságai mellett, hogy az operátor felügyelje az alkalmazáshoz kapcsolódó eszközöket. A megoldás fejlesztője a szabályok definíciójának részeként is használhat Felhőbeli tulajdonságokat, hogy az operátor egy küszöbértéket módosítson.

## <a name="customizations"></a>Testreszabások

A testreszabások az eszköz sablon részét képezik, de nem részei a DCM-nek. A testreszabások lehetővé teszik a megoldás fejlesztője számára a DCM néhány definíciójának bővítését vagy felülbírálását. A megoldás fejlesztője például módosíthatja a telemetria-típus vagy-tulajdonság megjelenítendő nevét. A megoldás fejlesztője testreszabásokat is használhat a karakterlánc-eszköz tulajdonságának minimális vagy maximális hosszának hozzáadásához.

A testreszabások hatással lehetnek arra a kódra, amelyet az eszköz fejlesztője a DCM megvalósítására ír. Egy Testreszabás például megadhatja a minimális és a maximális karakterlánc hosszát, illetve a telemetria minimális és maximális numerikus értékeit.

## <a name="views"></a>Nézetek

A megoldás fejlesztői olyan nézeteket hoznak létre, amelyek lehetővé teszik a kezelők számára a csatlakoztatott eszközök figyelését és kezelését. A nézetek az eszköz sablonjának részét képezik, így a nézet egy adott típusú eszközhöz van társítva. A nézetek A következők lehetnek:

- A telemetria ábrázoló diagramok.
- A csak olvasható eszközök tulajdonságainak megjelenítésére szolgáló csempék.
- Csempék, amelyek lehetővé teszik az operátor számára az írható eszköz tulajdonságainak szerkesztését.
- Csempék, amelyek lehetővé teszik az operátornak a felhő tulajdonságainak szerkesztését.
- Csempék, amelyek lehetővé teszik az operátor hívási parancsait, beleértve a hasznos adatokat tartalmazó parancsokat is.
- Feliratok, képek vagy Markdown szövegének megjelenítéséhez szükséges csempék.

A nézetekhez felvehető telemetria, tulajdonságokat és parancsokat a DCM, a Cloud Properties és a testreszabások határozzák meg az eszköz sablonjában.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az eszközök sablonjait, a következő lépésekből megtudhatja, hogyan regisztrálhat [Az Azure IoT Centralhoz](./concepts-get-connected.md) , és hogyan regisztrálja az eszközöket a IoT Central, és hogy miként IoT Central biztonságossá teszi az eszköz kapcsolatait.

A megoldás fejlesztője javasolt következő lépés egy [új IoT-eszköz típusának beolvasása az Azure IoT Central-alkalmazásban](./howto-set-up-template.md) az eszköz sablonjának létrehozásával kapcsolatos további információért.

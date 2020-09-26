---
title: Tulajdonságok használata Azure IoT Central-megoldásokban
description: Az írásvédett és az írható tulajdonságok használata az Azure IoT Central-megoldásban
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: fa9b07d80c34ec26ca920fe147ada8f8ef7f2fd7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91344297"
---
# <a name="how-to-use-properties-in-an-azure-iot-central-solution"></a>Tulajdonságok használata Azure IoT Central-megoldásokban

Ebből a cikkből megtudhatja, hogyan használhatja az Azure IoT Central-alkalmazásban található eszköz-sablonban definiált eszköz tulajdonságait.

A tulajdonságok a pont – idő értékeket jelölik. Egy eszköz használhat például egy tulajdonságot a elérni kívánt cél hőmérséklet jelentésére. A tulajdonságok azt is lehetővé teszik, hogy az eszköz és a IoT Central alkalmazás között szinkronizálja az állapotot.  Az írható tulajdonságokat IoT Central lehet beállítani.

A felhő tulajdonságait egy IoT Central alkalmazásban is megadhatja. A Felhőbeli tulajdonságok értékeit a rendszer soha nem cseréli ki az eszközön, és a jelen cikk hatókörén kívül esik.

## <a name="define-your-properties"></a>A tulajdonságok megadása

A tulajdonságok olyan adatmezők, amelyek az eszköz állapotát jelölik. Tulajdonságok használata az eszköz tartós állapotának, például az eszköz kikapcsolt állapotának megjelenítéséhez. A tulajdonságok az alapszintű eszköz tulajdonságait is jelezhetik, például az eszköz szoftveres verzióját. A tulajdonságokat csak olvasható vagy írható módon deklarálhatja.

Az alábbi képernyőfelvételen az Azure IoT Central alkalmazás egyik tulajdonságának definíciója látható

![Tulajdonság definiálása](./media/howto-use-properties/property-definition.png)

A következő táblázat a tulajdonságok funkciójának konfigurációs beállításait mutatja be:

| Mező           | Leírás                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Megjelenítendő név    | Az irányítópultokon és űrlapokon használt tulajdonságérték megjelenítendő neve.                                                                                                                                                              |
| Name            | A tulajdonság neve. IoT Central a megjelenített név alapján létrehoz egy értéket a mezőhöz, de szükség esetén kiválaszthatja a saját értékét is. A mezőnek alfanumerikusnak kell lennie.                                                 |
| Képesség típusa | Tulajdonság.                                                                                                                                                                                                                          |
| Szemantikai típus   | A tulajdonság szemantikai típusa, például hőmérséklet, állapot vagy esemény. A szemantikai típus megválasztása határozza meg, hogy a következő mezők közül melyek érhetők el.                                                                       |
| Séma          | A tulajdonság adattípusa, például Double, string vagy Vector. Az elérhető beállításokat a szemantikai típus határozza meg. A séma nem érhető el az esemény és az állapot szemantikai típusaihoz.                                               |
| Írható       | Ha a tulajdonság nem írható, az eszköz jelentést készíthet IoT Central. Ha a tulajdonság írható, az eszköz jelentést készíthet IoT Central, és IoT Central a tulajdonságok frissítését is elküldheti az eszköznek. |
| Súlyosság        | Csak az esemény szemantikai típusához érhető el. A megszakítások a következők: **hiba**, **információ**vagy **Figyelmeztetés**.                                                                                                                         |
| Állapot értékei    | Csak az állapot szemantikai típusához érhető el. Definiálja a lehetséges állapotinformációkat, amelyek mindegyike megjelenített névvel, névvel, számbavételi típussal és értékkel rendelkezik.                                                                                   |
| Egység            | A tulajdonság értékének egysége, például: **mph**, **%** , vagy ** &deg; C**.                                                                                                                                                              |
| Megjelenítési egység    | Irányítópultokon és űrlapokon használható megjelenítési egység.                                                                                                                                                                                    |
| Megjegyzés         | A tulajdonság képességével kapcsolatos megjegyzések.                                                                                                                                                                                        |
| Description     | A tulajdonság funkciójának leírása.                                                                                                                                                                                          |

A tulajdonságok az alábbi módon is meghatározhatók egy sablon illesztőfelületében:

``` json
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
 "@type": "Property",
 "displayName": "Date ",
 "description": "The date on which the device is currently operating",
 "name": "date",
 "writable": true,
 "schema": "date"
},
{ 
 "@type": "Property",
 "displayName": "Location",
 "description": "The current location of the device",
 "name": "location",
 "writable": true,
 "schema": "geopoint"
},
{
 "@type": "Property",
 "displayName": "Vector Level",
 "description": "The Vector level of the device",
 "name": "vector",
 "writable": true,
 "schema": "vector"
}
```

Ez a példa öt tulajdonságot mutat be, ezek a felhasználói felületen az alábbi módon hozhatók összefüggésbe:

* `@type` a képesség típusának megadása: `Property`
* `name` a tulajdonság értékeként.
* `schema` adja meg a tulajdonság adattípusát. Ez az érték egyszerű típus lehet, például dupla, Integer, Boolean vagy string. Az összetett objektumtípusok, tömbök és leképezések is támogatottak.
* `writable` Alapértelmezés szerint a tulajdonságok csak olvashatók. A tulajdonságot írhatóként lehet megjelölni a mező használatával

Az opcionális mezők, például a megjelenítendő név és a Leírás lehetővé teszik, hogy további részleteket adjon hozzá az interfészhez és a képességekhez.

Egy tulajdonság létrehozásakor megadhatja az összetett **sémák** típusát, például az objektum, az enumerálás stb.

![Képesség hozzáadása](./media/howto-use-properties/property.png)

Ha kijelöli az összetett sémát, például az **objektumot**, az objektumot is meg kell határoznia.

![Objektum definiálása](./media/howto-use-properties/object.png)

Az alábbi kód egy objektum típusú tulajdonság definícióját mutatja be. Ennek az objektumnak két típusa van: string és Integer típusú.

``` json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

## <a name="implement-read-only-properties"></a>Csak olvasható tulajdonságok implementálása

Alapértelmezés szerint a tulajdonságok csak olvashatók. A csak olvasható tulajdonságok érték azt jelenti, hogy az eszköz a IoT Central alkalmazásra frissíti a tulajdonság értékét. A IoT Central alkalmazás nem állíthatja be a csak olvasható tulajdonság értékét.

A IoT Central az eszközön lévő Twins használatával szinkronizálja az eszköz és a IoT Central alkalmazás közötti tulajdonságértékeket. Az eszköz tulajdonságainak értékei az eszköz Twin jelentett tulajdonságait használják. További információ: [eszközök ikrek](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins)

Az eszköz-képesség modell következő kódrészlete egy írásvédett tulajdonság definícióját mutatja be:

``` json
{
  "@type": "Property",
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

Az eszköz csak olvasható tulajdonságokat kap a IoT Central. A tulajdonságok JSON-adattartalomként továbbítódnak, további információ: [hasznos](./concepts-telemetry-properties-commands.md)adatok.

Az Azure IoT-eszköz SDK-val a IoT Central alkalmazáshoz tartozó tulajdonságok frissítése is elküldhető.

Az eszköz Twin tulajdonságai az alábbi függvény használatával küldhetők el az Azure IoT Central-alkalmazásba:

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

Ez a cikk az egyszerűség kedvéért Node.js használ, az eszköz alkalmazásával kapcsolatos példákért tekintse meg az [ügyfélalkalmazás létrehozása és csatlakoztatása az azure IoT Central alkalmazáshoz (Node.js)](tutorial-connect-device-nodejs.md) című cikket, és [hozzon létre és csatlakoztasson egy ügyfélalkalmazás az Azure IoT Central Application (Python)](tutorial-connect-device-python.md) oktatóanyagokhoz.

A következő nézet az Azure IoT Central alkalmazásban megjeleníti a tulajdonságokat, így a nézet automatikusan a _csak olvasható eszköz tulajdonságot_jeleníti meg.

![Csak olvasható tulajdonság megtekintése](./media/howto-use-properties/read-only.png)

## <a name="implement-writable-properties"></a>Írható tulajdonságok implementálása

Az írható tulajdonságokat egy operátor állítja be a IoT Central alkalmazásban az űrlapon. IoT Central elküldi a tulajdonságot az eszköznek. IoT Central egy nyugtát vár az eszközről. 

Az eszköz képességeinek modelljéből származó következő kódrészlet az írható tulajdonságok típusának definícióját mutatja be:

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

Az eszköz ügyfelének egy JSON-adattartalmat kell küldenie, amely a következő példához hasonlóan jelenik meg, mint az eszköz Twin-beli jelentett tulajdonsága:

``` json
{ "Brightness Level": 2 }
```

Az eszköz által válaszolt írható tulajdonságok definiálásához és kezeléséhez használja a következő kódot.

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

A válaszüzenetnek tartalmaznia kell a `ac` és a `av` mezőket. A `ad` mező kitöltése nem kötelező. Példákért tekintse meg az alábbi kódrészleteket.

* `ac` egy numerikus mező, amely az alábbi táblázatban szereplő értékeket használja:

* `av` az eszközre eljuttatott verziószám.

* `ad` egy paraméter-karakterlánc leírása.

| Érték | Címke | Description |
| ----- | ----- | ----------- |
| `'ac': 200` | Befejeződött | A tulajdonság-módosítási művelet sikeresen befejeződött. |
| `'ac': 202`  vagy `'ac': 201` | Függőben | A tulajdonság-módosítási művelet függőben van vagy folyamatban van |
| `'ac': 4xx` | Hiba | A kért tulajdonság módosítása érvénytelen volt, vagy hiba történt. |
| `'ac': 5xx` | Hiba | Az eszköz váratlan hibát észlelt a kért módosítás feldolgozása során. |


További információ: [eszközök ikrek](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins).

Ha a kezelő egy írható tulajdonságot állít be a IoT Central alkalmazásban, az alkalmazás egy, a kívánt eszközhöz tartozó, a kívánt tulajdonságot használva küldi el az értéket az eszköznek. Az eszköz ezután válaszol a Device Twin jelentett tulajdonság használatával. Ha a IoT Central megkapja a jelentett tulajdonság értékét, az frissíti a tulajdonság nézetét az **elfogadott**állapottal.

A következő nézetben az írható tulajdonságok láthatók. Amikor megadja az értéket és a **mentést**, a kezdeti állapot **függőben**van, amikor az eszköz elfogadja a változást, az állapot **elfogadva**értékre változik.

![Függő állapot](./media/howto-use-properties/status-pending.png)

![Elfogadott tulajdonság](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan használhatja a tulajdonságokat az Azure IoT Central alkalmazásban, megtekintheti a [hasznos](concepts-telemetry-properties-commands.md) adatokat, és [létrehozhatja és összekapcsolhat egy ügyfélalkalmazás-alkalmazást az Azure IoT Central-alkalmazáshoz (Node.js)](tutorial-connect-device-nodejs.md).

---
title: Adatfeldolgozás és a felhasználó által definiált függvények az Azure digitális Twins |} A Microsoft Docs
description: Adatok feldolgozása matchers és felhasználó által definiált függvények az Azure digitális Twins áttekintése.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: alinast
ms.openlocfilehash: 4db515a931bc7f423eb11ae31b7304a602f0da46
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531734"
---
# <a name="data-processing-and-user-defined-functions"></a>Adatfeldolgozás és felhasználó által definiált függvények

Az Azure digitális Twins fejlett számítási képességeket kínál. A fejlesztők definiálása és felhasználói függvények beérkező telemetriai üzeneteket küldhet eseményeket előre definiált végpontjainak futtatásához.

## <a name="data-processing-flow"></a>Adatfeldolgozási folyamat

Eszközök telemetriai adatokat küldeni az Azure digitális Twins, miután a fejlesztők hatékonyabbá tételéhez négy fázisban a adatokat tud feldolgozni: *ellenőrzése*, *megfelelő*, *számítási*, és *küldése* .

![Az Azure digitális Twins adatfeldolgozási folyamat][1]

1. Az érvényesítés fázis átalakítja a beérkező telemetriai üzenetet egy közérthető [adatátviteli objektumot](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) formátumban. Ebben a fázisban az eszközök és érzékelők érvényesítési is végez.
1. A match fázis megkeresi a megfelelő felhasználó által definiált függvények futtatását. Előre definiált matchers keresse meg a felhasználó által definiált függvények, az eszköz-, érzékelő és a bejövő telemetriát üzenet terület adatai alapján.
1. A számítási fázis fut, a felhasználó által definiált függvények egyezik az előző szakaszban. Ezek a függvények előfordulhat, hogy olvassa el, és a térbeli számított értékek frissítése graph-csomópontok és egyéni értesítések kibocsátható.
1. A feladó fázis bármely egyéni értesítéseket állíthat be a számítási fázis, a gráf meghatározva a végpontjai irányítja.

## <a name="data-processing-objects"></a>Adatok feldolgozása objektumok

Adatfeldolgozás az Azure digitális Twins áll három objektum meghatározása: *matchers*, *felhasználó által definiált függvények*, és *szerepkör-hozzárendelések*.

![Az Azure digitális Twins adatfeldolgozási objektumok][2]

<div id="matcher"></div>

### <a name="matchers"></a>Matchers

Matchers meghatároz egy olyan feltételek, amelyek kiértékelik a milyen műveletek végrehajtása a bejövő érzékelő telemetriai adatai alapján történik. Az egyezés meghatározásához feltételek az érzékelő, a érzékelő szülő eszköz és az érzékelő szülő hely tulajdonságai tartalmazhatnak. A feltételek fejezik összehasonlítások elleni egy [JSON-útvonal](https://jsonpath.com/) ebben a példában látható módon:

- Az adattípus az összes érzékelő **hőmérséklet** képviseli az escape-karakterrel megjelölt karakterlánc értéke `\"Temperature\"`
- Kellene `01` azok port
- A kiterjesztett tulajdonság kulccsal amely tartozó eszközök **gyártó** escape-karakterrel megjelölt karakterlánc értékre `\"GoodCorp\"`
- Ami az escape-karakterrel megjelölt karakterlánc által meghatározott típusú tárolóhelyek tartozik `\"Venue\"`
- Amely leszármazottai szülő **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

```JSON
{
  "SpaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Name": "My custom matcher",
  "Description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value GoodCorp and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"GoodCorp\"",
      "comparison": "Equals"
    },
    {
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - JSON-útvonalakat használ a kis-és nagybetűket.
> - A JSON-adattartalom megegyezik a által visszaadott hasznos:
>   - `/sensors/{id}?includes=properties,types` az érzékelő.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` az érzékelő szülő eszközhöz.
>   - `/spaces/{id}?includes=properties,types,location,timezone` az érzékelő szülő terület.
> - A-összehasonlítások megkülönböztetik a kis-és nagybetűket.

### <a name="user-defined-functions"></a>Felhasználó által definiált függvények

Egy felhasználó által definiált függvény nem egy egyéni függvény végrehajtása egy elkülönített Azure digitális Twins környezeten belül. Felhasználó által definiált függvények lehet nyers érzékelő telemetriai üzenetet a hozzáférést kapott beolvasása. Felhasználó által definiált függvények a térbeli graph és az eseményelosztóra szolgáltatáshoz való hozzáférést is. Miután a felhasználó által definiált függvény regisztrálva van az a graph-megfeleltetőben megadott (részletes [fent](#matcher)) kell létrehozni, adja meg a függvény végrehajtása esetén. Például amikor az Azure digitális Twins új telemetriai kap egy adott érzékelő, a felhasználó által definiált fügvényimport kiszámíthatja az elmúlt néhány érzékelőinek mozgóátlagát.

Felhasználó által definiált függvények használata Javascriptben csak írható. Segédmetódusokat jelölőnégyzettel kapcsolható be a felhasználó által definiált végrehajtási környezetben dolgozhat. A fejlesztők hajthat végre egyéni kódrészleteket érzékelő telemetriai üzeneteket programozhat. Példák erre vonatkozóan:

- Állítsa be az érzékelő közvetlenül az alakzatot a diagramon belül az érzékelő objektum olvasása.
- Egy műveletet a Graph-címtéren belüli különböző érzékelőinek alapján.
- Hozzon létre egy értesítést, ha bizonyos feltételek teljesülnek, egy bejövő érzékelő olvasása.
- Graph-metaadatok csatolása az érzékelő olvasása előtt értesítés küldésére.

További információkért lásd: [felhasználó által definiált függvények használata](./how-to-user-defined-functions.md).


#### <a name="examples"></a>Példák

A [GitHub-adattárat a digitális Twins a C# minta](https://github.com/Azure-Samples/digital-twins-samples-csharp/) tartalmaz néhány példát a felhasználó által definiált függvények:
- [Ez a függvény](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) szén-dioxid-mozgásban lévő adatoknak egyaránt és hőmérsékleti értékek meghatározásához a szoba elérhető-e ezekkel az értékekkel tartományban keresi. A [digitális Twins számára oktatóanyagokkal](tutorial-facilities-udf.md) ismerje meg a függvény a további részleteket. 
- [Ez a függvény](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) több mozgásban lévő adatoknak egyaránt érzékelőadatok keres adatokat, és megállapítja, hogy a hely érhető el, ha bármely mozgásfelismerés egyiket sem. A felhasználó által definiált függvény vagy a használt egyszerűen lecserélheti a [a rövid útmutató](quickstart-view-occupancy-dotnet.md), vagy a [oktatóanyagok](tutorial-facilities-setup.md), azáltal, hogy a módosítások a fájl a Megjegyzések szakaszban említett. 



### <a name="role-assignment"></a>Szerepkör-kijelölés

Egy felhasználó által definiált függvény műveletek vonatkoznak rá az Azure digitális Twins [szerepköralapú hozzáférés-vezérlés](./security-role-based-access-control.md) a szolgáltatáson belül az adatok védelmét. Szerepkör-hozzárendelések határozza meg, mely felhasználó által definiált függvények rendelkezik megfelelő engedélyekkel a térbeli graph és entitásai kommunikál. Például előfordulhat, hogy rendelkezik egy felhasználó által definiált függvény képességét és engedélyt *létrehozás*, *OLVASÁSI*, *frissítés*, vagy *törlése* graph-adatok a megadott hely. Egy felhasználó által definiált függvény hozzáférési szintet be van jelölve, amikor az adatok a graph kéri a felhasználó által definiált függvény vagy a művelet megkísérli. További információkért lásd: [szerepköralapú hozzáférés-vezérlés](./security-create-manage-role-assignments.md).

A felhasználó által definiált függvény, amely nem rendelkezik szerepkör hozzárendelésekkel aktiválhat egy megfeleltetőben megadott lehetőség. Ebben az esetben a felhasználó által definiált függvény nem tud beolvasni adatokat a diagramon.

## <a name="next-steps"></a>További lépések

- Események és a telemetriai üzeneteket átirányítása más Azure-szolgáltatásokkal kapcsolatos további tudnivalókért olvassa el [irányíthatja az események és az üzenetek](./concepts-events-routing.md).

- Matchers, a felhasználó által definiált függvények és a szerepkör-hozzárendelések létrehozásával kapcsolatos további tudnivalókért olvassa el a [útmutató a felhasználó által definiált függvények](./how-to-user-defined-functions.md).

- Tekintse át a [felhasználó által definiált függvény ügyféloldali kódtár dokumentációja](./reference-user-defined-functions-client-library.md).

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png

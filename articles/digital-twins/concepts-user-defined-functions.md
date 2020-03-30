---
title: Adatfeldolgozás és felhasználó által definiált függvények – Azure Digital Twins| Microsoft dokumentumok
description: Az Azure Digital Twins adatfeldolgozás, egyeztetők és a felhasználó által definiált függvények áttekintése.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 75ed2029582438ede43687addfd54c0a187e0120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265180"
---
# <a name="data-processing-and-user-defined-functions"></a>Adatfeldolgozás és felhasználó által definiált függvények

Az Azure Digital Twins fejlett számítási lehetőségeket kínál. A fejlesztők egyéni függvényeket definiálhatnak és futtathatnak a bejövő telemetriai üzeneteken, hogy eseményeket küldjenek előre definiált végpontokba.

## <a name="data-processing-flow"></a>Adatfeldolgozási folyamat

Miután az eszközök telemetriai adatokat küldenek az Azure Digital Twins-nek, a fejlesztők négy fázisban dolgozhatják fel az adatokat: *ellenőrzés*, *egyezés,* *számítási*és *feladás*.

[![Azure Digital Twins adatfeldolgozási folyamat](media/concepts/digital-twins-data-processing-flow.png)](media/concepts/digital-twins-data-processing-flow.png#lightbox)

1. Az érvényesítési fázis a bejövő telemetriai üzenetet egy általánosan ismert [adatátviteli objektum](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5) formátummá alakítja. Ez a fázis eszköz- és érzékelő-érvényesítést is végrehajt.
1. Az egyeztetési fázis megkeresi a megfelelő felhasználó által definiált függvényeket. Az előre definiált egyeztetők az eszköz, az érzékelő és a bejövő telemetriai üzenetből származó helyinformációk alapján találják meg a felhasználó által definiált függvényeket.
1. A számítási fázis futtatja a felhasználó által definiált függvények egyeztetett az előző fázisban. Ezek a függvények beolvashatják és frissíthetik a számított értékeket a térbeli gráfcsomópontokon, és egyéni értesítéseket bocsáthatnak ki.
1. A feladási fázis a számítási fázisból származó egyéni értesítéseket a diagramban meghatározott végpontok felé irányítja.

## <a name="data-processing-objects"></a>Adatfeldolgozási objektumok

Az Azure Digital Twins adatfeldolgozása három objektum definiálásából áll: *egyezők,* *felhasználó által definiált függvények*és *szerepkör-hozzárendelések*.

[![Azure Digital Twins adatfeldolgozási objektumok](media/concepts/digital-twins-user-defined-functions.png)](media/concepts/digital-twins-user-defined-functions.png#lightbox)

### <a name="matchers"></a>Egyeztetők

A matchers határozza meg a feltételeket, amelyek kiértékelik, hogy milyen műveletek et hajtanak végre a bejövő érzékelő telemetriai adatok alapján. Az egyezés meghatározásának feltételei lehetnek az érzékelő, az érzékelő szülőeszközének és az érzékelő szülőterének tulajdonságai. A feltételeket a [json-útvonalal](https://jsonpath.com/) való összehasonlításként fejezik ki, amint azt az alábbi példában vázoltuk:

- A **Kiszabadult** karakterlánc értékáltal képviselt Hőmérséklet adattípusú összes érzékelő`\"Temperature\"`
- Miután `01` a saját port
- Amely az olyan eszközökhöz tartozik, amelyeknél a **gyártó** kiterjesztett tulajdonságkulcsa az escaped String értékre van állítva`\"Contoso\"`
- Az escaped string által megadott típusú szóközökhöz tartozik`\"Venue\"`
- Amelyek leszármazottai szülő **SpaceId**`DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

```JSON
{
  "id": "23535afafd-f39b-46c0-9b0c-0dd3892a1c30",
  "name": "My custom matcher",
  "spaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value Contoso and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "conditions": [
    {
      "id": "43898sg43-e15a-4e9c-abb8-2gw464364",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "id": "wt3th44-e15a-35sg-seg3-235wf3ga463",
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "id": "735hs33-e15a-37jj-23532-db901d550af5",
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"Contoso\"",
      "comparison": "Equals"
    },
    {
      "id": "222325-e15a-49fg-5744-463643644",
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - A JSON-elérési utak ban a kis- és nagybetűket nem veszik figyelembe.
> - A JSON hasznos adat megegyezik a visszaküldési adattal:
>   - `/sensors/{id}?includes=properties,types`az érzékelőhöz.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes`az érzékelő szülőeszközéhez.
>   - `/spaces/{id}?includes=properties,types,location,timezone`az érzékelő szülőterének megtisztítására.
> - Az összehasonlítások nem érzékenyek a kis- és nagybetűkre.

### <a name="user-defined-functions"></a>Felhasználó által meghatározott függvények

A felhasználó által definiált függvény egy egyéni függvény egy elszigetelt Azure Digital Twins környezetben végrehajtott egyéni függvény. A felhasználó által definiált függvények hozzáférhetnek a nyers érzékelő telemetriai üzenet, ahogy megkapja a fogadást. A felhasználó által definiált függvények is hozzáférhetnek a térbeli gráf és diszpécser szolgáltatáshoz. Miután a felhasználó által definiált függvény regisztrálva van egy grafikonon belül, létre kell hozni egy egyező [(fent](#matchers)részletezett) egyezőt, amely megadja, hogy mikor hajtják végre a függvényt. Ha például az Azure Digital Twins új telemetriai adatokat kap egy adott érzékelőtől, az egyező felhasználó által definiált függvény kiszámíthatja az utolsó néhány érzékelőleolvasás mozgóátlagát.

A felhasználó által definiált függvények JavaScript nyelven írhatók. A segítő módszerek a felhasználó által definiált végrehajtási környezetben kommunikálnak a grafikonnal. A fejlesztők egyéni kódrészleteket futtathatnak az érzékelő telemetriai üzeneteivel szemben. Példák erre vonatkozóan:

- Állítsa az érzékelő taxikát közvetlenül az érzékelő objektumra a grafikonon belül.
- Hajtson végre egy műveletet a diagram egy területén belüli különböző érzékelőértékek alapján.
- Értesítés létrehozása, ha bizonyos feltételek teljesülnek a bejövő érzékelőleolvasáshoz.
- Az értesítés küldése előtt csatolja a grafikon metaadatait az érzékelő olvasásához.

További információ: [A felhasználó által definiált függvények használata.](./how-to-user-defined-functions.md)

#### <a name="examples"></a>Példák

A [GitHub-tárlata a digitális Twins C# minta tartalmaz](https://github.com/Azure-Samples/digital-twins-samples-csharp/) néhány példát a felhasználó által definiált függvények:
- [Ez](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) a funkció szén-dioxid-, mozgás- és hőmérsékletértékeket keres annak meghatározására, hogy rendelkezésre áll-e egy helyiség ezekkel az értékekkel a tartományban. A [digitális ikrek oktatóanyagai](tutorial-facilities-udf.md) részletesebben is megismerhetik ezt a funkciót. 
- [Ez a funkció](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js) több mozgásérzékelő adatait keresi, és megállapítja, hogy a hely rendelkezésre áll-e, ha egyikük sem észlel mozgást. Könnyedén lecserélheti a felhasználó által definiált függvényt, amelyet a [rövid útmutatóban](quickstart-view-occupancy-dotnet.md)vagy az [oktatóanyagokban](tutorial-facilities-setup.md)használ, ha elvégezheti a fájl megjegyzések szakaszában említett módosításokat. 

### <a name="role-assignment"></a>Szerepkör-kijelölés

A felhasználó által definiált függvény műveletek az Azure Digital Twins [szerepköralapú hozzáférés-vezérlés](./security-role-based-access-control.md) a szolgáltatáson belüli adatok biztonságossá tétele. A szerepkör-hozzárendelések határozzák meg, hogy mely felhasználó által definiált függvények rendelkeznek a térbeli gráfés entitások kezeléséhez szükséges megfelelő engedélyekkel. Egy felhasználó által definiált függvény például rendelkezhet azzal a képességgel és engedéllyel, hogy egy adott terület alatt *hozzon létre,* *OLVAS,* *UPDATE*vagy *DELETE* grafikonadatokat. A felhasználó által definiált függvény hozzáférési szintjét a rendszer akkor ellenőrzi, amikor a felhasználó által definiált függvény adatokat kér a grafikontól, vagy megpróbál műveletet végrehajtani. További információért olvassa el [a szerepköralapú hozzáférés-vezérlés című.](./security-create-manage-role-assignments.md)

Lehetséges, hogy a matcher olyan felhasználó által definiált függvényt indítson el, amely nem rendelkezik szerepkör-hozzárendelésekkel. Ebben az esetben a felhasználó által definiált függvény nem tudja beolvasni az adatokat a grafikonról.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni arról, hogyan irányíthat eseményeket és telemetriai üzeneteket más Azure-szolgáltatásokba, olvassa el [az Útvonalesemények és üzenetek olvasását.](./concepts-events-routing.md)

- Ha többet szeretne tudni amatcherek, a felhasználó által definiált függvények és szerepkör-hozzárendelések létrehozásáról, olvassa el [a Műsorkalauz a felhasználó által definiált függvények használatáról.](./how-to-user-defined-functions.md)

- Tekintse át a [felhasználó által definiált függvényügyfél-függvénytár referenciadokumentációját.](./reference-user-defined-functions-client-library.md)

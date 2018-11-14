---
title: Adatfeldolgozás és a felhasználó által definiált függvények az Azure digitális Twins |} A Microsoft Docs
description: Adatok feldolgozása matchers és felhasználó által definiált függvények az Azure digitális Twins áttekintése
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: 2703778cd2eab582a9e7311aaf2024f100261889
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624522"
---
# <a name="data-processing-and-user-defined-functions"></a>Adatfeldolgozás és felhasználó által definiált függvények

Az Azure digitális Twins fejlett számítási képességeket kínál. A fejlesztők definiálása és felhasználói függvények beérkező telemetriai üzeneteket küldhet eseményeket előre definiált végpontjainak futtatásához.

## <a name="data-processing-flow"></a>Adatfeldolgozási folyamat

Eszközök telemetriai adatokat küldeni az Azure digitális Twins, miután a fejlesztők adatokat tud feldolgozni tételéhez négy fázisban: ellenőrzése egyeznek, a számítási, és melyik.

![Az Azure digitális Twins adatfeldolgozási folyamat][1]

1. Az érvényesítés fázis átalakítja a beérkező telemetriai üzenetet egy közérthető [adatátviteli objektumot](https://en.wikipedia.org/wiki/Data_transfer_object) formátumban. Ebben a fázisban az eszközök és érzékelők érvényesítési is végez.
1. A match fázis megkeresi a megfelelő felhasználó által definiált függvények (UDF) futtatásához. Előre definiált matchers találja az UDF-EK az eszköz-, érzékelő és a bejövő telemetriát üzenet terület adatai alapján.
1. A számítási fázis futtatja az UDF-EK egyezik az előző szakaszban. Ezek a függvények előfordulhat, hogy olvassa el, és a térbeli számított értékek frissítése graph-csomópontok és egyéni értesítések kibocsátható.
1. A feladó fázis bármely egyéni értesítéseket állíthat be a számítási fázis, a gráf meghatározva a végpontjai irányítja.

## <a name="data-processing-objects"></a>Adatok feldolgozása objektumok

Adatfeldolgozás az Azure digitális Twins áll három objektum meghatározása: matchers, a felhasználó által definiált függvények és a szerepkör-hozzárendeléseket.

![Az Azure digitális Twins adatfeldolgozási objektumok][2]

### <a name="matchers"></a>Matchers

Matchers meghatároz egy olyan feltételek, amelyek kiértékelik a milyen műveletek végrehajtása a bejövő érzékelő telemetriai adatai alapján történik. Az egyezés meghatározásához feltételek az érzékelő, a érzékelő szülő eszköz és az érzékelő szülő hely tulajdonságai tartalmazhatnak. A feltételek fejezik összehasonlítások elleni egy [JSON-útvonal](http://jsonpath.com/) ebben a példában látható módon:

- Az adattípus az összes érzékelő **hőmérséklet**
- Kellene `01` azok port
- A kiterjesztett tulajdonság kulccsal amely tartozó eszközök **gyártó** értékre `"GoodCorp"`
- Amely típusú tárolóhelyek tartozik `"Venue"`
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

Egy felhasználó által definiált függvény az Azure digitális Twins izolált környezetben futó egyéni függvény. UDF-EK a nyers érzékelő telemetriai üzenetet hozzáféréssel rendelkezik, mint érkezett. Addig is a térbeli graph és az eseményelosztóra szolgáltatáshoz való hozzáférést. Az UDF-ben a diagramon belül regisztrálása után, adja meg, hogy fusson az UDF-megfeleltetőben megadott (lásd fent) kell létrehozni. Amikor az Azure digitális Twins új telemetria egy adott érzékelő kap, az egyező UDF kiszámíthatja az elmúlt néhány érzékelőinek mozgóátlagát például.

A JavaScript UDF-EK írható. A fejlesztők hajthat végre egyéni kódrészleteket érzékelő telemetriai üzeneteket programozhat. Segédmetódusokat jelölőnégyzettel kapcsolható be a felhasználó által definiált végrehajtási környezetben dolgozhat. Az UDF-ben a fejlesztők számára a következőket teheti:

- Állítsa be az érzékelő közvetlenül az alakzatot a diagramon belül az érzékelő objektum olvasása.
- Egy műveletet a Graph-címtéren belüli különböző érzékelőinek alapján.
- Hozzon létre egy értesítést, ha bizonyos feltételek teljesülnek, egy bejövő érzékelő olvasása.
- Graph-metaadatok csatolása az érzékelő olvasása előtt értesítés küldésére.

További információkért lásd: [felhasználó által definiált függvények használata](how-to-user-defined-functions.md).

### <a name="role-assignment"></a>Szerepkör-kijelölés

Egy UDF műveletek vonatkoznak rá a digitális Twins Azure szerepköralapú hozzáférés-vezérlést a szolgáltatáson belül az adatok védelmét. Szerepkör-hozzárendelések győződjön meg arról, hogy egy adott UDF kommunikál a térbeli graph megfelelő engedélyekkel rendelkezik-e. Például egy UDF megpróbálhatja létrehozása, olvasása, frissítése vagy törlése a grafikon adatainak egy adott hely alapján. Hozzáférési szintjét egy UDF jelölőnégyzet be van jelölve az UDF a graph kéri az adatokat, vagy megpróbálja a műveletet. További információkért lásd: [szerepköralapú hozzáférés-vezérlés](security-create-manage-role-assignments.md).

Számára egy egy UDF, amely nem rendelkezik szerepkör hozzárendelésekkel aktiválásához megfeleltetőben megadott lehetőség. Ebben az esetben az UDF bármilyen adatokat olvasni a gráf sikertelen lesz.

## <a name="next-steps"></a>További lépések

* Események és a telemetriai üzeneteket átirányítása más Azure-szolgáltatásokkal kapcsolatos további tudnivalókért olvassa el [irányíthatja az események és az üzenetek](concepts-events-routing.md).

* Matchers, a felhasználó által definiált függvények és a szerepkör-hozzárendelések létrehozásával kapcsolatos további tudnivalókért olvassa el a [útmutató a felhasználó által definiált függvények](how-to-user-defined-functions.md).

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png

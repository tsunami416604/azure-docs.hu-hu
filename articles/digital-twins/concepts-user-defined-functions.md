---
title: Adatfeldolgozás és a felhasználó által definiált függvények az Azure digitális Twins |} A Microsoft Docs
description: Adatfeldolgozás, matchers és felhasználó által definiált függvények az Azure digitális Twins áttekintése
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: a45f82b142ee4f4c9c88ea755607b88323feaae5
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210125"
---
# <a name="data-processing-and-user-defined-functions"></a>Adatfeldolgozás és felhasználó által definiált függvények

Az Azure digitális Twins fejlett számítási képességeket kínál. A fejlesztők definiálása és felhasználói függvények beérkező telemetriai üzeneteket küldhet eseményeket előre definiált végpontjainak futtatásához.

## <a name="data-processing-flow"></a>Adatfeldolgozási folyamat

Eszközök telemetriai adatokat küldeni a digitális Twins, miután a fejlesztők hatékonyabbá tételéhez négy fázisban a adatokat tud feldolgozni: _ellenőrzése_, _megfelelő_, _számítási_, és _dispatch_:

![Digitális Twins adatfeldolgozási folyamat][1]

1. A _ellenőrzése_ fázis átalakítja a beérkező telemetriai üzenetet egy közérthető [ **adatátviteli objektumot** ](https://en.wikipedia.org/wiki/Data_transfer_object) formátumban. Ebben a fázisban az eszközök és érzékelők érvényesítési is végez.
1. A _megfelelő_ fázis megkeresi a megfelelő felhasználó által megadott több funkciójának futtatásához. Előre definiált matchers megtalálja a felhasználó által megadott több funkciójának az eszköz-, érzékelő és a bejövő telemetriát üzenet terület adatai alapján.
1. A _számítási_ fázis futtatja a felhasználó által megadott több funkciójának egyezik az előző szakaszban. Ezek több funkciójának előfordulhat, hogy olvasása és frissítése a térbeli gráf csomópontjai kiszámított értékek és is gridre bocsáthatja ki az egyéni értesítések.
1. A _dispatch_ fázis irányítja bármely egyéni értesítéseket állíthat be a számítási fázis, a gráf meghatározva a végpontjai.

## <a name="data-processing-objects"></a>Adatok feldolgozása objektumok

Adatfeldolgozás az Azure digitális Twins áll három objektum meghatározása: _matchers_, _felhasználó által definiált függvények_, és _szerepkör-hozzárendelések_:

![Digitális Twins adatfeldolgozási folyamat][2]

### <a name="matchers"></a>Matchers

_Matchers_ meghatároz egy olyan feltételek, amelyek kiértékelik a milyen művelet végbemegy, bejövő érzékelő telemetriai adatokon alapul. Ezek a feltételek az egyezés meghatározásához lehetnek az érzékelő, a érzékelő szülő eszköz és az érzékelő szülő hely tulajdonságai. A feltételek fejezik összehasonlítások elleni egy [JSON-útvonal](http://jsonpath.com/) az alábbi példában látható módon:

- Az adattípus az összes érzékelő **hőmérséklet**.
- Kellene `01` azok port.
- A kiterjesztett tulajdonság kulccsal amely tartozó eszközök **gyártó** értékre `"GoodCorp"`.
- Amely típusú tárolóhelyek tartozik `"Venue"`.
- Amely leszármazottai szülő **SpaceId** `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`.

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
> - JSON elérési utak-és nagybetűk.
> - A JSON hasznos ugyanaz, mint a rendszer visszaadna adattartalom:
>   - `/sensors/{id}?includes=properties,types` az érzékelő.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` az érzékelő szülő eszközhöz.
>   - `/spaces/{id}?includes=properties,types,location,timezone` az érzékelő szülő terület.
> - A-összehasonlítások megkülönböztetik a kis-és nagybetűket.

### <a name="user-defined-functions"></a>Felhasználó által definiált függvények

A _felhasználó által definiált függvény_, vagy _UDF_, egy egyéni függvény, amely az Azure digitális Twins elkülönített környezeten belül. UDF-EK a nyers érzékelő telemetriai üzenetet hozzáféréssel rendelkezik, mint érkezett, valamint a térbeli graph és az eseményelosztóra szolgáltatás esetleges szabályozási hiányosságok elhárítását. Az UDF-ben a diagramon belül regisztrálása után, adja meg, hogy fusson az UDF-megfeleltetőben megadott (lásd fent) kell létrehozni. Digitális Twins új telemetria egy adott érzékelő kap, ha az egyeztetett UDF kiszámíthatja az elmúlt néhány érzékelőinek mozgóátlagát például.

UDF-EK JavaScript nyelven is, és lehetővé teszi a fejlesztők az érzékelő telemetriai üzeneteket programozhat egyéni kódrészletek végrehajtásához. Is találhatók segédmetódusokat kommunikál a grafikon, a felhasználó által definiált végrehajtási környezetben található. Az UDF-ben a fejlesztők számára a következőket teheti:

- Állítsa be az érzékelő közvetlenül az alakzatot a diagramon belül az érzékelő objektum olvasása.
- Egy műveletet a Graph-címtéren belüli különböző érzékelőinek alapján.
- Hozzon létre egy értesítést, ha bizonyos feltételek teljesülnek, egy bejövő érzékelő olvasása.
- Graph-metaadatok csatolása az érzékelő olvasása előtt értesítés küldésére.

Tekintse meg [User User-Defined funkciók hogyan](how-to-user-defined-functions.md) további részletekért.

### <a name="role-assignment"></a>Szerepkör-kijelölés

Egy UDF műveletek digitális Twins szerepköralapú hozzáférés-vezérlést a szolgáltatáson belül az adatok védelmére vonatkozik. Szerepkör-hozzárendelések győződjön meg arról, hogy egy adott UDF kommunikál a térbeli graph megfelelő engedélyekkel rendelkezik-e. Például egy UDF megpróbálhatja létrehozása, olvasása, frissítése vagy törlése a grafikon adatainak egy adott hely alapján. Hozzáférési szintjét egy UDF jelölőnégyzet be van jelölve az UDF a graph kéri az adatokat, vagy megpróbálja a műveletet. További információkért lásd: [szerepköralapú hozzáférés-vezérlés](security-create-manage-role-assignments.md).

Számára egy egy UDF, amely nem rendelkezik szerepkör hozzárendelésekkel aktiválásához megfeleltetőben megadott lehetőség. Ebben az esetben az UDF sikertelen lesz minden olyan adatokat olvasni a gráf.

## <a name="next-steps"></a>További lépések

Útválasztási események és a telemetriai üzeneteket más Azure-szolgáltatásokkal kapcsolatos további információkért olvassa el [útválasztás események és az üzenetek](concepts-events-routing.md).

Matchers, a felhasználó által definiált függvények és a szerepkör-hozzárendelések létrehozásával kapcsolatos további tudnivalókért olvassa el a [útmutató a felhasználó által definiált függvények](how-to-user-defined-functions.md).

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png

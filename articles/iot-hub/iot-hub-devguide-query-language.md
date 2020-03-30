---
title: Az Azure IoT Hub lekérdezési nyelvének ismertetése | Microsoft dokumentumok
description: Fejlesztői útmutató – az SQL-szerű IoT Hub lekérdezési nyelv leírása, amely az eszköz/modul ikrek és feladatok adatait az IoT hubról olvassa be.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: robinsh
ms.openlocfilehash: ad8b4b39e582d10c2a3b6003bfa07138f4697b71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499189"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>IoT Hub-lekérdezési nyelv az ikereszközökhöz - és modulokhoz, feladatokhoz, valamint az üzenetirányításhoz

Az IoT Hub hatékony SQL-szerű nyelvet biztosít az [ikereszközökkel,](iot-hub-devguide-device-twins.md) [a modulikrekkel,](iot-hub-devguide-module-twins.md) [a feladatokkal](iot-hub-devguide-jobs.md)és az üzenettovábbítással kapcsolatos információk [lekéréséhez.](iot-hub-devguide-messages-d2c.md) Ez a cikk bemutatja:

* Az IoT Hub lekérdezési nyelvének főbb funkcióinak bemutatása, és
* A nyelv részletes leírása. Az üzenettovábbítás lekérdezési nyelvéről az [üzenet-útválasztás lekérdezései ről az üzenettovábbítás lekérdezései](../iot-hub/iot-hub-devguide-routing-query-syntax.md)ben talál.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Eszköz- és moduliker-lekérdezések

[Az ikereszközök](iot-hub-devguide-device-twins.md) és [a modulikrek](iot-hub-devguide-module-twins.md) tetszőleges JSON-objektumokat tartalmazhatnak címkékként és tulajdonságokként. Az IoT Hub lehetővé teszi, hogy lekérdezze az eszköz twins és a modul ikrek egyetlen JSON-dokumentum, amely tartalmazza az összes ikerinformációt.

Tegyük fel például, hogy az IoT hub-eszköz twins rendelkezik a következő struktúra (ikermodul hasonló lenne csak egy további moduleId):

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

### <a name="device-twin-queries"></a>Ikereszköz-lekérdezések

Az IoT Hub az eszköz twins-t egy nevű eszközgyűjteményként teszi **elérhetővé.** A következő lekérdezés például lekéri az eszköztwins teljes készletét:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Az Azure IoT SDK-k támogatják](iot-hub-devguide-sdks.md) a nagy eredmények lapozását.

Az IoT Hub lehetővé teszi az eszköztwins-szűrés tetszőleges feltételekkel történő lekérését. Például az eszközök ikeriknek fogadásához, ahol a **location.region** címke **USA-ra** van állítva, használja a következő lekérdezést:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Logikai operátorok és számtani összehasonlítások is támogatottak. Ha például az Egyesült Államokban található és a telemetriai adatok percenként kisebb küldésére konfigurált eszköztwinseket szeretné lekérni, használja a következő lekérdezést:

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

A kényelmet, az IS lehetséges, hogy a tömb állandók az **IN** és **NIN** (nem) operátorok. Például a WiFi-t vagy vezetékes kapcsolatot bejelentő ikereszközök lekéréséhez használja a következő lekérdezést:

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Gyakran szükséges azonosítani az összes eszköz twins, amelyek egy adott tulajdonságot tartalmaznak. Az IoT Hub `is_defined()` támogatja a függvényt erre a célra. Például a `connectivity` tulajdonságot definiáló ikereszközök lekéréséhez használja a következő lekérdezést:

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

A szűrési lehetőségek teljes hivatkozását a [WHERE záradék](iot-hub-devguide-query-language.md#where-clause) szakaszban található.

A csoportosítás és az összesítések is támogatottak. Például az eszközök számának megkereséséhez az egyes telemetriai konfigurációs állapotok, használja a következő lekérdezést:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
  FROM devices
  GROUP BY properties.reported.telemetryConfig.status
```

Ez a csoportosítási lekérdezés a következő példához hasonló eredményt ad vissza:

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

Ebben a példában három eszköz jelentett sikeres konfiguráció, kettő továbbra is alkalmazza a konfigurációt, és egy jelentett hiba.

A vetítési lekérdezések lehetővé teszik a fejlesztők számára, hogy csak az őket fontos tulajdonságokat adják vissza. Ha például az összes leválasztott eszköz utolsó tevékenységidejét szeretné beolvasni, használja a következő lekérdezést:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Ikermodul-lekérdezések

A modul twins lekérdezése hasonló az ikereszközök lekérdezéséhez, de egy másik gyűjtemény/névtér használatával; **eszközök**helyett az **eszközökről kérdezünk:**

```sql
SELECT * FROM devices.modules
```

Nem engedélyezzük az eszközök és az eszközök.modulok gyűjtemények közötti csatlakozást. Ha azt szeretné, hogy a modul ikrek az eszközök között, akkor azt címkék alapján. Ez a lekérdezés az összes modul ikerpárját adja vissza a beolvasási állapotú összes eszközön:

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

Ez a lekérdezés az összes ikermodult visszaadja a beolvasási állapottal, de csak az eszközök megadott részhalmazán:

```sql
SELECT * FROM devices.modules
  WHERE properties.reported.status = 'scanning'
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>C# példa

A lekérdezési funkciót a **RegistryManager** osztály [C# szolgáltatás SDK](iot-hub-devguide-sdks.md) szolgáltatása teszi elérhetővé.

Íme egy példa egy egyszerű lekérdezésre:

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

A **lekérdezési** objektum példányosított oldalmérettel (legfeljebb 100). Ezután több oldalt a **GetNextAsTwinAsync** metódusok többszöri hívásával kérnek be.

A lekérdezésobjektum a lekérdezés által megkövetelt deszerializálási beállítástól függően több **következő** értéket is elérhetővé teszi. Például az iker- vagy feladatobjektumok, illetve a kivetítések használatakor sima JSON.

### <a name="nodejs-example"></a>Példa a Node.js

A lekérdezési funkció a [Rendszerleíróadatbázis-objektumban az Azure IoT szolgáltatás SDK-j.js](iot-hub-devguide-sdks.md) szolgáltatása által elérhetővé **teszi.**

Íme egy példa egy egyszerű lekérdezésre:

```javascript
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

A **lekérdezési** objektum példányosított oldalmérettel (legfeljebb 100). Ezután több oldalt is beolvas a **következőAsTwin** metódus többszöri hívásával.

A lekérdezésobjektum a lekérdezés által megkövetelt deszerializálási beállítástól függően több **következő** értéket is elérhetővé teszi. Például az iker- vagy feladatobjektumok, illetve a kivetítések használatakor sima JSON.

### <a name="limitations"></a>Korlátozások

> [!IMPORTANT]
> A lekérdezési eredmények néhány perc késleltetéssel rendelkezhetnek az ikereszközök legújabb értékeihez képest. Ha az egyes eszközök ikerpárjait kérdezi le azonosító szerint, használja a [get twin REST API-t.](https://docs.microsoft.com/rest/api/iothub/service/twin/getdevicetwin) Ez az API mindig a legújabb értékeket adja vissza, és magasabb sávszélesség-szabályozási korlátokkal rendelkezik. Közvetlenül is kiadhatja a REST API-t, vagy használhatja az [azure IoT Hub-szolgáltatás SDK-inak](iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks)egyikében az egyenértékű funkciókat.

Jelenleg az összehasonlítások csak primitív típusok (nincs `... WHERE properties.desired.config = properties.reported.config` objektum) között támogatottak, például csak akkor támogatottak, ha ezek a tulajdonságok primitív értékekkel rendelkeznek.

## <a name="get-started-with-jobs-queries"></a>Első lépések a feladatlekérdezésekkel

[A feladatok](iot-hub-devguide-jobs.md) lekínálják a műveletek végrehajtását az eszközök készletein. Minden ikereszköz tartalmazza azoknak a feladatoknak az adatait, amelyeknek részét képezi a **feladatok**nak nevezett gyűjteményben.

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

Jelenleg ez a gyűjtemény **lekérdezhető,** mint devices.jobs az IoT Hub lekérdezési nyelv.

> [!IMPORTANT]
> Jelenleg a feladatok tulajdonság soha nem adja vissza, amikor az eszköz twins lekérdezése. Ez azt illeti, a "FROM eszközöket" tartalmazó lekérdezések. A feladatok tulajdonság a használatával közvetlenül `FROM devices.jobs`érhető el a lekérdezésekkel.
>
>

Ha például az összes (múltbeli és ütemezett) feladatot szeretné lekérni, amelyek egyetlen eszközt érintenek, a következő lekérdezést használhatja:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

Vegye figyelembe, hogy ez a lekérdezés biztosítja az eszköz-specifikus állapotát (és esetleg a közvetlen metódus válasz) az egyes visszaadott feladat.

Lehetőség van tetszőleges logikai feltételekkel szűrni a **devices.jobs** gyűjtemény összes objektumtulajdonságára.

Például egy adott eszközhöz 2016 szeptembere után létrehozott összes befejezett ikerfrissítési feladat lekéréséhez használja a következő lekérdezést:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Egyetlen feladat eszközönkénti eredményeit is lekérheti.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Korlátozások

A **devices.jobs** kapcsolatos lekérdezéseket jelenleg nem támogatják:

* Előrejelzések, ezért `SELECT *` csak akkor lehetséges.
* Feltételek, amelyek hivatkoznak az eszköz iker mellett a feladat tulajdonságait (lásd az előző szakaszban).
* Összesítések végrehajtása, például darabszám, átlag, csoportosítás.

## <a name="basics-of-an-iot-hub-query"></a>Az IoT Hub-lekérdezés alapjai

Minden IoT Hub-lekérdezés SELECT és FROM záradékokból áll, választható WHERE és GROUP BY záradékokkal. Minden lekérdezés JSON-dokumentumok gyűjteményén fut, például az eszköz twins. A FROM záradék azt jelzi, hogy a dokumentumgyűjtemény **(eszközök,** **devices.modules**vagy **devices.jobs)** legyen. Ezután a WHERE záradékban lévő szűrőt alkalmazza a rendszer. Összesítések esetében ennek a lépésnek az eredményei a GROUP BY záradékban meghatározottak szerint vannak csoportosítva. Minden csoporthoz egy sor jön létre a SELECT záradékban megadottak szerint.

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>FROM záradék

A **FROM <from_specification>** záradék csak három értéket feltételezhet: **FROM-eszközök** az ikereszközök lekérdezéséhez, FROM **devices.modules** a module twins lekérdezéséhez, vagy **A FROM devices.jobs** az eszközönkénti feladat részleteinek lekérdezéséhez.

## <a name="where-clause"></a>WHERE záradék

A **WHERE <filter_condition>** záradék nem kötelező. Egy vagy több olyan feltételt határoz meg, amelyeknek a FROM-gyűjteményben szereplő JSON-dokumentumoknak meg kell felelniük ahhoz, hogy az eredmény részét vegyék fel. Minden JSON-dokumentumnak ki kell értékelnie a megadott feltételeket "true"-ra, hogy az eredmény bekerüljon.

Az engedélyezett feltételeket a [Kifejezések és feltételek](iot-hub-devguide-query-language.md#expressions-and-conditions)című szakasz ismerteti.

## <a name="select-clause"></a>SELECT záradék

A **SELECT <select_list>** kötelező, és megadja, hogy milyen értékeket olvas be a lekérdezés. Az új JSON-objektumok létrehozásához használandó JSON-értékeket határozza meg.
A FROM gyűjtemény szűrt (és opcionálisan csoportosított) részhalmazának minden eleme esetében a vetítési fázis új JSON-objektumot hoz létre. Ez az objektum a SELECT záradékban megadott értékekkel épül fel.

A SELECT záradék nyelvtana a következő:

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**Attribute_name** a FROM gyűjteményjében lévő JSON-dokumentum bármely tulajdonságára utal. Néhány példa a SELECT záradékok találhatók az első lépések az eszköz iker lekérdezések szakaszban található.

Jelenleg a **SELECT***-tól eltérő kijelölési záradékok csak az ikereszközök összesített lekérdezései ben támogatottak.

## <a name="group-by-clause"></a>GROUP BY záradék

A **GROUP BY <group_specification>** záradék egy választható lépés, amely a WHERE záradékban megadott szűrő után és a SELECT-ben megadott vetület előtt hajt ható. A dokumentumokat egy attribútum értéke alapján csoportosítja. Ezek a csoportok a SELECT záradékban megadott összesített értékek létrehozására szolgálnak.

A GROUP BY használatával egy lekérdezés re...

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

A GROUP BY formális szintaxisa:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** a FROM gyűjteményjében lévő JSON-dokumentum bármely tulajdonságára utal.

Jelenleg a GROUP BY záradék csak az ikereszközök lekérdezésekénekének esetén támogatott.

> [!IMPORTANT]
> A `group` kifejezés jelenleg speciális kulcsszóként van kezelve a lekérdezésekben. Abban az esetben, ha a tulajdon nevét használja, `group` fontolja meg a körülvevő `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'`két zárójelben a hibák elkerülése érdekében, pl. .
>

## <a name="expressions-and-conditions"></a>Kifejezések és feltételek

Magas szinten egy *kifejezés:*

* JSON-típusú példány (például logikai, szám, karakterlánc, tömb vagy objektum) kiértékelése.
* Az eszközJSON-dokumentumból és állandókból érkező adatok módosítása határozza meg a beépített operátorok és függvények használatával.

*A feltételek* olyan kifejezések, amelyek logikai értéket értékelnek ki. A logikai **igaztól** eltérő állandó **hamisnak**minősül. Ez a szabály **null ,** **undefined**, bármely objektum- vagy tömbpéldányt, karakterláncot és logikai **hamis**értéket tartalmaz.

A kifejezések szintaxisa a következő:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

Ha meg szeretné tudni, hogy a kifejezésszintaxisban szereplő egyes szimbólumok mit jelképeznek, tekintse meg az alábbi táblázatot:

| Szimbólum | Meghatározás |
| --- | --- |
| attribute_name | A From **gyűjteményben** lévő JSON-dokumentum bármely tulajdonsága. |
| binary_operator | Bármely bináris operátor, amely az [Operátorok](#operators) részben szerepel. |
| function_name| A [Funkciók](#functions) szakaszban felsorolt bármely függvény. |
| decimal_literal |Decimális jelöléssel kifejezett úszó. |
| hexadecimal_literal |A "0x" karakterláncmal kifejezett szám, amelyet hexadecimális számjegyek sorozata követ. |
| string_literal |A karakterlánc-konstansok olyan Unicode karakterláncok, amelyeket nulla vagy több Unicode karakter vagy escape-sorozat jelöl. A karakterlánc-konstansok egyszeres idézőjelekvagy idézőjelek közé vannak ágyazva. Megengedett menekülési `\'` `\"`útvonalak: , , `\\`, `\uXXXX` a 4 hexadecimális számjegy által definiált Unicode karakterek esetében. |

### <a name="operators"></a>Operátorok

A következő operátorok támogatottak:

| Family (Család) | Operátorok |
| --- | --- |
| Számtani |+, -, *, /, % |
| Logikai |ÉS, VAGY, NEM |
| Összehasonlítás |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Functions

Az ikrek és a feladatok lekérdezésekénések során az egyetlen támogatott függvény a következő:

| Függvény | Leírás |
| -------- | ----------- |
| IS_DEFINED(tulajdonság) | Logikai értéket ad vissza, amely azt jelzi, `null`hogy a tulajdonsághoz hozzá van-e rendelve egy érték (beleértve). |

Útvonalak esetén a következő matematikai függvények támogatottak:

| Függvény | Leírás |
| -------- | ----------- |
| ABS(x) | A megadott numerikus kifejezés abszolút (pozitív) értékét adja eredményül. |
| EXP(x) | A megadott numerikus kifejezés (e^x) exponenciális értékét adja eredményül. |
| POWER(x;y) | A megadott kifejezés értékét adja vissza a megadott teljesítményre (x^y).|
| NÉGYZET(x)    | A megadott numerikus érték négyzetét adja eredményül. |
| PLAFON(x) | A megadott numerikus kifejezésnél nagyobb vagy azzal egyenlő legkisebb egész értéket adja eredményül. |
| PADLÓ(x) | A megadott numerikus kifejezésnél kisebb vagy azzal egyenlő legnagyobb egész szám értéket adja eredményül. |
| JEL(x) | A megadott numerikus kifejezés pozitív (+1), nulla (0) vagy negatív (-1) előjele.|
| GYÖK(x) | A megadott numerikus érték négyzetgyökét adja eredményül. |

Útvonalak esetén a következő típusellenőrzési és öntési funkciók támogatottak:

| Függvény | Leírás |
| -------- | ----------- |
| AS_NUMBER | A bemeneti karakterláncot számmá alakítja. `noop`ha a bemenet szám; `Undefined` ha a karakterlánc nem jelent számot.|
| IS_ARRAY | Logikai értéket ad eredményül, amely azt jelzi, hogy a megadott kifejezés típusa tömb-e. |
| IS_BOOL | Logikai értéket ad eredményül, amely azt jelzi, hogy a megadott kifejezés típusa logikai érték-e. |
| IS_DEFINED | Logikai értéket ad vissza, amely azt jelzi, hogy a tulajdonsághoz érték van-e hozzárendelve. |
| IS_NULL | Logikai értéket ad eredményül, amely azt jelzi, hogy a megadott kifejezés típusa null értékű-e. |
| IS_NUMBER | Logikai értéket ad eredményül, amely azt jelzi, hogy a megadott kifejezés típusa szám-e. |
| IS_OBJECT | Logikai értéket ad eredményül, amely azt jelzi, hogy a megadott kifejezés típusa JSON-objektum-e. |
| IS_PRIMITIVE | Logikai értéket ad eredményül, amely azt jelzi, hogy a megadott kifejezés típusa `null`primitív (karakterlánc, logikai, numerikus vagy ). |
| IS_STRING | Logikai értéket ad eredményül, amely azt jelzi, hogy a megadott kifejezés típusa karakterlánc-e. |

Útvonalak esetén a következő karakterlánc-függvények támogatottak:

| Függvény | Leírás |
| -------- | ----------- |
| CONCAT(x; y, ...) | Olyan karakterláncot ad vissza, amely két vagy több karakterlánc-érték összefűzésének eredménye. |
| HOSSZ(x) | A megadott karakterlánc-kifejezés karaktereinek számát adja eredményül.|
| KISBETŰ(x) | Karakterlánc-kifejezést ad vissza, miután a nagybetűs karakteradatokat kisbetűssé alakította. |
| NAGYBETŰS(x) | A kisbetűs karakteradatok nagybetűssé alakítása után karakterlánc-kifejezést ad eredményül. |
| KARAKTERLÁNC(karakterlánc; kezdő [, hossz]) | A megadott karakternulla alapú pozícióval kezdődő karakterlánc-kifejezés egy részét adja eredményül, és a megadott hosszig vagy a karakterlánc végéig folytatódik. |
| INDEX_OF(karakterlánc, töredék) | Az első megadott karakterlánc-kifejezésen belül a második karakterlánc-kifejezés első előfordulásának kezdő helyét adja eredményül, vagy -1 értéket, ha a karakterlánc nem található.|
| STARTS_WITH(x, y) | Logikai értéket ad vissza, amely azt jelzi, hogy az első karakterlánc-kifejezés a másodikkal kezdődik-e. |
| ENDS_WITH(x; y) | Logikai értéket ad vissza, amely azt jelzi, hogy az első karakterlánc-kifejezés a másodikkal végződik-e. |
| Tartalmaz(x;y) | Logikai értéket ad vissza, amely azt jelzi, hogy az első karakterlánc-kifejezés tartalmazza-e a másodikat. |

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan hajthat végre lekérdezéseket az alkalmazásokban az [Azure IoT SDK-k](iot-hub-devguide-sdks.md)használatával.
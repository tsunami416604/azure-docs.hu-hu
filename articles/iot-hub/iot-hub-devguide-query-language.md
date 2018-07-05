---
title: Az Azure IoT Hub lekérdezési nyelv ismertetése |} A Microsoft Docs
description: Fejlesztői útmutató – a leírását az SQL-szerű IoT Hub lekérdezési nyelvet, az IoT hub eszköz/ikermodulokkal és feladatok kapcsolatos információk olvashatók be.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: elioda
ms.openlocfilehash: 321d70a04e3c524e578a01e8531d63733d088c3f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444184"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>IoT Hub lekérdezési nyelv az eszköz és a modul twins, feladatokkal és üzenet-útválasztása

IoT Hub által biztosított információk lekéréséhez hatékony SQL-szerű nyelv kapcsolatos [ikereszközök] [ lnk-twins] és [feladatok][lnk-jobs], és [üzenet-útválasztása][lnk-devguide-messaging-routes]. Ez a cikk bemutatja:

* Az IoT Hub lekérdezési nyelv, a fő funkciókat bemutató és
* A nyelv részletes leírása.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Eszköz- és modul ikereszköz-lekérdezések
[Ikereszközök] [ lnk-twins] és ikermodulokkal tetszőleges JSON-objektumok címkék és tulajdonságok is tartalmazhat. Az IoT Hub lehetővé teszi lekérdezések ikereszközök és ikermodulokkal JSON-dokumentumként egyetlen összes ikereszköz-adatokat tartalmazó.
Tegyük fel például, hogy az IoT hub device twins az alábbi struktúrával rendelkeznek (ikermodul lenne ehhez hasonló csak, egy további moduleId):

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

Az IoT Hub az ikereszközök mutatja egy dokumentum egy dokumentumgyűjteményben nevű **eszközök**.
Ezért az alábbi lekérdezés lekéri az ikereszközök teljes készletét:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Az Azure IoT SDK-k] [ lnk-hub-sdks] támogatja a nagy eredményben való lapozást.

Az IoT Hub lehetővé teszi tetszőleges feltételekkel szűrés ikereszközök lekéréséhez. A például az eszköz fogadni párok helyét a **location.region** címke értéke **USA** használja a következő lekérdezést:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Logikai operátorok és aritmetikai összehasonlítások is támogatott. Például lekérdezni az eszközt twins az Egyesült Államokban találhatók, és konfigurálta a telemetriai adatok küldése a kisebb, mint minden percben használja a következő lekérdezést:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Könnyebb áttekinthetőség érdekében lehetőség arra is tömb állandókat a használatára a **IN** és **nA** (nem a) operátorok. Például lekéréséhez, amelyek a Wi-Fi-vagy vezetékes kapcsolati ikereszközök használja a következő lekérdezést:

```sql
SELECT * FROM devices
WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Gyakran szükség egy adott tulajdonságot tartalmazó összes ikereszközök azonosításához. IoT Hub által támogatott, a függvény `is_defined()` erre a célra. Lekérése ikereszközök, amelyek meghatározzák, például a `connectivity` tulajdonságot használja a következő lekérdezést:

```SQL
SELECT * FROM devices
WHERE is_defined(properties.reported.connectivity)
```

Tekintse meg a [WHERE záradék] [ lnk-query-where] a szűrési képességek a teljes vonatkozó szakaszában.

Csoportosítás és összesítés is támogatottak. Például eszközök számát is minden egyes telemetriai adatokat a konfigurációs állapot használja a következő lekérdezést:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Ez a csoportosítás a lekérdezés eredménye termékazonosítóhoz az alábbi példához hasonló:

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

Ebben a példában három eszközöket jelentett sikeres konfigurációhoz, két továbbra is a konfiguráció alkalmazását, és egy hibát jelzett.

A leképezési lekérdezésekre lehetővé teszik a fejlesztők számára, hogy csak a érdeklő tulajdonságokat adja vissza. Például beolvasni az összes legutóbbi tevékenység leválasztott eszközöket használja a következő lekérdezést:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>A modul ikereszköz-lekérdezések

A lekérdezés: ikereszközök lekérdezése az ikermodulokkal hasonlít, de egy másik gyűjtemény/névteret, azaz "az eszközök" helyett használatával lekérdezheti, ha

```sql
SELECT * FROM devices.modules
```

Hogy az eszközök és devices.modules gyűjtemények közötti illesztési nem teszik lehetővé. Ha szeretne lekérdezés ikermodulokkal eszközök között, ezt megteheti címkék alapján. Ez a lekérdezés összes eszközön vizsgálati állapotú összes ikermodulokkal adja vissza:

```sql
Select * from devices.modules where properties.reported.status = 'scanning'
```

Ez a lekérdezés összes ikermodulokkal vizsgálati állapotú, de csak a megadott eszközök részhalmaza adja vissza.

```sql
Select * from devices.modules where properties.reported.status = 'scanning' and deviceId IN ('device1', 'device2')  
```

### <a name="c-example"></a>C#-példa
A lekérdezési funkciókat tesz elérhetővé a [C# Szolgáltatásoldali SDK-val] [ lnk-hub-sdks] a a **RegistryManager** osztály.
Íme egy példa egy egyszerű lekérdezést:

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

A **lekérdezés** egy oldal méretét (legfeljebb 100) az objektum létrejön. Több oldal blobnevet meghívásával, majd a **GetNextAsTwinAsync** módszerek több alkalommal.

A lekérdezési objektummal mutatja meg több **tovább** értékek, a lekérdezés által igényelt deszerializálás függően. Például eszköz ikereszköz vagy a feladat objektumot, vagy egyszerű JSON leképezések használata esetén.

### <a name="nodejs-example"></a>NODE.js-példa
A lekérdezési funkciókat tesz elérhetővé a [node.js-hez készült Azure IoT szolgáltatás SDK] [ lnk-hub-sdks] a a **beállításjegyzék** objektum.
Íme egy példa egy egyszerű lekérdezést:

```nodejs
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

A **lekérdezés** egy oldal méretét (legfeljebb 100) az objektum létrejön. Több oldal blobnevet meghívásával, majd a **nextAsTwin** metódus több alkalommal.

A lekérdezési objektummal mutatja meg több **tovább** értékek, a lekérdezés által igényelt deszerializálás függően. Például eszköz ikereszköz vagy a feladat objektumot, vagy egyszerű JSON leképezések használata esetén.

### <a name="limitations"></a>Korlátozások

> [!IMPORTANT]
> Lekérdezési eredmények ikereszközök lehet néhány perc alatt késleltetés garanciát a legújabb értékeket. Lekérdezés azonosítója alapján egyedi ikereszközök, ha a lekérési eszköz ikereszköz API-val. Az API-t mindig a legújabb értékeket tartalmaz, és rendelkezik a magasabb szabályozási korlátait.

Jelenleg összehasonlítások támogatottak csak egyszerű típusok (nincs objektum) között például `... WHERE properties.desired.config = properties.reported.config` akkor támogatott, csak akkor, ha azokat a tulajdonságokat a primitív értékek találhatók.

## <a name="get-started-with-jobs-queries"></a>Feladatok lekérdezések használatának első lépései

[Feladatok] [ lnk-jobs] teszik lehetővé az eszközök csoportokon műveletek végrehajtásához. Minden egyes ikereszköz tartalmazza, amely egy nevű gyűjtemény részét képezi a feladatok **feladatok**.
Logikailag,

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

Ez a gyűjtemény jelenleg információkat **devices.jobs** az IoT hub lekérdezési nyelv.

> [!IMPORTANT]
> Jelenleg a feladatok tulajdonság soha nem adja vissza ikereszközök lekérdezésekor. Azaz "ESZKÖZÖKRŐL" tartalmazó lekérdezések. A feladatok tulajdonság csak közvetlenül a lekérdezések használatával érhetők el `FROM devices.jobs`.
>
>

Például minden feladat (elmúlt és ütemezett) egy adott eszköz érintő lekéréséhez használhatja a következő lekérdezést:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
```

Vegye figyelembe, hogyan nyújt ez a lekérdezés az eszközspecifikus állapota (és esetleg a közvetlen metódusra adott válasz) minden feladatot adott vissza.
Az összes objektum tulajdonságai tetszőleges logikai feltételekkel szűrése lehetőség arra is a **devices.jobs** gyűjtemény.
Az összes befejezett iker frissítése eszközfeladatok egy adott eszközhöz létrehozott követően, 2016. szeptember lekéréséhez például használja a következő lekérdezést:

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Egy feladat eszközönkénti eredményeit is lekérhet.

```sql
SELECT * FROM devices.jobs
WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Korlátozások
Jelenleg a lekérdezések **devices.jobs** nem támogatják:

* Leképezések, ezért csak `SELECT *` lehetséges.
* Tekintse meg az ikereszközön (lásd az előző szakaszban) feladat tulajdonságai mellett feltételeknek.
* Összesítések, például a száma, avg, csoportosítás hajt végre.

## <a name="device-to-cloud-message-routes-query-expressions"></a>Eszközről-a-felhőbe üzenetútvonalai lekérdezési kifejezések

Használatával [eszközről a felhőbe útvonalak][lnk-devguide-messaging-routes], konfigurálhatja úgy az IoT Hub tart elküldeni a különböző végpontok eszköz – felhő üzeneteket. Zahájeno szemben az egyes üzenetek értékelt kifejezések alapján történik.

Az útvonal [feltétel] [ lnk-query-expressions] az ugyanazon az IoT Hub lekérdezési nyelv használja, mint az ikereszköz és a feladat lekérdezések feltételek. A fejlécek és törzs útvonal feltételek értékeli ki. Az útválasztási lekérdezési kifejezés csak üzenetfejlécekben is csak az üzenettörzs vagy mindkettőt. Az IoT Hub feltételezi, hogy egy adott séma a fejlécek és az üzenettörzs üzenetek irányítása érdekében. A következő szakaszok ismertetik, hogy mire szükség, az IoT Hub megfelelően továbbítani.

### <a name="routing-on-message-headers"></a>Fejlécek az Útválasztás

Az IoT Hub a következő JSON-reprezentációja az üzenet-útválasztása üzenetfejlécekben feltételezi:

```json
{
  "message": {
    "systemProperties": {
      "contentType": "application/json",
      "contentEncoding": "utf-8",
      "iothub-message-source": "deviceMessages",
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z"
    },
    "appProperties": {
      "processingPath": "<optional>",
      "verbose": "<optional>",
      "severity": "<optional>",
      "testDevice": "<optional>"
    },
    "body": "{\"Weather\":{\"Temperature\":50}}"
  }
}
```

Üzenet Rendszertulajdonságok van fűzve előtagként a `'$'` szimbólum.
Felhasználói tulajdonságok, amelyek neve mindig érhetők el. Ha egy felhasználó tulajdonságnév időpontjával rendszer tulajdonsággal rendelkező (például `$contentType`), a felhasználó tulajdonság kérhető le a `$contentType` kifejezés.
Mindig elérheti a zárójelek használatával rendszertulajdonság `{}`: például használhatja a kifejezés `{$contentType}` eléréséhez a rendszer tulajdonság `contentType`. A tulajdonságnevek zárójeles mindig kérik le a rendszer tulajdonsága.

Ne feledje, hogy tulajdonságnevek megkülönböztetik a kis-és nagybetűket.

> [!NOTE]
> Összes üzenet tulajdonság olyan karakterláncok. Rendszertulajdonságok leírtak szerint a [fejlesztői útmutató][lnk-devguide-messaging-format], jelenleg nem használható lekérdezésekben.
>

Ha például egy `messageType` tulajdonságot használja, érdemes lehet az összes telemetriai átirányítása egy végpontot, és a egy másik végpontra minden riasztás. Írhat irányíthatja a telemetriát a következő kifejezést:

```sql
messageType = 'telemetry'
```

És a figyelmeztető üzenetek a következő kifejezést:

```sql
messageType = 'alert'
```

A logikai kifejezéseket és funkciók is támogatottak. Ez a funkció lehetővé teszi, hogy megkülönböztethetők egymástól a súlyossági szintet, például:

```sql
messageType = 'alerts' AND as_number(severity) <= 2
```

Tekintse meg a [kifejezés és kikötések] [ lnk-query-expressions] támogatott operátorok és funkciók teljes listáját szakaszában.

### <a name="routing-on-message-bodies"></a>Az üzenet törzse az Útválasztás

Az IoT Hub csak irányíthatja üzenettörzs alapján tartalmát, ha az üzenet törzse nem megfelelően formázott JSON-kódolású UTF-8, UTF-16 vagy UTF-32. Üzenet a tartalomtípus beállítása `application/json`. Állítsa be a tartalmat, a támogatott UTF kódolást az üzenetfejlécekben egyik kódolást. Ha a fejlécek vagy nincs megadva, az IoT Hub nem kísérli meg bármely szemben az üzenet törzsében is érintő lekérdezési kifejezés kiértékelése. Ha nem az üzenet egy JSON-üzenetet, vagy ha az üzenet nem határoz meg a tartalom típusa és a tartalomkódolás, továbbra is használhatja az üzenetirányítással, továbbítsa az üzenetet a fejlécek alapján.

Az alábbi példa bemutatja, hogyan hozhat létre egy üzenetet egy megfelelően formázott és kódolt JSON-törzse:

```csharp
string messageBody = @"{ 
                            ""Weather"":{ 
                                ""Temperature"":50, 
                                ""Time"":""2017-03-09T00:00:00.000Z"", 
                                ""PrevTemperatures"":[ 
                                    20, 
                                    30, 
                                    40 
                                ], 
                                ""IsEnabled"":true, 
                                ""Location"":{ 
                                    ""Street"":""One Microsoft Way"", 
                                    ""City"":""Redmond"", 
                                    ""State"":""WA"" 
                                }, 
                                ""HistoricalData"":[ 
                                    { 
                                    ""Month"":""Feb"", 
                                    ""Temperature"":40 
                                    }, 
                                    { 
                                    ""Month"":""Jan"", 
                                    ""Temperature"":30 
                                    } 
                                ] 
                            } 
                        }"; 
 
// Encode message body using UTF-8 
byte[] messageBytes = Encoding.UTF8.GetBytes(messageBody); 
 
using (var message = new Message(messageBytes)) 
{ 
    // Set message body type and content encoding. 
    message.ContentEncoding = "utf-8"; 
    message.ContentType = "application/json"; 
 
    // Add other custom application properties.  
    message.Properties["Status"] = "Active";    
 
    await deviceClient.SendEventAsync(message); 
}
```

Használhat `$body` továbbítsa az üzenetet, a lekérdezési kifejezésben. Használhatja egy egyszerű törzs hivatkozást, törzs tömb hivatkozás vagy több szervezet hivatkozást a lekérdezési kifejezésben. A lekérdezési kifejezés kombinálhatja a szervezet odkazu s parametrem üzenet fejléc hivatkozást is. Ha például a következők az összes érvényes lekérdezési kifejezések:

```sql
$body.Weather.HistoricalData[0].Month = 'Feb'
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled
length($body.Weather.Location.State) = 2
$body.Weather.Temperature = 50 AND Status = 'Active'
```

## <a name="basics-of-an-iot-hub-query"></a>Az IoT Hub lekérdezési alapjai
Minden IoT Hub lekérdezési válassza ki és záradékok esetén nem kötelező hol és a GROUP BY záradékot tartalmaz. Minden lekérdezés fut, a JSON-dokumentumok, például az ikereszközök gyűjteménye. A FROM záradék azt jelzi, hogy a dokumentum egy dokumentumgyűjteményben, meg kell iterálni (**eszközök** vagy **devices.jobs**). Ezt követően a WHERE záradékban a szűrő alkalmazása. Az összesítéseket, az eredményeket az ebben a lépésben csoportosítva vannak benne a GROUP BY záradékban megadott. Minden csoport jön létre egy sort a SELECT záradékban megadott.

```sql
SELECT <select_list>
FROM <from_specification>
[WHERE <filter_condition>]
[GROUP BY <group_specification>]
```

## <a name="from-clause"></a>FROM záradékban
A **< from_specification > a** záradék feltételezheti, hogy csak két értéket: **ESZKÖZÖKRŐL** a lekérdezés ikereszközök, vagy **devices.jobs a** lekérdezés feladat eszközönkénti részletek.

## <a name="where-clause"></a>WHERE záradék
A **ahol < filter_condition >** záradék használata nem kötelező. Azt is meghatározza, hogy a JSON-dokumentumok a KIINDULÓ gyűjtemény egy vagy több feltételt meg kell felelniük az eredmény része. Bármely JSON-dokumentumok a megadott feltételeknek, a "true", az eredmény szerepeltetni kell kiértékelni.

Az engedélyezett feltételek a részben ismertetett [kifejezések és feltételekkel][lnk-query-expressions].

## <a name="select-clause"></a>SELECT záradék
A **VÁLASSZA < select_list >** megadása kötelező, és adja meg, milyen értékeket a rendszer lekéri a lekérdezést. Azt adja meg az új JSON-objektumok létrehozásához használt JSON-értékeit.
A szűrt (és igény szerint csoportosítva) részhalmazát alkotják, a KIINDULÓ gyűjtemény összes eleme a leképezés fázis egy új JSON-objektumot állít elő. Ez az objektum a SELECT záradékban megadott értékek úgy van felépítve.

Következő a nyelvtani, a SELECT záradék:

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

**Attribute_name** a JSON-dokumentum a KIINDULÓ gyűjtemény tulajdonságra sem hivatkozik. Néhány példa a SELECT záradékban található a [ikereszköz-lekérdezések használatának első lépései] [ lnk-query-getstarted] szakaszban.

Jelenleg kijelölt záradékok eltérő **kiválasztása*** csak az ikereszközök összesített lekérdezéseket támogat.

## <a name="group-by-clause"></a>GROUP BY záradék
A **GROUP BY < group_specification >** (nem kötelező), amely végrehajtja a után a szűrő megadva a WHERE záradékban, és mielőtt a leképezésben megadott SELECT záradékban. Azt a dokumentumok egy attribútum alapján csoportosítja. A csoportok használatban vannak a SELECT záradékban megadott összesített értékeket létrehozásához.

Például egy lekérdezés GROUP BY használatával a következő:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

A formális GROUP BY szintaxisa:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** a JSON-dokumentum a KIINDULÓ gyűjtemény tulajdonságra sem hivatkozik.

A GROUP BY záradék jelenleg csak támogatott ikereszközök lekérdezésekor.

## <a name="expressions-and-conditions"></a>Kifejezések és feltételekkel
Magas szinten egy *kifejezés*:

* A JSON típusú (például a logikai érték, szám, karakterlánc, tömb vagy objektum) kifejezés.
* Határozza meg az eszköz JSON-dokumentumok és a beépített operátorok és -függvények használatával állandók érkező adatok kezelését.

*Feltételek* kifejezések, amelyek egy logikai érték. Minden olyan logikai érték eltér állandó **igaz** minősül **hamis**. Ez a szabály tartalmazza **null**, **nem definiált**, tetszőleges objektum vagy tömb példányra, bármilyen karakterlánc és a logikai **hamis**.

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

Szeretné megtudni, mi minden szimbólum a kifejezések szintaxisát a rövidítése, tekintse meg a következő táblázatban:

| Szimbólum | Meghatározás |
| --- | --- |
| attribute_name | A JSON-dokumentum, a tulajdonságokat a **FROM** gyűjtemény. |
| binary_operator | Bármilyen bináris operátor szerepel a [operátorok](#operators) szakaszban. |
| function_name| Minden függvény szerepel a [funkciók](#functions) szakaszban. |
| decimal_literal |A lebegőpontos értékké decimális jelölés kifejezve. |
| hexadecimal_literal |Egy szám hexadecimális számjegyből álló karakterlánc követ ' 0 x"karakterlánc kifejezve. |
| string_literal |Karakterlánc-literálnak sorozata, nulla vagy több Unicode-karaktereket vagy escape-karaktersorozatokat által képviselt Unicode karakterláncokat is. Karakterlánc-literálnak aposztrófok és idézőjelek közé vannak. Kilépés engedélyezett: `\'`, `\"`, `\\`, `\uXXXX` a 4 hexadecimális számjegy által meghatározott Unicode-karaktereket. |

### <a name="operators"></a>Operátorok
A következő operátor használata támogatott:

| Család | Operátorok |
| --- | --- |
| Aritmetikai |+, -, *, /, % |
| Logikai |ÉS, VAGY SEM |
| Összehasonlítás |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Functions
Ha a lekérdezése az ikrekhez és a feladatok az egyetlen támogatott függvény lesz:

| Függvény | Leírás |
| -------- | ----------- |
| IS_DEFINED(property) | Ha a tulajdonság hozzá lett rendelve egy érték jelző logikai érték (beleértve a `null`). |

Az útvonalak feltételek a következő matematikai függvények támogatottak:

| Függvény | Leírás |
| -------- | ----------- |
| ABS(x) | A megadott numerikus kifejezés (pozitív) abszolút értékét adja vissza. |
| EXP(x) | Az exponenciális a megadott numerikus kifejezés értékét adja eredményül (e ^ x). |
| Power(x,y) | A megadott kifejezés értékét adja vissza a megadott hatványra (x ^ y).|
| Square(x) | A megadott számérték négyzetét adja vissza. |
| CEILING(x) | A legkisebb egész értéket ad vissza, nagyobb vagy egyenlő a megadott numerikus kifejezés. |
| FLOOR(x) | Visszaadja a legnagyobb egész szám kisebb vagy egyenlő a megadott numerikus kifejezés. |
| SIGN(x) | A pozitív (+ 1), a nulla (0) vagy a megadott numerikus kifejezés mínuszjel (-1) adja vissza.|
| Sqrt(x) | A megadott numerikus érték négyzetgyökét adja vissza. |

Az útvonalak feltételek a következő típus ellenőrzése és a döntő függvény támogatottak:

| Függvény | Leírás |
| -------- | ----------- |
| AS_NUMBER | A bemeneti karakterláncot számmá alakít. `noop` Ha a bemenet egy szám; `Undefined` Ha karakterlánc nem jelent meg egy számot.|
| IS_ARRAY | Egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa egy tömböt ad vissza. |
| IS_BOOL | Egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa egy logikai érték visszaadása. |
| IS_DEFINED | Jelzi, ha a tulajdonság hozzá lett rendelve egy érték logikai érték beolvasása. |
| IS_NULL | Adja vissza egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa null. |
| IS_NUMBER | Egy logikai értéket, amely azt jelzi, ha a megadott kifejezés típusa egy számot ad vissza. |
| IS_OBJECT | Egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa egy JSON-objektumot ad vissza. |
| IS_PRIMITIVE | Visszaad egy logikai értéket, amely azt jelzi, ha a megadott kifejezés típusa egy primitívet (karakterlánc, szám, logikai vagy `null`). |
| IS_STRING | Egy logikai érték, amely azt jelzi, ha a megadott kifejezés típusa egy karakterláncot ad vissza. |

Az útvonalak feltételek a következő karakterlánc-függvények támogatottak:

| Függvény | Leírás |
| -------- | ----------- |
| CONCAT (x, y,...) | Legalább két karakterlánc-értékek összetűzésének eredménye karakterláncként adja vissza. |
| LENGTH(x) | A megadott karakterlánc-kifejezés karakterek számát adja vissza.|
| Lower(x) | Egy karakterlánc-kifejezés nagybetűt adatok átalakítása kisbetűvé után adja vissza. |
| Upper(x) | Egy karakterlánc-kifejezés után kisbetűt adatok nagybetűssé alakításával adja vissza. |
| KARAKTERLÁNCRÉSZLET (karakterlánc, kezdő [, hossz]) | Már a megadott karakter számolt helyzetét megadja egy karakterlánc-kifejezés részét adja vissza, és továbbra is fennáll, a megadott időtartam, illetve a karakterlánc végén. |
| (String, fragment) INDEX_OF | A második első előfordulásának kezdőpozícióját adja vissza karakterlánc-kifejezés található a megadott karakterlánc első kifejezés, vagy a -1, ha a karakterlánc nem található.|
| STARTS_WITH (x, y) | Adja vissza egy logikai jelzi-e az első karakterlánc-kifejezés második kezdődik. |
| ENDS_WITH (x, y) | Adja vissza egy logikai jelzi-e az első karakterlánc-kifejezés második végződik. |
| CONTAINS(x,y) | Visszaadja egy logikai arról a második-e az első karakterlánc-kifejezést tartalmaz. |

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan hajthat végre lekérdezéseket alkalmazásaiba [Azure IoT SDK-k][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#get-started-with-device-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging-routes]: iot-hub-devguide-messages-read-custom.md
[lnk-devguide-messaging-format]: iot-hub-devguide-messages-construct.md
[lnk-devguide-messaging-routes]: ./iot-hub-devguide-messages-read-custom.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md

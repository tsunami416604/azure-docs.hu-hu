---
title: A JSON és az AVRO elemzése az Azure Stream Analytics szolgáltatásban
description: Ez a cikk azt ismerteti, hogyan működik az összetett adattípusok, például tömbök, JSON, CSV formátumú adatok.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: 73905483850a47a9d036bef1b9e1ee60d3484555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484587"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>A JSON és az Avro-adatok elemzése az Azure Stream Analytics szolgáltatásban

Az Azure Stream Analytics támogatja a feldolgozási események CSV, JSON és Avro adatformátumokban. A JSON- és az Avro-adatok is felépíthetők, és tartalmazhatnak összetett típusokat, például beágyazott objektumokat (rekordokat) és tömböket. 

>[!NOTE]
>Az Event Hub Capture által létrehozott AVRO-fájlok egy adott formátumot használnak, amely megköveteli az *egyéni deszerializáló* funkció használatát. További információt a [Bevitel olvasása bármilyen formátumban a .NET egyéni deszerializálókkal című](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples)témakörben talál.



## <a name="record-data-types"></a>Adattípusok rögzítése
Rekord adattípusok json és Avro tömbök, ha a megfelelő formátumokat használnak a bemeneti adatfolyamok. Ezek a példák bemutatják a mintaérzékelőt, amely json formátumban olvassa be a bemeneti eseményeket. Íme egy példa egyetlen eseményre:

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```

### <a name="access-nested-fields-in-known-schema"></a>Beágyazott mezők elérése ismert sémában
A pontjelölés (.) segítségével egyszerűen elérheti a beágyazott mezőket közvetlenül a lekérdezésből. Ez a lekérdezés például az előző JSON-adatok Hely tulajdonsága alatt kiválasztja a Szélességi és hosszúsági koordinátákat. A pontot jelölés sel több szinten is navigálhat az alábbiak szerint.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

Az eredmény:

|Deviceid|Lat|Hosszú|Hőmérséklet|Verzió|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>Az összes tulajdonság kijelölése
A beágyazott rekordok összes tulajdonságát kijelölheti a '*' helyettesítő karakter használatával. Tekintse meg a következő példát:

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

Az eredmény:

|Deviceid|Lat|Hosszú|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Beágyazott mezők elérése, ha a tulajdonság neve változó

Használja a [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) függvényt, ha a tulajdonság neve változó. Ez lehetővé teszi a dinamikus lekérdezések létrehozását hardcoding tulajdonságnevek nélkül.

Tegyük fel például, hogy a mintaadatfolyamot össze kell vonni az egyes eszközérzékelők küszöbértékeit tartalmazó **referenciaadatokkal.** Az alábbiakban az ilyen referenciaadatok egy részletét mutatuk be.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

A cél itt az, hogy csatlakozzon a minta adatkészlet a cikk tetejéről, hogy a referencia-adatok, és a kimeneti egy esemény minden érzékelő intézkedés a küszöbérték felett. Ez azt jelenti, hogy a fenti esemény több kimeneti eseményt is generálhat, ha az illesztésnek köszönhetően több érzékelő is meghaladja a megfelelő küszöbértékeket. Ha hasonló eredményeket szeretne elérni illesztés nélkül, olvassa el az alábbi szakaszt.

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

**A GetRecordPropertyValue** a *SensorReadings*tulajdonságot jelöli ki, amely nek megfelelő név megegyezik a referenciaadatokból származó tulajdonságnévvel. Ezután a *sensorreadings* társított értékét kivonjuk.

Az eredmény:

|Deviceid|Érzékelőneve|AlertMessage üzenet|
|-|-|-|
|12345|Páratartalom|Riasztás : Érzékelő küszöbérték felett|

### <a name="convert-record-fields-into-separate-events"></a>Rekordmezők átalakítása külön eseményekké

A rekordmezők külön eseményekké alakításához használja az [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operátort a [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) függvénnyel együtt.

Az eredeti mintaadatokkal a következő lekérdezés használható tulajdonságok kibontására különböző eseményekbe.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Az eredmény:

|Deviceid|Érzékelőneve|AlertMessage üzenet|
|-|-|-|
|12345|Hőmérséklet|80|
|12345|Páratartalom|70|
|12345|Egyéni érzékelő01|5|
|12345|Egyéni érzékelő02|99|
|12345|SensorMetadata (Szenzormetadata)|[objektumobjektum]|

[A WITH használatával](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics)ezután lehetőség van arra, hogy ezeket az eseményeket különböző helyekre irányítsa:

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

### <a name="parse-json-record-in-sql-reference-data"></a>JSON-rekord elemzése sql referenciaadatokban
Ha az Azure SQL Database-t használja referenciaadatként a feladatában, lehetséges, hogy egy json formátumú adatokat tartalmazó oszlopot használjon. Erre mutat példát az alábbi ábra.

|Deviceid|Adatok|
|-|-|
|12345|{"kulcs" : "érték1"}|
|54321|{"kulcs" : "value2"}|

A JSON-rekordot az *Adatok* oszlopban egy egyszerű JavaScript-felhasználó által definiált függvény írásával elemezheti.

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

Ezután létrehozhat egy lépést a Stream Analytics-lekérdezésben az alábbiak szerint a JSON-rekordok mezőinek eléréséhez.

 ```SQL
 WITH parseJson as
 (
 SELECT DeviceID, udf.parseJson(sqlRefInput.Data) as metadata,
 FROM sqlRefInput
 )
 
 SELECT metadata.key
 INTO output
 FROM streamInput
 JOIN parseJson 
 ON streamInput.DeviceID = parseJson.DeviceID
```

## <a name="array-data-types"></a>Tömbadattípusok

A tömbadattípusok értékek rendezett gyűjteményei. A tömbértékek néhány tipikus műveletét az alábbiakban részletezzük. Ezek a példák a [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)és az [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operátor függvényeit használják.

Íme egy példa egyetlen eseményre. `CustomSensor03` Mindkettő, `SensorMetadata` és a típusú **tömb:**

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>Adott tömbelem ekkel való együttműködés

Tömbelem kijelölése egy megadott indexnél (az első tömbelem kijelölése):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

Az eredmény:

|firstElement|
|-|
|12|

### <a name="select-array-length"></a>Tömbhosszának kiválasztása

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

Az eredmény:

|tömbhossz|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>Tömbelemek átalakítása különálló eseményekké

Az összes tömbelem kijelölése egyedi eseményként. Az [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operátor a [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) beépített függvényrel együtt az összes tömbelemet egyedi eseményekként bontja ki:

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

Az eredmény:

|DeviceId|ArrayIndex|Tömbérték|
|-|-|-|
|12345|0|12|
|12345|1|-5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
Az eredmény:

|DeviceId|smKey|smValue érték|
|-|-|-|
|12345|Gyártó|ABC|
|12345|Verzió|1.2.45|

Ha a kibontott mezőknek oszlopokban kell megjelennie, az adatkészletet a [JOIN](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) művelet mellett a [WITH](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics) szintaxissal is el lehet forgatni. Az illesztéshez [time boundary](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) időhatár-feltételszükséges, amely megakadályozza a párhuzamosságokat:

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

Az eredmény:

|DeviceId|Lat|Hosszú|smVersion|smGyártó|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>Lásd még:
[Adattípusok az Azure Stream Analytics szolgáltatásban](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)

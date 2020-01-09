---
title: JSON-és AVRO elemzése Azure Stream Analytics
description: Ez a cikk azt ismerteti, hogyan működik az összetett adattípusok, például a tömbök, a JSON és a CSV formátumú adat.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 1741510c7398ce74da81f006cb4109d9a33f8f9f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431605"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>JSON-és Avro-adatelemzés Azure Stream Analytics

Azure Stream Analytics támogatja a CSV-, JSON-és Avro-adatformátumok feldolgozási eseményeit. A JSON-és a Avro-adatok is strukturálva lehetnek, és tartalmazhatnak olyan összetett típusokat, mint például a beágyazott objektumok (rekordok) és a tömbök. 




## <a name="record-data-types"></a>Adattípusok rögzítése
Az adattípusok a JSON-és Avro-tömböket jelölik, ha a bemeneti adatfolyamokban a megfelelő formátumok használatosak. Ezek a példák egy minta-érzékelőt mutatnak be, amely JSON formátumban olvassa be a bemeneti eseményeket. Íme egy példa egyetlen eseményre:

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


### <a name="access-nested-fields-in-known-schema"></a>Beágyazott mezők elérése az ismert sémában
A beágyazott mezők közvetlenül a lekérdezésből való egyszerű eléréséhez használja a pont jelölését (.). Ez a lekérdezés például kiválasztja a szélességi és hosszúsági koordinátákat az előző JSON-adat Location tulajdonsága alatt. A dot jelöléssel több szint is navigálható az alább látható módon.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>Az összes tulajdonság kijelölése
A beágyazott rekordok összes tulajdonságát "*" helyettesítő karakterrel is kiválaszthatja. Tekintse meg a következő példát:

```SQL
SELECT input.Location.*
FROM input
```

Az eredmény a következőket eredményezi:

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Beágyazott mezők elérése, ha a tulajdonság neve változó
Használja a [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue) függvényt, ha a tulajdonság neve változó. 

Tegyük fel például, hogy egy minta adatfolyamot kell összekapcsolni az egyes eszköz-érzékelők küszöbértékeit tartalmazó hivatkozási adattal. Alább láthatók a hivatkozási adatrészletek.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 75
}
```

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
    -- the where statement selects the property value coming from the reference data
```

### <a name="convert-record-fields-into-separate-events"></a>Rekord mezők konvertálása különálló eseményekre
A rekordok mezőinek különálló eseményekre való átalakításához használja az [Apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operátort a [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) függvénnyel együtt. Ha például az előző példában több rekord is volt a SensorReading-hez, a következő lekérdezés használható a különböző eseményekre való kinyeréséhez:

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>Tömb adattípusai

A tömb típusú adattípusok értékek rendezett gyűjteményei. A tömb értékeivel kapcsolatos néhány jellemző művelet alább látható. Ezek a példák feltételezik, hogy a bemeneti események "arrayField" nevű tulajdonsággal rendelkeznek, amely egy tömb adattípusa.

Ezek a példák a functions [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), a [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), a [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics)és az [Apply](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operátort használják.

### <a name="working-with-a-specific-array-element"></a>Egy adott tömb elem használata
Válassza ki a tömb elemet a megadott indexben (az első tömb elem kiválasztásával):

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>Tömb hosszának kiválasztása

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>Tömb elemek konvertálása különálló eseményekre
Válassza az összes tömb elemet egyedi eseményként. Az operátor [alkalmazása](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) a [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) beépített függvénnyel együtt kinyeri az összes tömb elemet az egyes eseményekként:

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>Lásd még:
[Adattípusok a Azure Stream Analyticsban](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)

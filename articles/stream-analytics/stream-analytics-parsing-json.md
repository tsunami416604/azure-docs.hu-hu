---
title: JSON-elemzés és az avro-hoz az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti, hogyan működnek az összetett adattípusok, mint a tömbök, JSON-, CSV formátumú adatok.
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: daf5b97e4ac586f89e5964ee16ee73c86f59b01d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329356"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Az Azure Stream Analyticsben JSON-t és az Avro adatainak elemzése

Az Azure Stream Analytics támogatja a fürt megosztott kötetei szolgáltatás, a JSON és a Avro adatformátumok a célnyelven események feldolgozását. JSON és az Avro-adatok szervezhetők, és néhány komplex típusok, például a beágyazott objektumok (rekord) és a tömbök tartalmazhat. 




## <a name="record-data-types"></a>Rekord adattípusok
Rekord adattípusokat képviselő JSON-t és az Avro-tömbök, amikor a megfelelő formátumban vannak használatban a bemeneti adatok adatfolyamok használja. Ezek a példák bemutatják, minta érzékelő, amely a bemeneti események JSON formátumban olvasó. A következő példa egy adott eseményhez:

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


### <a name="access-nested-fields-in-known-schema"></a>Hozzáférés a beágyazott mezők ismert séma
Pont jelölés (.) segítségével könnyedén elérheti a beágyazott mezői a lekérdezést. Például ez a lekérdezés a szélességi és hosszúsági koordináták mellett a Location tulajdonsághoz kijelöli a fenti JSON-adatokat. Többszintű lépjen az alább látható módon használható a felépítését.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>Válassza ki az összes tulajdonság
Kiválaszthatja egy beágyazott rekord az összes tulajdonság "*" helyettesítő karakter. Vegye figyelembe az alábbi példában:

```SQL
SELECT input.Location.*
FROM input
```

Az eredmény a következő:

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Hozzáférés a beágyazott mezők tulajdonság neve megkülönbözteti a változó
Használja a [GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue) működnek, ha a tulajdonság neve megkülönbözteti a változót. 

Képzeljünk el például egy minta adatfolyam kell, amelyet egyesíteni kell a hivatkozás adatokat tartalmazó küszöbértékek minden egyes eszköz érzékelő. Az ilyen referenciaadatok egy kódrészletet az alábbiakban látható.

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

### <a name="convert-record-fields-into-separate-events"></a>Rekordmezők átalakítása külön események
Az elkülönített események rekordmezők konvertálásához használja a [ALKALMAZ](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operátorral együtt a [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) függvény. Például ha az előző példában több rekordot a SensorReading, az alábbi lekérdezés használható ki tudják nyerni azokat különböző események:

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>Tömb adattípusok

Tömb adattípusok az értékeket egy rendezett gyűjteménye. A tömb értékek néhány jellemző műveleteket lásd lent. Ezek a példák feltételezik, hogy a bemeneti események rendelkezik "arrayField" nevű tulajdonságot, amely egy tömböt adattípus.

Ezek a példák a függvények [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics), és a [ALKALMAZ](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operátor.

### <a name="working-with-a-specific-array-element"></a>Egy adott tömbelem használata
Válassza ki a tömbelem egy megadott indextől (a tömb első eleme kiválasztása):

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>Válassza ki a délka Pole

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>Tömb elemek átalakítása külön események
Válassza ki az összes tömbelem egyes eseményeket. A [ALKALMAZ](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) operátorral együtt a [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) beépített függvény kinyeri az összes tömbelemek rekordsémáját, az egyes események:

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>Lásd még:
[Adattípusok az Azure Stream Analytics szolgáltatásban](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)

---
title: JSON-elemzés és az avro-hoz az Azure Stream Analytics szolgáltatásban
description: Ez a cikk ismerteti, hogyan működnek az összetett adattípusok, mint a tömbök, JSON-, CSV formátumú adatok.
services: stream-analytics
ms.service: stream-analytics
author: jasonwhowell
ms.author: mamccrea
manager: kfile
ms.topic: conceptual
ms.date: 08/03/2018
ms.openlocfilehash: 456167d3f17811aa77bb0c43165e5e70bb641814
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2018
ms.locfileid: "50979012"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Az Azure Stream Analyticsben JSON-t és az Avro adatainak elemzése

Az Azure Stream Analytics támogatja az események feldolgozását a fürt megosztott kötetei szolgáltatás, a JSON és a Avro adatformátumok a célnyelven. JSON és az Avro-adatok is szerepelhetnek komplex típusok, például a beágyazott objektumok (rekord) és a tömbök. 
  
## <a name="array-data-types"></a>Tömb adattípusok  
Tömb adattípusok az értékeket egy rendezett gyűjteménye. A tömb értékek néhány jellemző műveleteket lásd lent. Ezek a példák feltételezik, hogy a bemeneti események rendelkezik "arrayField" nevű tulajdonságot, amely egy tömböt adattípus.

Ezek a példák a függvények [GetArrayElement](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelement-azure-stream-analytics), [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics), [GetArrayLength](https://msdn.microsoft.com/azure/stream-analytics/reference/getarraylength-azure-stream-analytics), és a [ALKALMAZ](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) operátor.

## <a name="examples"></a>Példák  
 Válassza ki a tömbelem egy megadott indextől (a tömb első eleme kiválasztása):  
  
```SQL 
SELECT   
    GetArrayElement(arrayField, 0) AS firstElement  
FROM input  
```  
  
 Válassza ki a délka Pole:  
  
```SQL  
SELECT   
    GetArrayLength(arrayField) AS arrayLength  
FROM input  
```  
  
Válassza ki az összes tömbelem egyes eseményeket. A [ALKALMAZ](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) operátorral együtt a [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics) beépített függvény kinyeri az összes tömbelemek rekordsémáját, az egyes események:  
  
```SQL  
SELECT   
    arrayElement.ArrayIndex,  
    arrayElement.ArrayValue  
FROM input as event  
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement  
```  
  
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
        "CustomSensor02" : 99  
    }  
}  
```  
  
## <a name="examples"></a>Példák  
Beágyazott mezői eléréséhez használja (.) felépítését. Ez a lekérdezés például kiválasztja a szél, és mennyi ideig koordinálja a Location tulajdonsághoz az előző JSON-adatok alapján: 
  
```SQL  
SELECT  
    DeviceID,  
    Location.Lat,  
    Location.Long  
FROM input  
```  

Használja a [GetRecordPropertyValue](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordpropertyvalue-azure-stream-analytics) működik, ha a tulajdonság neve ismeretlen. Képzeljünk el például egy minta adatfolyam kell, amelyet egyesíteni kell a hivatkozás adatokat tartalmazó küszöbértékek minden egyes eszköz érzékelő:  

```json  
{  
    "DeviceId" : "12345",  
    "SensorName" :  "Temperature",
    "Value" : 75
}  
```  
  
```SQL  
SELECT  
    input.DeviceID,  
    thresholds.SensorName  
FROM input  
JOIN thresholds  
ON  
    input.DeviceId = thresholds.DeviceId  
WHERE  
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value  
```  
  
Az elkülönített események rekordmezők konvertálásához használja a [ALKALMAZ](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) operátorral együtt a [GetRecordProperties](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordproperties-azure-stream-analytics) függvény. A minta adatfolyam átalakítása események az egyes érzékelőinek adatfolyam, például a lekérdezés használható:  
  
```SQL  
SELECT   
    event.DeviceID,  
    sensorReading.PropertyName,  
    sensorReading.PropertyValue  
FROM input as event  
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading  
```  

Kiválaszthatja egy beágyazott rekord az összes tulajdonság "*" helyettesítő karakter. Vegye figyelembe az alábbi példában:  

```SQL  
SELECT input.SensorReadings.*  
FROM input  
```  

Az eredmény a következő:  

```json  
{  
    "Temperature" : 80,  
    "Humidity" : 70,  
    "CustomSensor01" : 5,  
    "CustomSensor022" : 99  
}  
```  
  
## <a name="see-also"></a>Lásd még:  
 [Adattípusok az Azure Stream Analytics szolgáltatásban](https://msdn.microsoft.com/azure/stream-analytics/reference/data-types-azure-stream-analytics)  

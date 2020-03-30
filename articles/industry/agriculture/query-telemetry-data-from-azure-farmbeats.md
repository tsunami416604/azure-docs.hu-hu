---
title: Bevitt telemetriai adatok lekérdezése
description: Ez a cikk ismerteti, hogyan lehet lekérdezni a bevitt telemetriai adatokat.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: f717903b3f953e04c793092c86802f2006de7e82
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349802"
---
# <a name="query-ingested-telemetry-data"></a>Bevitt telemetriai adatok lekérdezése

Ez a cikk ismerteti, hogyan lehet lekérdezni a bevitt érzékelő adatait az Azure FarmBeats-ből.

Az eszközök internetes hálózatáról (IoT) származó adatok, például az eszközök és az érzékelők betöltése gyakori forgatókönyv a FarmBeats-ben. Metaadatokat hozhat létre az eszközökhöz és az érzékelőkhöz, majd kanonikus formátumban bekell adnia a korábbi adatokat a FarmBeats-be. Miután az érzékelő adatai elérhetők a FarmBeats Datahub, lekérdezhetjük ugyanazt a perelhető insights létrehozásához vagy modellek létrehozásához.

## <a name="before-you-begin"></a>Előkészületek

Mielőtt folytatná ezt a cikket, győződjön meg arról, hogy telepítette a FarmBeats-t, és az IoT-eszközökről a FarmBeats-be tárolta az érzékelő telemetriai adatait.

Az érzékelő telemetriai adatainak betöltéséhez látogasson el [a korábbi telemetriai adatok](ingest-historical-telemetry-data-in-azure-farmbeats.md)

Mielőtt folytatná, azt is meg kell győződnie arról, hogy ismeri a FarmBeats REST API-kat, mivel az API-k használatával lekérdezi a bevitt telemetriát. A FarmBeats API-król további információt a [FarmBeats REST API-k című témakörben talál.](rest-api-in-azure-farmbeats.md) **Győződjön meg arról, hogy képes-e API-kéréseket a FarmBeats Datahub végpontjára.**

## <a name="query-ingested-sensor-telemetry-data"></a>Bevitt érzékelő telemetriai adatainak lekérdezése

A FarmBeats telemetriai adatainak elérésére és lekérdezésére kétféleképpen lehet hozzáférni:

- API és
- Idősorozat-elemzések (TSI).

### <a name="query-using-rest-api"></a>LEKÉRDEZÉS REST API-val

Kövesse a lépéseket a bevitt érzékelő telemetriai adatainak lekérdezéséhez a FarmBeats REST API-k használatával:

1. Azonosítsa az önt érdeklő érzékelőt. Ezt úgy teheti meg, hogy get-kérelmet küld a /Sensor API-n.

> [!NOTE]
> Az érdeklődő érzékelőobjektum **azonosítója** és **érzékelőmodellazonosítója.**

2. Get/{id} a /SensorModel API-n a **sensorModelId** 1. A "Sensor Model" rendelkezik az összes metaadatés az érzékelő bevitt telemetria részleteit. Az **érzékelőmodell** objektumon **Sensor Model** belül például részletesen ismerteti, hogy milyen mértékeket küld az érzékelő, és milyen típusú és egységekben. Például:

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
Jegyezze fel az érzékelőmodell GET/{id} hívásából származó választ.

3. Post-hívás a /Telemetriai API-n a következő bemeneti tartalommal

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. A /Telemetriai API válasza a következővel fog kinézni:

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
A fenti példaválaszban a lekérdezett érzékelő telemetriai adatokat ad két időbélyegmellett a mérték neve ("moist_soil_last") és a jelentett telemetriai adatok a két időbélyegben. A jelentett értékek típusának és egységének értelmezéséhez a kapcsolódó érzékelőmodellre kell hivatkoznia (a 2. lépésben leírtak szerint).

### <a name="query-using-azure-time-series-insights-tsi"></a>Azure Time Series Insights (TSI) használatával történő lekérdezés

A FarmBeats az [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) segítségével az IoT-skálázási műveletekben lévő adatokat szeretné beadni, tárolni, lekérdezni és vizualizálni – olyan adatokat, amelyek nagy mértékben környezetfüggők és idősorozatokra optimalizáltak.

Telemetriai adatok at kap egy EventHub, majd feldolgozni, és leküldéses egy ÁME-környezetben farmbeats erőforráscsoporton belül. Az adatok ezután közvetlenül lekérdezhetők az ÁME-ből. További információ: [AZ 1SI dokumentációja](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)

Az 1SI-n lévő adatok megjelenítéséhez kövesse az adatok megjelenítéséhez szükséges lépéseket:

1. Nyissa meg **az Azure Portal** > **FarmBeats DataHub erőforráscsoportot,** > válassza a **Time Series Insights** környezetet (tsi-xxxx) > **adatelérési szabályzatokat.** Adjon hozzá felhasználói szerepkört a Reader vagy a Közreműködői hozzáféréssel.
2. Nyissa meg a **Time Series Insights** környezet **áttekintése lapját** (tsi-xxxx), és válassza a **Time Series Insights Explorer URL-címét.** Most már képes lesz vizuviziálni a bevitt telemetria.

A telemetriai adatok tárolása, lekérdezése és vizualizálása mellett az Áme lehetővé teszi a Power BI irányítópultjaira való integrációt is. További információkért lásd [itt]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>További lépések

Most már lekérdezett érzékelő adatokat az Azure FarmBeats-példányból. Most, megtanulják, hogyan kell [létrehozni térképeket](generate-maps-in-azure-farmbeats.md#generate-maps) a gazdaságok.

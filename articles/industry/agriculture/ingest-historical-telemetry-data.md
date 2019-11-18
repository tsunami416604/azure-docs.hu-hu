---
title: Korábbi telemetria-adatfeldolgozás
description: Leírja, hogyan lehet beolvasni a korábbi telemetria-információkat
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 5ae64371bd114a898ddca874e23b499bc4a2b8a3
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74128778"
---
# <a name="ingest-historical-telemetry-data"></a>Korábbi telemetriaadatok feldolgozása

Ez a cikk azt ismerteti, hogyan lehet bevezetni a korábbi érzékelőkre vonatkozó információkat az Azure FarmBeats.

Az olyan erőforrások, mint az eszközök és érzékelők eszközök internetes hálózata (IoT) múltbeli adatainak betöltése gyakori forgatókönyv a FarmBeats-ben. Az eszközökhöz és érzékelőkhöz metaadatokat hozhat létre, majd a korábbi adatokat kanonikus formátumban FarmBeats.

## <a name="before-you-begin"></a>Előkészületek

Mielőtt folytatná ezt a cikket, győződjön meg róla, hogy telepítette a FarmBeats-t, és összegyűjtötte a korábbi adatokat a IoT.

## <a name="enable-partner-access"></a>Partneri hozzáférés engedélyezése

Engedélyeznie kell a partner-integrációt az Azure FarmBeats-példányához. Ez a lépés egy olyan ügyfelet hoz létre, amely hozzáfér az Azure-FarmBeats az eszköz partnereként, és a következő lépésekben szükséges értékeket adja meg.

- API-végpont – ez az adatközpont URL-címe, például https://<datahub>. azurewebsites.net
- Bérlőazonosító
- Ügyfél-azonosító
- Titkos ügyfélkulcs
- EventHub-kapcsolatok karakterlánca

Ezek létrehozásához kövesse az alábbi lépéseket:

>[!NOTE]
> A következő lépések végrehajtásához rendszergazdának kell lennie.

1. Töltse le ezt a [szkriptet](https://aka.ms/farmbeatspartnerscript) , és bontsa ki a helyi meghajtón. A ZIP-fájlban két fájl található.
2. Jelentkezzen be [Azure Portal](https://portal.azure.com/) és nyissa meg Cloud shell (ez a lehetőség a portál jobb felső sarkában érhető el)  

    ![A Project Farm veri](./media/for-tutorials/navigation-bar-1.png)

3. Győződjön meg arról, hogy a környezet **PowerShell**-re van beállítva.

    ![A Project Farm veri](./media/for-tutorials/power-shell-new-1.png)

4. Töltse fel a letöltött két fájlt (a fenti 1. lépésből) a Cloud Shell.  

    ![A Project Farm veri](./media/for-tutorials/power-shell-two-1.png)

5. Nyissa meg azt a könyvtárat, ahová a fájlokat feltöltötte

   >[!NOTE]
   > Alapértelmezés szerint a rendszer feltölti a fájlt a kezdőkönyvtár/Home/username/.
6. Futtassa a szkriptet a parancs használatával:  

    ```azurepowershell-interactive
    ./generateCredentials.ps1
    ```

7. Kövesse a képernyőn megjelenő utasításokat az eljárás befejezéséhez.

## <a name="create-devicesensor-metadata"></a>Eszköz/érzékelő metaadatainak létrehozása

 Most, hogy rendelkezik a szükséges hitelesítő adatokkal, megadhatja az eszközt és az érzékelőket a metaadatok létrehozásával a FarmBeats API-k használatával.

 A FarmBeats-adatközpont a következő API-kkal rendelkezik, amelyek lehetővé teszik az eszköz/érzékelő metaadatainak létrehozását és felügyeletét.   

- /**DeviceModel** – az eszköz modellje megegyezik az eszköz metaadataival, például a gyártóval, az eszköz típusával vagy átjáróval vagy csomóponttal.  
- /**eszköz** – az eszköz megfelel a farmban lévő fizikai eszköznek.  
- /**SensorModel** -Sensor modell megfelel az érzékelő metaadatainak, például a gyártónak, az érzékelő típusának az analóg vagy a digitális, az érzékelő mértékét, például a környezeti hőmérsékletet vagy a nyomást.
- /**szenzor** – az érzékelő olyan fizikai érzékelőnek felel meg, amely értékeket rögzít. Az érzékelő általában eszköz-AZONOSÍTÓval van csatlakoztatva egy eszközhöz.  


|        Eszköz modellje   |  Javaslatok   |
| ------- | -------             |
|     Típus (csomópont, átjáró)        |          1 csillag      |
|          Gyártó            |         2 csillag     |
|  ProductCode                    |  Eszköz Termékkód vagy modell neve/száma. Például: EnviroMonitor # 6800.  |
|            Portok          |     Port neve és típusa (digitális/analóg)
|     Name (Név)                 |  Az erőforrást azonosító név. Például: modell neve/terméknév.
      Leírás     | Adjon meg egy értelmes leírást a modellről
|    Tulajdonságok          |    További tulajdonságok a gyártótól   |
|    **Device**             |                      |
|   DeviceModelId     |     A társított eszköz-modell azonosítója  |
|  HardwareId          | Az eszköz egyedi azonosítója, például MAC-címe stb.
|  ReportingInterval        |   Jelentéskészítési időköz (másodperc)
|  Földrajzi egység            |  Eszköz szélesség (-90 – + 90)/Longitude (-180 – 180)/Elevation (méter)   
|ParentDeviceId       |    Annak a fölérendelt eszköznek az azonosítója, amelyhez az eszköz csatlakoztatva van. Például egy átjáróhoz csatlakozó csomópont. A csomópontok átjáróként lesznek parentDeviceId.  |
|    Name (Név)            | Az erőforrás azonosítására szolgáló név. Az eszköz partnereinek olyan nevet kell küldeniük, amely konzisztens a partner oldalán található eszköznév nevével. Ha a partner eszköz neve felhasználó által definiált, akkor a felhasználó által definiált nevet is propagálni kell a FarmBeats.|
|     Leírás       |      Adjon meg egy értelmes Leírást  |
|     Tulajdonságok    |  További tulajdonságok a gyártótól
|     **Érzékelő modell**        |          |
|       Típus (analóg, digitális)          |      az érzékelő típusa, hogy az analóg vagy a digitális       |
|          Gyártó            |       az érzékelő gyártója     |
|     ProductCode| Termékkód vagy modell neve/száma. Például RS-CO2-N01. |
|       SensorMeasures > neve       | Az érzékelő mértékének neve. Csak kisbetűk támogatottak. A különböző mélységekben mért mértéknél határozza meg a mélységet. Például soil_moisture_15cm. Ennek a névnek konzisztensnek kell lennie a telemetria-adattal  |
|          sensorMeasures > adattípus       |Az telemetria adattípusa. Jelenleg dupla támogatott|
|    SensorMeasures > típusa    |Az érzékelő telemetria-adattípusának mértékegysége A rendszer által definiált típusok a következők: AmbientTemperature, CO2, mélység, ElectricalConductivity, LeafWetness, Length, LiquidLevel, nitrát, O2, PH, foszfát, PointInTime, kálium, nyomás, RainGauge, RelativeHumidity, sótartalom, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, szélsebesség, párolgás, PAR. További részletekért lásd a/ExtendedType API-t.|
|        SensorMeasures > egység              | Az érzékelő telemetria-adategysége. A következő rendszer által meghatározott egységek: nincs egység, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, milliméter, centiméter, Meter, hüvelyk, láb, Mile, km, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, százalék, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, liter, MilliLiter, másodperc, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour továbbiak hozzáadásához tekintse meg a/ ExtendedType API.|
|    SensorMeasures > aggregationType    |  Az értékek lehetnek nem, átlag, maximum, minimum vagy StandardDeviation.  |
|          Name (Név)            | Az erőforrást azonosító név. Például: modell neve/terméknév.  |
|    Leírás        | Adjon meg egy értelmes leírást a modellről  |
|   Tulajdonságok       |  További tulajdonságok a gyártótól  |
|    **Érzékelő**      |          |
| HardwareId          |   A gyártó által beállított érzékelő egyedi azonosítója |
|  sensorModelId     |    A társított érzékelő modell azonosítója   |
| location          |  Érzékelő szélesség (-90 – + 90)/Longitude (-180 – 180)/Elevation (méter)|
|   port > neve        |  Annak a portnak a neve és típusa, amelyhez az érzékelő csatlakozik az eszközhöz. Ennek az eszköz modelljében definiált névnek kell lennie. |
|    DeviceID  |    Annak az eszköznek az azonosítója, amelyhez az érzékelő csatlakozik     |
| Name (Név)            |   Az erőforrást azonosító név. Például: érzékelő neve/terméknév és modell száma/Termékkód.|
|    Leírás      | Adjon meg egy értelmes Leírást |
|    Tulajdonságok        |További tulajdonságok a gyártótól |

További információ az objektumokról: [hencegés](https://aka.ms/FarmBeatsDatahubSwagger).

**Metaadatok létrehozására szolgáló API-kérelem**

API-kérelem létrehozásához egyesítse a HTTP-(POST-) metódust, az API szolgáltatás URL-címét, a lekérdezéshez használandó erőforrás URI-JÁT, küldje el az adatkérést egy kérelem létrehozására vagy törlésére, majd adjon hozzá egy vagy több HTTP-kérelem fejlécét. Az API-szolgáltatás URL-címe az API-végpont, azaz az adatközpont URL-címe (https://<yourdatahub>. azurewebsites.net)  

**Hitelesítés**:

A FarmBeats adatközpont tulajdonosi hitelesítést használ, amelyhez a következő szakaszban létrehozott hitelesítő adatok szükségesek.

- Ügyfél-azonosító
- Titkos ügyfélkulcs
- Bérlőazonosító  

A fenti hitelesítő adatok használatával a hívó kérhet egy hozzáférési jogkivonatot, amelyet a következő API-kérelmekben kell elküldeni a fejléc szakaszban a következő módon:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

**Http-kérelem fejlécei**:

Itt láthatók a leggyakoribb kérelmek fejlécei, amelyeket meg kell adni az API-hívások FarmBeats-adatközpontba való készítésekor:

- Content-Type: Application/JSON
- Engedélyezés: tulajdonos < hozzáférés-token >
- Elfogadás: alkalmazás/JSON

**A metaadatok létrehozására szolgáló bemeneti adattartalom**:

**DeviceModel**


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Eszköz

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
Érzékelő

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
Az alábbi példa egy eszköz létrehozását mutatja be (ez egy bemeneti JSON-t tartalmaz adattartalomként a kérés törzsével).  

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

> [!NOTE]
> Az API-k egyedi azonosítókat adnak vissza minden létrehozott példányhoz. Meg kell őriznie az azonosítókat a megfelelő telemetria-üzenetek küldéséhez.

**Telemetria küldése**

Most, hogy létrehozta az eszközöket és az érzékelőket a FarmBeats-ben, elküldheti a kapcsolódó telemetria-üzeneteket.  

**Telemetria-ügyfél létrehozása**

A telemetria az Azure Event hub-nak kell elküldeni feldolgozásra. Az Azure EventHub egy olyan szolgáltatás, amely lehetővé teszi a valós idejű adatok (telemetria) betöltését a csatlakoztatott eszközökről és alkalmazásokból. Ahhoz, hogy telemetria-adatokat küldjön a FarmBeats, létre kell hoznia egy ügyfelet, amely üzeneteket küld egy FarmBeats lévő Event hubhoz. A telemetria küldésével kapcsolatos további információkért lásd: [Azure Event hubok](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

**Telemetria-üzenet küldése ügyfélként**

Ha már létrejött egy EventHub-ügyfél, akkor JSON-ként küldhet üzeneteket a EventHub.  
Alakítsa át a korábbi érzékelő adatformátumát olyan kanonikus formátumra, amelyet az Azure FarmBeats értelmez. A kanonikus üzenet formátuma a következő:  

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

A megfelelő eszközök és érzékelők hozzáadása után szerezze be a DeviceID és a sensorid a telemetria üzenetben az előző szakaszban leírtak szerint.

Példa telemetria-üzenetre:


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}
```


## <a name="next-steps"></a>További lépések

További információ a REST API-alapú integráció részleteiről: [REST API](references-for-farmbeats.md#rest-api).

---
title: Korábbi telemetriaadatok feldolgozása
description: Ez a cikk a korábbi telemetria-adatmennyiségek betöltését ismerteti.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 32eb8e71cfb978fac5b4d6d05af4da4fdc9f67b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715520"
---
# <a name="ingest-historical-telemetry-data"></a>Korábbi telemetriaadatok feldolgozása

Ez a cikk azt ismerteti, hogyan lehet bevezetni a korábbi érzékelőkre vonatkozó információkat az Azure FarmBeats.

Az eszközök internetes hálózata (IoT) erőforrások, például az eszközök és érzékelők korábbi adatainak betöltése gyakori forgatókönyv a FarmBeats-ben. Metaadatokat hozhat létre az eszközök és érzékelők számára, majd a korábbi adatokat kanonikus formátumban FarmBeats.

## <a name="before-you-begin"></a>Előkészületek

Mielőtt folytatná ezt a cikket, győződjön meg róla, hogy telepítette a FarmBeats-t, és összegyűjtötte a korábbi adatokat a IoT-eszközökről.
A partnerek hozzáférését is engedélyeznie kell a következő lépésekben leírtak szerint.

## <a name="enable-partner-access"></a>Partneri hozzáférés engedélyezése

Engedélyeznie kell a partner-integrációt az Azure FarmBeats-példányához. Ez a lépés egy olyan ügyfelet hoz létre, amely hozzáfér az Azure FarmBeats-példányhoz az eszköz partnereként, és a következő lépésekben szükséges értékeket biztosítja:

- API-végpont: Ez a Datahub URL-cím, például https://\<Datahub >. azurewebsites. net.
- Bérlőazonosító
- Ügyfél-azonosító
- Titkos ügyfélkulcs
- EventHub-kapcsolatok karakterlánca

Kövesse az alábbi lépéseket.

>[!NOTE]
> A következő lépések végrehajtásához rendszergazdának kell lennie.

1. Töltse le a [zip-fájlt](https://aka.ms/farmbeatspartnerscriptv2), és bontsa ki a helyi meghajtóra. Egy fájl lesz a zip-fájlban.
2. Jelentkezzen be https://portal.azure.com/, és lépjen a Azure Active Directory-> alkalmazás regisztrációi

3. Kattintson a FarmBeats-telepítés részeként létrehozott alkalmazás-regisztrációra. Ez a neve megegyezik a FarmBeats Datahub.

4. Kattintson az "API közzététele" elemre, > kattintson az "ügyfélalkalmazás hozzáadása" elemre, és írja be a **04b07795-8ddb-461a-bbee-02f9e1bf7b46** , és jelölje be az "engedélyezés hatóköre" lehetőséget. Ez hozzáférést biztosít az Azure CLI-hez (Cloud Shell) az alábbi lépések végrehajtásához.

5. Nyissa meg a Cloud Shellt. Ez a beállítás a Azure Portal jobb felső sarkában található eszköztáron érhető el.

    ![Azure Portal eszköztár](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. Győződjön meg arról, hogy a környezet **PowerShell**-re van beállítva. Alapértelmezés szerint a bash értékre van beállítva.

    ![PowerShell eszköztár-beállítás](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Töltse fel a fájlt az 1. lépésben az Cloud Shell-példányban.

    ![Eszköztár feltöltése gomb](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. Nyissa meg azt a könyvtárat, ahová a fájl fel lett töltve. Alapértelmezés szerint a fájlok a Felhasználónév alatt töltődnek fel a saját könyvtárba.

9. Futtassa a következő szkriptet. A parancsfájl kéri a bérlő AZONOSÍTÓját, amely a Azure Active Directory-> áttekintő oldaláról kérhető le.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

10. A képernyőn megjelenő utasításokat követve rögzítheti az **API-végpont**, a **bérlői azonosító**, az **ügyfél-azonosító**, az **ügyfél titkos kulcsa**és a **EventHub kapcsolódási karakterláncának**értékét.
## <a name="create-device-or-sensor-metadata"></a>Eszköz vagy érzékelő metaadatainak létrehozása

 Most, hogy rendelkezik a szükséges hitelesítő adatokkal, megadhatja az eszközt és az érzékelőket. Ehhez hozza létre a metaadatokat a FarmBeats API-k meghívásával. Vegye figyelembe, hogy az API-kat a fenti szakaszban létrehozott ügyfélalkalmazásként kell meghívni.

 A FarmBeats Datahub a következő API-kkal rendelkezik, amelyek lehetővé teszik az eszköz vagy az érzékelő metaadatainak létrehozását és felügyeletét. Vegye figyelembe, hogy partnereként csak olvasási, létrehozási és frissítési metaadatokat lehet elérni. **Egy partner nem engedélyezte a törlést.**

- /**DeviceModel**: a DeviceModel az eszköz metaadatait, például a gyártót és az eszköz típusát, amely vagy egy átjáró vagy egy csomópont.
- /**eszköz**: az eszköz a farmon lévő fizikai eszköznek felel meg.
- /**SensorModel**: a SensorModel az érzékelő metaadatait, például a gyártót, az analóg vagy digitális érzékelő típusát, valamint az érzékelő mértékét, például a környezeti hőmérsékletet és a nyomást is megfelel.
- /**érzékelő**: az érzékelő olyan fizikai érzékelőnek felel meg, amely értékeket rögzít. Az érzékelő általában eszköz-AZONOSÍTÓval van csatlakoztatva egy eszközhöz.  


|        DeviceModel   |  Javaslatok   |
| ------- | -------             |
|     Típus (csomópont, átjáró)        |          Az eszköz – csomópont vagy átjáró típusa      |
|          Gyártó            |         A gyártó neve    |
|  ProductCode                    |  Eszköz Termékkód vagy modell neve vagy száma. Például: EnviroMonitor # 6800.  |
|            Portok          |     A port neve és típusa, amely digitális vagy analóg.
|     Név                 |  Az erőforrást azonosító név. Például a modell neve vagy a termék neve.
      Leírás     | Adjon meg egy értelmes leírást a modellről.
|    Tulajdonságok          |    További tulajdonságok a gyártótól.   |
|    **Eszköz**             |                      |
|   DeviceModelId     |     A társított eszköz-modell azonosítója.  |
|  HardwareId          | Az eszköz egyedi azonosítója, például a MAC-címe.
|  ReportingInterval        |   Jelentéskészítési időköz (másodperc).
|  Hely            |  Az eszköz földrajzi szélessége (-90 és + 90), a hosszúság (-180 – 180) és a Jogosultságszint-emelés (méterben).   
|ParentDeviceId       |    Annak a fölérendelt eszköznek az azonosítója, amelyhez az eszköz csatlakoztatva van. Például egy átjáróhoz csatlakoztatott csomópont. A csomópontok átjáróként vannak parentDeviceId.  |
|    Név            | Az erőforrás azonosítására szolgáló név. Az eszköz partnereinek olyan nevet kell küldeniük, amely konzisztens a partner oldalán található eszköznév nevével. Ha a partner eszköz neve felhasználó által definiált, akkor a felhasználó által definiált nevet is propagálni kell a FarmBeats.|
|     Leírás       |      Adjon meg egy értelmes leírást. |
|     Tulajdonságok    |  További tulajdonságok a gyártótól.
|     **SensorModel**        |          |
|       Típus (analóg, digitális)          |      Az érzékelő típusa, legyen az analóg vagy a digitális.       |
|          Gyártó            |       Az érzékelő gyártója.     |
|     ProductCode| Termékkód vagy modell neve vagy száma. Például RS-CO2-N01. |
|       SensorMeasures > neve       | Az érzékelő mértékének neve. Csak kisbetűk támogatottak. A különböző mélységű mérések esetében határozza meg a mélységet. Például soil_moisture_15cm. Ennek a névnek konzisztensnek kell lennie a telemetria-adattal.  |
|          SensorMeasures > adattípus       |Az telemetria adattípusa. Jelenleg a Double támogatott.|
|    SensorMeasures > típusa    |Az érzékelő telemetria-adattípusának mértékegysége A rendszer által meghatározott típusok: AmbientTemperature, CO2, mélység, ElectricalConductivity, LeafWetness, Length, LiquidLevel, nitrát, O2, PH, foszfát, PointInTime, kálium, nyomás, RainGauge, RelativeHumidity, sótartalom, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, szélsebesség, párolgás, PAR. További részletekért tekintse meg a/ExtendedType API-t.|
|        SensorMeasures > egység              | Az érzékelő telemetria-adategysége. A rendszer által meghatározott egységek: nem egység, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, milliméter, centiméter, méter, hüvelyk, láb, Mile, kilométer, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, százalék, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, liter, MilliLiter, másodperc, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour továbbiak, tekintse meg a/ExtendedType API-t.|
|    SensorMeasures > AggregationType    |  Az értékek a következők lehetnek: none, Average, Max, minimum vagy StandardDeviation.  |
|          Név            | Egy erőforrás azonosítására szolgáló név. Például a modell neve vagy a termék neve.  |
|    Leírás        | Adjon meg egy értelmes leírást a modellről.  |
|   Tulajdonságok       |  További tulajdonságok a gyártótól.  |
|    **Érzékelő**      |          |
| HardwareId          |   A gyártó által beállított érzékelő egyedi azonosítója. |
|  SensorModelId     |    A társított érzékelő modell azonosítója.   |
| Hely          |  Érzékelő szélesség (-90 és + 90), hosszúság (-180 – 180) és Jogosultságszint-emelés (méterben).|
|   Port > neve        |  Annak a portnak a neve és típusa, amelyhez az érzékelő csatlakozik az eszközhöz. Ennek a névnek meg kell egyeznie az eszköz modelljében megadott névvel. |
|    DeviceID  |    Annak az eszköznek az azonosítója, amelyhez az érzékelő csatlakozik.     |
| Név            |   Az erőforrást azonosító név. Például az érzékelő neve, a terméknév és a modell száma vagy a termékkód.|
|    Leírás      | Adjon meg egy értelmes leírást. |
|    Tulajdonságok        |További tulajdonságok a gyártótól. |

További információ az objektumokról: [hencegés](https://aka.ms/FarmBeatsDatahubSwagger).

### <a name="api-request-to-create-metadata"></a>Metaadatok létrehozására szolgáló API-kérelem

API-kérelem létrehozásához össze kell kapcsolni a HTTP-(POST-) metódust, az API szolgáltatás URL-címét és az erőforráshoz tartozó URI-t, amely lekérdezi, beküldi a kérelmet, létrehoz vagy töröl egy kérést. Ezután adjon hozzá egy vagy több HTTP-kérelem fejlécét. Az API-szolgáltatás URL-címe az API-végpont, azaz a Datahub URL-cím (https://\<yourdatahub >. azurewebsites. net).  

### <a name="authentication"></a>Hitelesítés

A FarmBeats Datahub tulajdonosi hitelesítést használ, amelyhez az alábbi, az előző szakaszban létrehozott hitelesítő adatok szükségesek:

- Ügyfél-azonosító
- Titkos ügyfélkulcs
- Bérlőazonosító

Ezen hitelesítő adatok használatával a hívó hozzáférési jogkivonatot kérhet. A tokent a következő API-kérelmekben kell elküldeni a fejléc szakaszban a következő módon:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

A következő Python-kód megadja a hozzáférési jogkivonatot, amely a következő API-hívásokhoz használható a FarmBeats: 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```


**HTTP-kérelmek fejlécei**

Itt láthatók a leggyakoribb kérelmek fejlécei, amelyeket meg kell adni, amikor API-hívást végez a FarmBeats Datahub:

- **Content-Type**: Application/JSON
- **Engedélyezés**: tulajdonos < hozzáférés-token >
- **Elfogadás**: alkalmazás/JSON

### <a name="input-payload-to-create-metadata"></a>A metaadatok létrehozására szolgáló bemeneti adattartalom

DeviceModel


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
A következő minta-kérelem létrehoz egy eszközt. Ez a kérelem a JSON-t adattartalomként adja meg a kérelem törzsében.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

> [!NOTE]
> Az API-k egyedi azonosítókat adnak vissza minden létrehozott példányhoz. Meg kell őriznie az azonosítókat a megfelelő telemetria-üzenetek elküldéséhez.

### <a name="send-telemetry"></a>Telemetria küldése

Most, hogy létrehozta az eszközöket és az érzékelőket a FarmBeats-ben, elküldheti a kapcsolódó telemetria-üzeneteket.

### <a name="create-a-telemetry-client"></a>Telemetria-ügyfél létrehozása

A telemetria az Azure Event Hubsba kell küldenie feldolgozásra. Az Azure Event Hubs egy olyan szolgáltatás, amely lehetővé teszi a valós idejű adatok (telemetria) betöltését a csatlakoztatott eszközökről és alkalmazásokból. Ha telemetria-adatokat szeretne küldeni a FarmBeats, hozzon létre egy ügyfelet, amely üzeneteket küld egy FarmBeats lévő Event hubhoz. A telemetria küldésével kapcsolatos további információkért lásd: [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

### <a name="send-a-telemetry-message-as-the-client"></a>Telemetria-üzenet küldése ügyfélként

Ha a kapcsolat Event Hubs ügyfélként lett létrehozva, akkor JSON-ként küldhet üzeneteket az Event hub-nak.

Itt található egy olyan Python-kód, amely a telemetria ügyfélként küldi el a megadott Event hub számára:

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Alakítsa át a korábbi érzékelő adatformátumát olyan kanonikus formátumra, amelyet az Azure FarmBeats értelmez. A Canonical üzenet formátuma a következő:

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
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

A megfelelő eszközök és érzékelők hozzáadása után szerezze be az eszköz AZONOSÍTÓját és az érzékelő AZONOSÍTÓját a telemetria üzenetben az előző szakaszban leírtak szerint.

Példa egy telemetria-üzenetre:


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

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Nem lehet megtekinteni a telemetria adatait az érzékelőkből származó múltbeli/adatfolyam-adatok betöltése után

**Tünet**: az eszközök vagy érzékelők üzembe helyezése megtörténik, és létrehozta az eszközöket/érzékelőket a FarmBeats-on és a betöltött telemetria a EventHub, de nem tudja lekérdezni vagy megtekinteni a telemetria-adatot a FarmBeats.

**Javítási művelet**:

1. Győződjön meg arról, hogy helyesen végrehajtotta a partner regisztrációját – ezt megteheti, ha a datahub henceg, a/partner API-ra navigálva elvégezheti a lekérést, és ellenőrizheti, hogy a partner regisztrálva van-e. Ha nem, kövesse az [itt található lépéseket](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) a partner hozzáadásához.
2. Győződjön meg arról, hogy létrehozta a metaadatokat (DeviceModel, eszköz, SensorModel, érzékelő) a partner ügyfél hitelesítő adataival.
3. Győződjön meg arról, hogy a megfelelő telemetria-formátumot használta (az alább megadott módon):

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
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```


## <a name="next-steps"></a>Következő lépések

További információ a REST API-alapú integráció részleteiről: [REST API](references-for-azure-farmbeats.md#rest-api).

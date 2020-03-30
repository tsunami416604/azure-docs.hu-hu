---
title: Korábbi telemetriaadatok feldolgozása
description: Ez a cikk ismerteti, hogyan lehet betöltése előzmény telemetriai adatok.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b0b9d62e8761cfb67d0642d8e5a97e7d1f05af12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064457"
---
# <a name="ingest-historical-telemetry-data"></a>Korábbi telemetriaadatok feldolgozása

Ez a cikk ismerteti, hogyan lehet bedolgozni a korábbi érzékelő adatokat az Azure FarmBeats.This article describes how to beit his to A ororzor data into Azure FarmBeats.

A FarmBeats gyakori forgatókönyve a korábbi adatok betöltése az eszközök internetes (IoT) erőforrásaiból, például eszközök és érzékelők. Metaadatokat hozhat létre az eszközökhöz és az érzékelőkhöz, majd kanonikus formátumban bekell adnia a korábbi adatokat a FarmBeats-be.

## <a name="before-you-begin"></a>Előkészületek

Mielőtt folytatná ezt a cikket, győződjön meg arról, hogy telepítette a FarmBeats-t, és összegyűjtötte az IoT-eszközökről származó előzményadatokat. Emellett engedélyeznie kell a partnerek hozzáférését a következő lépésekben említettek szerint.

## <a name="enable-partner-access"></a>Partnerhozzáférés engedélyezése

Engedélyeznie kell a partnerintegrációt az Azure FarmBeats-példányhoz. Ez a lépés létrehoz egy ügyfelet, amely eszközpartnerként hozzáfér az Azure FarmBeats-példányhoz, és a következő lépésekben szükséges értékeket biztosítja:

- API-végpont: Ez a Datahub URL-címe, például https://\<datahub>.azurewebsites.net
- Bérlőazonosító
- Ügyfél-azonosító
- Titkos ügyfélkulcs
- EventHub-kapcsolati karakterlánc

Kövesse az alábbi lépéseket:

> [!NOTE]
> A következő lépések végrehajtásához rendszergazdának kell lennie.

1. Töltse le a [zip fájlt](https://aka.ms/farmbeatspartnerscriptv2), és bontsa ki a helyi meghajtóra. Egy fájl lesz a zip fájlban.

2. https://portal.azure.com/ Jelentkezzen be, és nyissa meg az **Azure Active Directory** > **alkalmazásregisztrációk**at.

3. Válassza ki a FarmBeats központi telepítésének részeként létrehozott **alkalmazásregisztrációt.** Ugyanaz lesz a neve, mint a FarmBeats Datahub.

4. Válassza **az API->** Válassza **az Ügyfélalkalmazás hozzáadása lehetőséget,** és írja be **a 04b07795-8ddb-461a-bbee-02f9e1bf7b46 parancsot,** és jelölje be a Hatókör engedélyezése **jelölőnégyzetet.** Ez hozzáférést biztosít az Azure CLI -hez (Cloud Shell) a következő lépések végrehajtásához:

5. Nyissa meg a Cloud Shellt. Ez a beállítás az Azure Portal jobb felső sarkában található eszköztáron érhető el.

    ![Az Azure Portal eszköztára](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

6. Győződjön meg arról, hogy a környezet **PowerShell-re**van állítva. Alapértelmezés szerint Bash értékre van állítva.

    ![A PowerShell eszköztárának beállítása](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

7. Töltse fel a fájlt az 1.

    ![Eszköztár feltöltése gomb](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

8. Nyissa meg azt a könyvtárat, ahová a fájlt feltöltötték. Alapértelmezés szerint a rendszer a felhasználónév alatt tölti fel a fájlokat a kezdőkönyvtárba.

9. Futtassa az alábbi parancsprogramot. A parancsfájl kéri a bérlőazonosítót, amely beszerezhető az **Azure Active Directory** > **áttekintése lapon.**

    ```azurepowershell-interactive

    ./generatePartnerCredentials.ps1   

    ```

10. A képernyőn megjelenő utasításokat követve rögzítse az **API-végpont,** **a bérlőazonosító,** **az ügyfélazonosító**, **az ügyféltitkos kulcs**és az **EventHub-kapcsolati karakterlánc értékeit.**

## <a name="create-device-or-sensor-metadata"></a>Eszköz- vagy érzékelőmetaadatok létrehozása

 Most, hogy rendelkezik a szükséges hitelesítő adatokkal, meghatározhatja az eszközt és az érzékelőket. Ehhez hozza létre a metaadatokat a FarmBeats API-k hívásával. Győződjön meg arról, hogy az API-kat a fenti szakaszban létrehozott ügyfélalkalmazásként hívja meg.

 A FarmBeats Datahub a következő API-kat, amelyek lehetővé teszik az eszköz vagy érzékelő metaadatok létrehozását és kezelését.

 > [!NOTE]
 > Partnerként csak a metaadatok olvasásához, létrehozásához és frissítéséhez férhet hozzá; **törlési lehetőség a partnerre korlátozódik.**

- /**DeviceModel**: DeviceModel megfelel a metaadatok az eszköz, például a gyártó és az eszköz típusát, amely vagy egy átjáró vagy egy csomópont.
- /**Eszköz**: Az eszköz a gazdaságban lévő fizikai eszköznek felel meg.
- /**SensorModel**: SensorModel megfelel az érzékelő metaadatainak, például a gyártónak, az analóg vagy digitális érzékelő típusának, valamint az érzékelő mérésének, például a környezeti hőmérsékletnek és a nyomásnak.
- /**Érzékelő**: Az érzékelő egy fizikai érzékelőnek felel meg, amely rögzíti az értékeket. Az érzékelő általában eszközazonosítóval rendelkező eszközhöz csatlakozik.  


|        DeviceModel   |  Javaslatok   |
| ------- | -------             |
|     Típus (csomópont, átjáró)        |          Az eszköz típusa - csomópont vagy átjáró      |
|          Gyártó            |         A gyártó neve    |
|  Termékkód                    |  Eszköztermék-kódja vagy modellneve vagy száma. Például EnviroMonitor#6800.  |
|            Portok          |     Port neve és típusa, amely digitális vagy analóg.
|     Név                 |  Az erőforrás azonosítására szolgáló név. Például a modell vagy a termék neve.
      Leírás     | Adja meg a modell értelmes leírását.
|    Tulajdonságok          |    A gyártó további tulajdonságai.   |
|    **Eszköz**             |                      |
|   DeviceModelId     |     A társított eszközmodell azonosítója.  |
|  Hardverazonosító          | Az eszköz egyedi azonosítója, például a MAC-cím.
|  ReportingInterval        |   Jelentési időköz másodpercben.
|  Hely            |  Az eszköz szélességi foka (-90 és +90 között), hosszúság (-180-180) és magasság (méterben).   
|ParentDeviceId azonosító       |    Annak a szülőeszköznek az azonosítója, amelyhez az eszköz csatlakozik. Például egy átjáróhoz csatlakoztatott csomópont. Egy csomópont rendelkezik parentDeviceId átjáróként.  |
|    Név            | Az erőforrás azonosítására szolgáló név. Az eszközpartnereknek olyan nevet kell küldeniük, amely megfelel a partneroldalon lévő eszköznevének. Ha a partnereszköz neve felhasználó által definiált, akkor ugyanazt a felhasználó által definiált nevet kell terjeszteni a FarmBeats-re.|
|     Leírás       |      Adjon meg egy értelmes leírást. |
|     Tulajdonságok    |  A gyártó további tulajdonságai.
|     **Érzékelőmodell**        |          |
|       Típus (analóg, digitális)          |      Az érzékelő típusa, legyen az analóg vagy digitális.       |
|          Gyártó            |       Az érzékelő gyártója.     |
|     Termékkód| Termékkód vagy modellnév vagy -szám. Például RS-CO2-N01. |
|       SensorMeasures > név       | Az érzékelőmérték neve. Csak kisbetűs támogatott. A különböző mélységből történő mérésekhez adja meg a mélységet. Például soil_moisture_15cm. Ennek a névnek konzisztensnek kell lennie a telemetriai adatokkal.  |
|          SensorMeasures > DataType       |Telemetriai adattípus. Jelenleg a dupla támogatott.|
|    SensorMeasures > Típus    |Az érzékelő telemetriai adatainak mérési típusa. A rendszer által meghatározott típusok: AmbientTemperature, CO2, Mélység, Elektromos vezetés, LeafWetness, Hossz, LiquidLevel, Nitrát, O2, PH, foszfát, PointInTime, kálium, nyomás, RainGauge, RelatívPáratartalom, Sótartalom, TalajNedvesség, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. To add more, refer to the /ExtendedType API.|
|        SensorMeasures > egység              | Érzékelő telemetriai adatok egysége. A rendszer által meghatározott egységek: NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Higany, PSI, Milliméter, Centiméter, Méter, Inch, Láb, Mérföld, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Százalék, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeter MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricionContent, Liter, MilliLiter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour További hozzáadható, olvassa el a /ExtendedType API-t.|
|    SensorMeasures > AggregationType    |  Az értékek lehetnek nincsek, átlagosak, maximumak, minimumok vagy StandardDeviation.  |
|          Név            | Erőforrás azonosítására szolgáló név. Például a modell vagy a termék neve.  |
|    Leírás        | Adja meg a modell értelmes leírását.|
|   Tulajdonságok       |  A gyártó további tulajdonságai.|
|    **Érzékelő**      |          |
| Hardverazonosító          |   A gyártó által beállított érzékelő egyedi azonosítója.|
|  SensorModelId     |    A társított érzékelőmodell azonosítója.|
| Hely          |  Az érzékelő szélességi foka (-90 és +90 között), hosszúság (-180-180) és magasság (méterben).|
|   Port > neve        |  Annak a portnak a neve és típusa, amelyhez az érzékelő csatlakozik az eszközön. Ennek meg kell egyeznie az eszközmodellben meghatározott névvel.|
|    Deviceid  |    Annak az eszköznek az azonosítója, amelyhez az érzékelő csatlakozik. |
| Név            |   Az erőforrás azonosítására szolgáló név. Például az érzékelő neve vagy a termék neve és a modellszáma vagy a termékkód.|
|    Leírás      | Adjon meg egy értelmes leírást.|
|    Tulajdonságok        |A gyártó további tulajdonságai.|

Az objektumokról további információt a [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)című témakörben talál.

### <a name="api-request-to-create-metadata"></a>API-kérelem metaadatok létrehozására

API-kérelem benyújtásához kombinálja a HTTP (POST) metódust, az API-szolgáltatás URL-címét és az URI-t egy erőforrással, amely lekérdezheti, adatokat küldhet, létrehozhat vagy törölhet egy kérelmet. Ezután egy vagy több HTTP-kérelemfejlécet ad hozzá. Az API-szolgáltatás URL-címe az API-végpont, azaz a\<Datahub URL-címe (https:// yourdatahub>.azurewebsites.net).  

### <a name="authentication"></a>Hitelesítés

A FarmBeats Datahub bemutatóra szóló hitelesítést használ, amelyaz előző szakaszban létrehozott alábbi hitelesítő adatokat igényel:

- Ügyfél-azonosító
- Titkos ügyfélkulcs
- Bérlőazonosító

Ezekkel a hitelesítő adatokkal a hívó kérhet egy hozzáférési jogkivonatot. A jogkivonatot a következő API-kérelmekben kell elküldeni a fejlécszakaszban, az alábbiak szerint:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

A következő Python-mintakód adja meg a hozzáférési jogkivonatot, amely a FarmBeats későbbi API-hívásaihoz használható: 

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

**HTTP-kérelem fejlécei**

Az alábbiakban a farmbeats datahub API-hívásakor megkell adni a leggyakoribb kérésfejléceket:

- **Tartalomtípus:** alkalmazás/json
- **Engedély**: tulajdonos <Access-Token>
- **Elfogadás**: alkalmazás/json

### <a name="input-payload-to-create-metadata"></a>Bemeneti hasznos adat metaadatok létrehozásához

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

Érzékelőmodell

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

A következő mintakérelem létrehoz egy eszközt. Ez a kérelem bemeneti JSON a kérelem törzse.

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

Az alábbiakban egy python-beli mintakód látható. A mintában használt hozzáférési jogkivonat megegyezik a hitelesítés során kapott hozzáférési jogkivonat.

```python
import requests
import json

# Got access token - Calling the Device Model API

headers = {
    "Authorization": "Bearer " + access_token,
    "Content-Type" : "application/json"
    }
payload = '{"type" : "Node", "productCode" : "TestCode", "ports": [{"name": "port1","type": "Analog"}], "name" : "DummyDevice"}'
response = requests.post(ENDPOINT + "/DeviceModel", data=payload, headers=headers)
```

> [!NOTE]
> Az API-k egyedi azonosítókat adnak vissza minden létrehozott példányhoz. Meg kell őriznie az azonosítókat a megfelelő telemetriai üzenetek küldéséhez.

### <a name="send-telemetry"></a>Telemetria küldése

Most, hogy létrehozta az eszközöket és érzékelőket a FarmBeats alkalmazásban, elküldheti a kapcsolódó telemetriai üzeneteket.

### <a name="create-a-telemetry-client"></a>Telemetriai ügyfél létrehozása

A telemetriai adatokat az Azure Event Hubs feldolgozásra kell küldenie. Az Azure Event Hubs egy olyan szolgáltatás, amely lehetővé teszi a valós idejű adat -(telemetria) betöltését a csatlakoztatott eszközökről és alkalmazásokról. Telemetriai adatok küldéséhez a FarmBeats, hozzon létre egy ügyfelet, amely üzeneteket küld egy eseményközpont farmbeats. A telemetriai adatok küldéséről az [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)című témakörben talál további információt.

### <a name="send-a-telemetry-message-as-the-client"></a>Telemetriai üzenet küldése ügyfélként

Miután létrehozott egy kapcsolatot, mint egy Event Hubs-ügyfél, üzeneteket küldhet az eseményközpontjjal JSON néven.

Az alábbiakban mintát talál a Python-kódból, amely telemetriai adatokat küld ügyfélként egy megadott eseményközpontba:

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

Konvertálja a korábbi érzékelő adatformátumát olyan kanonikus formátumra, amelyet az Azure FarmBeats megért. A gyűjtőüzenet formátuma a következő:

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
          "<sensor measure name (as defined in the Sensor Model)>": "<values>"
        }
      ]
    }
 ]
}
```

Miután hozzáadja a megfelelő eszközöket és érzékelőket, szerezze be az eszközazonosítót és az érzékelő azonosítóját a telemetriai üzenetben, az előző szakaszban leírtak szerint.

Íme egy példa egy telemetriai üzenetre:


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

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Nem lehet megtekinteni a telemetriai adatokat az érzékelők ből származó előzmény-/streamelési adatok betöltése után

**Jelenség:** Eszközök vagy érzékelők vannak telepítve, és létrehozta az eszközöket/érzékelőket a FarmBeats és a bevitt telemetriai adatokat az EventHubon, de nem tudja letenni vagy megtekinteni a telemetriai adatokat a FarmBeats.Symptoms: Devices or sensors are deployed, and you've created the devices/sensors on FarmBeats and intested telemetry to the EventHub, but you can't get or view telemettry data on FarmBeats.

**Korrekciós intézkedés**:

1. Győződjön meg arról, hogy elvégezte a megfelelő partnerregisztrációt – ezt ellenőrizheti a datahub swagger, keresse meg a /Partner API, Do a Get, és ellenőrizze, hogy a partner regisztrálva van-e. Ha nem, kövesse az [itt leírt lépéseket](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) a partner hozzáadásához.

2. Győződjön meg arról, hogy a metaadatokat (DeviceModel, Device, SensorModel, Sensor) a partnerügyfél hitelesítő adatai val hozta létre.

3. Győződjön meg arról, hogy a megfelelő Telemetriai üzenetformátumot használta (az alábbiakban megadottak szerint):

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

## <a name="next-steps"></a>További lépések

A REST API-alapú integrációrészleteiről a [REST API című témakörben](rest-api-in-azure-farmbeats.md)talál további információt.

---
title: Érzékelői partner integrációja
description: Ez a cikk az érzékelőpartnerek integrációját ismerteti.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 48a2ed5e4774ac07b4b8fa72a5ee0be86811cfb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298733"
---
# <a name="sensor-partner-integration"></a>Érzékelői partner integrációja

Ez a cikk az Azure FarmBeats **Translator** összetevőről tartalmaz információt, amely lehetővé teszi az érzékelőpartnerek integrációját.

Ezzel az összetevővel a partnerek integrálhatók a FarmBeats-rel a FarmBeats Datahub API-k használatával, és ügyféleszköz-adatokat és telemetriát küldhetnek a FarmBeats Datahub-nak. Miután az adatok elérhetők a FarmBeats-ben, a FarmBeats gyorsító használatával láthatóvá válnak, és felhasználhatók az adatok fúziójára és a gépi tanulás/mesterséges intelligencia modellek készítésére.

## <a name="before-you-start"></a>Előkészületek

A Translator-összetevő fejlesztéséhez a következő hitelesítő adatokra lesz szüksége, amelyek lehetővé teszik a FarmBeats API-k elérését.

- API-végpont
- Bérlőazonosító
- Ügyfél-azonosító
- Titkos ügyfélkulcs
- EventHub-kapcsolati karakterlánc

Ebben a szakaszban a fenti hitelesítő adatok: [Eszközintegráció engedélyezése](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>Fordítófejlesztés

**REST API-alapú integráció**

A FarmBeats érzékelőadatok-integrációs képességei a REST API-n keresztül vannak elérhetővé téve. A képességek közé tartozik a metaadat-definíció, az eszköz- és érzékelőkiépítés, valamint az eszköz- és érzékelőkezelés.

**Telemetriai betöltés**

A telemetriai adatok egy olyan kanonikus üzenethez vannak leképezve, amely feldolgozásra az Azure Event Hubs-on van közzétéve. Az Azure Event Hubs egy olyan szolgáltatás, amely lehetővé teszi a valós idejű adat -(telemetria) betöltését a csatlakoztatott eszközökről és alkalmazásokról.

**API-fejlesztés**

Az API-k swagger műszaki dokumentációt tartalmaznak. Az API-król és a hozzájuk tartozó kérésekről vagy válaszokról a [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)című témakörben talál további információt.

**Hitelesítés**

A FarmBeats a Microsoft Azure Active Directory-hitelesítést használja.Az Azure App Service beépített hitelesítési és engedélyezési támogatást biztosít.

További információ: [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

A FarmBeats Datahub bemutatóra szóló hitelesítést használ, amelyhez a következő hitelesítő adatokra van szükség:
   - Ügyfél-azonosító
   - Titkos ügyfélkulcs
   - Bérlőazonosító

Ezekkel a hitelesítő adatokkal a hívó kérhet egy hozzáférési jogkivonatot. A jogkivonatot a következő API-kérelmekben kell elküldeni a fejlécszakaszban, az alábbiak szerint:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

A következő python-kódmintát adja a hozzáférési jogkivonatot, amely a FarmBeats későbbi API-hívásaihoz használható.

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

Az alábbiakban a leggyakoribb kérésfejléceket kell megadni, amikor API-hívást kezdeményez a FarmBeats Datahub-ra.


**Fejléc** | **Leírás és példa**
--- | ---
Content-Type | A kérelem formátuma (Content-Type: application/<format>). A FarmBeats Datahub API-k esetében a formátum JSON. Tartalom-típus: alkalmazás/json
Engedélyezés | Megadja az API-híváshoz szükséges hozzáférési jogkivonatot. Engedélyezés: a tulajdonos <hozzáférési>
Elfogadás | A válasz formátuma. A FarmBeats Datahub API-k esetében a formátum JSON. Elfogadás: alkalmazás/json

**API-kérelmek**

REST API-kérelem benyújtásához kombinálja a HTTP (GET, POST vagy PUT) metódust, az API-szolgáltatás URL-címét, az egységes erőforrás-azonosítót (URI) egy erőforrásba, amely lekérdezheti, adatokat küldhet, frissít vagy törölhet, valamint egy vagy több HTTP-kérelemfejlécet. Az API-szolgáltatás URL-címe a megadott API-végpont. Íme egy példa:\<https:// yourdatahub-website-name>.azurewebsites.net

Szükség esetén lekérdezési paramétereket is megadhat a GET-hívásokhoz, korlátozhatja a válaszok ban lévő adatok méretét, és rendezheti az adatokat.

A következő mintakérelem az eszközök listájának bekérése.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
A legtöbb GET, POST és PUT hívások igényel JSON kérelem törzs.

A következő mintakérelem egy eszköz létrehozása. (Ez a minta rendelkezik egy bemeneti JSON a kérelem törzse.)

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Adatformátum

A JSON egy közös, nyelvfüggetlen adatformátum, amely egyszerű szövegábrázolást biztosít tetszőleges adatstruktúrákról. További információ: [json.org](http://json.org).

## <a name="metadata-specifications"></a>Metaadatok specifikációk

A FarmBeats Datahub a következő API-kat kínál, amelyek lehetővé teszik az eszközpartnerek számára az eszköz- vagy érzékelőmetaadatok létrehozását és kezelését.

- /**DeviceModel**: DeviceModel megfelel a metaadatok az eszköz, például a gyártó és az eszköz típusát, amely vagy átjáró vagy csomópont.
- /**Eszköz**: Az eszköz a gazdaságban lévő fizikai eszköznek felel meg.
- /**SensorModel**: SensorModel megfelel az érzékelő metaadatainak, például a gyártónak, az analóg vagy digitális érzékelő típusának, valamint az érzékelő mértékének, például a környezeti hőmérsékletnek és a nyomásnak.
- /**Érzékelő**: Az érzékelő egy fizikai érzékelőnek felel meg, amely rögzíti az értékeket. Az érzékelő általában eszközazonosítóval rendelkező eszközhöz csatlakozik.

  **DeviceModel** |  |
  --- | ---
  Típus (csomópont, átjáró)  | Az eszköz típusa - Csomópont vagy átjáró |
  Gyártó  | A gyártó neve |
  Termékkód  | Eszköztermék-kódja vagy modellneve vagy száma. Például EnviroMonitor#6800. |
  Portok  | Port neve és típusa, amely digitális vagy analóg.  |
  Név  | Az erőforrás azonosítására szolgáló név. Például a modell vagy a termék neve. |
  Leírás  | Adja meg a modell értelmes leírását. |
  Tulajdonságok  | A gyártó további tulajdonságai. |
  **Eszköz** |  |
  DeviceModelId  |A társított eszközmodell azonosítója. |
  Hardverazonosító   |Az eszköz egyedi azonosítója, például EGY MAC-cím.  |
  ReportingInterval |Jelentési időköz másodpercben. |
  Hely    |Az eszköz szélességi foka (-90 és +90 között), hosszúság (-180-180) és magasság (méterben). |
  ParentDeviceId azonosító | Annak a szülőeszköznek az azonosítója, amelyhez az eszköz csatlakozik. Ha például egy csomópont csatlakozik egy átjáróhoz, a csomópont rendelkezik parentDeviceID átjáróként. |
  Név  | Az erőforrás azonosítására szolgáló név. Az eszközpartnereknek olyan nevet kell küldeniük, amely megfelel az eszköz partneroldalán lévő eszköznevének. Ha az eszköznév felhasználó által definiált az eszközpartner oldalán, ugyanazt a felhasználó által definiált nevet kell terjeszteni a FarmBeats-re.  |
  Leírás  | Adjon meg egy értelmes leírást.  |
  Tulajdonságok  |A gyártó további tulajdonságai.  |
  **Érzékelőmodell** |  |
  Típus (analóg, digitális)  |Említse meg az analóg vagy digitális érzékelőt.|
  Gyártó  | A gyártó neve. |
  Termékkód  | Termékkód vagy modellnév vagy -szám. Például RS-CO2-N01.  |
  SensorMeasures > név  | Az érzékelőmérték neve. Csak kisbetűs támogatott. A különböző mélységből történő mérésekhez adja meg a mélységet. Például soil_moisture_15cm. Ennek a névnek konzisztensnek kell lennie a telemetriai adatokkal. |
  SensorMeasures > DataType  | Telemetriai adattípus. Jelenleg a dupla támogatott. |
  SensorMeasures > Típus  | Az érzékelő telemetriai adatainak mérési típusa. A rendszer által meghatározott típusok: AmbientTemperature, CO2, Depth, ElectricalConducttivity, LeafWetness, Length, LiquidLevel, Nitrát, O2, PH, foszfát, PointInTime, Kálium, Nyomás, RainGauge, RelativeHumidity, Sótartalom, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, WindSpeed, Evapotranspiration, PAR. To add more, refer to the /ExtendedType API.
  SensorMeasures > egység | Érzékelő telemetriai adatok egysége. A következők a rendszer által meghatározott egységek: NoUnit, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, Milliméter, CentiMéter, Méter, Inch, Láb, Mérföld, KiloMeter, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MéterperSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Százalék, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricionContent, Liter, Milliliter, Seconds, UnixTimestamp, MicroMolPerMeterSquaredPerSecond és InchesPerHour. To add more, refer to the /ExtendedType API.
  SensorMeasures > AggregationType  | Vagy nincs, átlagos, maximális, minimális vagy StandardDeviation.
  SensorMeasures > mélység  | Az érzékelő mélysége centiméterben. Például a mérésa nedvesség 10 cm a föld alatt.
  SensorMeasures > leírás  | Adja meg a mérés értelmes leírását.
  Név  | Az erőforrás azonosítására szolgáló név. Például a modell vagy a termék neve.
  Leírás  | Adja meg a modell értelmes leírását.
  Tulajdonságok  | A gyártó további tulajdonságai.
  **Érzékelő**  |  |
  Hardverazonosító  | A gyártó által beállított érzékelő egyedi azonosítója.
  SensorModelId  | A társított érzékelőmodell azonosítója.
  Hely  | Az érzékelő szélességi foka (-90 és +90 között), hosszúság (-180-180) és magasság (méterben).
  Port > neve  |Annak a portnak a neve és típusa, amelyhez az érzékelő csatlakozik az eszközön. Ennek meg kell egyeznie az eszközmodellben meghatározott névvel.
  DeviceId  | Annak az eszköznek az azonosítója, amelyhez az érzékelő csatlakozik.
  Név  | Az erőforrás azonosítására szolgáló név. Például az érzékelő neve vagy a termék neve és a modell száma vagy a termékkód.
  Leírás  | Adjon meg egy értelmes leírást.
  Tulajdonságok  | A gyártó további tulajdonságai.

 Az egyes objektumokról és azok tulajdonságairól a [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)című témakörben talál további információt.

 > [!NOTE]
 > Az API-k egyedi azonosítókat adnak vissza minden létrehozott példányhoz. Ezt az azonosítót a Fordítónak meg kell őriznie az eszközkezeléshez és a metaadatok szinkronizálásához.


**Metaadatok szinkronizálása**

A fordítónak frissítéseket kell küldenie a metaadatokról. A frissítési forgatókönyvek például az eszköz vagy az érzékelő nevének és az eszköz vagy az érzékelő helyének megváltoztatása.

A fordítónak képesnek kell lennie arra, hogy új eszközöket vagy érzékelőket adjon hozzá, amelyeket a FarmBeats felhasználói bejegyzése ily ként telepített. Hasonlóképpen, ha a felhasználó egy eszközt vagy érzékelőt frissített, akkor a farmbeats-ben is frissíteni kell a megfelelő eszközt vagy érzékelőt. Az eszköz vagy érzékelő frissítését igénylő tipikus forgatókönyvek az eszköz helyének módosítása vagy érzékelők hozzáadása egy csomópontban.


> [!NOTE]
> Az eszköz- vagy érzékelőmetaadatok nem támogatottak a törlés.
>
> A metaadatok frissítéséhez kötelező meghívni a /Get/{id} kapcsolót az eszközön vagy az érzékelőn, frissíteni a módosított tulajdonságokat, majd a /Put/{id} parancsot, hogy a felhasználó által beállított tulajdonságok ne vesszenek el.

### <a name="add-new-types-and-units"></a>Új típusok és egységek hozzáadása

A FarmBeats támogatja az új érzékelőmérték-típusok és -egységek hozzáadását. Az /ExtendedType API-ról további információt a [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)című témakörben talál.

## <a name="telemetry-specifications"></a>Telemetriai specifikációk

A telemetriai adatok egy olyan kanonikus üzenethez vannak leképezve, amely feldolgozásra az Azure Event Hubs-on van közzétéve. Az Azure Event Hubs egy olyan szolgáltatás, amely lehetővé teszi a valós idejű adat -(telemetria) betöltését a csatlakoztatott eszközökről és alkalmazásokról.

## <a name="send-telemetry-data-to-farmbeats"></a>Telemetriai adatok küldése a FarmBeats-nek

Telemetriai adatok küldéséhez a FarmBeats, hozzon létre egy ügyfelet, amely üzeneteket küld egy eseményközpont farmbeats. A telemetriai adatokról a [Telemetriai adatok küldése egy eseményközpontba című](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)témakörben talál további információt.

Az alábbiakban egy python-kódmintát talál, amely ügyfélként telemetriai adatokat küld egy adott eseményközpontba.

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

A gyűjtőüzenet formátuma a következő:

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
A JSON telemetriai adatokban szereplő összes kulcsnévnek kisbetűsnek kell lennie. Ilyenek például a deviceid és sensordata.

Például az alábbiakban egy telemetriai üzenetet olvashat:


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
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

> [!NOTE]
> A következő szakaszok kapcsolódnak más változások (pl. Felhasználói felület, hibakezelés stb.) hogy az érzékelő partner utalhat a Fordító komponens fejlesztése során.


## <a name="link-a-farmbeats-account"></a>FarmBeats-fiók csatolása

Miután az ügyfelek megvásárolták és telepítették az eszközöket vagy érzékelőket, hozzáférhetnek az eszközadatokhoz és a telemetriai adatokhoz az eszközpartnerek szoftverén szolgáltatásként (SaaS) portálként. Az eszközpartnerek lehetővé tehetik az ügyfelek számára, hogy összekapcsolják fiókjukat az Azure-beli FarmBeats-példányukkal azáltal, hogy a következő hitelesítő adatok megadását biztosítják:

   - Megjelenítendő név (az integráció nevének definiálására szolgáló választható mező)
   - API-végpont
   - Bérlőazonosító
   - Ügyfél-azonosító
   - Titkos ügyfélkulcs
   - EventHub-kapcsolati karakterlánc
   - Kezdő dátum

   > [!NOTE]
   > A kezdő dátum engedélyezi az előzményadatcsatornát, azaz a felhasználó által megadott dátumadatait.

## <a name="unlink-farmbeats"></a>FarmBeats leválasztása

Az eszközpartnerek lehetővé tehetik az ügyfelek számára, hogy leváltsák a meglévő FarmBeats-integrációt. A FarmBeats leválasztása nem törölhet idovel vagy érzékelőmetaadatokkal, amelyeket a FarmBeats Datahub-ban hoztak létre. A leválasztás a következő:

   - Leállítja a telemetriai folyamatot.
   - Törli és törli az eszközpartner integrációs hitelesítő adatait.

## <a name="edit-farmbeats-integration"></a>FarmBeats-integráció szerkesztése

Az eszközpartnerek lehetővé tehetik az ügyfelek számára a FarmBeats-integrációs beállítások szerkesztését, ha az ügyféltitkos vagy kapcsolati karakterlánc megváltozik. Ebben az esetben csak a következő mezők szerkeszthetők:

   - Megjelenítendő név (ha van ilyen)
   - Ügyféltitkos adat (a tiszta szöveg helyett "2x8*********** " formátumban vagy a Megjelenítés/elrejtés funkcióban kell megjelennie)
   - Kapcsolati karakterlánc (a tiszta szöveg helyett "2x8*********** " formátumban vagy Megjelenítés/elrejtés funkcióban kell megjelennie)

## <a name="view-the-last-telemetry-sent"></a>Az utoljára elküldött telemetriai adatok megtekintése

Az eszközpartnerek lehetővé tehetik az ügyfelek számára, hogy megtekintsék az utoljára elküldött telemetriai adatok időbélyegét, amely az **Elküldött telemetriai**adatok alatt található. Ez az az időpont, amikor a legutóbbi telemetriai adatok sikeresen elküldésre kerültek a FarmBeats-nek.

## <a name="troubleshooting-and-error-management"></a>Hibaelhárítás és hibakezelés

**Hibaelhárítási lehetőség vagy támogatás**

Ha az ügyfél nem tudja fogadni az eszközadatokat vagy a telemetriai adatokat a megadott FarmBeats-példányban, az eszközpartnernek támogatást és hibaelhárítási mechanizmust kell biztosítania.

**Telemetriai adatmegőrzés**

A telemetriai adatokat is meg kell őrizni egy előre meghatározott ideig, hogy hasznos lehet a hibakeresés, vagy újraküldése a telemetriai hiba vagy adatvesztés bekövetkezése esetén.

**Hibakezelés vagy hibaértesítés**

Ha egy hiba befolyásolja az eszköz vagy érzékelő metaadatait, vagy az adatintegrációs vagy telemetriai adatfolyam az eszköz partnerrendszerben, az ügyfél értesítést kell kapnia. A hibák megoldására szolgáló mechanizmust is meg kell tervezni és végre kell hajtani.

**Kapcsolat-ellenőrzőlista**

Az eszközgyártók vagy -partnerek az alábbi ellenőrzőlistát használhatják annak biztosítására, hogy az ügyfél által megadott hitelesítő adatok pontosak legyenek:

   - Ellenőrizze, hogy a megadott hitelesítő adatokkal kap-e hozzáférési jogkivonatot.
   - Ellenőrizze, hogy egy API-hívás sikeres-e a kapott hozzáférési jogkivonattal.
   - Ellenőrizze, hogy létrejött-e az EventHub-ügyfélkapcsolat.

## <a name="next-steps"></a>További lépések

A REST API-ról további információt a [REST API című témakörben](rest-api-in-azure-farmbeats.md)talál.

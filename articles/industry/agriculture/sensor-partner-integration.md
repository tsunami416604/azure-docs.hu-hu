---
title: Sensor partner-integráció
description: Az érzékelő partner-integráció ismertetése
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e7de815b7254fb071b3094f9ae636b712b38684b
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797685"
---
# <a name="sensor-partner-integration"></a>Sensor partner-integráció
Ez a cikk az Azure FarmBeats **Translator** összetevővel kapcsolatos információkat tartalmaz.

Ennek az összetevőnek a használatával a partnerek olyan érzékelők fejlesztéséhez, amelyek integrálva vannak az FarmBeats, az API kihasználásával, valamint az ügyfél-eszköz adatai és a telemetria az FarmBeats-adatközpontba való küldésével. Az adatvizualizáció a FarmBeats-gyorsító használatával történik. Az adatok az adatfúzióhoz, valamint a gépi nyelvi/mesterséges intelligencia modellek létrehozásához is használhatók.

## <a name="link-farmbeats-account"></a>FarmBeats-fiók csatolása

Miután az ügyfelek megvásárolták és telepítették az eszközöket/érzékelőket, hozzáférhetnek az eszközre és a telemetria az eszközökhöz tartozó SaaS-portálon (szoftver szolgáltatásként). Az eszközökhöz tartozó partnereknek lehetővé kell tenniük az ügyfelek számára, hogy az Azure-beli FarmBeats-példánnyal összekapcsolják a fiókját. A következő hitelesítő adatok szükségesek az ügyfél/SI kitöltéséhez:

   - Megjelenítendő név (egy opcionális mező a felhasználó számára az integrációhoz tartozó név definiálásához)
   - API-végpont
   - Bérlőazonosító
   - Ügyfél-azonosító
   - Titkos ügyfélkulcs
   - EventHub-kapcsolatok karakterlánca
   - Kezdő dátum

   > [!NOTE]
   > A kezdő dátum lehetővé teszi a korábbi adatcsatorna, azaz a felhasználó által megadott dátumtól származó adatok használatát.

## <a name="unlink-farmbeats"></a>FarmBeats leválasztása

Az ügyfeleknek lehetősége van egy meglévő FarmBeats-integráció leválasztására. A FarmBeats leválasztása nem törölheti az ügyfél adatközpontjában létrehozott összes eszköz-vagy érzékelő-metaadatot. A Leválasztás a következő műveleteket végzi el:

   - Leállítja a telemetria folyamatot.
   - Törli és törli az integrációs hitelesítő adatokat az eszköz partnerén.

## <a name="edit-farmbeats-integration"></a>FarmBeats-integráció szerkesztése

Az ügyfél szerkesztheti a FarmBeats-integrációt. A Szerkesztés elsődleges forgatókönyve az, amikor az ügyfél titka vagy a kapcsolódási sztring lejárat miatt módosul, ebben az esetben az ügyfél csak a következő mezőket tudja szerkeszteni.

   - Megjelenítendő név (ha van ilyen)
   - Az ügyfél titka (a "2x8 * * * * * * * * * * * * *" formátumban vagy a Megjelenítés/elrejtés funkció nem tiszta szöveg helyett) jelenik meg.
   - Kapcsolati karakterlánc (a "2x8 * * * * * * * * * * * * * *"

   > [!NOTE]
   > A szerkesztés nem szakítja meg a metaadat-objektumok létrehozását.

## <a name="view-last-telemetry-sent"></a>Legutóbbi telemetria-Elküldések megtekintése

Megtekintheti az **eljuttatott utolsó telemetria**időbélyegét. Ez az az idő, amikor a legújabb telemetria sikeresen elküldtük a FarmBeats.

## <a name="translator-development"></a>Fordítói fejlesztés

**REST API-alapú integráció**

A FarmBeats érzékelő-adatintegrációs képességei elérhetők a REST APIon keresztül. A képességek közé tartozik a metaadatok definíciója, az eszköz/érzékelő üzembe helyezése, az eszköz és az érzékelő kezelése.

**Telemetria betöltése**

A telemetria-adatainak feldolgozásra az Azure Event hub-ban közzétett kanonikus üzenetre van leképezve. Az Azure EventHub egy olyan szolgáltatás, amely lehetővé teszi a valós idejű adatok (telemetria) betöltését a csatlakoztatott eszközökről és alkalmazásokból.

**API-fejlesztés**

Az API-k a hencegés műszaki dokumentációját tartalmazzák. További információ az API-król és a hozzájuk tartozó kérésekről/válaszokról: [hencegés](https://aka.ms/FarmBeatsDatahubSwagger)

**Hitelesítés**

A FarmBeats Microsoft Azure Active Directory hitelesítését használja. A Azure App Service beépített hitelesítési és engedélyezési támogatást biztosít.

További információ: [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

A FarmBeats adatközpont tulajdonosi hitelesítést használ, amelynek a következő hitelesítő adatokra van szüksége:
   - Ügyfél-azonosító
   - Ügyfél titka
   - Bérlőazonosító

A fenti hitelesítő adatok használatával a hívó kérhet egy hozzáférési jogkivonatot, amelyet a következő API-kérelmekben kell elküldeni a fejléc szakaszban a következő módon:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Az alábbi példa egy Python-kódot tartalmaz, amely megadja a hozzáférési jogkivonatot, amely a következő API-hívásokhoz használható a FarmBeats: 

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

Itt láthatók a leggyakoribb kérelmek fejlécei, amelyeket meg kell adni az API-hívások FarmBeats-adatközpontba való készítésekor:


**Fejléc** | **Leírás és példa**
--- | ---
Content-Type | A FarmBeats adatközpont API-formátumának kérelem formátuma (Content-Type: Application/<format>) JSON. Content-Type: Application/JSON
Engedélyezés | Megadja az API-hívások engedélyezéséhez szükséges hozzáférési jogkivonatot: tulajdonos < hozzáférési jogkivonat >
fogadja el | A válasz formátuma. A FarmBeats adatközponti API-k formátuma a következő: JSON Accept (alkalmazás/JSON)

**API-kérelmek**

Ha REST-(reprezentációs állapot-átadási) API-kérést szeretne létrehozni, akkor a HTTP (GET, POST, vagy PUT) metódust, az API-szolgáltatás URL-címét, az URI-t (Uniform Resource Identifier) a lekérdezéshez, az adatok elküldéséhez, frissítéséhez vagy törléséhez, valamint egy vagy több HTTP-kérelemhez kell egyesíteni. fejlécek. Az API-szolgáltatás URL-címe az ügyfél által megadott API-végpont. Példa: https://\<yourdatahub-website-Name >. azurewebsites. net

Igény szerint a lekérdezési paramétereket is megadhatja a szűréshez, korlátozhatja az adatok méretét, és rendezheti a válaszokat.

Az alábbi példa az eszközök listájának beszerzésére szolgál:

```
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```
A legtöbb GET, POST és PUT híváshoz JSON-kérést tartalmazó törzs szükséges.

Az alábbi példa egy eszköz létrehozására szolgál (ez a minta egy bemeneti JSON-t tartalmaz a kérelem törzsével).

```
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Adatformátum

A JSON (JavaScript Object Notation) egy közös, nyelvtől független adatformátum, amely tetszőleges adatstruktúrák egyszerű szöveges ábrázolását teszi lehetővé. További információ: [JSON.org](http://json.org).

## <a name="metadata-specifications"></a>Metaadatok specifikációi

A FarmBeats adatközpontja a következő API-kkal rendelkezik, amelyek lehetővé teszik az eszközök partnerei számára az eszköz/érzékelő metaadatainak létrehozását és felügyeletét.  

- /**DeviceModel** – az eszköz modellje megegyezik az eszköz metaadataival, például a gyártóval, az eszköz típusával vagy átjáróval vagy csomóponttal.  
- /**eszköz** – az eszköz megfelel a farmban lévő fizikai eszköznek.
- /**SensorModel** -Sensor modell az érzékelő meta-adattípusának felel meg, például a gyártó, az érzékelő típusa analóg vagy digitális, az érzékelő mértéke, például a környezeti hőmérséklet, a nyomás stb.
- /**szenzor** – az érzékelő olyan fizikai érzékelőnek felel meg, amely értékeket rögzít. Az érzékelő általában eszköz-AZONOSÍTÓval van csatlakoztatva egy eszközhöz.

  Eszköz modellje| A DeviceModel az eszköz metaadatainak felelnek meg, például a gyártó, az eszköz típusa vagy átjáró vagy csomópont.
  --- | ---
  Típus (csomópont, átjáró)  | 1 csillag |
  Gyártó  | 2 csillag |
  ProductCode  | Eszköz Termékkód vagy modell neve/száma. Például: EnviroMonitor # 6800 |
  Portok  | Port neve és típusa (digitális/analóg)  |
  Név  | Az erőforrást azonosító név. Például: modell neve/terméknév |
  Leírás  | Adjon meg egy értelmes leírást a modellről |
  Tulajdonságok  | További tulajdonságok a gyártótól |
  **Eszköz** | **Az eszköz megfelel a farmban lévő fizikai eszköznek. Minden eszköz egyedi eszköz-AZONOSÍTÓval rendelkezik** |
DeviceModelId  |A társított eszköz-modell azonosítója. |
HardwareId   |Az eszköz egyedi azonosítója, például MAC-címe stb.  |
ReportingInterval |Jelentéskészítési időköz (másodperc) |
Hely    |Eszköz szélesség (-90 – + 90)/Longitude (-180 – 180)/Elevation (méter) |
ParentDeviceId | Azon szülő eszköz azonosítója, amelyhez az eszköz csatlakozik. Például:. Átjáróhoz csatlakozó csomópont; a csomópont parentDeviceID lesz az átjáróként |
  Név  | Az erőforrást azonosító név.  Az eszköz partnereinek olyan nevet kell küldeniük, amely konzisztens az eszköz nevével az eszköz partner oldalán. Ha az eszköz neve felhasználó által definiálva van az eszköz partnere oldalon, a felhasználó által definiált nevet is propagálni kell a FarmBeats  |
  Leírás  | Adjon meg egy értelmes Leírást  |
  Tulajdonságok  |További tulajdonságok a gyártótól  |
  **Érzékelő modell** | A SensorModel az érzékelő metaadatait, például a gyártót, az érzékelő típusát vagy analóg vagy digitális, az érzékelő mértékét, például a környezeti hőmérsékletet, a nyomást stb. |
  Típus (analóg, digitális)  |Analóg vagy digitális érzékelőről van szó|
  gyártó  | gyártó neve |
  ProductCode  | Termékkód vagy modell neve/száma. Például RS-CO2-N01  |
  SensorMeasures > neve  | Az érzékelő mértékének neve. Csak kisbetűk támogatottak. A különböző mélységekben mért mértéknél határozza meg a mélységet. Például soil_moisture_15cm ennek a névnek konzisztensnek kell lennie a telemetria-adattal. |
  sensorMeasures > adattípus  | Az telemetria adattípusa. Jelenleg dupla támogatott  |
  SensorMeasures > típusa  | Az érzékelő telemetria-adattípusának mértékegysége A rendszer által definiált típusok a következők: AmbientTemperature, CO2, mélység, ElectricalConductivity, LeafWetness, Length, LiquidLevel, nitrát, O2, PH, foszfát, PointInTime, kálium, nyomás, RainGauge, RelativeHumidity, sótartalom, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, szélsebesség, párolgás, PAR. További részletekért lásd a/ExtendedType API-t
  SensorMeasures > egység | Az érzékelő telemetria-adategysége. A következő rendszer által meghatározott egységek: nincs egység, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, milliméter, centiméter, Meter, hüvelyk, láb, Mile, km, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, százalék, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, liter, MilliLiter, másodperc, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour továbbiak hozzáadásához tekintse meg a/ ExtendedType API
  SensorMeasures > aggregationType  | Egyik sem, átlag, maximum, minimum, StandardDeviation
  SensorMeasures > mélység  | Az érzékelő mélysége centiméterben kifejezve (például a nedvesség mértéke 10 cm a terepen)
  sensorMeasures > leírása  | Adjon meg egy értelmes leírást a mértékről
  név  | Az erőforrást azonosító név. Például: modell neve/terméknév
  leírás  | Adjon meg egy értelmes leírást a modellről
  properties  | További tulajdonságok a gyártótól
  **Érzékelő**  |
  HardwareId  | A gyártó által beállított érzékelő egyedi azonosítója
  sensorModelId  | A társított érzékelő modell azonosítója.
  location  | Érzékelő szélesség (-90 – + 90)/Longitude (-180 – 180)/Elevation (méter)
  port > neve  |Annak a portnak a neve és típusa, amelyhez az érzékelő csatlakozik az eszközhöz. Ennek a névnek meg kell egyeznie az eszköz modelljében megadott névvel
  deviceId  | Annak az eszköznek az azonosítója, amelyhez az érzékelő csatlakozik
  név  | Az erőforrást azonosító név. Például: érzékelő neve/terméknév és modell száma/Termékkód.
  leírás  | Adjon meg egy értelmes Leírást
  properties  | További tulajdonságok a gyártótól

 Az egyes objektumokra és azok tulajdonságaira vonatkozó információkért lásd: [hencegés](httpa://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > Az API-k egyedi azonosítókat adnak vissza minden létrehozott példányhoz. Ezt az azonosítót a fordítónak meg kell őriznie az eszközkezelés és a metaadatok szinkronizálása érdekében.


**Metaadatok szinkronizálása**

A fordítónak frissítéseket kell küldenie a metaadatokon. A frissítési forgatókönyvek esetében például az eszköz/érzékelő nevének módosítása, az eszköz/érzékelő helyének módosítása.

A fordítónak képesnek kell lennie olyan új eszközök és/vagy érzékelők hozzáadására, amelyeket a FarmBeats felhasználó utáni csatolása telepített. Hasonlóképpen, ha a felhasználó frissített egy eszközt/érzékelőt, ugyanezt a megfelelő eszköz/érzékelő FarmBeats kell frissíteni. Az eszköz vagy az érzékelő frissítésének tipikus forgatókönyvei: az eszköz helyének módosítása, érzékelők hozzáadása egy csomóponthoz stb.


> [!NOTE]
> Az eszköz/érzékelő metaadatai nem támogatják a törlést.
>
> A metaadatok frissítéséhez kötelező a/Get/{ID} meghívása az eszközön/érzékelőn, frissítse a módosult tulajdonságokat, majd végezzen el egy/Put/{ID}, hogy a felhasználó által megadott tulajdonságok ne legyenek elveszve

### <a name="adding-new-typesunit"></a>Új típusok/egység hozzáadása

A FarmBeats támogatja az új szenzor mértékének típusát és egységeit. További információ a/ExtendedType API-ról, a [hencegés](https://aka.ms/FarmBeatsDatahubSwagger).

## <a name="telemetry-specifications"></a>Telemetria-specifikációk

A telemetria-adatainak feldolgozásra az Azure Event hub-ban közzétett kanonikus üzenetre van leképezve. Az Azure EventHub egy olyan szolgáltatás, amely lehetővé teszi a valós idejű adatok (telemetria) betöltését a csatlakoztatott eszközökről és alkalmazásokból.

## <a name="send-telemetry-data-to-farmbeats"></a>Telemetria-FarmBeats küldése

Ahhoz, hogy telemetria adatokat küldjön a FarmBeats, létre kell hoznia egy ügyfelet, amely üzeneteket küld egy FarmBeats lévő Event hubhoz. További információ a telemetria-adatokról: [telemetria küldése az Event hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)-ba.

Itt látható egy olyan Python-kód, amely a telemetria ügyfélként küldi el a megadott Event hub számára:

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

A kanonikus üzenet formátuma a következő:

```
{
“deviceid”: “<id of the Device created>”,
 "timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>”
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": value
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": value
        }
      ]
    }
}

```

A telemetria JSON-ban az összes kulcsnévnek kisebbnek kell lennie, például: DeviceID, sensordata stb.

Például telemetria-üzenet:


```
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

## <a name="troubleshooterror-management"></a>Hibák és hibák kezelése

**A beállítások/támogatás hibáinak megoldása**

Abban az esetben, ha az ügyfél nem tudja fogadni az eszközöket és/vagy a telemetria a megadott FarmBeats-példányban, az eszköz partnerének támogatást és egy mechanizmust kell megadnia a hibák megoldásához.

**Telemetria adatok megőrzése**

A telemetria adatokat egy előre meghatározott időszakra vonatkozóan is meg kell őrizni, hogy ugyanaz a hiba vagy adatvesztés esetén is hasznos lehet a telemetria hibakereséséhez vagy újraküldéséhez.

**Hiba kezelése/hiba értesítése**

Ha olyan hiba történt, amely hatással van az eszköz/érzékelő metaadatainak/adatintegrációra vagy a telemetria-adatfolyamatra az eszköz-partneri rendszeren, ugyanezt az ügyfelet is értesíteni kell. A hibák (ok) feloldására szolgáló mechanizmust is meg kell tervezni és megvalósítani.

**A kapcsolatok ellenőrzőlistája**

Az eszközök gyártói/partnerei rendelkezhetnek a következő épelméjű teszttel/ellenőrzőlista segítségével, hogy az ügyfél által megadott hitelesítő adatok pontosak legyenek.

   - Ellenőrizze, hogy kapott-e hozzáférési jogkivonatot a megadott hitelesítő adatokkal
   - Ellenőrizze, hogy az API-hívás sikeres-e a kapott hozzáférési jogkivonattal
   - Ellenőrizze, hogy létrejött-e az EventHub-Ügyfélkapcsolat

## <a name="next-steps"></a>További lépések

További információ a REST APIről: [REST API](references-for-farmbeats.md#rest-api).

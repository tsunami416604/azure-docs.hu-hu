---
title: Időjárási partner integrációja
description: Ez a cikk azt ismerteti, hogyan integrálható az időjárási adatszolgáltató a FarmBeats
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: a2677b5343b2d65a39e7c9f6d5006db599c1ac73
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86496995"
---
# <a name="weather-partner-integration"></a>Időjárási partner integrációja

Ez a cikk az Azure FarmBeats- **összekötő** Docker-összetevőjével kapcsolatos információkat tartalmaz, amelyeket az időjárási adatszolgáltatók az API-kkal való FarmBeats és az időjárási adatok FarmBeats történő elküldésére használhatnak. Ha az adatok elérhetők a FarmBeats-ben, az adatfúzióhoz és a gépi tanulás/mesterséges intelligencia modellek létrehozásához is használható.

 > [!NOTE]
 > Ebben a dokumentációban egy, a NOAA által az Azure Open-adatkészletek használatával létrehozott referenciát fogjuk használni, amely a következő címen érhető el: [https://github.com/azurefarmbeats/noaa_docker](https://github.com/azurefarmbeats/noaa_docker) .
 > A megfelelő Docker-rendszerkép a következő címen érhető el:[https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)

Az időjárási partnereknek meg kell adniuk egy Docker-rendszerképet/programot (az alább említett specifikációkkal), és a Docker-rendszerképet egy, az ügyfelek által elérhető tároló-beállításjegyzékben kell üzemeltetni. Az időjárási partnernek a következő információkat kell megadnia ügyfeleinek:

- Docker-rendszerkép URL-címe
- Docker-rendszerkép címkéje
- Kulcsok/hitelesítő adatok a Docker-rendszerkép eléréséhez
- Ügyfél-specifikus API-kulcsok/hitelesítő adatok az időjárási partner rendszerből származó adatok eléréséhez
- VM SKU-részletek (a partnerek biztosíthatják ezt az esetet, ha a Docker speciális virtuálisgép-követelményekkel rendelkezik, ellenkező esetben az ügyfelek választhatnak az Azure-ban támogatott virtuálisgép-SKU-ból)

A fenti Docker-információk használatával az ügyfél egy időjárási partnert fog regisztrálni a FarmBeats-példányban. Ha többet szeretne megtudni arról, hogy az ügyfelek hogyan használhatják a Docker-t az időjárási információk FarmBeats való betöltéséhez, tekintse meg az [időjárási információk beszerzését](https://docs.microsoft.com/azure/industry/agriculture/get-weather-data-from-weather-partner) ismertető útmutatót.

## <a name="connector-docker-development"></a>Összekötő Docker-fejlesztés

**REST API-alapú integráció**

A FarmBeats API-k hencegő technikai dokumentációt tartalmaznak. További információ az API-król és a hozzájuk kapcsolódó kérésekről és válaszokról: [FarmBeats hencegés](https://aka.ms/farmbeatsswagger). 

Ha telepítette a FarmBeats-t, akkor a FarmBeats a következő helyen érheti el:`https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

Vegye figyelembe, hogy a "-API" a FarmBeats-webhely nevéhez van hozzáfűzve.
Az API-végpont a következőket teszi:`https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>Datahub lib

A FarmBeats olyan lib-t biztosít, amelyet az időjárási partner is használhat. A lib jelenleg a hivatkozási implementáció részeként érhető el [itt](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib). A jövőben ugyanez lesz a több nyelvhez készült SDK-val is.

### <a name="authentication"></a>Hitelesítés

**Hitelesítés FarmBeats API-kkal**

A FarmBeats tulajdonosi hitelesítést használ, és az API-k a kérelem fejléc szakaszában található hozzáférési jogkivonat használatával érhetők el:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

A hozzáférési jogkivonatot az ügyfél FarmBeats-példányán futó Azure-függvénytől kérheti le. Az Azure Function URL-címet a Docker program argumentumként kapja meg, a hozzáférési jogkivonatot pedig az URL-címre vonatkozó GET kéréssel lehet beszerezni. Az URL-cím válasza a hozzáférési jogkivonatot fogja tartalmazni. A Datahub lib segítő függvények lehetővé teszik a partnerek számára a hozzáférési token beszerzését. További részleteket [itt](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py) talál.

A hozzáférési jogkivonat csak néhány órán át érvényes, és a lejáratkor újra kell kérni.

**Hitelesítés partneri oldalsó API-kkal**

Annak engedélyezéséhez, hogy az ügyfelek a Docker-végrehajtás során hitelesítsék magukat a partneri API-kkal, az ügyfeleknek a következőképpen kell megadniuk a hitelesítő adatokat a partner regisztrációja során:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
Az API szolgáltatás szerializálja ezt a dict, és egy kulcstartóban tárolja [azt.](https://docs.microsoft.com/azure/key-vault/basic-concepts)

[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) az időjárási feladatok összekapcsolására szolgál, és az erőforrásokat a Docker-kód végrehajtásához használja fel. Emellett egy mechanizmust is biztosít az adatok biztonságos továbbítására a virtuális gépre, amelyen a Docker-feladatot végrehajtja. A kulcstartóban biztonságosan tárolt API-hitelesítő adatokat a kulcstartó biztonságos karakterláncként tárolja, és a Docker-activity.jstároló munkakönyvtárában elérhetővé tett kiterjesztett tulajdonságokként (Path a fájl "/mnt/working_dir/activity.json") a Docker-kód a Futtatás ideje alatt elolvashatja a fájl hitelesítő adatait, hogy hozzáférhessen a partneri API-khoz az ügyfél nevében. A hitelesítő adatok a következő módon lesznek elérhetők a fájlban:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
Vegye figyelembe, hogy a "partnerCredentials" a partner regisztrációja során az ügyfél által megadott pontos módon lesz elérhető.

A FarmBeats lib segítő függvények lehetővé teszik a partnerek számára a hitelesítő adatok olvasását a tevékenység tulajdonságaiból. További részleteket [itt](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py) talál.

A fájl élettartama csak a Docker-kód végrehajtása során lesz törölve, és a Docker futtatásának befejeződése után törlődik.

További információ az ADF-folyamatok és-tevékenységek működéséről: [https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping) .

**HTTP-kérelmek fejlécei**

Itt láthatók a leggyakoribb kérelmek fejlécei, amelyeket meg kell adni, amikor API-hívást végez a FarmBeats.

**Fejléc** | **Leírás és példa**
--- | ---
Content-Type | A kérelem formátuma (Content-Type: Application/ <format> ). A FarmBeats Datahub API-k formátuma a JSON. Content-Type: Application/JSON
Engedélyezés | Meghatározza az API-hívások létrehozásához szükséges hozzáférési jogkivonatot. Engedélyezés: tulajdonos <hozzáférés-token>
Elfogadás | A válasz formátuma. A FarmBeats Datahub API-k formátuma a JSON. Elfogadás: alkalmazás/JSON

## <a name="data-format"></a>Adatformátum

A JSON egy közös nyelvtől független adatformátum, amely tetszőleges adatstruktúrák egyszerű szöveges ábrázolását teszi lehetővé. További információ: [JSON.org](http://json.org).

## <a name="docker-specifications"></a>Docker-specifikációk

A Docker programnak két összetevővel kell rendelkeznie **: a** rendszerindításhoz és a **feladatokhoz**. Több feladattípus is lehet.

### <a name="bootstrap"></a>Bootstrap

Ezt az összetevőt akkor kell végrehajtani, amikor az ügyfél elindítja a Docker-regisztrációt a FarmBeats. A programnak átadandó argumentumok (arg1, Arg2):

- FarmBeats API-végpont: FarmBeats API-végpont API-kérelmekhez: ez az a végpont, amely API-hívásokat tesz a FarmBeats-telepítésre.
- Azure-függvény URL-címe: Ez a saját személyes végpontja, amely megadja a FarmBeats API-k hozzáférési jogkivonatát. Ha csak erre az URL-címre hív le, a válaszában beolvassa a hozzáférési jogkivonatot.

A rendszerindítási feladat feladata, hogy létrehozza a szükséges metaadatokat, hogy a felhasználók futtassák a feladatokat az időjárási adatok beszerzéséhez. Tekintse meg a hivatkozás implementációját [itt](https://github.com/azurefarmbeats/noaa_docker). Igény szerint frissítheti a fájl bootstrap_manifest.jsét, és a hivatkozás rendszerindítási program létrehozza a szükséges metaadatokat.

A folyamat részeként a következő metaadatok jönnek létre. 

 > [!NOTE]
 > **Vegye figyelembe** , hogy ha a [hivatkozás megvalósításában](https://github.com/azurefarmbeats/noaa_docker)említettek szerint frissíti a bootstrap_manifest.jsa fájlon, nem kell létrehoznia az alábbi metaadatokat, mivel a rendszerindítási fájl a jegyzékfájl alapján hozza létre a fájlt.

- /**WeatherDataModel**: a WeatherDataModel olyan modell, amely az időjárási adatmennyiséget jelöli, és a forrás által biztosított különböző adatkészleteknek felel meg. Előfordulhat például, hogy egy DailyForecastSimpleModel átlagos hőmérséklet-, páratartalom-és csapadék-információt biztosít a napi egyszer, míg egy DailyForecastAdvancedModel sokkal több információt biztosíthat az óránkénti részletességgel. Tetszőleges számú WeatherDataModels is létrehozhat.
- /**JobType**: a FarmBeats bővíthető felügyeleti rendszerrel rendelkezik. Időjárási adatszolgáltatóként különböző adatkészleteket/API-kat (például GetDailyForecasts) használhat, a FarmBeats-ben JobType-ként engedélyezheti azokat. A JobType létrehozása után az ügyfél elindíthatja az adott típusú feladatokat, hogy az időjárási adatok beolvassák a helyüket/farmot (lásd: JobType és Job API-k a [FarmBeats](https://aka.ms/farmbeatsswagger)-ben).

### <a name="jobs"></a>Feladatok

Ez az összetevő minden alkalommal meghívásra kerül, amikor egy FarmBeats-felhasználó futtatja a/JobType, amelyet a rendszerindítási folyamat részeként hozott létre. A feladatokhoz tartozó Docker-futtatási parancs a létrehozott **/JobType** részeként van definiálva.
- A feladat feladata az adatok lekérése a forrásból, és leküldése a FarmBeats. Az adatok lekéréséhez szükséges paramétereket a rendszerindítási folyamat/JobType részeként kell meghatározni.
- A művelet részeként a programnak létre kell hoznia egy **/WeatherDataLocation** a rendszerindítási folyamat részeként létrehozott/WeatherDataModel alapján. A **/WeatherDataLocation** egy olyan helyhez (Lat/Long) felel meg, amelyet a felhasználó a feladatokhoz tartozó paraméterként biztosít.

### <a name="details-of-the-objects"></a>Az objektumok részletei

  WeatherDataModel | Leírás |
  --- | ---
  Név  | Az időjárási adatmodell neve |
  Leírás  | Adjon meg egy értelmes leírást a modellről. |
  Tulajdonságok  | Az adatszolgáltató által definiált további tulajdonságok. |
  weatherMeasures > neve  | Az időjárási mérték neve. Például humidity_max |
  weatherMeasures > adattípus  | Dupla vagy enumerálás. Ha Enum, a measureEnumDefinition megadása kötelező |
  weatherMeasures > measureEnumDefinition  | Csak akkor szükséges, ha az adattípus enumerálás. Például {"No Rain": 0, "Snow": 1, "szitálás": 2, "Rain": 3} |
  weatherMeasures > típusa  | az időjárási telemetria-adattípusok típusa. Például: "RelativeHumidity". A rendszer által definiált típusok a következők: AmbientTemperature, ununit, CO2, mélység, ElectricalConductivity, LeafWetness, Length, LiquidLevel, nitrát, O2, PH, foszfát, PointInTime, kálium, Pressure, RainGauge, RelativeHumidity, sótartalom, SoilMoisture, SoilTemperature, SolarRadiation, State, TimeDuration, UVRadiation, UVIndex, Volume, WindDirection, WindRun, szélsebesség, párolgás, PAR. További részletekért tekintse meg a/ExtendedType API-t vagy az alábbi [típusok és egységek hozzáadása szakaszt](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) .
  weatherMeasures > egység | Az időjárási telemetria vonatkozó adategység. A rendszer által definiált egységek a következők: nincs egység, Celsius, Fahrenheit, Kelvin, Rankine, Pascal, Mercury, PSI, milliméter, centiméter, méter, hüvelyk, láb, Mile, kilométer, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, Degree, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, százalék, PartsPerMillion, MicroMol, MicroMolesPerLiter, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Centibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, liter, MilliLiter, másodperc, UnixTimestamp, MicroMolPerMeterSquaredPerSecond és InchesPerHour. További részletekért tekintse meg a/ExtendedType API-t, vagy az alábbi [típusok és egységek hozzáadása szakaszt](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype) .
  weatherMeasures > AggregationType  | Nincs, átlag, maximum, minimum, StandardDeviation, Sum, Total
  weatherMeasures > mélység  | Az érzékelő mélysége centiméterben. Például a nedvesség 10 cm-es méretének mérése a terepen.
  weatherMeasures > leírása  | Adjon meg egy értelmes leírást a mérésről. |
  **JobType** | **Leírás** |
  Name  | a feladattípus neve – például Get_Daily_Forecast; az ügyfél által az időjárási adatok beolvasása érdekében futtatandó feladatok|
  pipelineDetails > paraméterek > neve  | a paraméter neve |
  pipelineDetails > paraméterek > típusa | Karakterlánc, int, float, bool vagy Array |
  pipelineDetails > paraméterek > isRequired | logikai igaz, ha kötelező paraméter, hamis, ha nem; az alapértelmezett érték TRUE (igaz) |
  pipelineDetails > paraméterek > defaultValue | A paraméter alapértelmezett értéke |
  pipelineDetails > paraméterek > leírása | A paraméter leírása |
  Tulajdonságok  | További tulajdonságok a gyártótól.
  Tulajdonságok > **programRunCommand** | Docker parancs futtatása – ez a parancs akkor lesz végrehajtva, amikor az ügyfél futtatja az időjárási feladatot. |
  **WeatherDataLocation** | **Leírás** |
  weatherDataModelId  | A rendszerindításkor létrehozott megfelelő WeatherDataModel azonosítója|
  location  | a szélesség, a hosszúság és a jogosultságszint-emelést jelöli |
  Name | Az objektum neve |
  Leírás | Leírás |
  farmId | nem **kötelező** Az ügyfél által a feladatok paraméterének részeként megadott Farm azonosítója |
  Tulajdonságok  | További tulajdonságok a gyártótól.

 Az egyes objektumokra és azok tulajdonságaira vonatkozó információkért lásd: [hencegés](https://aka.ms/FarmBeatsSwagger).

 > [!NOTE]
 > Az API-k egyedi azonosítókat adnak vissza minden létrehozott példányhoz. Ezt az azonosítót a fordítónak meg kell őriznie az eszközkezelés és a metaadatok szinkronizálása érdekében.

**Metaadatok szinkronizálása**

Az összekötő Docker-nek képesnek kell lennie frissítések küldésére a metaadatokon. Példák a frissítési forgatókönyvekre: új időjárási paraméterek hozzáadása az időjárás-szolgáltató adatkészletében, funkció hozzáadása (pl.: 30 napos előrejelzés hozzáadása)

> [!NOTE]
> A törlés nem támogatott a metaadatok esetében, például. időjárási adatmodell.
>
> A metaadatok frissítéséhez meg kell hívnia a/Get/{ID} az időjárási adatok modelljére, frissítenie kell a módosított tulajdonságokat, majd el kell végeznie egy/Put/{ID}, hogy a felhasználó által megadott tulajdonságok ne vesszenek el.

## <a name="weather-data-telemetry-specifications"></a>Időjárási adatok (telemetria) – specifikációk

Az időjárási adatszolgáltatások egy olyan kanonikus üzenetre vannak leképezve, amelyet egy Azure Event hub-ba küldenek feldolgozásra. Az Azure EventHub egy olyan szolgáltatás, amely lehetővé teszi a valós idejű adatok (telemetria) betöltését a csatlakoztatott eszközökről és alkalmazásokból. Az időjárási adatokat FarmBeats küldéséhez létre kell hoznia egy ügyfelet, amely üzeneteket küld egy FarmBeats lévő Event hub-nak. Ha többet szeretne megtudni a telemetria küldéséről, tekintse meg a [telemetria küldése az Event hubhoz](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send) című témakört.

Itt található egy Python-kód, amely a telemetria ügyfélként küldi el a megadott Event hub-nak.

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

A Canonical üzenet formátuma a következő:

```json
{
   "weatherstations": [
   {
   "id": "id of the WeatherDataLocation",
   "weatherdata": [
   {
     "timestamp": "timestamp of the data. For historical, this is the time for which the observations are sent. For forecast this is the time for which data is forecasted. Format is ISO 8601. Default time-zone is UTC",
     "predictiontimestamp": "timestamp on which the forecast data is predicted i.e time of prediction. Required only for forecast data. Format is ISO 8601. Default timezone is UTC ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Például itt van egy telemetria-üzenet:

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>Hibaelhárítás és hibák kezelése

**Hiba naplózása**

Mivel a partneri feladat a meglévő feladat-keretrendszerben fog futni, a hibák ugyanúgy jelentkeznek be, mint a FarmBeats-ban lévő többi meglévő feladat hibái (például GetFarmData, SensorPlacement stb.). Az ADF-folyamaton belül futó ADF-tevékenység a STDERR és az STDOUT-ot is naplózza. Mindkét fájl a "datahublogs-xxx" Storage-fiókban érhető el a FarmBeats erőforráscsoport alatt.

A Datahub lib segítő függvények lehetővé teszik a naplózást a teljes Datahub-naplók részeként. További részleteket [itt](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py) talál.

**A beállítások vagy a támogatás hibáinak megoldása**

Abban az esetben, ha az ügyfél nem tud időjárási értéket fogadni a megadott FarmBeats-példányban, az időjárási partnernek támogatást és egy mechanizmust kell megadnia a hibakereséshez.

## <a name="add-extendedtype"></a>ExtendedType hozzáadása

A FarmBeats támogatja az új szenzor mértékének típusát és egységeit. Vegye figyelembe, hogy egy időjárási partner új egységeket/típusokat adhat hozzá, ha frissíti a fájl bootstrap_manifest.jsét a [hivatkozás megvalósításában](https://github.com/azurefarmbeats/noaa_docker) .

Új WeatherMeasure-típus (például "PrecipitationDepth") hozzáadásához kövesse az alábbi lépéseket.

1. GET kérés a/ExtendedType a lekérdezési szűrővel – kulcs = WeatherMeasureType
2. Jegyezze fel a visszaadott objektum AZONOSÍTÓját.
3. Adja hozzá az új típust a visszaadott objektum listájához, és hozzon létre egy PUT-kérelmet a/ExtendedType{ID} az alábbi új listával. A bemeneti adattartalomnak meg kell egyeznie a fent kapott választal és az értékek listájának végén lévő új egységgel.

További információ a/ExtendedType API-ról: [hencegés](https://aka.ms/FarmBeatsSwagger).

## <a name="next-steps"></a>Következő lépések

Most már rendelkezik egy összekötő Docker-vel, amely integrálható a FarmBeats. A következőben láthatja, hogyan szerezhet be időjárási adatait a Docker használatával a FarmBeats. Lásd: [időjárási információk beolvasása](get-weather-data-from-weather-partner.md).

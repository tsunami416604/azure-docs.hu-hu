---
title: Időjárási adatok bekéselése az időjárási partnerektől
description: Ez a cikk ismerteti, hogyan lehet időjárási adatokat letudni a partnerektől.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 66fa4e7d3edf839ab1e497e86362bcfc979dc279
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266161"
---
# <a name="get-weather-data-from-weather-partners"></a>Időjárási adatok bekéselése az időjárási partnerektől

Az Azure FarmBeats segítségével hozhat időjárási adatokat az időjárási adatszolgáltató(k) egy docker-alapú összekötő keretrendszer használatával. Ezzel a keretrendszerrel az időjárási adatszolgáltatók egy dockert valósítanak meg, amely integrálható a FarmBeats-be. Jelenleg a következő időjárási adatszolgáltatók támogatottak:

[NOAA-adatok az Azure Open adatkészletekből](https://azure.microsoft.com/services/open-datasets/)

Az időjárási adatok használható a perelhető insights létrehozásához és a FarmBeats AI/ML-modellek létrehozásához.

## <a name="before-you-start"></a>Előkészületek

Az időjárási adatok leéséhez győződjön meg arról, hogy telepítette a FarmBeats-t. **Az időjárás-integrációt az 1.2.11-es vagy újabb verzió támogatja.** Az Azure FarmBeats telepítéséről a [FarmBeats telepítése](https://aka.ms/farmbeatsinstalldocumentation)című témakörben látható.

## <a name="enable-weather-integration-with-farmbeats"></a>Időjárás-integráció engedélyezése a FarmBeats-szel

Az időjárási adatok farmbeats-i adatközponton való leszerzésének megkezdéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a FarmBeats Data hub swagger (https://yourdatahub.azurewebsites.net/swagger)

2. Keresse meg a /Partner API-t, és küldjön POST-kérelmet a következő bemeneti tartalommal:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name. Default is azurefarmbeats/fambeats-noaa>",
       "imageTag" : "<docker image tag, default:latest>",
       "azureBatchVMDetails": {  
         "batchVMSKU" : "<VM SKU. Default is standard_d2_v2>",  
         "dedicatedComputerNodes" : 1,
         "nodeAgentSKUID": "<Node SKU. Default is batch.node.ubuntu 18.04>"  
       }
     },
     "partnerCredentials": {  
       "key1": "value1",  
       "key2": "value2"  
     },  
     "partnerType": "Weather",  
     "name": "<Name of the partner>",  
     "description": "<Description>",
     "properties": {  }  
   }  
   ```

   Például az időjárás-adatok betöltése a NOAA az Azure Open Datasets, használja az alábbi hasznos adat. Módosíthatja a nevet és a leírást, ahogy azt a kívánt módon.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "azurefarmbeats/farmbeats-noaa",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerType": "Weather",
     "name": "ods-noaa",
     "description": "NOAA data from Azure Open Datasets registered as a Weather Partner"
   }  
   ```

   > [!NOTE]
   > A Partner objektummal kapcsolatos további információkért [lásd: Függelék](get-weather-data-from-weather-partner.md#appendix)

   Az előző lépés az erőforrások at, hogy a docker futtatásához az ügyfél FarmBeats környezetben.  

   A fenti erőforrások kiépítése körülbelül 10–15 percet vesz igénybe.

3. Ellenőrizze a /Partner objektum állapotát, amelyet a 2. Ehhez kérjen GET-kérelmet a /Partner API-n, és ellenőrizze a partnerobjektum **állapotát.** Miután a FarmBeats sikeresen elhelyezi a partnert, az állapot **Aktív**lesz.

4. Keresse meg a /JobType API-t, és kérjen GET-kérelmet. A 2. Az időjárási feladatok **pipelineName** mezője a következő formátumú lesz: "partner-name_partner-type_job-name".

5. Most a FarmBeats példány rendelkezik egy aktív időjárási adatpartnerrel, és feladatok futtatásával időjárási adatokat kérhet egy adott helyhez (szélességi/hosszúsági) és egy dátumtartományhoz. A JobType(ok) részletesen ismertetik, hogy milyen paraméterek szükségesek az időjárási feladatok futtatásához.

   Például az Azure Open adatkészletek NOAA-adataihoz a következő Feladattípus(ok) jönnek létre:

   - get_weather_data (ISD/történelmi időjárási adatok beszerezni)
   - get_weather_forecast_data (Get GFS / előrejelzés időjárás adatok)

6. Jegyezze fel az **azonosítót** és a JobType(ok) paramétereit.

7. Keresse meg a /Jobs API-t, és küldjön POST-kérelmet a /Jobs oldalon a következő bemeneti tartalommal:

   ```json
    {
         "typeId": "<id of the JobType>",
         "arguments": {
           "additionalProp1": {},
           "additionalProp2": {},
           "additionalProp3": {}
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
       }
   ```

   Például a **get_weather_data**futtatásához használja a következő hasznos adat:

   ```json
   {
 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "start_date": "yyyy-mm-dd",
               "end_date": "yyyy-mm-dd"
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. Az előző lépés a partner docker-ben meghatározott időjárási feladatok futtatására és az időjárási adatok FarmBeats-be történő betöltésére. Ellenőrizheti a feladat állapotát a GET kéréssel a /Jobs oldalon, és megkeresheti a **currentState-et** a válaszban. Ha elkészült, a currentState beállítása **Sikeres**.

## <a name="query-ingested-weather-data"></a>Bevitt időjárási adatok lekérdezése

Az időjárási feladatok befejezése után lekérdezheti a bevitt időjárási adatokat modellek vagy használható elemzési adatok létrehozásához. A FarmBeats időjárási adatainak elérésére és lekérdezésére kétféleképpen lehet hozzáférni:

- API és
- Idősorozat-elemzések (TSI).

### <a name="query-using-rest-api"></a>LEKÉRDEZÉS REST API-val

Az időjárási adatok FarmBeats REST API-val történő lekérdezéséhez kövesse az alábbi lépéseket:

1. A FarmBeats Data hub swagger ( (https://yourdatahub.azurewebsites.net/swagger), keresse meg a /WeatherDataLocation API-t, és get kérelmet. A válasz ban létre jön a /WeatherDataLocation objektum (szélességi és hosszúsági) objektum, amely a feladatfuttatás részeként lett megadva. Jegyezze fel az objektum(ok) **azonosítóját** és **időjárásátDataModelId.**

2. Get/{id} a /WeatherDataModel API-n az **1.** Az "Időjárás-adatmodell" tartalmazza a bevitt időjárási adatok összes metaadatát és részleteit. Az **Időjárás-mérés** az **Időjárási adatok modell** objektumában például részletesen ismerteti, hogy milyen időjárási adatok támogatottak, és milyen típusú és mértékegységes. Például:

   ```json
   {
       "name": "Temperature <name of the weather measure - this is what we will receive as part of the queried weather data>",
       "dataType": "Double <Data Type - eg. Double, Enum>",
       "type": "AmbientTemperature <Type of measure eg. AmbientTemperature, Wind speed etc.>",
       "unit": "Celsius <Unit of measure eg. Celsius, Percentage etc.>",
       "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation, Sum, Total>",
       "description": "<Description of the measure>"
   }
   ```

   Jegyezze fel az Időjárás-adatmodell GET/{id} hívásából származó választ.

3. Nyissa meg a **Telemetriai** API-t, és a következő bemeneti tartalommal kérjen POST-kérelmet:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. A megadott időtartományhoz rendelkezésre álló időjárási adatokat tartalmazó válasz a következőkre fog kinézni:

   ```json
   {
     "timestamps": [
       "2020-XX-XXT07:30:00Z",
       "2020-XX-XXT07:45:00Z"
     ],
     "properties": [
       {
         "values": [
           "<id of the weatherDataLocation>",
           "<id of the weatherDataLocation>"
         ],
         "name": "Id",
         "type": "String"
       },
       {
         "values": [
           29.1,
           30.2
         ],
         "name": "Temperature <name of the WeatherMeasure as defined in the WeatherDataModel object>",
         "type": "Double <Data Type of the value - eg. Double>"
       }
     ]
   }
   ```

Az előző példában a válasz két időbélyeg adatait tartalmazza a mérték nevével ("Hőmérséklet") és a jelentett időjárási adatok értékeivel együtt a két időbélyegben. A jelentett értékek típusának és egységének értelmezéséhez a kapcsolódó időjárási adatmodellre kell hivatkoznia (a fenti 2. lépésben leírtak szerint).

### <a name="query-using-azure-time-series-insights-tsi"></a>Azure Time Series Insights (TSI) használatával történő lekérdezés

A FarmBeats [az Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) segítségével használja az adatok betöltését, tárolását, lekérdezését és megjelenítését az IoT méretezési állapotán – olyan adatokat, amelyek nagy mértékben környezetfüggők és idősorozatokra optimalizáltak.

Az időjárási adatok egy EventHubon érkeznek, majd egy TSI-környezetbe kerüla FarmBeats erőforráscsoporton belül. Az adatok ezután közvetlenül lekérdezhetők az ÁME-ből. További információt az [1SI dokumentációjában](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)talál.

Az 1SI-n lévő adatok megjelenítéséhez kövesse az adatok megjelenítéséhez szükséges lépéseket:

1. Nyissa meg az **Azure Portal** > **FarmBeats DataHub erőforráscsoportot,** > válassza ki **a Time Series Insights** környezetet (tsi-xxxx) > **adatelérési szabályzatokat.** Adjon hozzá felhasználói szerepkört a Reader vagy a Közreműködői hozzáféréssel.

2. Nyissa meg a **Time Series Insights** környezet **áttekintése lapját** (tsi-xxxx), és válassza a **Time Series Insights Explorer URL-címét.** Most már vizualizálhatja a bevitt időjárási adatokat.

Az időjárási adatok tárolása, lekérdezése és vizualizálása mellett az ÁME lehetővé teszi a Power BI irányítópultra való integrációt is. További információt a Time Series Insights adatainak megjelenítése a [Power BI-ban című témakörben talál.](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="appendix"></a>Függelék

|        Partner   |  Részletek   |
| ------- | -------             |
|     DockerDetails - imageName         |          Docker-lemezkép neve. Például docker.io/azurefarmbeats/farmbeats-noaa (hub.docker.com) vagy myazureacr.azurecr.io/mydockerimage (az Azure Container Registry) és így tovább. Ha nincs megadva beállításjegyzék, az alapértelmezett beállítás hub.docker.com      |
|          DockerDetails - imageTag             |         A docker-lemezkép címkéje. Az alapértelmezett érték a "legújabb"     |
|  DockerDetails - hitelesítő adatok      |  Hitelesítő adatok a privát docker eléréséhez. Ezt a partner biztosítja az ügyfélnek   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Az Azure Batch virtuálisgép-termékváltozat. Lásd [itt](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) az összes elérhető Linux virtuális gépek. Érvényes értékek: "Kicsi", "ExtraLarge", "Nagy", "A8", "A9", "Közepes", "A5", "A6", "A7", "STANDARD_D1", "STANDARD_D1", "STANDARD_D2", "STANDARD_D3", "STANDARD_D4", "STANDARD_D11", "STANDARD_D12", "STANDARD_D13", "STANDARD_D14", "A10", "A11", "STANDARD_D1_V2", "STANDARD_D2_V2", "STANDARD_D3_V2 STANDARD_D4_V2", "STANDARD_D11_V2 STANDARD_D4_V2", "STANDARD_D12_V2", "STANDARD_D13_V2", "STANDARD_D14_V2", "STANDARD_G1", "STANDARD_G2", "STANDARD_G2", "STANDARD_G3", "STANDARD_G4" STANDARD_G4 , "STANDARD_G5", "STANDARD_D5_V2", "BASIC_A1", "BASIC_A2", "BASIC_A3", "BASIC_A4", "STANDARD_F1", "STANDARD_A1", "STANDARD_A2 STANDARD_A3", "STANDARD_A3", "STANDARD_A4", "STANDARD_A5 STANDARD_A6", "STANDARD_A7", "STANDARD_A8", "STANDARD_A9", "STANDARD_A10", "STANDARD_A11", "STANDARD_D15_V2 STANDARD_F1", "STANDARD_F1", "STANDARD_F2 STANDARD_F4", "STANDARD_F8 STANDARD_F4", "STANDARD_F16", "STANDARD_NV6", "STANDARD_NV12", "STANDARD_NV24", "STANDARD_NC6", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC24 STANDARD_F4", "STANDARD_F4", "", "STANDARD_NV12", "STANDARD_NV24", "STANDARD_NC6", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_F4", "", "", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "", "", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "", "", ""STANDARD_NC24r, "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "", "", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "", "", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "", "", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "STANDARD_NC12", "", ""STANDARD_F4" , "STANDARD_H8", "STANDARD_H8m", "STANDARD_H16 STANDARD_H16m", "STANDARD_H16m", "STANDARD_H16mr", "STANDARD_H16r", "STANDARD_A1_V2 STANDARD_H16m", "STANDARD_A2_V2", "STANDARD_A4_V2", "STANDARD_A8_V2 STANDARD_A2m_V2", "STANDARD_A4m_V2", "STANDARD_A8m_V2", "STANDARD_M64ms", "STANDARD_M128s", "STANDARD_D2_V3". **Az alapértelmezett érték a "standard_d2_v2"**  |
|    DockerDetails - azureBatchVMDetails - dedikáltSzámítógép-nodes   |  Nem. a kötegkészlethez dedikált számítógép-csomópontok. Az alapértelmezett érték 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Az Azure Batch-csomópont ügynök termékazonosítója. Jelenleg csak a "batch.node.ubuntu 18.04" kötegcsomópont-ügynök támogatott.    |
| DockerDetails - partnerCredentials | hitelesítő adatokat a docker partner API-jának hívására. A partnernek ezt az információt meg kell adnia ügyfeleinek a támogatott hitelesítési mechanizmus alapján, pl. Felhasználónév/jelszó vagy API-kulcsok. |
| partnertípus | "Időjárás" (A FarmBeats egyéb partnertípusai a "Sensor" és a "Imagery")  |
|  név   |   A FarmBeats rendszer partnerének kívánt neve   |
|  leírás |  Leírás   |

## <a name="next-steps"></a>További lépések

Most már lekérdezett érzékelő adatokat az Azure FarmBeats-példányból. Most, megtanulják, hogyan kell [létrehozni térképeket](generate-maps-in-azure-farmbeats.md#generate-maps) a gazdaságok.

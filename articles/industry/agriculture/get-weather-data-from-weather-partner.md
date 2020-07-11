---
title: Időjárási adatok beolvasása a partnerektől
description: Ez a cikk azt ismerteti, hogyan kérhet le időjárási információkat a partnerektől.
author: sunasing
ms.topic: article
ms.date: 03/31/2020
ms.author: sunasing
ms.openlocfilehash: 7666ee1a81c2ed93ee5e246b3ec79f056f9d63ab
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187778"
---
# <a name="get-weather-data-from-weather-partners"></a>Időjárási adatok beolvasása a partnerektől

Az Azure FarmBeats segítségével a Docker-alapú összekötő-keretrendszer használatával időjárási adatokhoz viheti az időjárási adatok szolgáltatója (ke) t. Ezen keretrendszer használatával az időjárási adatszolgáltatók olyan Docker-t valósítanak meg, amely integrálható a FarmBeats. Jelenleg a következő időjárási adatszolgáltatók támogatottak:

  ![DTN](./media/get-sensor-data-from-sensor-partner/dtn-logo.png)

  [DTN](https://www.dtn.com/dtn-content-integration/)

Az időjárási információk használatával a gyakorlatban hasznosítható elemzések hozhatók létre, és mesterséges intelligencia/ML-modelleket hozhatnak létre a FarmBeats.

## <a name="before-you-start"></a>Előkészületek

Az időjárási idő beszerzéséhez győződjön meg arról, hogy telepítette a FarmBeats. **Az időjárási integrációt a 1.2.11 vagy újabb verzió támogatja**. Az Azure FarmBeats telepítéséhez lásd: a [FarmBeats telepítése](https://aka.ms/farmbeatsinstalldocumentation).

## <a name="enable-weather-integration-with-farmbeats"></a>Időjárás-integráció engedélyezése a FarmBeats

A FarmBeats-adatközpont időjárási adatainak beszerzéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a FarmBeats adatközpont hencegő területét (https://farmbeatswebsite-api.azurewebsites.net/swagger)

2. Navigáljon a/partner API-hoz, és tegye meg a következő bemeneti adattartalommal rendelkező POST-kérelmet:

   ```json
   {  
 
     "dockerDetails": {  
       "credentials": { 
         "username": "<credentials to access private docker - not required for public docker>", 
         "password": "<credentials to access private docker – not required for public docker>"  
       },  
       "imageName" : "<docker image name",
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

   Ha például a DTN időjárási adatait szeretné lekérni, használja az alábbi adattartalmat. A név és a leírás a saját igényei szerint módosítható.

   > [!NOTE]
   > Az alábbi lépéshez API-kulcsot kell megadnia, forduljon a DTN-hoz, és tegye meg ugyanezt a DTN-előfizetéséhez.

   ```json
   {
 
     "dockerDetails": {
       "imageName": "dtnweather/dtn-farm-beats",
       "imageTag": "latest",
       "azureBatchVMDetails": {
         "batchVMSKU": "standard_d2_v2",
         "dedicatedComputerNodes": 1,
         "nodeAgentSKUID": "batch.node.ubuntu 18.04"
       }
     },
     "partnerCredentials": {
      "apikey": "<API key from DTN>"
      },
     "partnerType": "Weather",
     "name": "dtn-weather",
     "description": "DTN registered as a Weather Partner in FarmBeats"
   }  
   ```

   > [!NOTE]
   > További információ a partner objektumról: [függelék](get-weather-data-from-weather-partner.md#appendix)

   Az előző lépésben kiépítheti azokat az erőforrásokat, amelyek lehetővé teszik, hogy a Docker fusson az ügyfél FarmBeats-környezetében.  

   A fenti erőforrások kiépítése körülbelül 10-15 percet vesz igénybe.

3. Tekintse meg a 2. lépésben létrehozott/partner objektum állapotát. Ehhez hozzon igénybe egy GET kérelmet a/partner API-ban, és ellenőrizze a partner objektum **állapotát** . Miután a FarmBeats kiépíti a partnert, az állapota **aktív**lesz.

4. Navigáljon a/JobType API-hoz, és végezze el a GET-kérést. Tekintse át a 2. lépésben a partner hozzáadási folyamat részeként létrehozott időjárási feladatokat. Az időjárási feladatok **pipelineName** mezőjének formátuma a következő lesz: "partner-name_partner-type_job-Name".

5. Most, hogy a FarmBeats-példány aktív időjárási adatpartnerrel rendelkezik, és a feladatok futtatásával egy adott hely (szélesség/hosszúság) és egy dátumtartomány időjárási adatait kérheti le. A JobType (k) részletes információkkal szolgálnak az időjárási feladatok futtatásához szükséges paraméterekről.

   A DTN esetében például a következő JobType (ok) lesz létrehozva:
   
   - get_dtn_daily_observations (napi megfigyelések beolvasása egy adott helyen és időszakban)
   - get_dtn_daily_forecasts (napi előrejelzés beolvasása egy adott helyhez és időtartamhoz)
   - get_dtn_hourly_observations (a hely és az időszak óránkénti megfigyelésének beolvasása)
   - get_dtn_hourly_forecasts (a hely és az időszak óránkénti előrejelzésének beolvasása)

6. Jegyezze fel a JobType (ok) **azonosítóját** és paramétereit.

7. Navigáljon a/Jobs API-hoz, és tegyen közzé egy POST-kérést a/Jobs a következő bemeneti adattartalommal:

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

   **Get_dtn_daily_observations**futtatásához például használja a következő adattartalmat:

   ```json
   { 
         "typeId": "<id of the JobType>",
         "arguments": {
               "latitude": 47.620422,
               "longitude": -122.349358,
               "days": 5
         },
         "name": "<name of the job>",
         "description": "<description>",
         "properties": {}
   }
   ```

8. Az előző lépés a partner Docker által meghatározott időjárási feladatokat fogja futtatni, és az időjárási adatok betöltését a FarmBeats. A/Jobs GET kérelem beszerzésével és a válaszban található **currentState** keresésével ellenőrizhető a feladatok állapota. Ha elkészült, a currentState értéke **sikeres**.

## <a name="query-ingested-weather-data"></a>Betöltött időjárási adatmennyiség lekérdezése

Az időjárási feladatok befejezése után lekérdezheti a betöltött időjárási adatokból a FarmBeats Datahub REST API-k használatával modelleket vagy gyakorlatban hasznosítható elemzéseket készíthet.

### <a name="query-using-rest-api"></a>Lekérdezés REST API használatával

Az időjárási FarmBeats REST API használatával történő lekérdezéséhez kövesse az alábbi lépéseket:

1. A FarmBeats adatközpontjának hencegő https://yourdatahub.azurewebsites.net/swagger) felületén (, navigáljon a/WeatherDataLocation API-hoz, és hozzon igénybe egy Get-kérelmet. A válasz/WeatherDataLocation objektummal (szélesség/hosszúság) lett létrehozva, amelyet a rendszer a feladatok futtatásának részeként adott meg. Jegyezze fel az objektum (ok) **azonosítóját** és **weatherDataModelId** .

2. Hozzon végre egy GET/{ID}/WeatherDataModel API-t a **weatherDataModelId** az 1. lépésben feljegyzett módon. Az "időjárási adatmodell" a betöltött időjárási adatokkal kapcsolatos összes metaadatot és részletet tartalmaz. Az időjárás **adatmodell** -objektumon belüli **időjárási mérték** például részletesen ismerteti, hogy milyen időjárási információk támogatottak, és milyen típusokban és egységekben. Például:

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

   Jegyezze fel a GET/{ID} hívás válaszát az időjárási adatok modelljéhez.

3. Navigáljon a **telemetria** API-hoz, és tegye meg a következő bemeneti adattartalommal rendelkező post-kérelmet:

   ```json
   {
       "weatherDataLocationId": "<id from step 1 above>",
     "searchSpan": {
       "from": "2020-XX-XXT07:30:00Z",
       "to": "2020-XX-XXT07:45:00Z"
     }
   }
   ```

4. A megadott időtartományhoz elérhető időjárási adatértéket tartalmazó válasz a következőképpen fog kinézni:

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

Az előző példában a válasz két időbélyeg esetében tartalmaz adatokkal együtt a mérték nevét ("hőmérséklet") és a jelentett időjárási adatok értékeit a két időbélyegben. A jelentett értékek típusának és egységének értelmezéséhez a kapcsolódó időjárási adatmodellre kell hivatkoznia (a fenti 2. lépésben leírtak szerint).

## <a name="troubleshoot-job-failures"></a>Feladatok hibáinak elhárítása

A feladatokkal kapcsolatos hibák elhárításához keresse meg a feladatok naplóit. Kérjük, kövesse az alábbi [lépéseket](troubleshoot-azure-farmbeats.md#weather-data-job-failures) .


## <a name="appendix"></a>Függelék

|        Partner   |  Részletek   |
| ------- | -------             |
|     DockerDetails – imageName         |          A Docker-rendszerkép neve. Például: docker.io/mydockerimage (rendszerkép hub.docker.com) vagy myazureacr.azurecr.io/mydockerimage (rendszerkép Azure Container Registry) és így tovább. Ha nincs megadva beállításjegyzék, az alapértelmezett érték hub.docker.com      |
|          DockerDetails - imageTag             |         A Docker-rendszerkép címkéjének neve. Az alapértelmezett érték a "legújabb"     |
|  DockerDetails – hitelesítő adatok      |  A privát Docker eléréséhez szükséges hitelesítő adatok. Ezt a partner kapja meg az ügyfél számára   |
|  DockerDetails - azureBatchVMDetails - batchVMSKU     |    Azure Batch VM SKU-t. [Itt](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) megtekintheti az összes elérhető linuxos virtuális gépet. Érvényes értékek: "Small", "ExtraLarge", "Large", "A8", "A9", "medium", "a5", "A6", "A7", "STANDARD_D1", "STANDARD_D2", "STANDARD_D3", "STANDARD_D4", "STANDARD_D11", "STANDARD_D12", "STANDARD_D13", "STANDARD_D14", "A10", "A11", "STANDARD_D1_V2", "STANDARD_D2_V2", "STANDARD_D3_V2", "STANDARD_D4_V2", "STANDARD_D11_V2", "STANDARD_D12_V2", "STANDARD_D13_V2", "STANDARD_D14_V2", "STANDARD_G1", "STANDARD_G2", "STANDARD_G3", "STANDARD_G4" , "STANDARD_G5", "STANDARD_D5_V2", "BASIC_A1", "BASIC_A2", "BASIC_A3", "BASIC_A4", "STANDARD_A1", "STANDARD_A2", "STANDARD_A3", "STANDARD_A4", "STANDARD_A5", "STANDARD_A6", "STANDARD_A7", "STANDARD_A8", "STANDARD_A9", "STANDARD_A10", "STANDARD_A11", "STANDARD_D15_V2", "STANDARD_F1", "STANDARD_F2", "STANDARD_F4", "STANDARD_F8", "STANDARD_F16", "STANDARD_NV6", "STANDARD_NV12", "STANDARD_NV24", "STANDARD_NC6", "STANDARD_NC12", "STANDARD_NC24", "STANDARD_NC24r" , "STANDARD_H8", "STANDARD_H8m", "STANDARD_H16", "STANDARD_H16m", "STANDARD_H16mr", "STANDARD_H16r", "STANDARD_A1_V2", "STANDARD_A2_V2", "STANDARD_A4_V2", "STANDARD_A8_V2", "STANDARD_A2m_V2", "STANDARD_A4m_V2", "STANDARD_A8m_V2", "STANDARD_M64ms", "STANDARD_M128s", "STANDARD_D2_V3". **Az alapértelmezett érték a "standard_d2_v2"**  |
|    DockerDetails - azureBatchVMDetails - dedicatedComputerNodes   |  Nem. a Batch-készlet dedikált számítógép-csomópontjain. Az alapértelmezett érték 1. |
|    DockerDetails - azureBatchVMDetails - nodeAgentSKUID          |    Azure Batch csomópont-ügynök SKU-azonosítója. Jelenleg csak a "batch. Node. Ubuntu 18,04" kötegelt csomópont-ügynök támogatott.    |
| DockerDetails - partnerCredentials | a partner API meghívására szolgáló hitelesítő adatok a Docker-ben. A partnernek ezeket az információkat az ügyfelek által támogatott hitelesítési mechanizmus alapján kell megadnia az ügyfeleknek. Felhasználónév/jelszó vagy API-kulcsok. |
| partnerType | "Weather" (a FarmBeats más típusai az "érzékelő" és a "képek")  |
|  name   |   A partner kívánt neve a FarmBeats-rendszeren   |
|  leírás |  Leírás   |

## <a name="next-steps"></a>További lépések

Mostantól lekérdezte az érzékelő adatait az Azure FarmBeats-példányból. Most megismerheti, hogyan [hozhatja](generate-maps-in-azure-farmbeats.md#generate-maps) ki a térképeket a farmokhoz.

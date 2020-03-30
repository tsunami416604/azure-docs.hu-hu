---
title: Képalkotási partner integrációja
description: Ez a cikk a képi partnerek integrációját ismerteti.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 62e5b363f8008380a61e24c0549573a30ecaeb73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131856"
---
# <a name="imagery-partner-integration"></a>Képalkotási partner integrációja

Ez a cikk ismerteti, hogyan használhatja az Azure FarmBeats Translator összetevő képadatokat farmbeats.This article describes how to use the Azure FarmBeats Translator component to send imagery data to FarmBeats. Mezőgazdasági képadatok különböző forrásokból, például multispektrális kamerákból, műholdakból és drónokból generálhatók. A mezőgazdasági képekkel rendelkező partnerek integrálhatják a FarmBeats-t, hogy az ügyfelek számára egyedileg generált térképeket biztosítsanak a farmjaikhoz.

Az adatok, amint rendelkezésre állnak, a FarmBeats Accelerator-on keresztül jeleníthetők meg, és potenciálisan felhasználhatók az adatok fúziójára és a gépi tanulásra/mesterséges intelligenciára (ML/AI) a mezőgazdasági vállalkozások vagy az ügyfélrendszer integrátorok által.

A FarmBeats lehetővé teszi:

- Egyéni képtípusokat, forrás- és fájlformátumot definiáljon az /ExtendedType API-k használatával.
- Különböző forrásokból származó képadatok betöltése a /Scene és a /SceneFile API-kon keresztül.

A következő információk arra összpontosítanak, hogy bármilyen képet bekerüljön a FarmBeats rendszerbe.

Amikor kiválasztja a **Drone Imagery** szakaszt, megnyílik egy előugró ablak, amely a drón ortomozaik nagy felbontású képét jeleníti meg. Elérheti a partnerszoftvert, amely segít a drónrepülések megtervezésében és a nyers adatok beérkezésén. Továbbra is használni fogja a partner szoftverét az útvonaltervezéshez és az ortomozaik képvarráshoz.

A drónpartnereknek lehetővé kell tenniük az ügyfelek számára, hogy összekapcsolják az ügyfélfiókjukat az Azure-beli FarmBeats-példányukkal.

A FarmBeats összekapcsolására a drone partnerszoftverben a következő hitelesítő adatokat kell használnia:

- API-végpont
- Bérlőazonosító
- Ügyfél-azonosító
- Titkos ügyfélkulcs

## <a name="api-development"></a>API-fejlesztés

Az API-k swagger műszaki dokumentációt tartalmaznak. Az API-król és a megfelelő kérésekről és válaszokról a [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)című témakörben talál további információt.

## <a name="authentication"></a>Hitelesítés

A FarmBeats a Microsoft Azure [Active Directoryt](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) (Azure AD) használja. Az Azure App Service beépített hitelesítési és engedélyezési támogatást biztosít. 

Az Azure AD-ről az [Azure Active Directory című témakörben](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)talál további információt.   

A FarmBeats Datahub bemutatóra szóló hitelesítést használ, amelyhez a következő hitelesítő adatokra van szükség:

- Ügyfél-azonosító
- Titkos ügyfélkulcs
- Bérlőazonosító

Az előző hitelesítő adatok használatával a hívó hozzáférési jogkivonatot kérhet, amelyet a következő API-kérelmekben kell elküldeni a fejlécszakaszban, az alábbiak szerint:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

A következő Python-kódminta lekéri a hozzáférési jogkivonatot. Ezután használhatja a jogkivonatot a FarmBeats későbbi API-hívásaihoz.

```python
from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net"   
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

## <a name="http-request-headers"></a>HTTP-kérelem fejlécei

Az alábbiakban a leggyakoribb kérésfejléceket kell megadni, amikor API-hívást kezdeményez a FarmBeats Datahub-ra.

**Fejléc** | **Leírás és példa**
--- | ---
Content-Type  | A kérelem formátuma (Content-Type: application/<format>). A FarmBeats Datahub API-k esetében a formátum JSON. Tartalom-típus: alkalmazás/json
Engedélyezés | Megadja az API-híváshoz szükséges hozzáférési jogkivonatot. Engedélyezés: a tulajdonos <hozzáférési>
Elfogadás  | A válasz formátuma. A FarmBeats Datahub API-k esetében a formátum JSON. Elfogadás: alkalmazás/json


## <a name="api-requests"></a>API-kérelmek

REST API-kérelem benyújtásához a következőket kell kombinálnia:

- A HTTP metódus (GET, POST és PUT).
- Az API-szolgáltatás URL-címe.
- Az erőforrás URI-ja (adatok lekérdezéséhez, elküldéséhez, frissítéséhez vagy törléséhez).
- Egy vagy több HTTP-kérelemfejléc.

Szükség esetén lekérdezési paramétereket is megadhat a GET-hívásokhoz, korlátozhatja a válaszok ban lévő adatok méretét, és rendezheti az adatokat.

A következő mintakérelem az eszközök listájának bekérése:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

A legtöbb GET, POST és PUT hívások igényel JSON kérelem törzs.

A következő mintakérelem egy eszköz létrehozása. Ez a minta rendelkezik egy bemeneti JSON a kérelem törzse.


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Adatformátum

A JSON egy közös, nyelvfüggetlen adatformátum, amely egyszerű szövegábrázolást biztosít tetszőleges adatstruktúrákról. További információ: [JSON org](https://JSON.org).

## <a name="ingest-imagery-into-farmbeats"></a>Képek betöltése a FarmBeats-be

Miután a partner hitelesítő adatokkal rendelkezik a FarmBeats Datahubhoz való csatlakozáshoz, a partner a következő lépéseket teszi a Translator összetevőben.

1.  Hozzon létre egy új kiterjesztett típust a következő mezőkhöz a feltöltendő képek típusának megfelelően:

    - **Jelenet forrása**: Például drone_partner_name
    - **Jelenet típusa**: Például, drone
    - **Jelenetfájl típusa**: Például klorofill index
    - **Jelenetfájl-tartalom típusa**: Például kép/tiff

2.  Hívja meg a /Farms API-t, hogy lekérjék a farmok listáját az Azure FarmBeats rendszerből.
3.  Adja meg az ügyfél nek, hogy egyetlen farmot válasszon a gazdaságok listájából.

    A partnerrendszernek meg kell mutatnia a farmot a partnerszoftveren belül az útvonaltervezéshez, valamint a drónrepüléshez és a képgyűjtéshez.

4.  Hívja meg a /Scene API-t, és adja meg a szükséges részleteket egy új jelenet egyedi jelenetazonosítóval.
5.  Kap egy blob SAS URL-címet a szükséges rendszerképek feltöltéséhez farmbeats datahub, a kiválasztott farm környezetben, a FarmBeats rendszer.

Íme egy részletes folyamat az API-hívásokról.

### <a name="step-1-extendedtype"></a>1. lépés: ExtendedType

Ellenőrizze a /ExtendedType API-t, hogy a típus és a fájlforrás elérhető-e a FarmBeats-en. Ehhez hívja meg a GET a /ExtendedType API.To do do do to do to do to do to do it, call a GET on the /ExtendedType API.

A rendszer által meghatározott értékek a következők:

```json
{
  "items": [
    {
      "id": "ae2aa527-7d27-42e2-8fcf-841937c651d7",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:47:42.5910344Z",
      "key": "SceneFileType",
      "value": [
        "evi",
        "ndvi",
        "ndwi",
        "tci",
        "soil-moisture",
        "sensor-placement",
        "sentinel-b01",
        "sentinel-b02",
        "sentinel-b03",
        "sentinel-b04",
        "sentinel-b05",
        "sentinel-b06",
        "sentinel-b07",
        "sentinel-b08",
        "sentinel-b8a",
        "sentinel-b09",
        "sentinel-b10",
        "sentinel-b11",
        "sentinel-b12",
        "cloud-mask",
        "farm-mask",
        "image/tiff",
        "orthomosaic"
      ],
      "description": "List of scene file types available in system. User can add more values."
    },
    {
      "id": "b7824d2e-3e43-46d3-8fd4-ec7ba8918d84",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:44.8914691Z",
      "key": "SceneFileContentType",
      "value": [
        "image/tiff",
        "image/png",
        "image/jpeg",
        "text/csv",
        "text/plain",
        "text/tab-separated-values",
        "application/json",
        "application/octet-stream",
        "orthomosaic"
      ],
      "description": "List of scene file content types available in system. User can add more values."
    },
    {
      "id": "21d2924d-8b3e-4ce4-a7f8-318e85c0f7f2",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:37.5096445Z",
      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P",
        "DJI"
      ],
      "description": "List of scene sources available in system. User can add more values."
    },
    {
      "id": "6e09ca69-cacf-4b53-b63a-53c659aae4a4",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-10-25T09:45:34.7483899Z",
      "key": "SceneType",
      "value": [
        "base-bands",
        "sensor-placement",
        "soil-moisture",
        "evi",
        "ndwi",
        "ndvi",
        "drone",
        "orthomosaic-drone"
      ],
      "description": "List of scene types available in system. User can add more values."
    },
    {
      "id": "55d8436b-3a86-4bea-87ab-e1b51226525f",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureType",
      "value": [
        "AmbientTemperature",
        "CO2",
        "Depth",
        "ElectricalConductivity",
        "LeafWetness",
        "Length",
        "LiquidLevel",
        "Nitrate",
        "O2",
        "PH",
        "Phosphate",
        "PointInTime",
        "Potassium",
        "Pressure",
        "RainGauge",
        "RelativeHumidity",
        "Salinity",
        "SoilMoisture",
        "SoilTemperature",
        "SolarRadiation",
        "State",
        "TimeDuration",
        "UVRadiation",
        "UVIndex",
        "Volume",
        "WindDirection",
        "WindRun",
        "WindSpeed",
        "Evapotranspiration",
        "PAR"
      ],
      "description": "List of sensor measure types available in system. User can add more values."
    },
    {
      "id": "0dfd6e6b-df58-428f-9ab8-a0674bfdcbe5",
      "createdAt": "2019-06-17T07:32:30.1135716Z",
      "lastModifiedAt": "2019-06-17T07:32:30.1135716Z",
      "key": "SensorMeasureUnit",
      "value": [
        "NoUnit",
        "Celsius",
        "Fahrenheit",
        "Kelvin",
        "Rankine",
        "Pascal",
        "Mercury",
        "PSI",
        "MilliMeter",
        "CentiMeter",
        "Meter",
        "Inch",
        "Feet",
        "Mile",
        "KiloMeter",
        "MilesPerHour",
        "MilesPerSecond",
        "KMPerHour",
        "KMPerSecond",
        "MetersPerHour",
        "MetersPerSecond",
        "Degree",
        "WattsPerSquareMeter",
        "KiloWattsPerSquareMeter",
        "MilliWattsPerSquareCentiMeter",
        "MilliJoulesPerSquareCentiMeter",
        "VolumetricWaterContent",
        "Percentage",
        "PartsPerMillion",
        "MicroMol",
        "MicroMolesPerLiter",
        "SiemensPerSquareMeterPerMole",
        "MilliSiemensPerCentiMeter",
        "Centibar",
        "DeciSiemensPerMeter",
        "KiloPascal",
        "VolumetricIonContent",
        "Liter",
        "MilliLiter",
        "Seconds",
        "UnixTimestamp",
        "MicroMolPerMeterSquaredPerSecond",
        "InchesPerHour"
      ],
      "description": "List of sensor measure units available in system. User can add more values."
    }
  ]
}
```

Ez a lépés egyszeri beállítás. Az új jelenettípus hatóköre arra az előfizetésre korlátozódik, amelyben az Azure FarmBeats telepítve van.

Például a SceneSource: "SlantRange" hozzáadásához a /ExtendedType API azonosítójának put-ját a "SceneSource" bemeneti tartalommal kell megadni.

```json
{

      "key": "SceneSource",
      "value": [
        "sentinel-l1c",
        "sentinel-l2a",
        "farmbeats-model",
        "dji",
        "SlantRange-3P"
      ],
      "description": "List of scene sources available in system. User can add more values."
}

```

A zöld mező a rendszer által definiált jelenetforrás-értékek új kiegészítése.

### <a name="step-2-get-farm-details"></a>2. lépés: A farm részleteinek beszerezni

A jelenetek (.tiff vagy .csv fájlok) egy farm kontextusában találhatók. Be kell, hogy a farm részleteit csinál egy GET a /Farm API.You need to get the farm details by doing a GET on the /Farm API. Az API a FarmBeats-ben elérhető farmok listáját adja vissza. Kiválaszthatja azt a farmot, amelyhez be szeretné adni az adatokat.

GET /Farm válasz:

```json
{
  "id": "07f3e09c-89aa-4619-8d50-e57fb083d8f9",
  "createdAt": "2019-11-01T13:55:41.8804663Z",
  "lastModifiedAt": "2019-11-01T13:55:41.8804663Z",
  "geometry": {
    "type": "Polygon",
    "coordinates": [
      [
        [
          78.34252251428694,
          17.402556352862675
        ],
        [
          78.34278000635095,
          17.407920852463022
        ],
        [
          78.34883106989963,
          17.40878079576528
        ],
        [
          78.3498181228054,
          17.403539173730607
        ],
        [
          78.34805859369493,
          17.39977166504127
        ],
        [
          78.34252251428694,
          17.402556352862675
        ]
      ]
    ]
  },
  "name": "Samplefarm",
  "properties": {
    "crops": "Corn",
    "address": "Sample street"
  }
}
 ```

### <a name="step-3-create-a-scene-id-post-call"></a>3. lépés: Jelenetazonosító létrehozása (POST hívás)

Hozzon létre egy új jelenetet (.tiff vagy .csv fájlt) a megadott adatokkal, amely megadja azt a dátumot, sorrendet és farmazonosítót, amelyhez a jelenet társítva van. A jelenethez társított metaadatok a tulajdonságok alatt definiálhatók, beleértve a mérték időtartamát és típusát.

Új jelenet létrehozása új jelenetazonosítót hoz létre, amely a farmhoz kapcsolódik. A jelenetazonosító létrehozása után a felhasználó ugyanezt használhatja egy új fájl (.tiff vagy .csv) létrehozásához és a fájl tartalmának tárolásához.

Példa a POST-hívás bemeneti hasznos adatára a /Scene API-n:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

API-válasz:

```json
{
  "id": "a0505928-c480-491b-ba31-d38285a28c1d",
  "createdAt": "2019-10-04T16:19:12.4838584Z",
  "lastModifiedAt": "2019-10-04T16:19:12.4838584Z",
  "type": "new type",
  "source": "SlantRange-3P",
  "farmId": "d41a33e7-b73e-480e-9279-0fcb3207332b",
  "date": "2019-10-04T16:13:39.064Z",
  "sequence": 5,
  "name": "test scene",
  "description": "test scene description",
  "properties": {}
}

```

**Jelenetfájl létrehozása**

A 3. A jelenetfájl egy SAS URL-token, amely 24 órán keresztül érvényes.

Ha a felhasználónak programozott módon kell feltöltenie a képek adatfolyamát, a blob storage SDK segítségével definiálhat egy módszert a jelenetfájl azonosítójának, helyének és URL-címének használatával.

Példa a POST-hívás bemeneti hasznos adatára a /SceneFile API-n:

```json
{
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
API-válasz:

```json
{
  "uploadSASUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840?sv=2018-03-28&sr=b&sig=%2F1426JkDcIFE5g3d%2BjOevCVMIn%2FJo9YKwBn3La5zL8Y%3D&se=2019-10-05T16%3A23%3A57Z&sp=w",
  "id": "e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "createdAt": "2019-10-04T16:23:57.1192916Z",
  "lastModifiedAt": "2019-10-04T16:23:57.1192916Z",
  "blobUrl": "https://storagej2lho.blob.core.windows.net/farm-scene/2019/a0505928-c480-491b-ba31-d38285a28c1d/e91139a7-4ebd-4e2f-b17c-c677822dc840",
  "sceneId": "a0505928-c480-491b-ba31-d38285a28c1d",
  "type": "newtype",
  "contentType": "image/tiff",
  "name": "test scene file",
  "description": "test scene file description",
  "properties": {}
}


```

A /SceneFile API POST-hívása egy SAS-feltöltési URL-címet ad vissza, amely a .csv vagy .tiff fájl feltöltésére használható az Azure Blob storage-ügyfél vagy -tár használatával.


## <a name="next-steps"></a>További lépések

A REST API-alapú integráció részleteiről a [REST API című témakörben](rest-api-in-azure-farmbeats.md)talál további információt.

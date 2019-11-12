---
title: Képalkotási partner integrációja
description: A képekkel kapcsolatos partneri integráció ismertetése
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 232ade55350ad1ac85612cf8c69117c19cb20d0c
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927795"
---
# <a name="imagery-partner-integration"></a>Képalkotási partner integrációja

Ez a cikk azt ismerteti, hogyan lehet az Azure FarmBeats Translator összetevő használatával képeket küldeni a FarmBeats. A mezőgazdaságban található képek különböző forrásokból származnak, többek között a többspektrumos fényképezőgépektől, a műholdaktól és a heréktől. A mezőgazdasági képképekkel rendelkező partnerek a FarmBeats integrálva biztosíthatják ügyfeleiknek a saját farmokhoz tartozó egyéni leképezéseket.

A rendelkezésre álló adatok a FarmBeats-gyorsító használatával megjeleníthetők, és potenciálisan felhasználhatók adatfúzióra, illetve (Machine Learning/mesterséges intelligencia) a mezőgazdasági vállalkozások vagy az ügyfélrendszer-integrátorok által létrehozott ML/AI-modellekhez.

A FarmBeats a következőket teszi lehetővé:

- Egyéni képtípusok, forrás, fájlformátumok definiálása kiterjesztett típusú API-k használatával
- A különböző forrásokból származó képadatok betöltése & SceneFile API-k segítségével.

Az alábbi információk a rendszerképek bármilyen formáját ismertetik a FarmBeats rendszerbe.

Amikor kiválasztja a drone-képek szakaszt, megnyílik egy előugró ablak, amely a drone-orthomosaic nagy felbontású képét jeleníti meg. Elérheti a partner szoftverét, amely segít megtervezni a drone-repülőjáratokat és nyers adatgyűjtést. Továbbra is használhatja a partner szoftverét az útvonal-tervezéshez és a orthomosaicához.

A drone-partnereknek engedélyeznie kell az ügyfeleket, hogy az Azure-beli FarmBeats-példánnyal összekapcsolják az ügyfeleik fiókját.

A FarmBeats összekapcsolásához a következő hitelesítő adatokat kell használnia a drone-partner szoftverében:

- API-végpont
- Bérlőazonosító
- Ügyfél-azonosító
- Ügyfél titka

## <a name="api-development"></a>API-fejlesztés

Az API-k a hencegés műszaki dokumentációját tartalmazzák. [Tekintse át az API](https://aka.ms/FarmBeatsDatahubSwagger) -kkal és a megfelelő kérésekkel/válaszokkal kapcsolatos információkat.

## <a name="authentication"></a>Authentication

A FarmBeats Microsoft Azure [Active Directoryt]((https://docs.microsoft.com/azure/app-service/overview-authentication-authorization))használ. A hitelesítési Azure App Service beépített hitelesítési és engedélyezési támogatást biztosít. 

További információ a használatáról: [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).   

A FarmBeats adatközpont tulajdonosi hitelesítést használ, amelynek a következő hitelesítő adatokra van szüksége:

- Ügyfél-azonosító
- Ügyfél titka
- Bérlőazonosító

A fenti hitelesítő adatok használatával a hívó hozzáférési jogkivonatot kérhet, amelyet a következő API-kérelmekben kell elküldeni, a fejléc szakaszban a következő módon:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Az alábbiakban egy Python-kódrészletet talál, amely lekéri a hozzáférési jogkivonatot. Ezután használhatja a tokent a későbbi API-hívások FarmBeat:   
Az Azure importálása 

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

## <a name="http-request-headers"></a>HTTP-kérelmek fejlécei

Itt láthatók a leggyakoribb kérelmek fejlécei, amelyeket meg kell adni az API-hívások FarmBeats-adatközpontba való készítésekor:

**Fejléc** | **Leírás és példa**
--- | ---
Content-Type  | A kérelem formátuma (Content-Type: Application/<format>) a FarmBeats adatközponti API formátuma JSON. Content-Type: Application/JSON
Engedélyezés | Meghatározza az API-hívások létrehozásához szükséges hozzáférési jogkivonatot. Engedélyezés: tulajdonos < hozzáférés-token >
fogadja el  | A válasz formátuma. A FarmBeats adatközponti API-k formátuma a következő: JSON Accept (alkalmazás/JSON)


## <a name="api-requests"></a>API-kérelmek

REST API kérelem elvégzéséhez kombinálja a HTTP-metódust (GET/POST/PUT), az API szolgáltatás URL-címét, az erőforrás URI-JÁT (lekérdezés, adatküldés, frissítés vagy törlés), valamint egy vagy több HTTP-kérelem fejlécét.

Igény szerint a lekérdezési paramétereket is megadhatja a szűréshez, korlátozhatja az adatok méretét, és rendezheti a válaszokat.

Az alábbi példa az eszközök listájának beszerzésére szolgál:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H
"Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>”
```

A legtöbb GET, POST és PUT híváshoz JSON-kérést tartalmazó törzs szükséges.

Az alábbi példa egy eszköz létrehozását mutatja be (ez egy bemeneti JSON-t tartalmaz a kérés törzsével).


```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  
"accept: application/json" -H  
"Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d
"{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  
\"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Adatformátum

A JSON (JavaScript Object Notation) egy közös, nyelvtől független adatformátum, amely tetszőleges adatstruktúrák egyszerű szöveges ábrázolását teszi lehetővé. További információ: [JSON org](https://JSON.org).

## <a name="ingesting-imagery-into-farmbeats"></a>Képek betöltése a FarmBeats-be

Miután a partner rendelkezik hitelesítő adatokkal a FarmBeats-adatközponthoz való kapcsolódáshoz, a partner a következőt hajtja végre a Translator összetevőben:

1.  Hozzon létre egy új kiterjesztett típust a következő mezőkhöz a feltöltésre kerülő képek típusának megfelelően:

    - Jelenet forrása: például < drone_partner_name >
    - Jelenet típusa: például <drone>
    - Jelenet fájltípusa: például <chlorophyll index>
    - Jelenet tartalmának típusa: például < rendszerkép/TIFF >

2.  A Farms API meghívásával lekérheti a farmok listáját az Azure FarmBeats rendszerből.
3.  Adja meg az ügyfél számára, hogy egyetlen farmot válasszon a farmok listájáról.

    A partneri rendszeren a partneri szoftveren belül meg kell jeleníteni a farmon az útvonal megtervezését és a drone repülési és képgyűjteményt.

4.  Hívja meg a jelenet API-t, és adja meg a szükséges adatokat egy új jelenet egyedi SceneID való létrehozásához.
5.  Egy blob SAS URL-cím megadásával feltöltheti a szükséges képeket a FarmBeats adatközpontba a kiválasztott Farm kontextusában a FarmBeats rendszerbe.

Itt látható az API-hívások részletes folyamata:

### <a name="step-1-extendedtype"></a>1\. lépés: ExtendedType

A ExtendedType API-ban ellenőrizze, hogy a típus és a fájl forrása elérhető-e a FarmBeats. Ezt úgy teheti meg, hogy meghívja az/ExtendedType API-t.

A rendszer által definiált értékek a következők:

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

Ez egy egyszeri beállítás lesz, és az új scenetype hatóköre arra az előfizetésre korlátozódik, amelyben a FarmBeats-projekt telepítve van.

Példa: a "SlantRange" SceneSource hozzáadásához tegye a/ExtendedType AZONOSÍTÓját a következő kulccsal: "SceneSource" bemeneti hasznos adat:

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

A zöld mező a rendszer által definiált jelenetekhez tartozó értékek új hozzáadása.

### <a name="step-2-get-farmdetails"></a>2\. lépés: a FarmDetails beolvasása

A jelenetek (TIFF vagy. CSV fájlok) egy farm kontextusában lesznek. A farm részleteit a Get on/farm API-val kell beolvasnia. Az API visszaadja a FarmBeats elérhető farmok listáját, és kiválaszthatja, hogy melyik farmon kívánja befoglalni az adatot.

/Farm-válasz beolvasása:

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

### <a name="step-3-create-ascene-id-post-call"></a>3\. lépés: a/jelenet AZONOSÍTÓjának létrehozása (hívás utáni művelet)

Hozzon létre egy új jelenetet (TIFF vagy. csv fájl) a megadott adatokkal, és adja meg a dátumot, a sorozatot és a farm AZONOSÍTÓját, amelyhez a jelenet társítva lesz. A jelenethez társított metaadatok a tulajdonságok területen definiálhatók, beleértve a mérték időtartamát és típusát is.

Ez létrehoz egy új SceneID, amely a farmhoz lesz társítva. A SceneID létrehozása után a felhasználó ugyanezt használhatja egy új fájl (TIFF vagy. csv) létrehozásához & tárolja a fájl tartalmát.

Példa bemeneti adattartalomra a post Call on/Scene API-hoz

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

API-Válasz:

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

**Létrehozás/SceneFile**

A 3. lépésben visszaadott színtér-azonosító a SceneFile bemenete. A SceneFile egy SAS URL-tokent ad vissza, amely 24 órán keresztül érvényes.

Ha a felhasználónak programozott módon kell feltöltenie a képek egy részét, a blob Storage SDK segítségével definiálhat egy metódust a Scenefile-azonosító, a hely & URL-cím használatával.

Példa bemeneti adattartalmat a post híváshoz a/Scenefile API-ban:

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
API-Válasz:

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

A/SceneFile API-hoz intézett post hívása egy SAS-feltöltési URL-címet ad vissza, amellyel feltöltheti a CSV-vagy TIFF-fájlt az Azure Blob Storage ügyfél/könyvtár használatával.


## <a name="next-steps"></a>Következő lépések

További információ a REST API-alapú integráció részleteiről: [REST API](references-for-farmbeats.md#rest-api).

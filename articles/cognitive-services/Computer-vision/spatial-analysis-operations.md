---
title: Térbeli elemzési műveletek
titleSuffix: Azure Cognitive Services
description: A térbeli elemzési műveletek.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: f9df17afe8b6d25df3d9dcc5f4eec0b9a028404f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254006"
---
# <a name="spatial-analysis-operations"></a>Térbeli elemzési műveletek

A térbeli elemzések lehetővé teszik a valós idejű folyamatos átvitelű videók elemzését a kamera-eszközökről. Minden konfigurált kamera-eszközön a térbeli elemzés műveletei az Azure IoT Hub-példányának küldött JSON-üzenetek kimeneti streamjét fogják eredményezni. 

A térbeli elemzési tároló a következő műveleteket hajtja végre:

| Műveleti azonosító| Leírás|
|---------|---------|
| cognitiveservices. vízió. spatialanalysis – personcount | Egy kijelölt zónában lévő személyeket számít a kamera mezőjében. <br> Kibocsátja a kezdeti _personCountEvent_ eseményt, majd _personCountEvent_ az eseményeket a számlálás megváltozásakor.  |
| cognitiveservices. vízió. spatialanalysis – personcrossingline | Nyomon követi, hogy egy személy Mikor halad át egy kijelölt vonalat a kamera mezőjében. <br>_PersonLineEvent_ eseményt bocsát ki, amikor a személy átlépi a vonalat, és útmutatást nyújt. 
| cognitiveservices. vízió. spatialanalysis – personcrossingpolygon | Nyomon követi, hogy egy személy Mikor halad át egy kijelölt vonalat a kamera mezőjében. <br> _PersonLineEvent_ eseményt bocsát ki, amikor a személy átlépi a zónát, és útmutatást nyújt. |
| cognitiveservices. vízió. spatialanalysis – persondistance | Nyomon követi, ha a felhasználók nem sértik a távolsági szabályt. <br> Rendszeresen bocsát ki egy _personDistanceEvent_ az egyes távolságok megsértésének helyén. |

Az összes művelet a `.debug` verzióban is elérhető, amely képes megjeleníteni a képkockákat a feldolgozás során. A `xhost +` képkockák és események megjelenítésének engedélyezéséhez futtatnia kell a gazdagépen.

| Műveleti azonosító| Leírás|
|---------|---------|
| cognitiveservices. vízió. spatialanalysis-personcount. debug | Egy kijelölt zónában lévő személyeket számít a kamera mezőjében. <br> Kibocsátja a kezdeti _personCountEvent_ eseményt, majd _personCountEvent_ az eseményeket a számlálás megváltozásakor.  |
| cognitiveservices. vízió. spatialanalysis-personcrossingline. debug | Nyomon követi, hogy egy személy Mikor halad át egy kijelölt vonalat a kamera mezőjében. <br>_PersonLineEvent_ eseményt bocsát ki, amikor a személy átlépi a vonalat, és útmutatást nyújt. 
| cognitiveservices. vízió. spatialanalysis-personcrossingpolygon. debug | Nyomon követi, hogy egy személy Mikor halad át egy kijelölt vonalat a kamera mezőjében. <br> _PersonLineEvent_ eseményt bocsát ki, amikor a személy átlépi a zónát, és útmutatást nyújt. |
| cognitiveservices. vízió. spatialanalysis-persondistance. debug | Nyomon követi, ha a felhasználók nem sértik a távolsági szabályt. <br> Rendszeresen bocsát ki egy _personDistanceEvent_ az egyes távolságok megsértésének helyén. |

A térbeli elemzések videó AI-modulként is futtathatók [élő video Analytics](https://azure.microsoft.com/services/media-services/live-video-analytics/) használatával. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| Műveleti azonosító| Leírás|
|---------|---------|
| cognitiveservices. vízió. spatialanalysis-personcount. livevideoanalytics | Egy kijelölt zónában lévő személyeket számít a kamera mezőjében. <br> Kibocsátja a kezdeti _personCountEvent_ eseményt, majd _personCountEvent_ az eseményeket a számlálás megváltozásakor.  |
| cognitiveservices. vízió. spatialanalysis-personcrossingline. livevideoanalytics | Nyomon követi, hogy egy személy Mikor halad át egy kijelölt vonalat a kamera mezőjében. <br>_PersonLineEvent_ eseményt bocsát ki, amikor a személy átlépi a vonalat, és útmutatást nyújt. 
| cognitiveservices. vízió. spatialanalysis-personcrossingpolygon. livevideoanalytics | Nyomon követi, hogy egy személy Mikor halad át egy kijelölt vonalat a kamera mezőjében. <br> _PersonLineEvent_ eseményt bocsát ki, amikor a személy átlépi a zónát, és útmutatást nyújt. |
| cognitiveservices. vízió. spatialanalysis-persondistance. livevideoanalytics | Nyomon követi, ha a felhasználók nem sértik a távolsági szabályt. <br> Rendszeresen bocsát ki egy _personDistanceEvent_ az egyes távolságok megsértésének helyén. |

Az élő videó elemzési műveletei a verzióban is elérhetők `.debug` (pl. cognitiveservices. vízió. spatialanalysis-personcount. livevideoanalytics. debug), amely képes megjeleníteni a képkockákat a feldolgozás során. A `xhost +` képkockák és események vizualizációjának engedélyezéséhez futtatnia kell a gazdagépen.

> [!IMPORTANT]
> A Computer látási AI-modellek észlelik és megkeresik az emberi jelenlétet a videofelvételekben és a kimenetekben egy emberi törzs körüli határoló mező használatával. Az AI-modellek nem kísérlik meg az arcok észlelését, illetve az egyéni identitások vagy demográfiai adatok felderítését.

Ezek a térbeli elemzési műveletek által igényelt paraméterek.

| Üzemeltetési paraméterek| Leírás|
|---------|---------|
| Művelet azonosítója | A fenti tábla műveleti azonosítója.|
| engedélyezve | Boolean: true vagy FALSE|
| VIDEO_URL| A kamera eszközhöz tartozó RTSP URL-cím (példa: `rtsp://username:password@url` ). A térbeli elemzés támogatja a H. 264 kódolású adatfolyamot RTSP, http vagy MP4 használatával |
| VIDEO_SOURCE_ID | A kamera-eszköz vagy a videó stream rövid neve. Ezt a rendszer az esemény JSON-kimenetével adja vissza.|
| VIDEO_IS_LIVE| A kamera eszközei esetében igaz; hamis a rögzített videókhoz.|
| VIDEO_DECODE_GPU_INDEX| A videó keretének dekódolására szolgáló GPU. Alapértelmezés szerint 0. Azonosnak kell lennie a `gpu_index` többi csomópont-konfigurációval `VICA_NODE_CONFIG` , például: `DETECTOR_NODE_CONFIG` .|
| DETECTOR_NODE_CONFIG | JSON, amely azt jelzi, hogy melyik GPU-t kell futtatni a detektor csomópontot. A következő formátumúnak kell lennie: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | A zóna és a vonal JSON-konfigurációja az alább vázolt módon.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Cognitiveservices. vízió. spatialanalysis – personcount

 Ez egy példa a SPACEANALYTICS_CONFIG paraméter JSON-bemenetére, amely egy zónát konfigurál. A művelethez több zóna is konfigurálható.

```json
{
"zones":[{
    "name": "lobbycamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "count",
        "config":{
            "trigger": "event",
            "output_frequency": 1
      }
    }]
}
```

| Név | Típus| Leírás|
|---------|---------|---------|
| `zones` | list| Zónák listája. |
| `name` | sztring| A zóna rövid neve.|
| `polygon` | list| Minden érték pár a sokszög csúcspontjának x, y értékét jelöli. A sokszög azokat a területeket jelöli, amelyekben a személyek nyomon követése vagy megszámlálása történik, a sokszög pontok pedig normalizált koordinátákon (0-1) alapulnak, ahol a bal felső sarokban (0,0, 0,0) és a jobb alsó sarokban található (1,0, 1,0).   
| `threshold` | float| Az események akkor egressed, ha az AI-modellek megbízhatósága nagyobb vagy egyenlő ez az érték. |
| `type` | sztring| A **cognitiveservices. vízió. spatialanalysis-personcount** esetében ennek a következőnek kell lennie: `count` .|
| `trigger` | sztring| Egy esemény küldésére szolgáló eseményindító típusa. A támogatott értékek olyan `event` események küldésére szolgálnak, amikor a darabszám megváltozik vagy `interval` rendszeres időközönként küldi el az eseményeket, függetlenül attól, hogy a szám módosult-e.
| `interval` | sztring| Egy idő másodpercben, ameddig a rendszer összesíti a személyeket, mielőtt egy eseményt kilőtték. A művelet továbbra is állandó arányban elemzi a jelenetet, és visszaadja az adott intervallum leggyakoribb számlálóját. Az összesítési időköz a és a értékre is érvényes `event` `interval` .|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>A cognitiveservices. vízió. spatialanalysis-personcrossingline vonal konfigurációja

Ez egy példa egy olyan JSON-bemenetre a SPACEANALYTICS_CONFIG paraméter számára, amely egy vonalat konfigurál. A művelethez több metsző sort is beállíthat.

```json
{
"lines":[{
    "name": "doorcamera" 
    "line": {
        "start": {"x": 0, "y": 0.5},
        "end": {"x": 1, "y": 0.5}
            },
    "threshold": 50.00,
    "events":[{
        "type": "linecrossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Név | Típus| Leírás|
|---------|---------|---------|
| `lines` | list| Sorok listája.|
| `name` | sztring| A sor rövid neve.|
| `line` | list| A sor definíciója. Ez egy irányított vonal, amely lehetővé teszi a "belépés" és a "kilépés" megértését.|
| `start` | érték pár| x, y koordináták a vonal kezdőpontja számára. A lebegőpontos értékek a csúcspont pozícióját jelölik, a bal felső sarokban. Az abszolút x és y értékek kiszámításához szorozza meg ezeket az értékeket a keret méretével. |
| `end` | érték pár| x, y koordináták a vonal záró pontjához. A lebegőpontos értékek a csúcspont pozícióját jelölik, a bal felső sarokban. Az abszolút x és y értékek kiszámításához szorozza meg ezeket az értékeket a keret méretével. |
| `threshold` | float| Az események akkor egressed, ha az AI-modellek megbízhatósága nagyobb vagy egyenlő ez az érték. |
| `type` | sztring| A **cognitiveservices. vízió. spatialanalysis-personcrossingline** esetében ennek a következőnek kell lennie: `linecrossing` .|
|`trigger`|sztring|Egy esemény küldésére szolgáló eseményindító típusa.<br>Támogatott értékek: "Event": tűz, ha valaki keresztezi a vonalat.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Cognitiveservices. vízió. spatialanalysis – personcrossingpolygon

Ez egy példa a SPACEANALYTICS_CONFIG paraméter JSON-bemenetére, amely egy zónát konfigurál. A művelethez több zóna is konfigurálható.

 ```json
{
"zones":[{
    "name": "queuecamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "zone_crossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Név | Típus| Leírás|
|---------|---------|---------|
| `zones` | list| Zónák listája. |
| `name` | sztring| A zóna rövid neve.|
| `polygon` | list| Minden érték pár a sokszög csúcspontjának x, y értékét jelöli. A sokszög a személyek nyomon követésének vagy megszámlálásának területét jelöli. A lebegőpontos értékek a csúcspont pozícióját jelölik, a bal felső sarokban. Az abszolút x és y értékek kiszámításához szorozza meg ezeket az értékeket a keret méretével. 
| `threshold` | float| Az események akkor egressed, ha az AI-modellek megbízhatósága nagyobb vagy egyenlő ez az érték. |
| `type` | sztring| A **cognitiveservices. vízió. spatialanalysis-personcrossingpolygon** esetében ennek a következőnek kell lennie: `enter` vagy `exit` .|
| `trigger`|sztring|Egy esemény küldéséhez használt eseményindító típusa<br>Támogatott értékek: "Event": tűz, ha valaki belép vagy kilép a zónából.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Cognitiveservices. vízió. spatialanalysis – persondistance

Ez egy példa a SPACEANALYTICS_CONFIG paraméter JSON-bemenetére, amely egy zónát konfigurál a **cognitiveservices. vízió. spatialanalysis-persondistance**számára. A művelethez több zóna is konfigurálható.

```json
{
"zones":[{
    "name": "lobbycamera",
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 35.00,
    "events":[{
        "type": "persondistance",
        "config":{
            "trigger": "event",
            "output_frequency":1,
            "minimum_distance_threshold":6.0,
            "maximum_distance_threshold":35.0
            }
        }]
    }]
}
```

| Név | Típus| Leírás|
|---------|---------|---------|
| `zones` | list| Zónák listája. |
| `name` | sztring| A zóna rövid neve.|
| `polygon` | list| Minden érték pár a sokszög csúcspontjának x, y értékét jelöli. A sokszög azokat a területeket jelöli, amelyekben a rendszer megszámolja a személyeket, és megméri az emberek közötti távolságot. A lebegőpontos értékek a csúcspont pozícióját jelölik, a bal felső sarokban. Az abszolút x és y értékek kiszámításához szorozza meg ezeket az értékeket a keret méretével. 
| `threshold` | float| Az események akkor egressed, ha az AI-modellek megbízhatósága nagyobb vagy egyenlő ez az érték. |
| `type` | sztring| A **cognitiveservices. vízió. spatialanalysis-persondistance** esetében ennek a következőnek kell lennie: `people_distance` .|
| `trigger` | sztring| Egy esemény küldésére szolgáló eseményindító típusa. A támogatott értékek olyan `event` események küldésére szolgálnak, amikor a darabszám megváltozik vagy `interval` rendszeres időközönként küldi el az eseményeket, függetlenül attól, hogy a szám módosult-e.
| `interval` | sztring | Egy idő másodpercben, amely szerint a szabálysértések összesítése megtörténik az esemény elindítását megelőzően. Az összesítési időköz a és a értékre is érvényes `event` `interval` .|
| `output_frequency` | int | Az események egressed aránya. Ha `output_frequency` = x, minden X esemény egressed, pl. `output_frequency` = 2: minden más esemény kimenet. A output_frequency a és a is `event` alkalmazható `interval` .|
| `minimum_distance_threshold` | float| A távolság a lábon, amely egy "TooClose" eseményt indít el, ha az emberek kisebbek, mint a távolságok egymástól.|
| `maximum_distance_threshold` | float| A távolság a lábon, amely egy "TooFar" eseményt indít el, ha az emberek nagyobbak, mint a távolságok egymástól.|

Ez egy példa a DETECTOR_NODE_CONFIG paraméter JSON-bemenetére, amely egy **cognitiveservices. vízió. spatialanalysis-persondistance** zónát konfigurál.

```json
{ 
"gpu_index": 0, 
"do_calibration": true
}
```

| Név | Típus| Description|
|---------|---------|---------|
| `gpu_index` | sztring| A GPU-index, amelyen a művelet futni fog.|
| `do_calibration` | sztring | Azt jelzi, hogy a kalibrálás be van kapcsolva. `do_calibration` a **cognitiveservices. vizualizáció. spatialanalysis-persondistance** megfelelő működéséhez igaznak kell lennie.|

A zónák és a vonalak konfigurációjának megismeréséhez tekintse meg a [kamera elhelyezésére](spatial-analysis-camera-placement.md)  vonatkozó irányelveket.

## <a name="spatial-analysis-operation-output"></a>Térbeli elemzési művelet kimenete

Az egyes műveletek eseményeinek egressed az Azure IoT Hub JSON formátumban.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>JSON-formátum a cognitiveservices. vízióhoz. spatialanalysis – personcount AI-ismeretek

A művelet által adott esemény kimenetének JSON-mintája.

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Esemény mező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Eseményazonosító|
| `type` | sztring| Eseménytípus|
| `detectionsId` | array| Az eseményt kiváltó személy észlelésének egyedi azonosítója 1. méretű tömb|
| `properties` | gyűjtemény| Értékek gyűjteménye|
| `trackinId` | sztring| A észlelt személy egyedi azonosítója|
| `status` | sztring| "Enter" vagy "Exit"|
| `side` | int| A sokszög azon oldalának száma, amelyen a személy áthaladt|
| `zone` | sztring | A keresztben lévő zónát jelölő sokszög "Name" mezője|
| `trigger` | sztring| Az eseményindító típusa "Event" vagy "Interval" a (z) SPACEANALYTICS_CONFIG értékének függvényében `trigger`|

| Észlelések mező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Észlelési azonosító|
| `type` | sztring| Észlelés típusa|
| `region` | gyűjtemény| Értékek gyűjteménye|
| `type` | sztring| Régió típusa|
| `points` | gyűjtemény| A bal felső és a jobb alsó pont, amikor a régió típusa téglalap |
| `confidence` | float| Algoritmus megbízhatósága|

| SourceInfo mező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Kamera azonosítója|
| `timestamp` | dátum| A JSON-tartalom kibocsátásának UTC szerinti dátuma|
| `width` | int | Videó keretének szélessége|
| `height` | int | Videó keretének magassága|
| `frameId` | int | Keret azonosítója|
| `cameraCallibrationInfo` | gyűjtemény | Értékek gyűjteménye|
| `status` | sztring | Azt jelzi, hogy a kamera kalibrálása a "teljes" értékre van-e állítva|
| `cameraHeight` | float | A kamera magassága a talajszint felett lábakban. Ez az automatikus kalibrálásra utal. |
| `focalLength` | float | A kamera gyújtótávolsága képpontban megadva Ez az automatikus kalibrálásra utal. |
| `tiltUpAngle` | float | A kamera dőlésének szöge függőlegesen Ez az automatikus kalibrálásra utal.|

| SourceInfo mező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Kamera azonosítója|
| `timestamp` | dátum| A JSON-tartalom kibocsátásának UTC szerinti dátuma|
| `width` | int | Videó keretének szélessége|
| `height` | int | Videó keretének magassága|
| `frameId` | int | Keret azonosítója|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>JSON-formátum a cognitiveservices. vízióhoz. spatialanalysis – personcrossingline AI-ismeretek

Példa a JSON-ra a művelet észlelési kimenetéhez.

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```
| Esemény mező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Eseményazonosító|
| `type` | sztring| Eseménytípus|
| `detectionsId` | array| Az eseményt kiváltó személy észlelésének egyedi azonosítója 1. méretű tömb|
| `properties` | gyűjtemény| Értékek gyűjteménye|
| `trackinId` | sztring| A észlelt személy egyedi azonosítója|
| `status` | sztring| Vonali kereszteződések iránya, vagy "CrossLeft" vagy "CrossRight"|
| `zone` | sztring | A sor "Name" (név) mezője|

| Észlelések mező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Észlelési azonosító|
| `type` | sztring| Észlelés típusa|
| `region` | gyűjtemény| Értékek gyűjteménye|
| `type` | sztring| Régió típusa|
| `points` | gyűjtemény| A bal felső és a jobb alsó pont, amikor a régió típusa téglalap |
| `confidence` | float| Algoritmus megbízhatósága|

| SourceInfo mező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Kamera azonosítója|
| `timestamp` | dátum| A JSON-tartalom kibocsátásának UTC szerinti dátuma|
| `width` | int | Videó keretének szélessége|
| `height` | int | Videó keretének magassága|
| `frameId` | int | Keret azonosítója|


> [!IMPORTANT]
> Az AI-modell észleli a személyt, függetlenül attól, hogy a személy a kameráról vagy onnan érkezőre irányul. Az AI-modell nem futtat Arcfelismerés vagy felismerést, és nem bocsát ki biometrikus adatokat. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>JSON-formátum a cognitiveservices. vízióhoz. spatialanalysis – personcrossingpolygon AI-ismeretek

Példa a JSON-ra a művelet észlelési kimenetéhez.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": ""
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Esemény mező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Eseményazonosító|
| `type` | sztring| Eseménytípus|
| `detectionsId` | array| Az eseményt kiváltó személy észlelésének egyedi azonosítója 1. méretű tömb|
| `properties` | gyűjtemény| Értékek gyűjteménye|
| `trackinId` | sztring| A észlelt személy egyedi azonosítója|
| `status` | sztring| A sokszög-kereszteződések iránya, vagy "Enter" vagy "Exit"|
| `zone` | sztring | A keresztben lévő zónát jelölő sokszög "Name" mezője|

| Észlelések mező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Észlelési azonosító|
| `type` | sztring| Észlelés típusa|
| `region` | gyűjtemény| Értékek gyűjteménye|
| `type` | sztring| Régió típusa|
| `points` | gyűjtemény| A bal felső és a jobb alsó pont, amikor a régió típusa téglalap |
| `confidence` | float| Algoritmus megbízhatósága|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>JSON-formátum a cognitiveservices. vízióhoz. spatialanalysis – persondistance AI-ismeretek

Példa a JSON-ra a művelet észlelési kimenetéhez.

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Esemény mező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Eseményazonosító|
| `type` | sztring| Eseménytípus|
| `detectionsId` | array| Az eseményt kiváltó személy észlelésének egyedi azonosítója 1. méretű tömb|
| `properties` | gyűjtemény| Értékek gyűjteménye|
| `personCount` | int| Az esemény kibocsátásakor észlelt személyek száma|
| `averageDistance` | float| A láb összes észlelt személye közötti átlagos távolság|
| `minimumDistanceThreshold` | float| A távolság a lábakban, amely egy "TooClose" eseményt indít el, ha az emberek kisebbek, mint a távolság egymástól.|
| `maximumDistanceThreshold` | float| A távolság a lábakban, amely egy "TooFar" eseményt indít el, ha a személyek a távolságnál nagyobbak lesznek.|
| `eventName` | sztring| Az esemény neve a (z `TooClose` `minimumDistanceThreshold` ) megsértve, `TooFar` Ha a `maximumDistanceThreshold` rendszer sérti, vagy `unknown` Ha az automatikus kalibrálás nem fejeződött be|
| `distanceViolationPersonCount` | int| A vagy a rendszer megsértésében észlelt személyek száma `minimumDistanceThreshold``maximumDistanceThreshold`|
| `zone` | sztring | Annak a sokszögnek a neve mezője, amely a személyek közötti elválasztáshoz figyelt zónát jelöli|
| `trigger` | sztring| Az eseményindító típusa "Event" vagy "Interval" a (z) SPACEANALYTICS_CONFIG értékének függvényében `trigger`|

| Észlelések mező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Észlelési azonosító|
| `type` | sztring| Észlelés típusa|
| `region` | gyűjtemény| Értékek gyűjteménye|
| `type` | sztring| Régió típusa|
| `points` | gyűjtemény| A bal felső és a jobb alsó pont, amikor a régió típusa téglalap |
| `confidence` | float| Algoritmus megbízhatósága|
| `centerGroundPoint` | 2 lebegőpontos érték| `x`, `y` értékek a személy kikövetkeztetett helyének koordinátáival a lábon. `x` távolság a kamerától a talpon kijelzett kamera-képsíkok felé merőlegesen. `y` távolság a kamerával párhuzamosan, a terepen a lábon kijelzett képsíkon.|

| SourceInfo mező neve | Típus| Description|
|---------|---------|---------|
| `id` | sztring| Kamera azonosítója|
| `timestamp` | dátum| A JSON-tartalom kibocsátásának UTC szerinti dátuma|
| `width` | int | Videó keretének szélessége|
| `height` | int | Videó keretének magassága|
| `frameId` | int | Keret azonosítója|
| `cameraCallibrationInfo` | gyűjtemény | Értékek gyűjteménye|
| `status` | sztring | Azt jelzi, hogy a kamera kalibrálása a "teljes" értékre van-e állítva|
| `cameraHeight` | float | A kamera magassága a talajszint felett lábakban. Ez az automatikus kalibrálásra utal. |
| `focalLength` | float | A kamera gyújtótávolsága képpontban megadva Ez az automatikus kalibrálásra utal. |
| `tiltUpAngle` | float | A kamera dőlésének szöge függőlegesen Ez az automatikus kalibrálásra utal.|


## <a name="use-the-output-generated-by-the-container"></a>A tároló által generált kimenet használata

Előfordulhat, hogy integrálni szeretné a térbeli elemzési észlelést vagy az eseményeket az alkalmazásba. A következő szempontokat érdemes figyelembe venni: 

* Használja az Azure Event hub SDK-t a kiválasztott programozási nyelvhez az Azure IoT Hub-végponthoz való kapcsolódáshoz és az események fogadásához. További információért lásd: [eszközről a felhőbe irányuló üzenetek olvasása a beépített végpontról](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) . 
* Állítsa be az **üzenet-útválasztást** az Azure-IoT hub az események más végpontoknak való elküldéséhez, vagy mentse az eseményeket az adattárba. További információ: [IoT hub üzenet-útválasztás](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) . 
* Állítson be egy Azure Stream Analytics feladatot az események valós idejű feldolgozásához, amikor megérkeznek és vizualizációkat hoznak létre. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Térbeli elemzési műveletek nagy léptékű üzembe helyezése (több kamera)

A GPU-k legjobb teljesítményének és kihasználtságának kihasználása érdekében a Graph-példányok használatával több kamerán is üzembe helyezhet bármilyen térbeli elemzési műveletet. Az alábbi példa a `cognitiveservices.vision.spatialanalysis-personcount` művelet öt kamerán való futtatására szolgál.

```json
 "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
          "personcount": {
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
              "version": 1,
              "enabled": true,
              "sharedNodes": {
                  "shared_detector1": {
                      "node": "PersonCountGraph.detector",
                      "parameters": {
                          "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 5}",
                      }
                  }
              },
              "parameters": {
                  "VIDEO_DECODE_GPU_INDEX": 0,
                  "VIDEO_IS_LIVE": true
              },
              "instances": {
                  "1": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                          "VIDEO_SOURCE_ID": "camera 1",
                          "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"zone5\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]],\"threshold\":50.0, \"events\":[{\"type\":\"count\", \"output_frequency\": 1}]}]}"
                      }
                  },
                  "2": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                          "VIDEO_SOURCE_ID": "camera 2",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "3": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                          "VIDEO_SOURCE_ID": "camera 3",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "4": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                          "VIDEO_SOURCE_ID": "camera 4",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "5": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                          "VIDEO_SOURCE_ID": "camera 5",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  }
              }
          }
      }
  }
  ```
| Név | Típus| Leírás|
|---------|---------|---------|
| `batch_size` | int | A műveletben használt fényképezőgépek számát jelzi. |

## <a name="next-steps"></a>Következő lépések

* [Felhasználók üzembe helyezése webes alkalmazásokban](spatial-analysis-web-app.md)
* [Naplózás és hibaelhárítás](spatial-analysis-logging.md)
* [Kamera elhelyezése – útmutató](spatial-analysis-camera-placement.md)
* [A zóna és a vonal elhelyezési útmutatója](spatial-analysis-zone-line-placement.md)

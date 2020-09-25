---
title: Frissítés a Computer Vision API
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan frissíthet a 2.0/v 2.1-es verzióra Computer Vision v 3.0 olvasási API-ra.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 5910c40729d07d5a759b2e5cc7b7a4272524c150
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91253853"
---
# <a name="upgrade-from-read-v2x-to-read-v3x"></a>Frissítés a Read v2. x verzióról a v3. x olvasásához

Ez az útmutató bemutatja, hogyan frissítheti a meglévő tárolót vagy a felhő API-kódját az Read v2. x verzióról a v 3.0 és a v 3.1 előzetes verziójának olvasásához.

## <a name="determine-your-api-path"></a>Az API elérési útjának meghatározása
A következő táblázat segítségével határozhatja meg az API-útvonal **verziószámát** az áttelepíteni kívánt Read 3. x verzió alapján.

|Terméktípus| Verzió | Verzió sztringje 3. x API elérési úton |
|:-----|:----|:----|
|Szolgáltatás | Olvasási 3,0 | **v3.0** |
|Tároló | Olvasási 3,0 előzetes verzió | **v3.0** |
|Szolgáltatás/tároló | Olvasási 3,1 előzetes verzió | **v 3.1 – előzetes verzió. 2** |

Ezután a következő részekben leszűkítheti a műveleteit, és lecserélheti az API-útvonalon található **Version karakterláncot** a táblázatból származó értékre. Például az **olvasás v 3.1 előzetes** verziójának felhő-és tároló-verziói esetében frissítse az API-elérési utat a **https://{Endpoint}/View/v 3.1-Preview-ra. 2/Read/elemezze [? Language]**.

## <a name="servicecontainer"></a>Szolgáltatás/tároló

### `Batch Read File`

|Olvasás 2. x |3. x olvasása  |
|----------|-----------|
|https://{Endpoint}/vízió/**v 2.0/olvasás/mag/asyncBatchAnalyze**     |https://{Endpoint}/vízió/<**Version karakterlánc**>/Read/Analyze [? Language]|
    
Új opcionális _nyelvi_ paraméter érhető el. Ha nem ismeri a dokumentum nyelvét, vagy többnyelvű is lehet, ne adja meg azt. 

### `Get Read Results`

|Olvasás 2. x |3. x olvasása  |
|----------|-----------|
|https://{Endpoint}/látási/**v 2.0/olvasási/üzemeltetési**/{operationId}     |https://{Endpoint}/vízió/<**Version karakterlánc**>/Read/analyzeresults/{OperationID}|

### <a name="get-read-operation-result-status-flag"></a>`Get Read Operation Result` állapot jelzője

A sikeres hívás után a `Get Read Operation Result` rendszer visszaadja a JSON-törzs állapot karakterlánc mezőjét.
 
|Olvasás 2. x |3. x olvasása  |
|----------|-----------|
|`"NotStarted"` |   `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|
    
### <a name="api-response-json"></a>API-válasz (JSON) 

Vegye figyelembe a következő módosításokat a JSON-ben:
* A v2. x verzióban az `Get Read Operation Result` OCR-felismerési JSON-t fogja visszaadni, ha az állapota `Succeeded"` . A v 3.0-ban ez a mező a következő: `succeeded` .
* Az oldal-tömb gyökerének beszerzéséhez módosítsa a JSON-hierarchiát a verzióra `recognitionResults` `analyzeResult` / `readResults` . Az egyoldalas sor és a JSON-hierarchia változatlan marad, ezért nincs szükség kód módosítására.
* A rendszer átnevezte az oldal szögét, `clockwiseOrientation` `angle` és a tartomány 0-360 fok értékről-180 értékről 180 fok értékűre módosult. A kódoktól függően előfordulhat, hogy nem kell módosítania a módosításokat, mivel a legtöbb matematikai funkció a tartomány bármelyikét képes kezelni.

A v 3.0 API a következő tökéletesítéseket is bemutatja:
* `createdDateTime` és `lastUpdatedDateTime` hozzáadva, hogy nyomon tudja követni a feldolgozás időtartamát. További részletekért tekintse meg a dokumentációt. 
* `version` az API-nak az eredmények létrehozásához használt verzióját mutatja be
* A rendszer hozzáad egy szót `confidence` . Ez az érték úgy van kalibrálva, hogy a 0,95 érték azt jelenti, hogy a rendszer 95%-os eséllyel az elismerés helyes. A megbízhatósági pontszám használatával kiválaszthatja, hogy melyik szöveget szeretné elküldeni az emberi felülvizsgálatba. 
    
    
A 2. X verzióban a kimeneti formátum a következő: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
}
```
    
A v 3.0-s verziójában a rendszer módosította az alábbiakat:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="service-only"></a>Csak szolgáltatás

### `Recognize Text`
`Recognize Text` az *előnézeti* művelet, amely a *Computer Vision API összes verziójában elavult*. Át kell térnie `Recognize Text` a `Read` (v 3.0) vagy `Batch Read File` (v 2.0, v 2.1) verzióra. a v 3.0- `Read` s verziója újabb és jobb modelleket tartalmaz a szöveges felismeréshez és a további funkciókhoz, ezért ajánlott. Frissítés a verzióról a következőre `Recognize Text` `Read` :

|Szövegfelismerés 2. x |3. x olvasása  |
|----------|-----------|
|https://{Endpoint}/vízió/**v 2.0/recognizeText [? Mode]**|https://{Endpoint}/vízió/<**Version karakterlánc**>/Read/Analyze [? Language]|
    
A (z) nem támogatja a _Mode_ paramétert `Read` . A kézírásos és a nyomtatott szöveg is automatikusan támogatott lesz.
    
A 3.0-s verzióban egy új opcionális _nyelvi_ paraméter érhető el. Ha nem ismeri a dokumentum nyelvét, vagy többnyelvű is lehet, ne adja meg azt. 

### `Get Recognize Text Operation Result`

|Szövegfelismerés 2. x |3. x olvasása  |
|----------|-----------|
|https://{Endpoint}/vízió/**v 2.0/textOperations/**{operationId}|https://{Endpoint}/vízió/<**Version karakterlánc**>/Read/analyzeresults/{OperationID}|

### <a name="get-recognize-text-operation-result-status-flags"></a>`Get Recognize Text Operation Result` állapot jelzői
A sikeres hívás után a `Get Recognize Text Operation Result` rendszer visszaadja a JSON-törzs állapot karakterlánc mezőjét. 
 
|Szövegfelismerés 2. x |3. x olvasása  |
|----------|-----------|
|`"NotStarted"` |   `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|

### <a name="api-response-json"></a>API-válasz (JSON)

Vegye figyelembe a következő módosításokat a JSON-ben:    
* A v2. x verzióban az `Get Read Operation Result` OCR-felismerési JSON-t fogja visszaadni, ha az állapota `Succeeded` . A v3. x-ben ez a mező `succeeded` .
* Az oldal-tömb gyökerének beszerzéséhez módosítsa a JSON-hierarchiát a verzióra `recognitionResult` `analyzeResult` / `readResults` . Az egyoldalas sor és a JSON-hierarchia változatlan marad, ezért nincs szükség kód módosítására.

A v 3.0 API emellett a következő, igény szerint hasznosítható funkciókat is bemutatja. További részletekért tekintse meg az API-referenciát:
* `createdDateTime` és `lastUpdatedDateTime` hozzáadva, hogy nyomon tudja követni a feldolgozás időtartamát. További részletekért tekintse meg a dokumentációt. 
* `version` az API-nak az eredmények létrehozásához használt verzióját mutatja be
* A rendszer hozzáad egy szót `confidence` . Ez az érték úgy van kalibrálva, hogy a 0,95 érték azt jelenti, hogy a rendszer 95%-os eséllyel az elismerés helyes. A megbízhatósági pontszám használatával kiválaszthatja, hogy melyik szöveget szeretné elküldeni az emberi felülvizsgálatba. 
* `angle` a szöveg általános tájolása a megegyező irányban, fokban mérve (-180, 180].
* `width` és `"height"` adja meg a dokumentum dimenzióit, és `"unit"` Megadja a méretek (képpont vagy hüvelyk) egységét a dokumentum típusától függően.
* `page` a többoldalas dokumentumok támogatottak
* `language` a dokumentum szövegbeviteli nyelve (a választható _nyelvi_ paraméterből)


A 2. X verzióban a kimeneti formátum a következő: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
```
    
A v3. x-ben a beállítás módosult:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="container-only"></a>Csak tároló

### `Synchronous Read`

|Olvasási 2,0 |3. x olvasása  |
|----------|-----------|
|https://{Endpoint}/vízió/**v 2.0/olvasás/mag/elemzés**     |https://{Endpoint}/vízió/<**Version karakterlánc**>/Read/syncanalyze [? Language]|

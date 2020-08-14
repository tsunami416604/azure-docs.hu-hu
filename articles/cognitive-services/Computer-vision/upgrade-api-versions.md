---
title: Frissítés a Computer Vision API v 3.0-ra
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
ms.openlocfilehash: 6e695fcfacac19ca82273d84d049bdb2afe14b54
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214194"
---
# <a name="upgrade-to-computer-vision-v30-read-api-from-v20v21"></a>Frissítés Computer Vision v 3.0 olvasási API-ra 2.0/v 2.1

Ez az útmutató bemutatja, hogyan frissítheti meglévő Computer Vision v 2.0 vagy v 2.1 REST API kódját a v 3.0 olvasási műveletekre. 

## <a name="upgrade-batch-read-file-to-read"></a>Frissítés `Batch Read File` erre `Read`


1. Módosítsa a 2. x API-elérési útját a `Batch Read File` következő módon:


    |Olvasás 2. x |Olvasási 3,0  |
    |----------|-----------|
    |https://{Endpoint}/vízió/**v 2.0/olvasás/mag/asyncBatchAnalyze**     |https://{Endpoint}/vízió/**v 3.0/olvasás/elemzés**[? Language]|
    
    Új opcionális _nyelvi_ paraméter érhető el. Ha nem ismeri a dokumentum nyelvét, vagy többnyelvű is lehet, ne adja meg azt. 

2. Módosítsa a 2. x verzió API-elérési útját a `Get Read Results` következő módon:

    |Olvasás 2. x |Olvasási 3,0  |
    |----------|-----------|
    |https://{Endpoint}/látási/**v 2.0**/Read/**Operations**/{operationId}     |https://{Endpoint}/vízió/**v 3.0**/Read/**analyzeResults**/{operationId}|

3. Módosítsa a JSON-eredmények ellenőrzéséhez használt kódot `Get Read Operation Result` . A sikeres hívás után a `Get Read Operation Result` rendszer visszaadja a JSON-törzs állapot karakterlánc mezőjét. A v 2.0 következő értékei úgy változtak, hogy jobban illeszkedjenek a többi kognitív szolgáltatás REST API-khoz. 
 
    |Olvasás 2. x |Olvasási 3,0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|
    
4. Módosítsa a kódot a végső felismerési eredmény JSON-fájljának értelmezéséhez `Get Read Operation Result` . 

    Vegye figyelembe a következő módosításokat a JSON-ben:
    
    - A v2. x verzióban az `"Get Read Operation Result"` OCR-felismerési JSON-t fogja visszaadni, ha az állapota `"Succeeded"` . A v 3.0-ban ez a mező a következő: `"succeeded"` .
    - Az oldal-tömb gyökerének beszerzéséhez módosítsa a JSON-hierarchiát a verzióra `"recognitionResults"` `"analyzeResult"` / `"readResults"` . Az egyoldalas sor és a JSON-hierarchia változatlan marad, ezért nincs szükség kód módosítására.
    -   A rendszer átnevezte az oldal szögét, `"clockwiseOrientation"` `"angle"` és a tartomány 0-360 fok értékről-180 értékről 180 fok értékűre módosult. A kódoktól függően előfordulhat, hogy nem kell módosítania a módosításokat, mivel a legtöbb matematikai funkció a tartomány bármelyikét képes kezelni.
    -   A v 3.0 API emellett a következő tökéletesítéseket is bevezeti:- `"createdDateTime"` és `"lastUpdatedDateTime"` hozzáadásával, így nyomon követheti a feldolgozás időtartamát. További részletekért tekintse meg a dokumentációt. 
        - `"version"` az API-nak az eredmények létrehozásához használt verzióját mutatja be
        - A rendszer hozzáad egy szót `"confidence"` . Ez az érték úgy van kalibrálva, hogy a 0,95 érték azt jelenti, hogy a rendszer 95%-os eséllyel az elismerés helyes. A megbízhatósági pontszám használatával kiválaszthatja, hogy melyik szöveget szeretné elküldeni az emberi felülvizsgálatba. 
    
    
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

## <a name="upgrade-from-recognize-text-to-read"></a>Frissítés innen `Recognize Text``Read`
`Recognize Text` az *előnézeti* művelet, amely a *Computer Vision API összes verziójában elavult*. Át kell térnie `Recognize Text` a `Read` (v 3.0) vagy `Batch Read File` (v 2.0, v 2.1) verzióra. a v 3.0- `Read` s verziója újabb és jobb modelleket tartalmaz a szöveges felismeréshez és a további funkciókhoz, ezért ajánlott. Frissítés a verzióról a következőre `Recognize Text` `Read` :

1. Módosítsa a v2. x API-elérési útját a `Recognize Text` következő módon:


    |Szövegfelismerés 2. x |Olvasási 3,0  |
    |----------|-----------|
    |https://{Endpoint}/vízió/**v 2.0/recognizeText [? Mode]**|https://{Endpoint}/vízió/**v 3.0/olvasás/elemzés**[? Language]|
    
    A (z) nem támogatja a _Mode_ paramétert `Read` . A kézírásos és a nyomtatott szöveg is automatikusan támogatott lesz.
    
    A 3.0-s verzióban egy új opcionális _nyelvi_ paraméter érhető el. Ha nem ismeri a dokumentum nyelvét, vagy többnyelvű is lehet, ne adja meg azt. 

2. Módosítsa a v2. x API-elérési útját a `Get Recognize Text Operation Result` következő módon:

    |Szövegfelismerés 2. x |Olvasási 3,0  |
    |----------|-----------|
    |https://{Endpoint}/vízió/**v 2.0/textOperations/**{operationId}|https://{Endpoint}/vízió/**v 3.0/Read/analyzeResults**/{operationId}|

3. Módosítsa a JSON-eredmények ellenőrzéséhez használt kódot `Get Recognize Text Operation Result` . A sikeres hívás után a `Get Read Operation Result` rendszer visszaadja a JSON-törzs állapot karakterlánc mezőjét. 
 
    |Szövegfelismerés 2. x |Olvasási 3,0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|


4. Módosítsa a kódot úgy, hogy az a végső felismerési eredmény JSON-fájlját a `Get Recognize Text Operation Result` támogatásra értelmezze `Get Read Operation Result` .

    Vegye figyelembe a következő módosításokat a JSON-ben:    

    - A v2. x verzióban az `"Get Read Operation Result"` OCR-felismerési JSON-t fogja visszaadni, ha az állapota `"Succeeded"` . A v 3.0-ban ez a mező a következő: `"succeeded"` .
    - Az oldal-tömb gyökerének beszerzéséhez módosítsa a JSON-hierarchiát a verzióra `"recognitionResult"` `"analyzeResult"` / `"readResults"` . Az egyoldalas sor és a JSON-hierarchia változatlan marad, ezért nincs szükség kód módosítására.
    -   A v 3.0 API emellett a következő, igény szerint hasznosítható funkciókat is bemutatja. További részleteket az API-referenciában talál: – `"createdDateTime"` és a `"lastUpdatedDateTime"` rendszer hozzáadja a feldolgozás időtartamának nyomon követéséhez. További részletekért tekintse meg a dokumentációt. 
        - `"version"` az API-nak az eredmények létrehozásához használt verzióját mutatja be
        - A rendszer hozzáad egy szót `"confidence"` . Ez az érték úgy van kalibrálva, hogy a 0,95 érték azt jelenti, hogy a rendszer 95%-os eséllyel az elismerés helyes. A megbízhatósági pontszám használatával kiválaszthatja, hogy melyik szöveget szeretné elküldeni az emberi felülvizsgálatba. 
        - `"angle"` a szöveg általános tájolása a megegyező irányban, fokban mérve (-180, 180].
        -  `"width"` és `"height"` adja meg a dokumentum dimenzióit, és `"unit"` Megadja a méretek (képpont vagy hüvelyk) egységét a dokumentum típusától függően.
        - `"page"` a többoldalas dokumentumok támogatottak
        - `"language"` a dokumentum szövegbeviteli nyelve (a választható _nyelvi_ paraméterből)


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
    
## <a name="all-other-operations"></a>Minden egyéb művelet

A v2 nem vált ki más megszakítást. Az X és a v 3.0 Computer Vision API. Egyszerűen módosíthatja az API elérési útját a `v2.0` helyére `v3.0` .

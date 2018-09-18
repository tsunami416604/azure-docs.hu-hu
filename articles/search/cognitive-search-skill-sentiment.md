---
title: Vélemények cognitive search szakértelem (Azure Search) |} A Microsoft Docs
description: Azure Search-felderítési bővítést folyamatban szövegből bontsa ki a róluk szóló véleményeket.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 1e4028c3a810de41efe217e6dd4347fc3bc6bf16
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730884"
---
#   <a name="sentiment-cognitive-skill"></a>Vélemények cognitive szakértelem

A **vélemények** szakértelem kiértékeli a strukturálatlan szöveg egy pozitív negatív egészében mentén, és az egyes rekordok, 0 és 1 közötti numerikus pontszámot visszaadása. Az 1-hez közeli pontszámok pozitív véleményt jeleznek, míg a 0-hoz közeliek negatívat.

> [!NOTE]
> A kognitív keresés nyilvános előzetes verzióban érhető el. Képességcsoport végrehajtási, és a lemezkép kinyerése és a normalizálási jelenleg rendelkezésre állnak az ingyenes. Később az ezen funkciók díjszabásáról jelentjük be. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>A Data korlátai
A maximális mérete egy rekord kell által mért 5000 karakternél `String.Length`. Ha szeretné feloszthatja az adatokat, mielőtt elküldené a róluk szóló véleményeket elemző, használja a [szöveg felosztása szakértelem](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Ismeretek paraméterek

A paraméterei a kis-és nagybetűket.

| Paraméter neve |                      |
|----------------|----------------------|
| defaultLanguageCode | (nem kötelező) A alkalmazni a dokumentumokat, explicit módon nem ad meg nyelvi nyelvkód. <br/> Lásd: [támogatott nyelvek teljes listáját](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Ismeretek bemenetek 

| Bemeneti név | Leírás |
|--------------------|-------------|
| szöveg | Elemezni kívánt szöveget.|
| languageCode  |  (Nem kötelező) A nyelv, a rekordok jelző karakterlánc. Ha ez a paraméter nincs megadva, alapértelmezett értéke "en". <br/>Lásd: [támogatott nyelvek teljes listáját](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Ismeretek kimenetek

| Kimeneti név | Leírás |
|--------------------|-------------|
| pontszám | 0 és 1 jelöli az elemzett szöveg képviselő közötti értéket. Értékek 0 negatívat, 0,5 közel van a semleges vélemények, pedig 1-hez közeli értékek pozitív véleményt.|


##  <a name="sample-definition"></a>Minta-definíció

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
        }
    ]
}
```

##  <a name="sample-input"></a>Minta beviteli

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
                "languageCode": "en"
            }
        }
    ]
}
```


##  <a name="sample-output"></a>Példa kimenet

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>Megjegyzések
Ha üres, a véleménypontszám nem azokat a rekordokat adja vissza.

## <a name="error-cases"></a>Hibák esetén
A nyelv nem támogatott, ha hiba történik, és nincs értelmezhetőségi pontszámot ad vissza.

## <a name="see-also"></a>Lásd még

+ [Előre megadott képesség](cognitive-search-predefined-skills.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
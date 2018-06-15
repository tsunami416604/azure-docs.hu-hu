---
title: Véleményeket kognitív keresési szakértelem (Azure Search) |} Microsoft Docs
description: A szöveg egy Azure Search dúsító folyamat bontsa ki a céggel kapcsolatos véleményeket.
services: search
manager: pablocas
author: luiscabrer
documentationcenter: ''
ms.assetid: ''
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 1ddbba5b881cd05a997cd24a9396d5b722376e6f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790999"
---
#   <a name="sentiment-cognitive-skill"></a>Véleményeket kognitív szakértelem

A **véleményeket** szakértelem strukturálatlan szöveg egy pozitív-negatív alakíthatnak ki olyan mellett, és az egyes rekordokhoz kiértékeli, adja vissza egy numerikus pontszám 0 és 1 között. Az 1-hez közeli pontszámok pozitív véleményt jeleznek, míg a 0-hoz közeliek negatívat.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Adatok korlátok
Egy olyan rekordot maximális mérete alapján mért 5000 karakter lehet `String.Length`. Ha szeretné feloszthatja az adatokat, mielőtt elküldené a céggel kapcsolatos véleményeket analyzer, használja a [szöveges szakértelem](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Szakértelem paraméterek

A paraméterei a kis-és nagybetűket.

| Paraméter neve |                      |
|----------------|----------------------|
| defaultLanguageCode | (választható) Az explicit módon nem ad meg nyelvi alkalmazza nyelvi kódot. <br/> Lásd: [támogatott nyelvek teljes listája](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Szakértelem bemenetek 

| Bemeneti név | Leírás |
|--------------------|-------------|
| Szöveg | A szöveg elemzése.|
| languageCode  |  (Választható) Egy karakterlánc, amely a nyelvi bejegyzések. Ha ez a paraméter nincs megadva, akkor az alapértelmezett érték: "hu". <br/>Lásd: [támogatott nyelvek teljes listáját](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Szakértelem kimenetek

| Kimeneti neve | Leírás |
|--------------------|-------------|
| Pontszám | 0 és 1 a céggel kapcsolatos véleményeket elemzett szöveg jelölő közötti értéket. Megközelíti a 0 érték negatív véleményeket, 0,5 közel van a semleges véleményeket, pedig 1 hamarosan értékek pozitív véleményeket.|


##  <a name="sample-definition"></a>A minta meghatározása

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

##  <a name="sample-input"></a>A minta bemenet

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
Ha üres, a céggel kapcsolatos véleményeket pontszám nem jelennek meg azokat a rekordokat.

## <a name="error-cases"></a>Hibák esetén
A nyelv nem támogatott, ha hiba történik, és nem véleményeket pontszám adja vissza.

## <a name="see-also"></a>Lásd még

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
+ [Egy skillset definiálása](cognitive-search-defining-skillset.md)
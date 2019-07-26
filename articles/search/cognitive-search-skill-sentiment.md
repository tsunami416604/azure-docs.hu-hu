---
title: Érzelmek kognitív keresési képességei – Azure Search
description: Egy Azure Search alkoholtartalom-növelési folyamat szövege alapján kinyerheti a pozitív negatív érzelmi pontszámot.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f950bea4ea32ecc95b1721c6930903c3afaee848
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347658"
---
#   <a name="sentiment-cognitive-skill"></a>Érzelmek kognitív szaktudása

A **hangulati** készség kiértékeli a strukturálatlan szöveget a pozitív negatív folytonosság mellett, és minden rekord esetében 0 és 1 közötti numerikus pontszámot ad vissza. Az 1-hez közeli pontszámok pozitív véleményt jeleznek, míg a 0-hoz közeliek negatívat. Ez a képesség a Cognitive Services [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) által biztosított gépi tanulási modelleket használja.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást](cognitive-search-attach-cognitive-services.md)kell csatolnia. Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedési szakaszának részeként Azure Search. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását a [Azure Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Adatkorlátok
A rekordok maximális méretének 5000 karakternek kell lennie, a [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)következőképpen mérve:. Ha meg kell szakítania az adatait, mielőtt elküldené az érzelmeket elemző eszköznek, használja a [szöveg felosztása készséget](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterek megkülönböztetik a kis-és nagybetűket.

| Paraméter neve |                      |
|----------------|----------------------|
| defaultLanguageCode | választható A nyelvet explicit módon nem megadó dokumentumokra alkalmazandó nyelvi kód. <br/> [A támogatott nyelvek teljes listája](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Szaktudás bemenetei 

| Bemeneti név | Leírás |
|--------------------|-------------|
| text | Az elemezni kívánt szöveg.|
| languageCode  |  Választható A rekordok nyelvét jelző sztring. Ha a paraméter nincs megadva, az alapértelmezett érték az "en". <br/>[A támogatott nyelvek teljes listáját](../cognitive-services/text-analytics/text-analytics-supported-languages.md)itt tekintheti meg.|

## <a name="skill-outputs"></a>Szaktudás kimenetei

| Kimenet neve | Leírás |
|--------------------|-------------|
| pontszám | 0 és 1 közötti érték, amely az elemzett szöveg hangulatát jelöli. A 0 értéknél közelebbi értékek negatív érzelmekkel rendelkeznek, és a 0,5-es számú érték semleges, az 1. pedig pedig pozitív hangulatú.|


##  <a name="sample-definition"></a>Minta definíciója

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

##  <a name="sample-input"></a>Minta bemenet

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
Ha üres, a rendszer nem adja vissza az adott rekordokhoz tartozó hangulati pontszámot.

## <a name="error-cases"></a>Hibák esetei
Ha a nyelv nem támogatott, a rendszer hibát generál, és nem ad vissza értéket.

## <a name="see-also"></a>Lásd még

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)

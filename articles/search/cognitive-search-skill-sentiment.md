---
title: Érzelmek kognitív szaktudása
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search AI-dúsítási folyamatában lévő szövegből kinyerheti a pozitív negatív érzelmi pontszámot.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3aab703b9c5ffcb5f3280060417ce32fcec2fc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791915"
---
# <a name="sentiment-cognitive-skill"></a>Érzelmek kognitív szaktudása

A **hangulati** készség kiértékeli a strukturálatlan szöveget a pozitív negatív folytonosság mellett, és minden rekord esetében 0 és 1 közötti numerikus pontszámot ad vissza. Az 1-hez közeli pontszámok pozitív véleményt jeleznek, míg a 0-hoz közeliek negatívat. Ez a képesség a Cognitive Services [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) által biztosított gépi tanulási modelleket használja.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást kell csatolnia](cognitive-search-attach-cognitive-services.md). Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedésének részeként az Azure Cognitive Searchban. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását az [Azure Cognitive Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.


## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. SentimentSkill

## <a name="data-limits"></a>Adatkorlátok
A rekordok maximális méretének 5000 karakternek kell lennie [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)alapján mérve. Ha meg kell szakítania az adatait, mielőtt elküldené az érzelmeket elemző eszköznek, használja a [szöveg felosztása készséget](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterek megkülönböztetik a kis-és nagybetűket.

| Paraméter neve |                      |
|----------------|----------------------|
| defaultLanguageCode | választható A nyelvet explicit módon nem megadó dokumentumokra alkalmazandó nyelvi kód. <br/> [A támogatott nyelvek teljes listája](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Szaktudás bemenetei 

| Bemeneti név | Leírás |
|--------------------|-------------|
| szöveg | Az elemezni kívánt szöveg.|
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

## <a name="see-also"></a>Lásd még:

+ [Beépített szaktudás](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)

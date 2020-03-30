---
title: Hangulat kognitív készség
titleSuffix: Azure Cognitive Search
description: Pozitív-negatív hangulatpontszám kinyerése az Azure Cognitive Search AI-dúsítási folyamat szövegéből.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3aab703b9c5ffcb5f3280060417ce32fcec2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791915"
---
# <a name="sentiment-cognitive-skill"></a>Hangulat kognitív készség

A **Hangulat** szakértelem kiértékeli a strukturálatlan szöveget egy pozitív-negatív kontinuum mentén, és minden rekordhoz 0 és 1 közötti numerikus pontszámot ad vissza. Az 1-hez közeli pontszámok pozitív véleményt, a 0-hoz közeli pontszámok pedig negatív hangulatot jeleznek. Ez a szakértelem a Cognitive Services [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) által biztosított gépi tanulási modelleket használja.

> [!NOTE]
> A hogy a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti a hatókört, [egy számlázható Cognitive Services-erőforrást kell csatolnia.](cognitive-search-attach-cognitive-services.md) A díjak akkor keletkeznek, amikor API-kat hívnak a Cognitive Servicesben, és az Azure Cognitive Search dokumentumfeltörési szakaszának részeként képkinyerést végeznek. A dokumentumokból történő szövegkinyerésért nem kell díjat fizetni.
>
> A beépített képességek végrehajtása a meglévő [Cognitive Services díja int.](https://azure.microsoft.com/pricing/details/cognitive-services/) A képkinyerésdíj szabása az [Azure Cognitive Search díjszabási lapján található.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Adatkorlátok
A rekord maximális méretének 5000 karakternek [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)kell lennie a szerint mérve. Ha meg kell szakítania az adatokat, mielőtt elküldené azokat a véleményelemzőnek, használja a [Szövegfelosztás (Szövegfelosztás) készséget.](cognitive-search-skill-textsplit.md)


## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Paraméter neve |                      |
|----------------|----------------------|
| defaultLanguageCode | (nem kötelező) A nem kifejezetten nyelvet megnem meghatározó dokumentumokra alkalmazandó nyelvkód. <br/> Lásd: [A támogatott nyelvek teljes listája](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Szakértelem bemenetei 

| Bemenet neve | Leírás |
|--------------------|-------------|
| szöveg | Az elemzendő szöveg.|
| languageCode  |  (Nem kötelező) A rekordok nyelvét jelző karakterlánc. Ha ez a paraméter nincs megadva, az alapértelmezett érték "en". <br/>Lásd: [A támogatott nyelvek teljes listája.](../cognitive-services/text-analytics/text-analytics-supported-languages.md)|

## <a name="skill-outputs"></a>Szakértelem kimenetei

| Kimenet neve | Leírás |
|--------------------|-------------|
| pontszám | 0 és 1 közötti érték, amely az elemzett szöveg hangulatát tükrözi. A 0-hoz közeli értékek negatív hangulattal, a 0,5-höz közeli semleges hangulattal rendelkeznek, az 1-hez közeli értékek pedig pozitív hangulattal rendelkeznek.|


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

##  <a name="sample-input"></a>Mintabevitel

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
Ha üres, a rekordokhoz nem ad vissza véleménypontszám.

## <a name="error-cases"></a>Hibaesetek
Ha egy nyelv nem támogatott, hiba jön létre, és nem ad vissza hangulatpontszámot.

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)

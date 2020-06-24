---
title: Nyelvfelismerés – kognitív képességek
titleSuffix: Azure Cognitive Search
description: Kiértékeli a strukturálatlan szöveget, és minden egyes rekord esetében egy olyan nyelvi azonosítót ad vissza, amely az Azure Cognitive Search AI-dúsítási folyamatában lévő elemzés erősségét jelzi.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: bac2f86f4134cc8d22e9f388b46bc76ab2d0e5ff
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85080804"
---
#   <a name="language-detection-cognitive-skill"></a>Nyelvfelismerés – kognitív képességek

A **nyelvfelismerési** képesség észleli a bemeneti szöveg nyelvét, és egyetlen nyelvi kódot jelent a kérelemben elküldött összes dokumentumhoz. A nyelvi kód párosítva van egy pontszámmal, amely az elemzés erősségét jelzi. Ez a képesség a Cognitive Services [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) által biztosított gépi tanulási modelleket használja.

Ez a képesség különösen akkor hasznos, ha meg kell adnia a szöveg nyelvét más képességeknek (például a [Hangulatelemzés skill](cognitive-search-skill-sentiment.md) vagy a [text Split skill](cognitive-search-skill-textsplit.md)).

A nyelvfelismerés a Bing természetes nyelvi feldolgozó kódtárait használja, ami meghaladja a [támogatott nyelvek és régiók](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) számát Text Analytics. A nyelvek pontos listája nincs közzétéve, de az összes széles körben beszélt nyelvet, valamint a változatokat, a dialektusokat és néhány regionális és kulturális nyelvet tartalmaz. Ha a tartalom ritkábban használt nyelven van kifejezve, [kipróbálhatja a NYELVFELISMERÉS API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) -t, és megnézheti, hogy visszaadja-e a kódot. A nem észlelhető nyelvek válasza a következő: `unknown` .

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást kell csatolnia](cognitive-search-attach-cognitive-services.md). Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedésének részeként az Azure Cognitive Searchban. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását az [Azure Cognitive Search díjszabási oldalán](https://azure.microsoft.com/pricing/details/search/)találja.


## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. LanguageDetectionSkill

## <a name="data-limits"></a>Adatkorlátok
A rekordok maximális méretének 50 000 karakternek kell lennie, a következőképpen mérve: [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length) . Ha meg kell szakítania az adatokat, mielőtt elküldené azt a nyelvfelismerés szaktudásának, használhatja a [szöveg felosztása készséget](cognitive-search-skill-textsplit.md)is.

## <a name="skill-inputs"></a>Szaktudás bemenetei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Bevitelek     | Leírás |
|--------------------|-------------|
| `text` | Az elemezni kívánt szöveg.|

## <a name="skill-outputs"></a>Szaktudás kimenetei

| Kimenet neve    | Leírás |
|--------------------|-------------|
| `languageCode` | Az ISO 6391 nyelvi kódja az azonosított nyelvhez. Például: "en". |
| `languageName` | A nyelv neve. Például: "English". |
| `score` | 0 és 1 közötti érték. A nyelv helyes azonosításának valószínűsége. A pontszám értéke 1-nél kisebb lehet, ha a mondat vegyes nyelvekkel rendelkezik.  |

##  <a name="sample-definition"></a>Minta definíciója

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
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
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      }
    ]
```


##  <a name="sample-output"></a>Példa kimenet

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>Hibák esetei
Ha a szöveg nem támogatott nyelven van kifejezve, a rendszer hibát generál, és nem ad vissza nyelvi azonosítót.

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)

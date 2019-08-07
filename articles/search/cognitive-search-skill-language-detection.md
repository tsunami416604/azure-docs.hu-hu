---
title: Nyelvfelismerés kognitív keresési képesség – Azure Search
description: Kiértékeli a strukturálatlan szöveget, és minden rekord esetében egy olyan nyelvi azonosítót ad vissza, amely egy Azure Search alkoholtartalom-növelési folyamat elemzésének erősségét jelzi.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: bf683ad54fb75ad666a4635e942d8d0a51f19caf
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841006"
---
#   <a name="language-detection-cognitive-skill"></a>Nyelvfelismerés – kognitív képességek

A **nyelvfelismerési** képesség észleli a bemeneti szöveg nyelvét, és egyetlen nyelvi kódot jelent a kérelemben elküldött összes dokumentumhoz. A nyelvi kód párosítva van egy pontszámmal, amely az elemzés erősségét jelzi. Ez a képesség a Cognitive Services [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) által biztosított gépi tanulási modelleket használja.

Ez a képesség különösen akkor hasznos, ha meg kell adnia a szöveg nyelvét más képességeknek (például a [Hangulatelemzés skill](cognitive-search-skill-sentiment.md) vagy a [text Split skill](cognitive-search-skill-textsplit.md)).

A nyelvfelismerés a Bing természetes nyelvi feldolgozó kódtárait használja, ami meghaladja a [támogatott nyelvek és régiók](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) számát Text Analytics. A nyelvek pontos listája nincs közzétéve, de az összes széles körben beszélt nyelvet, valamint a változatokat, a dialektusokat és néhány regionális és kulturális nyelvet tartalmaz. Ha a tartalom ritkábban használt nyelven van kifejezve, kipróbálhatja [a NYELVFELISMERÉS API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) -t, és megnézheti, hogy visszaadja-e a kódot. A nem észlelhető `unknown`nyelvek válasza a következő:.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást](cognitive-search-attach-cognitive-services.md)kell csatolnia. Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedési szakaszának részeként Azure Search. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását a [Azure Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.


## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. LanguageDetectionSkill

## <a name="data-limits"></a>Adatkorlátok
A rekordok maximális méretének 50 000 karakternek kell lennie, a [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)következőképpen mérve:. Ha meg kell szakítania az adatait, mielőtt elküldené az a hangulat-elemzőnek, használhatja a [szöveg felosztása](cognitive-search-skill-textsplit.md)lehetőséget.

## <a name="skill-inputs"></a>Szaktudás bemenetei

A paraméterek megkülönböztetik a kis-és nagybetűket.

| Bemenetek     | Leírás |
|--------------------|-------------|
| text | Az elemezni kívánt szöveg.|

## <a name="skill-outputs"></a>Szaktudás kimenetei

| Kimenet neve    | Leírás |
|--------------------|-------------|
| languageCode | Az ISO 6391 nyelvi kódja az azonosított nyelvhez. Például: "en". |
| languageName | A nyelv neve. Például: "English". |
| pontszám | 0 és 1 közötti érték. A nyelv helyes azonosításának valószínűsége. A pontszám értéke 1-nél kisebb lehet, ha a mondat vegyes nyelvekkel rendelkezik.  |

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

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)

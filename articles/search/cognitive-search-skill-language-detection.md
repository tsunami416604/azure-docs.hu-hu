---
title: Nyelv észlelése cognitive search szakértelem – Azure Search
description: Kiértékeli a strukturálatlan szöveg, és az egyes rekordokhoz, az Azure Search-felderítési bővítést folyamatban elemzés erőssége jelző pontszámot nyelvi azonosítónak visszaad.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 9ad74a6e0199688899b803d8cda7b98606308e13
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54389867"
---
#   <a name="language-detection-cognitive-skill"></a>Nyelv észlelése cognitive szakértelem

Legfeljebb 120 nyelv esetén a **nyelvfelismerés** szakértelem észleli a bemeneti szöveg nyelvét, és a kérés küldése minden dokumentum egyetlen nyelvkód jelentéseket. A nyelvi kód az elemzés erőssége jelző pontszámot párban áll. Ezen a képzettségi használja a gépi tanulási modellek által biztosított [Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) a Cognitive Services.

Ez a funkció akkor különösen hasznos, ha a meg kell adnia a szöveg nyelvét, egyéb képességek bemenetként (például a [Hangulatelemzés szakértelem](cognitive-search-skill-sentiment.md) vagy [szöveg felosztása szakértelem](cognitive-search-skill-textsplit.md)).

> [!NOTE]
> 2018. December 21., kezdési is [Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md) és a egy Azure Search-képességek alkalmazási lehetőségét. Ez lehetővé teszi indexmezők végrehajtási díjszabási elindításához. Ezen a napon azt is már díjszabási kép kinyerése a dokumentumfeltörést fázis részeként. A dokumentumok szövegkinyerés továbbra is ingyenesen kínáljuk.
>
> Beépített képességek végrehajtási, a Cognitive Services számítunk fel díjat, számoljuk fel a meglévő [használatalapú – mint-akkor lépjen ár](https://azure.microsoft.com/pricing/details/cognitive-services/) . Kép kinyerési díjszabás, egy Azure Search számítunk fel díjat, jelenleg előzetes verzió díjszabása leírt számlázása a [díjszabását ismertető oldalt az Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Adatkorlátok
Egy rekord maximális mérete 50 000 karakter által mért kell lennie `String.Length`. Ha az adatok tördelésével, mielőtt elküldené a róluk szóló véleményeket analyzer van szüksége, használhat a [szöveg felosztása szakértelem](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Ismeretek bemenetek

A paraméterei a kis-és nagybetűket.

| Bemenetek     | Leírás |
|--------------------|-------------|
| szöveg | Elemezni kívánt szöveget.|

## <a name="skill-outputs"></a>Ismeretek kimenetek

| Kimeneti név    | Leírás |
|--------------------|-------------|
| languageCode | Az azonosított nyelv ISO 6391 nyelvi kódot. Például "hu". |
| LanguageName | Nyelv neve. Például "angol". |
| pontszám | 0 és 1 közötti értéket. Annak valószínűsége, hogy nyelvi megfelelően legyen azonosítva. A pontszám 1-nél kisebb lehet, ha a mondat van vegyes nyelveket.  |

##  <a name="sample-definition"></a>Minta-definíció

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill ",
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

##  <a name="sample-input"></a>Minta beviteli

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


## <a name="error-cases"></a>Hibák esetén
Szöveg egy nem támogatott nyelven van kifejezve, ha hiba történik, és nincs nyelvi azonosítót adja vissza.

## <a name="see-also"></a>Lásd még

+ [Előre megadott képesség](cognitive-search-predefined-skills.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)

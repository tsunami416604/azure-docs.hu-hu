---
title: Nyelvfelismerési kognitív készség
titleSuffix: Azure Cognitive Search
description: Kiértékeli a strukturálatlan szöveget, és minden rekordhoz egy nyelvi azonosítót ad vissza egy pontszámmal, amely jelzi az elemzés erősségét az Azure Cognitive Search ai-dúsítási folyamatában.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8439788c63ec1b9feaea148ab52aba498791dc12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045016"
---
#   <a name="language-detection-cognitive-skill"></a>Nyelvfelismerési kognitív készség

A **Nyelvfelismerés** idoszak a bemeneti szöveg nyelvét észleli, és a kérelemre elküldött minden dokumentumhoz egyetlen nyelvi kódot jelent. A nyelvkód párosítva van egy pontszámmal, amely jelzi az elemzés erősségét. Ez a szakértelem a Cognitive Services [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) által biztosított gépi tanulási modelleket használja.

Ez a képesség különösen akkor hasznos, ha a szöveg nyelvét más készségek (például a [Hangulatelemzés idoszaka](cognitive-search-skill-sentiment.md) vagy [a Szövegfelosztás készség)](cognitive-search-skill-textsplit.md)bemeneteként kell megadnia.

A nyelvfelismerés a Bing természetes nyelvi feldolgozási kódtárait használja, amely meghaladja a Text Analytics számára felsorolt [támogatott nyelvek és régiók](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) számát. A pontos nyelvlista nem publikált, de tartalmazza az összes széles körben beszélt nyelvet, valamint a változatokat, a nyelvjárásokat, valamint néhány regionális és kulturális nyelvet. Ha a tartalom egy ritkábban használt nyelven van kifejezve, [megpróbálhatja a Language Detection API-t,](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) hogy lássa, visszaad-e egy kódot. A nem észlelhető nyelvekre `unknown`a válasz a.

> [!NOTE]
> A hogy a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti a hatókört, [egy számlázható Cognitive Services-erőforrást kell csatolnia.](cognitive-search-attach-cognitive-services.md) A díjak akkor keletkeznek, amikor API-kat hívnak a Cognitive Servicesben, és az Azure Cognitive Search dokumentumfeltörési szakaszának részeként képkinyerést végeznek. A dokumentumokból történő szövegkinyerésért nem kell díjat fizetni.
>
> A beépített képességek végrehajtása a meglévő [Cognitive Services díja int.](https://azure.microsoft.com/pricing/details/cognitive-services/) A képkinyerésdíj szabása az [Azure Cognitive Search díjszabási lapján található.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Adatkorlátok
A rekord maximális méretének 50 000 karakternek [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)kell lennie a szerint mérve. Ha meg kell szakítania az adatokat, mielőtt elküldi azadatokat a nyelvfelismerési szakértelemre, használhatja a [Szövegfelosztás készséget.](cognitive-search-skill-textsplit.md)

## <a name="skill-inputs"></a>Szakértelem bemenetei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Bemenetek     | Leírás |
|--------------------|-------------|
| szöveg | Az elemzendő szöveg.|

## <a name="skill-outputs"></a>Szakértelem kimenetei

| Kimenet neve    | Leírás |
|--------------------|-------------|
| languageCode | Az ISO 6391 nyelvkódja az azonosított nyelvhez. Például "en". |
| nyelvneve | A nyelv neve. Például "Angol". |
| pontszám | 0 és 1 közötti érték. Annak valószínűsége, hogy a nyelv helyesen van azonosítva. A pontszám 1-nél alacsonyabb is lehet, ha a mondat vegyes nyelvű.  |

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

##  <a name="sample-input"></a>Mintabevitel

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


## <a name="error-cases"></a>Hibaesetek
Ha a szöveg nem támogatott nyelven van kifejezve, a rendszer hibát hoz létre, és nem ad vissza nyelvi azonosítót.

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)

---
title: Kulcsszókeresés kognitív képesség
titleSuffix: Azure Cognitive Search
description: Kiértékeli a strukturálatlan szöveget, és minden rekord esetében az Azure Cognitive Search AI-dúsítási folyamatában található fő kifejezések listáját adja vissza.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ccdd25d82af2b4893260af18dac818816d9e4579
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791981"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Kulcsszókeresés kognitív képesség

A **kulcsszókeresési** készség kiértékeli a strukturálatlan szöveget, és minden rekord esetében a legfontosabb kifejezések listáját adja vissza. Ez a képesség a Cognitive Services [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) által biztosított gépi tanulási modelleket használja.

Ez a funkció akkor hasznos, ha gyorsan meg kell határoznia a rekordban lévő fő beszélő pontokat. Például a bemeneti szöveg "az élelmiszer finom volt, és csodálatos volt a személyzet", a szolgáltatás a "Food" és a "csodálatos személyzet" értéket adja vissza.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást kell csatolnia](cognitive-search-attach-cognitive-services.md). Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedésének részeként az Azure Cognitive Searchban. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását az [Azure Cognitive Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.


## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Adatkorlátok
A rekordok maximális méretének 50 000 karakternek kell lennie [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)alapján mérve. Ha meg kell szakítania az adatokat, mielőtt elküldené a kivonó kifejezést, érdemes lehet a [szöveg felosztása képességet](cognitive-search-skill-textsplit.md)használni.

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterek megkülönböztetik a kis-és nagybetűket.

| Bemenetek                | Leírás |
|---------------------|-------------|
| defaultLanguageCode | Választható A nyelvet explicit módon nem megadó dokumentumokra alkalmazandó nyelvi kód.  Ha nincs megadva az alapértelmezett nyelvkód, a rendszer az angol (en) nyelvet használja alapértelmezett nyelvi kódnak. <br/> [A támogatott nyelvek teljes listáját](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)itt tekintheti meg. |
| maxKeyPhraseCount   | Választható A létrehozni kívánt legfontosabb kifejezések maximális száma. |

## <a name="skill-inputs"></a>Szaktudás bemenetei

| Bemenetek     | Leírás |
|--------------------|-------------|
| szöveg | Az elemezni kívánt szöveg.|
| languageCode  |  A rekordok nyelvét jelző sztring. Ha ez a paraméter nincs megadva, a rendszer az alapértelmezett nyelvkódot fogja használni a rekordok elemzéséhez. <br/>[A támogatott nyelvek teljes listája](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>Minta definíciója

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
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
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
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
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Hibák és figyelmeztetések
Ha nem támogatott nyelvi kódot ad meg, a rendszer hibát generál, és a rendszer nem bontja ki a fő kifejezéseket.
Ha a szöveg üres, a rendszer figyelmeztetést készít.
Ha a szöveg 50 000 karakternél nagyobb, akkor csak az első 50 000 karakter lesz elemezve, és a rendszer figyelmeztetést ad ki.

## <a name="see-also"></a>Lásd még:

+ [Beépített szaktudás](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)

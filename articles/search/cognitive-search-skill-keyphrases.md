---
title: Key Phrase Extraction kognitív készség
titleSuffix: Azure Cognitive Search
description: Kiértékeli a strukturálatlan szöveget, és minden rekordhoz visszaadja az Azure Cognitive Search AI-dúsítási folyamat kulcskifejezéseinek listáját.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ccdd25d82af2b4893260af18dac818816d9e4579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791981"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Key Phrase Extraction kognitív készség

A **kulcskifejezés-kivonási** szakértelem kiértékeli a strukturálatlan szöveget, és minden rekordhoz a legfontosabb kifejezések listáját adja vissza. Ez a szakértelem a Cognitive Services [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) által biztosított gépi tanulási modelleket használja.

Ez a képesség akkor hasznos, ha gyorsan azonosítania kell a fő beszédpontokat a rekordban. Például adott bemeneti szöveg " Az étel finom volt, és csodálatos személyzet volt", a szolgáltatás "ételt" és "csodálatos személyzetet" ad vissza.

> [!NOTE]
> A hogy a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti a hatókört, [egy számlázható Cognitive Services-erőforrást kell csatolnia.](cognitive-search-attach-cognitive-services.md) A díjak akkor keletkeznek, amikor API-kat hívnak a Cognitive Servicesben, és az Azure Cognitive Search dokumentumfeltörési szakaszának részeként képkinyerést végeznek. A dokumentumokból történő szövegkinyerésért nem kell díjat fizetni.
>
> A beépített képességek végrehajtása a meglévő [Cognitive Services díja int.](https://azure.microsoft.com/pricing/details/cognitive-services/) A képkinyerésdíj szabása az [Azure Cognitive Search díjszabási lapján található.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Adatkorlátok
A rekord maximális méretének 50 000 karakternek [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)kell lennie a szerint mérve. Ha meg kell szakítania az adatokat, mielőtt elküldi azokat a kulcskifejezés-kiválasztónak, fontolja meg a [Szövegfelosztás szakértelem](cognitive-search-skill-textsplit.md)használatát.

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Bemenetek                | Leírás |
|---------------------|-------------|
| defaultLanguageCode | (Nem kötelező) A nem kifejezetten nyelvet megnem meghatározó dokumentumokra alkalmazandó nyelvkód.  Ha nincs megadva az alapértelmezett nyelvkód, a program az angol (hu) nyelvet használja alapértelmezett nyelvkódként. <br/> Lásd: [A támogatott nyelvek teljes listája.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) |
| maxKeyPhraseCount (maxKeyPhraseCount)   | (Nem kötelező) A létrehozandó kulcskifejezések maximális száma. |

## <a name="skill-inputs"></a>Szakértelem bemenetei

| Bemenetek     | Leírás |
|--------------------|-------------|
| szöveg | Az elemzendő szöveg.|
| languageCode  |  A rekordok nyelvét jelző karakterlánc. Ha ez a paraméter nincs megadva, a rendszer az alapértelmezett nyelvkódot használja a rekordok elemzéséhez. <br/>Lásd: [A támogatott nyelvek teljes listája](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

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

##  <a name="sample-input"></a>Mintabevitel

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
Ha nem támogatott nyelvi kódot ad meg, a rendszer hibát hoz létre, és a kulcskifejezéseket nem bontja ki.
Ha a szöveg üres, a program figyelmeztetést jelenít meg.
Ha a szöveg 50 000 karakternél nagyobb, csak az első 50 000 karakter telemzi a program, és figyelmeztetést ad ki.

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)

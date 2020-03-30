---
title: Szöveg Fordítás kognitív készség
titleSuffix: Azure Cognitive Search
description: Kiértékeli a szöveget, és minden rekord, visszaadja a megadott célnyelvre lefordított szöveget egy AI-bővítési folyamat az Azure Cognitive Search.Evaluates text and, for each record, returns text translated to the specified target language in an AI drichment pipeline in Azure Cognitive Search.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5089174fcfd5a97128c1f789b818243243a5282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460775"
---
#   <a name="text-translation-cognitive-skill"></a>Szöveg Fordítás kognitív készség

A **Szövegfordítás** szakértelem kiértékeli a szöveget, és minden rekordesetében visszaadja a megadott célnyelvre lefordított szöveget. Ez a szakértelem a Fordító szöveges API-t használja a Cognitive Servicesben elérhető [3.0-s](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) verzióban.

Ez a funkció akkor hasznos, ha arra számít, hogy a dokumentumok nem egy nyelven jelennek meg, ebben az esetben a szöveget egyetlen nyelvre normalizálhatja, mielőtt lefordíthatja a keresést.  A beállítási használati esetekben is hasznos lehet, ahol előfordulhat, hogy ugyanazt a szöveget több nyelven is elérhetővé szeretné tenni.

A [Translator Text API v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) egy nem regionális kognitív szolgáltatás, ami azt jelenti, hogy az adatok nem garantált, hogy ugyanabban a régióban marad, mint az Azure Cognitive Search vagy a kapcsolódó Cognitive Services-erőforrás.

> [!NOTE]
> A hogy a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti a hatókört, [egy számlázható Cognitive Services-erőforrást kell csatolnia.](cognitive-search-attach-cognitive-services.md) A díjak akkor keletkeznek, amikor API-kat hívnak a Cognitive Servicesben, és az Azure Cognitive Search dokumentumfeltörési szakaszának részeként képkinyerést végeznek. A dokumentumokból történő szövegkinyerésért nem kell díjat fizetni.
>
> A beépített képességek végrehajtása a meglévő [Cognitive Services díja int.](https://azure.microsoft.com/pricing/details/cognitive-services/) A képkinyerésdíj szabása az [Azure Cognitive Search díjszabási lapján található.](https://go.microsoft.com/fwlink/?linkid=2042400)

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.TranslationSkill

## <a name="data-limits"></a>Adatkorlátok
A rekord maximális méretének 50 000 karakternek [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)kell lennie a szerint mérve. Ha meg kell szakítania az adatokat, mielőtt elküldi azokat a szöveg fordítási szakértelem, fontolja meg a [Szöveg felosztása szakértelem](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Bemenetek                | Leírás |
|---------------------|-------------|
| defaultToLanguageCode | (Kötelező) Az a nyelvkód, amelybe a dokumentumokat olyan dokumentumokra fordítják, amelyek nem adják meg kifejezetten a nyelvet. <br/> Lásd: [A támogatott nyelvek teljes listája.](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) |
| alapértelmezettFromLanguageCode | (Nem kötelező) A dokumentumok fordítására szolgáló nyelvkód olyan dokumentumok hoz, amelyek nem határozzák meg kifejezetten a származtatott nyelvet.  Ha nincs megadva az alapértelmezettFromLanguageCode, a Fordító szöveg API-ja által biztosított automatikus nyelvfelismerés lesz használva a nyelv meghatározásához. <br/> Lásd: [A támogatott nyelvek teljes listája.](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) |
| javasoltFrom | (Nem kötelező) A dokumentumok fordításához használt nyelvkód, ha sem a fromLanguageCode bemenet, sem az alapértelmezettFromLanguageCode paraméter nincs megadva, és az automatikus nyelvfelismerés sikertelen.  Ha a suggestedFrom nyelv nincs megadva, a (hu) nyelv lesz a javasolthonnan nyelv. <br/> Lásd: [A támogatott nyelvek teljes listája.](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) |

## <a name="skill-inputs"></a>Szakértelem bemenetei

| Bemenet neve     | Leírás |
|--------------------|-------------|
| szöveg | A lefordítandó szöveg.|
| toLanguageCode    | A szöveg által lefordítandó nyelvet jelző karakterlánc. Ha ez a bemenet nincs megadva, a program az alapértelmezettToLanguageCode-ot használja a szöveg fordításához. <br/>Lásd: [A támogatott nyelvek teljes listája](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|
| fromLanguageCode  | A szöveg aktuális nyelvét jelző karakterlánc. Ha ez a paraméter nincs megadva, a program az alapértelmezettFromLanguageCode (vagy automatikus nyelvfelismerés, ha az alapértelmezettFromLanguageCode nincs megadva) lesz használva a szöveg fordításához. <br/>Lásd: [A támogatott nyelvek teljes listája](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>Szakértelem kimenetei

| Kimenet neve    | Leírás |
|--------------------|-------------|
| lefordított szöveg | A lefordítottFromLanguageCode programból a lefordítottToLanguageCode-ra fordított szövegfordítás karakterlánceredménye.|
| translatedToLanguageCode  | A szöveg által lefordított nyelvkódot jelző karakterlánc. Akkor hasznos, ha több nyelvre fordít, és szeretné nyomon követni, hogy melyik szöveg melyik nyelv.|
| translatedFromLanguageCode    | A szöveg által lefordított nyelvkódot jelző karakterlánc. Akkor hasznos, ha az automatikus nyelvfelismerési lehetőséget választotta, mivel ez a kimenet az észlelés eredményét adja.|

##  <a name="sample-definition"></a>Minta definíciója

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
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
          "text": "We hold these truths to be self-evident, that all men are created equal."
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
}
```


##  <a name="sample-output"></a>Példa kimenet

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Hibák és figyelmeztetések
Ha nem támogatott nyelvi kódot ad meg a beérkező vagy a következő nyelvhez, a rendszer hibát generál, és a szöveget nem fordítja le.
Ha a szöveg üres, a program figyelmeztetést jelenít meg.
Ha a szöveg 50 000 karakternél nagyobb, csak az első 50 000 karakter lesz lefordítva, és figyelmeztetést ad ki.

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)

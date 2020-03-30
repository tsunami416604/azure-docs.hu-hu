---
title: PII-észlelési kognitív képességek (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search gazdagítási folyamatában lévő szövegből kinyerheti és elfedheti a személyazonosításra alkalmas adatokat. Ez a szakértelem jelenleg nyilvános előzetes verzióban érhető el.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: f21200bc6f5b25f3330f5bb87c0843caa5a84e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298881"
---
#    <a name="pii-detection-cognitive-skill"></a>PII-észlelési kognitív készség

> [!IMPORTANT] 
> Ez a szakértelem jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verzió funkció szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Jelenleg nincs portál- vagy .NET SDK-támogatás.

A **személyazonosításra alkalmas adatok észlelése** szakértelem kinyeri a személyazonosításra alkalmas adatokat egy bemeneti szöveget, és lehetővé teszi, hogy maszk, hogy a szöveg különböző módokon. Ez a szakértelem a Cognitive Services [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) által biztosított gépi tanulási modelleket használja.

> [!NOTE]
> A hogy a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti a hatókört, [egy számlázható Cognitive Services-erőforrást kell csatolnia.](cognitive-search-attach-cognitive-services.md) A díjak akkor keletkeznek, amikor API-kat hívnak a Cognitive Servicesben, és az Azure Cognitive Search dokumentumfeltörési szakaszának részeként képkinyerést végeznek. A dokumentumokból történő szövegkinyerésért nem kell díjat fizetni.
>
> A beépített képességek végrehajtása a meglévő [Cognitive Services díja int.](https://azure.microsoft.com/pricing/details/cognitive-services/) A képkinyerésdíj szabása az [Azure Cognitive Search díjszabási lapján található.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>Adatkorlátok
A rekord maximális méretének 50 000 karakternek [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)kell lennie a szerint mérve. Ha meg kell szakítania az adatokat, mielőtt elküldené őket a szakértelemnek, fontolja meg a [Szövegfelosztás szakértelem használatát.](cognitive-search-skill-textsplit.md)

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterek ben a kis- és nagybetűket kell figyelembe vevőnek hívni, és mindegyik nem kötelező.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| defaultLanguageCode |    A bemeneti szöveg nyelvkódja. Egyelőre csak `en` támogatott. |
| minimumPontosság | 0,0 és 1,0 közötti érték. Ha a megbízhatósági `piiEntities` pontszám (a kimenetben) alacsonyabb, mint a beállított `minimumPrecision` érték, az entitás nem adja vissza, vagy maszkolt. Az alapértelmezett érték 0.0. |
| maszkolásMód | Olyan paraméter, amely különböző módokon fedi el az észlelt személyazonosításra vonatkozó adatokat a bemeneti szövegben. A következő beállítások támogatottak: <ul><li>`none`(alapértelmezett): Ez azt jelenti, hogy nem `maskedText` lesz maszkolás, és a kimenet nem lesz visszaadva. </li><li> `redact`: Ez a beállítás eltávolítja az észlelt entitásokat a bemeneti szövegből, és nem helyettesíti őket semmivel. Ne feledje, hogy ebben `piiEntities` az esetben a kimenetelt az eredeti szöveghez, nem pedig a maszkolt szöveghez viszonyítva. </li><li> `replace`: Ez a beállítás az észlelt entitásokat `maskingCharacter` a paraméterben megadott karakterre cseréli.  A karakter ismétlődik az észlelt entitás hosszával, így az eltolások helyesen felelnek `maskedText`meg mind a bemeneti szövegnek , mind a kimenetnek .</li></ul> |
| maszkolásKarakter | Az a karakter, amely a szöveg `maskingMode` maszkosítására `replace`szolgál, ha a paraméter beállítása . A következő beállítások `*` támogatottak: (alapértelmezett), `#`, . `X` Ez a paraméter `null` `maskingMode` csak akkor `replace`lehet, ha nincs beállítva . |


## <a name="skill-inputs"></a>Szakértelem bemenetei

| Bemenet neve      | Leírás                   |
|---------------|-------------------------------|
| languageCode    | Választható. Az alapértelmezett szint a `en`.  |
| szöveg          | Az elemző szöveg.          |

## <a name="skill-outputs"></a>Szakértelem kimenetei

| Kimenet neve      | Leírás                   |
|---------------|-------------------------------|
| piiEntities (piiEntities) | Összetett típusok tömbje, amely a következő mezőket tartalmazza: <ul><li>szöveg (A tényleges személyazonosításra kivont személyazonosításra vonatkozó adatok)</li> <li>type</li><li>Altípus</li><li>pontszám (A magasabb érték azt jelenti, hogy nagyobb a valószínűsége, hogy egy valós egység)</li><li>eltolás (a bemeneti szövegbe)</li><li>hossz</li></ul> </br> [A lehetséges típusok és altípusok itt találhatók.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| maszkolt szöveg | Ha `maskingMode` a beállítás nem `none`az , akkor ez a kimenet lesz a kijelölt `maskingMode`szöveg által leírt beviteli szövegen végrehajtott maszkolás karakterlánceredménye .  Ha `maskingMode` a `none`beállítás a , ez a kimenet nem lesz jelen. |

##    <a name="sample-definition"></a>Minta definíciója

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
      }
    ]
  }
```
##    <a name="sample-input"></a>Mintabevitel

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Példa kimenet

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

Vegye figyelembe, hogy a szakértelem kimenetében lévő entitások által visszaadott eltolások közvetlenül a [Text Analytics API-ból](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)származnak, ami azt jelenti, hogy ha az okat az eredeti karakterláncba indexeli, a [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) osztályt kell használnia a .NET-ben a megfelelő tartalom kinyeréséhez.  [További részletek itt találhatók.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-and-warning-cases"></a>Hiba- és figyelmeztetési esetek
Ha a dokumentum nyelvi kódja nem támogatott, a rendszer figyelmeztetést ad vissza, és nem bont ki entitásokat.
Ha a szöveg üres, a program figyelmeztetést jelenít meg.
Ha a szöveg 50 000 karakternél nagyobb, csak az első 50 000 karakter telemzi a program, és figyelmeztetést ad ki.

Ha a szakértelem figyelmeztetést ad vissza, a kimenet `maskedText` üres lehet.  Ez azt jelenti, hogy ha azt várod, hogy a kimenet létezik a későbbi képességek bemenete, nem fog működni a kívánt módon. Ezt tartsa szem előtt a skillset definíciójának megírásakor.

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)

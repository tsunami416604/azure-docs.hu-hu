---
title: Személyes adatok észlelése – kognitív képességek (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: Személyazonosításra alkalmas adatok kinyerése és maszkolása az Azure Cognitive Searchban lévő alkoholtartalom-növelési folyamatban. Ez a képesség jelenleg nyilvános előzetes verzióban érhető el.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 7011d971ddb1888b312173a42ecd4a50f0954395
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849044"
---
#   <a name="pii-detection-cognitive-skill"></a>Személyes adatok észlelése – kognitív képességek

> [!IMPORTANT] 
> Ez a képesség jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Jelenleg nincs portál vagy .NET SDK-támogatás.

A személyes adatok **észlelése** funkció beolvassa a személyazonosításra alkalmas adatokat egy bemeneti szövegből, és lehetővé teszi, hogy az adott szövegtől különböző módokon maszkot adjon. Ez a képesség a Cognitive Services [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) által biztosított gépi tanulási modelleket használja.

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást kell csatolnia](cognitive-search-attach-cognitive-services.md). Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedésének részeként az Azure Cognitive Searchban. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását az [Azure Cognitive Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.


## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. PIIDetectionSkill

## <a name="data-limits"></a>Adatkorlátok
A rekordok maximális méretének 50 000 karakternek kell lennie [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)alapján mérve. Ha meg kell szüntetnie az adatokat, mielőtt elküldené a szakértelmet, érdemes lehet a [szöveg felosztása képességet](cognitive-search-skill-textsplit.md)használni.

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterek megkülönböztetik a kis-és nagybetűket, és az összes nem kötelező.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| defaultLanguageCode | A bemeneti szöveg nyelvi kódja Egyelőre csak `en` támogatott. |
| minimumPrecision | 0,0 és 1,0 közötti érték. Ha a megbízhatósági pontszám (a `piiEntities` kimenetében) alacsonyabb, mint a set `minimumPrecision` érték, az entitás nem lesz visszaadva vagy maszkolt. Az alapértelmezett érték a 0,0. |
| maskingMode | Egy paraméter, amely különböző módokon teszi lehetővé az észlelt személyes adatok maszkolását a bemeneti szövegben. A következő lehetőségek támogatottak: <ul><li>`none` (alapértelmezett): Ez azt jelenti, hogy nem történik maszkolás, és a rendszer nem adja vissza a `maskedText` kimenetét. </li><li> `redact`: ezzel a beállítással a rendszer eltávolítja az észlelt entitásokat a bemeneti szövegből, és nem cseréli azokat semmire. Vegye figyelembe, hogy ebben az esetben a `piiEntities` kimenet eltolása az eredeti szöveggel lesz összefüggésben, nem a maszkolt szöveggel. </li><li> `replace`: Ez a beállítás felülírja az észlelt entitásokat a `maskingCharacter` paraméterben megadott karakterrel.  A rendszer megismétli a karaktert az észlelt entitás hosszára, hogy az eltolások megfelelően megfeleljenek a bemeneti szövegnek és a kimeneti `maskedText`nak.</li></ul> |
| maskingCharacter | A szöveg maszkolására szolgáló karakter, ha a `maskingMode` paraméter `replace`értékre van állítva. A következő lehetőségek támogatottak: `*` (alapértelmezett), `#`, `X`. Ezt a paramétert csak akkor lehet `null`, ha `maskingMode` nincs beállítva `replace`értékre. |


## <a name="skill-inputs"></a>Szaktudás bemenetei

| Bemeneti név      | Leírás                   |
|---------------|-------------------------------|
| languageCode  | Választható. Az alapértelmezett szint a `en`.  |
| szöveg          | Az elemezni kívánt szöveg.          |

## <a name="skill-outputs"></a>Szaktudás kimenetei

| Kimenet neve     | Leírás                   |
|---------------|-------------------------------|
| piiEntities | Összetett típusok tömbje, amely a következő mezőket tartalmazza: <ul><li>szöveg (a tényleges személyes adatok kinyerése)</li> <li>type</li><li>Altípus</li><li>pontszám (a magasabb érték azt jelenti, hogy nagyobb a valószínűsége, hogy valódi entitás lesz)</li><li>eltolás (a bemeneti szövegbe)</li><li>hossza</li></ul> </br> [Itt megtalálhatja a lehetséges típusokat és altípusokat.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/named-entity-types?tabs=personal) |
| maskedText | Ha a `maskingMode` a `none`tól eltérő értékre van beállítva, akkor ez a kimenet a bemeneti szövegen végrehajtott maszkolás karakterlánc-eredményét adja meg a kiválasztott `maskingMode`szerint.  Ha a `maskingMode` `none`re van beállítva, akkor ez a kimenet nem jelenik meg. |

##  <a name="sample-definition"></a>Minta definíciója

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
##  <a name="sample-input"></a>Minta bemenet

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

##  <a name="sample-output"></a>Példa kimenet

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


## <a name="error-and-warning-cases"></a>Hiba-és figyelmeztetési esetek
Ha a dokumentumhoz tartozó nyelvi kód nem támogatott, a rendszer figyelmeztetést ad vissza, és egyetlen entitás sincs kibontva.
Ha a szöveg üres, a rendszer figyelmeztetést készít.
Ha a szöveg 50 000 karakternél nagyobb, akkor csak az első 50 000 karakter lesz elemezve, és a rendszer figyelmeztetést ad ki.

Ha a szakértelem figyelmeztetést ad vissza, előfordulhat, hogy a kimeneti `maskedText` üres.  Ez azt jelenti, hogy ha azt szeretné, hogy a kimenet a későbbi készségekbe való bevitelhez is rendelkezésre áll, nem fog megfelelően működni. Tartsa szem előtt ezt a készségkészlet-definíció írásakor.

## <a name="see-also"></a>Lásd még:

+ [Beépített szaktudás](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)

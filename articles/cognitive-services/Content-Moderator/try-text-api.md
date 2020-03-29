---
title: Szöveg moderálása a Szövegmoderálás API használatával – Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: Test-drive szöveges moderálás segítségével a szöveg moderálás API-t az online konzolon.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: e0930558f31b27a77fa2cd6b44fcea2fe9091086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74538827"
---
# <a name="moderate-text-from-the-api-console"></a>Mérsékelt szöveg az API-konzolról

Az Azure Content Moderator [szövegmoderálási API-jával](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) a szöveges tartalom káromkodását kése bírja, és összehasonlíthatja az egyéni és megosztott listákkal.

## <a name="get-your-api-key"></a>Az API-kulcs beszerezése

Mielőtt tesztelheti az API-t az online konzolon, szüksége van az előfizetési kulcsra. Ez a **Beállítások** lapon, az **Ocp-Apim-Subscription-Key** mezőben található. További információkért lásd az [Áttekintést](overview.md).

## <a name="navigate-to-the-api-reference"></a>Navigálás az API-hivatkozásra

Nyissa meg a [Szövegmoderálás API hivatkozását.](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) 

  Megnyílik **a Szöveg - Képernyő** lap.

## <a name="open-the-api-console"></a>Az API-konzol megnyitása

Az **Open API tesztelési konzol,** válassza ki a régiót, amely a legszorosabban leírja a helyét. 

  ![Szöveg – Képernyőoldal területének kijelölése](images/test-drive-region.png)

  Megnyílik **a Text - Screen** API konzol.

## <a name="select-the-inputs"></a>A bemenetek kiválasztása

### <a name="parameters"></a>Paraméterek

Válassza ki a szövegképernyőn használni kívánt lekérdezési paramétereket. Ebben a példában használja a **nyelv**alapértelmezett értékét. Üresen is hagyhatja, mert a művelet a végrehajtás részeként automatikusan felismeri a valószínű nyelvet.

> [!NOTE]
> A **nyelvi** paraméter, `eng` rendelje hozzá, vagy hagyja üresen, hogy a gép által támogatott **besorolási** válasz (előzetes funkció). **Ez a funkció csak az angol nyelvet támogatja.**
>
> A **káromkodási kifejezések** észleléséhez használja a cikkben felsorolt támogatott nyelvek [ISO 639-3 kódját,](http://www-01.sil.org/iso639-3/codes.asp) vagy hagyja üresen.

Az **automatikus javításhoz**, **a személyazonosításra alkalmas**adatokhoz és a **besoroláshoz (előnézet)** válassza a **True**lehetőséget. Hagyja üresen a **ListId mezőt.**

  ![Szöveg – Képernyőkonzol lekérdezési paraméterei](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Tartalomtípus

A **Tartalomtípus mezőben**válassza ki a képernyőzni kívánt tartalom típusát. Ebben a példában használja az alapértelmezett **szöveget/egyszerű** tartalomtípust. Az **Ocp-Apim-Subscription-Key** mezőbe írja be az előfizetési kulcsot.

### <a name="sample-text-to-scan"></a>Mintaszöveg beszkaval

A **Kérelem törzsmezőbe** írjon be egy szöveget. A következő példa egy szándékos elírást mutat be a szövegben.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>A válasz elemzése

A következő válasz az API-ból származó különböző elemzéseket mutatja be. Ez tartalmazza a potenciális káromkodás, személyes adatok, besorolás (előzetes verzió), és az automatikusan javított változat.

> [!NOTE]
> A géppel támogatott "Besorolás" funkció előzetes verzióban érhető el, és csak az angol nyelvet támogatja.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.\r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123.\r\nAlso, 999-99-9999 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 4255550111, IP: 255. 255. 255. 255, 1234 Main Boulevard, Panapolis WA 96555. \r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123. \r\nAlso, 999- 99- 9999 looks like a social security number ( SSN) .",
"Misrepresentation":null,
"PII":{  
  "Email":[  
    {  
      "Detected":"abcdef@abcd.com",
      "SubType":"Regular",
      "Text":"abcdef@abcd.com",
      "Index":32
    }
  ],
  "IPA":[  
    {  
      "SubType":"IPV4",
      "Text":"255.255.255.255",
      "Index":72
    }
  ],
  "Phone":[  
    {  
      "CountryCode":"US",
      "Text":"4255550111",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"425 555 0111",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 123 456 7890",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0234 567 8901",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0456 789 0123",
      "Index":244
    }
  ],
  "Address":[  
    {  
      "Text":"1234 Main Boulevard, Panapolis WA 96555",
      "Index":89
    }
  ],
  "SSN":[  
    {  
      "Text":"999999999",
      "Index":56
    },
    {  
      "Text":"999-99-9999",
      "Index":266
    }
  ]
},
"Classification":{  
  "ReviewRecommended":true,
  "Category1":{  
    "Score":1.5113095059859916E-06
  },
  "Category2":{  
    "Score":0.12747249007225037
  },
  "Category3":{  
    "Score":0.98799997568130493
  }
},
"Language":"eng",
"Terms":[  
  {  
    "Index":21,
    "OriginalIndex":21,
    "ListId":0,
    "Term":"crap"
  }
],
"Status":{  
  "Code":3000,
  "Description":"OK",
  "Exception":null
},
"TrackingId":"2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

A JSON-válasz összes szakaszának részletes magyarázatát a [Szövegmoderálás](text-moderation-api.md) fogalmi útmutatójában talál.

## <a name="next-steps"></a>További lépések

Használja a REST API-t a kódban, vagy kövesse a [.NET SDK rövid útmutatót](dotnet-sdk-quickstart.md) az alkalmazásba való integráláshoz.

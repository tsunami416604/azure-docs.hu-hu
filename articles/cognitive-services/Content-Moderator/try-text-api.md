---
title: Mérsékelt szöveg a szöveges moderálási API használatával – Content Moderator
titleSuffix: Azure Cognitive Services
description: Tesztelje a szöveg moderálását a Text moderációs API használatával az online konzolon.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 467bf7fe26df2f826d6d44d42a9e30b98795232f
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043929"
---
# <a name="moderate-text-from-the-api-console"></a>Mérsékelt szöveg az API-konzolból

Az Azure Content Moderator [szöveges moderálási API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) -ját használva megvizsgálhatja a szöveges tartalmakat, és összehasonlíthatja azokat az egyéni és a megosztott listával.

## <a name="get-your-api-key"></a>Az API-kulcs beszerzése

Mielőtt tesztelni tudja az API-t az online konzolon, szüksége lesz az előfizetési kulcsra. Ez a **Beállítások** lap **OCP-APIM-előfizetés-Key** mezőjében található. További információkért lásd az [Áttekintést](overview.md).

## <a name="navigate-to-the-api-reference"></a>Navigáljon az API-referenciához

Nyissa meg a [szöveges MODERÁLÁS API-referenciáját](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  Megnyílik a **szöveges képernyő** oldal.

## <a name="open-the-api-console"></a>Az API-konzol megnyitása

Az **Open API Testing Console**esetében válassza ki azt a régiót, amely a legszorosabban leírja a helyét. 

  ![Szöveg-képernyő oldal régió kiválasztása](images/test-drive-region.png)

  Megnyílik a **text-Screen** API-konzol.

## <a name="select-the-inputs"></a>A bemenetek kiválasztása

### <a name="parameters"></a>Paraméterek

Válassza ki a szöveges képernyőn használni kívánt lekérdezési paramétereket. Ebben a példában az alapértelmezett értéket használja a **Language (nyelv**) beállításnál. Azt is üresen hagyhatja, mert a művelet végrehajtása során a rendszer automatikusan felismeri a valószínűsíthető nyelvet.

> [!NOTE]
> A **Language** paraméterhez rendeljen `eng`, vagy hagyja üresen, hogy megjelenjen a gép által támogatott **besorolási** válasz (előzetes verzió). **Ez a funkció csak az angol nyelvet támogatja**.
>
> A **trágár kifejezések** észleléséhez használja a cikkben felsorolt támogatott nyelvek [ISO 639-3 kódját](http://www-01.sil.org/iso639-3/codes.asp) , vagy hagyja üresen.

Az **Automatikus javítás** **, a**személyes adatok és a **besorolás (előzetes verzió)** beállításnál válassza az **igaz**lehetőséget. Hagyja üresen a **ListId** mezőt.

  ![Szöveges képernyős konzol lekérdezési paraméterei](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Tartalom típusa

A **Content-Type (tartalom típusa**) mezőben válassza ki a képernyőn megjelenő tartalom típusát. Ebben a példában az alapértelmezett **text/plain** tartalomtípust használjuk. Az **OCP-APIM-Subscription-Key** mezőbe írja be az előfizetési kulcsot.

### <a name="sample-text-to-scan"></a>Vizsgálandó mintaszöveg

A **kérelem törzse** mezőbe írjon be egy szöveget. Az alábbi példa egy szándékos elírást mutat be a szövegben.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>A válasz elemzése

Az alábbi válasz az API különböző bepillantást jeleníti meg. A szolgáltatás tartalmazhatja a lehetséges trágár, személyes és besorolást (előzetes verzió) és az automatikusan javított verziót.

> [!NOTE]
> A géppel támogatott "besorolás" funkció előzetes verzióban érhető el, és csak az angol nyelvet támogatja.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
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
      "Text":"6657789887",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"870 608 4000",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 870 608 4000",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0344 800 2400",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0800 820 3300",
      "Index":244
    }
  ],
  "Address":[  
    {  
      "Text":"1 Microsoft Way, Redmond, WA 98052",
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

A JSON-válasz összes részének részletes ismertetését a [szöveges moderálás](text-moderation-api.md) fogalmi útmutatójában találja.

## <a name="next-steps"></a>Következő lépések

Használja a kódban szereplő REST API, vagy kövesse a [.net SDK](dotnet-sdk-quickstart.md) gyors üzembe helyezését az alkalmazással való integráláshoz.

---
title: Mérsékelt szöveg a moderálási API - Content Moderator használatával
titlesuffix: Azure Cognitive Services
description: Az online konzolon a moderálási API használatával a szövegmoderálás próbálhatják ki őket.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 21209548d1cfe7b6eebb1757e817a12c797e78a9
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688837"
---
# <a name="moderate-text-from-the-api-console"></a>Az API-konzolról mérsékelt szöveg

Használja a [szöveg moderálási API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) az Azure Content Moderator trágárság cenzúrázása a szöveges tartalom vizsgálata és összehasonlítása, egyéni és megosztott listájával.

## <a name="get-your-api-key"></a>Az API-kulcs beszerzése

Az API az online konzolon is próbálhatják ki őket, meg kell az előfizetési kulcs. Ez található a **beállítások** lap a **Ocp-Apim-Subscription-Key** mezőbe. További információkért lásd az [Áttekintést](overview.md).

## <a name="navigate-to-the-api-reference"></a>Keresse meg az API-referencia

Nyissa meg a [szöveg moderálási API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  A **szöveg – a képernyő** lap megnyitásakor.

## <a name="open-the-api-console"></a>Nyissa meg az API-konzol

A **Open API tesztelési konzollal**, válassza ki a régiót, amelyben leginkább a tartózkodási ismerteti. 

  ![Szöveg – a lap Képernyőterület kijelölése](images/test-drive-region.png)

  A **szöveg – a képernyő** API-konzol megnyitása.

## <a name="select-the-inputs"></a>Válassza ki a bemenetek

### <a name="parameters"></a>Paraméterek

Válassza ki a szöveget képernyőjén használni kívánt lekérdezési paraméterek. Ebben a példában használja az alapértelmezett érték a **nyelvi**. Akkor is üresen is hagyhatja, mert a művelet valószínűleg nyelvét automatikusan felismeri a végrehajtása során.

> [!NOTE]
> A a **nyelvi** paraméter hozzárendelése `eng` , vagy hagyja üresen, hogy a gépi támogatású **besorolási** válasz (előzetes verziójú funkció). **Ez a funkció támogatja csak angol nyelven**.
>
> A **cenzúrázása feltételek** észlelése, használja a [ISO 639-3 kód](http://www-01.sil.org/iso639-3/codes.asp) ezen a támogatott nyelvek a cikket, vagy hagyja üresen.

A **automatikus javítási**, **személyazonosításra alkalmas adatok**, és **besorolása (előzetes verzió)** válassza **igaz**. Hagyja a **ListId** mezője üres.

  ![Szöveg – a képernyő konzol lekérdezési paraméterek](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Tartalom típusa

A **Content-Type**, válassza ki a kívánt képernyőn tartalom típusát. Ebben a példában használja az alapértelmezett **text/plain** tartalom típusa. Az a **Ocp-Apim-Subscription-Key** adja meg az előfizetési kulcs.

### <a name="sample-text-to-scan"></a>Minta szöveges vizsgálata

Az a **kérelem törzse** mezőbe írjon be valamilyen szöveget. Az alábbi példa bemutatja egy szándékos elgépelte a szövegben.

> [!NOTE]
> Az érvénytelen társadalombiztosítási szám a következő minta szöveg szándékosan így. A célja, hogy továbbítja a minta bemeneti és kimeneti formátumot.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>A válasz elemzése

A következő választ a különféle elemzéseket az API-val jeleníti meg. Tartalomszűrés, a személyes adatok, a besorolás (előzetes verzió) és a az automatikusan kijavíthatók verziót tartalmazza.

> [!NOTE]
> A gépi támogatású "Osztályozás" funkció előzetes verzióban érhető el, és kizárólag angol támogatja.

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

A JSON-választ minden szakasz részletes leírását, tekintse meg a [szövegmoderálás](text-moderation-api.md) fogalmi útmutató.

## <a name="next-steps"></a>További lépések

A REST API használata a kódban, vagy kezdje a [szöveg moderálása .NET – rövid útmutató](text-moderation-quickstart-dotnet.md) integrálhatja az alkalmazást.

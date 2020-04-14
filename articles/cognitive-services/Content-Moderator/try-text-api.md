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
ms.openlocfilehash: ad365c2d4c171105d8dec89d818ef481361d1ff8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272593"
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
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>A válasz elemzése

A következő válasz az API-ból származó különböző elemzéseket mutatja be. Ez tartalmazza a potenciális káromkodás, személyes adatok, besorolás (előzetes verzió), és az automatikusan javított változat.

> [!NOTE]
> A géppel támogatott "Besorolás" funkció előzetes verzióban érhető el, és csak az angol nyelvet támogatja.

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
   "pii":{
      "email":[
         {
            "detected":"abcdef@abcd.com",
            "sub_type":"Regular",
            "text":"abcdef@abcd.com",
            "index":32
         }
      ],
      "ssn":[

      ],
      "ipa":[
         {
            "sub_type":"IPV4",
            "text":"255.255.255.255",
            "index":72
         }
      ],
      "phone":[
         {
            "country_code":"US",
            "text":"6657789887",
            "index":56
         }
      ],
      "address":[
         {
            "text":"1 Microsoft Way, Redmond, WA 98052",
            "index":89
         }
      ]
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

A JSON-válasz összes szakaszának részletes magyarázatát a [Szövegmoderálás](text-moderation-api.md) fogalmi útmutatójában talál.

## <a name="next-steps"></a>További lépések

Használja a REST API-t a kódban, vagy kövesse a [.NET SDK rövid útmutatót](dotnet-sdk-quickstart.md) az alkalmazásba való integráláshoz.

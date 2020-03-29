---
title: Szövegmoderálás - Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: A szövegmoderálást használhatja az esetleges nem kívánt szövegekhez, személyes adatokhoz és egyéni kifejezések listáihoz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 5a07f0749b59efc96b67df3ad5ed2fbf353be614
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74538846"
---
# <a name="learn-text-moderation-concepts"></a>A szövegmoderálási fogalmak megismerése

A tartalommoderátor géppel támogatott szövegmoderálási és [emberi ellenőrzési](Review-Tool-User-Guide/human-in-the-loop.md) képességeivel moderálható a szöveges tartalom.

Házirendjei és küszöbértékei függvényében Ön blokkolhatja, jóváhagyhatja vagy véleményezheti a tartalmat. Használja a környezet emberi moderálásának bővítésére, ahol a partnerek, az alkalmazottak és a fogyasztók szöveges tartalmat hoznak létre. Ilyen környezetnek minősülnek a csevegőszobák, a vitafórumok, a csevegőrobotok, az e-kereskedelmi katalógusok és a dokumentumok. 

A szolgáltatástól érkező válaszban az alábbi információk szerepelnek:

- Káromkodás: kifejezésalapú egyeztetés a különböző nyelveken található profán kifejezések beépített listájával
- Besorolás: gépi asszisztált besorolás három kategóriába
- Személyes adatok
- Automatikusan javított szöveg
- Eredeti szöveg
- Nyelv

## <a name="profanity"></a>Trágár kifejezések

Ha az API bármilyen profán kifejezést észlel a [támogatott nyelvek](Text-Moderation-API-Languages.md)bármelyikén, akkor ezek a kifejezések szerepelnek a válaszban. A válasz tartalmazza az`Index`eredeti szövegben található tartózkodási helyüket is ( ) . A `ListId` következő minta JSON az [egyéni kifejezéslistákban](try-terms-list-api.md) található kifejezésekre utal, ha rendelkezésre állnak.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> A **nyelvi** paraméter, `eng` rendelje hozzá, vagy hagyja üresen, hogy a gép által támogatott **besorolási** válasz (előzetes funkció). **Ez a funkció csak az angol nyelvet támogatja.**
>
> A **káromkodási kifejezések** észleléséhez használja a cikkben felsorolt támogatott nyelvek [ISO 639-3 kódját,](http://www-01.sil.org/iso639-3/codes.asp) vagy hagyja üresen.

## <a name="classification"></a>Osztályozás

A tartalommoderátor gépi **szövegbesorolási funkciója** **csak az angol nyelvet**támogatja, és segít a potenciálisan nem kívánatos tartalmak észlelésében. A megjelölt tartalom a kontextustól függően nem megfelelőnek tekinthető. Ez közvetíti annak valószínűségét, minden kategóriában, és javasolhatja az emberi felülvizsgálat. A funkció egy betanított modellt használ az esetleges visszaélésszerű, becsmérlő vagy diszkriminatív nyelv azonosítására. Ez magában foglalja a szleng, rövidített szavak, sértő, és szándékosan elgépelt szavakat felülvizsgálatra. 

A következő kivonat a JSON kivonat mutatja egy példa kimenet:

    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
            },
        "Category2": {
            "Score": 0.12747249007225037
            },
        "Category3": {
            "Score": 0.98799997568130493
        }
    }

### <a name="explanation"></a>Magyarázat

- `Category1`olyan nyelv jelenlétére utal, amely bizonyos helyzetekben szexuálisan explicitnek vagy felnőttnek tekinthető.
- `Category2`olyan nyelv jelenlétére utal, amely bizonyos helyzetekben szexuálisan szuggesztívnek vagy érettnek tekinthető.
- `Category3`olyan nyelv jelenlétére utal, amely bizonyos helyzetekben sértőnek tekinthető.
- `Score`0 és 1 között van. Minél magasabb a pontszám, annál magasabb a modell azt jósolja, hogy a kategória alkalmazható lehet. Ez a funkció a manuálisan kódolt eredmények helyett egy statisztikai modellen alapul. Javasoljuk, hogy a saját tartalommal tesztelje annak meghatározásához, hogy az egyes kategóriák hogyan igazodjanak az Ön igényeihez.
- `ReviewRecommended`vagy igaz, vagy hamis a belső pontszám küszöbértékek. Az ügyfeleknek fel kell mérniük, hogy ezt az értéket használják-e, vagy a tartalmi irányelveik alapján döntenek-e az egyéni küszöbértékek ről.

## <a name="personal-data"></a>Személyes adatok

A személyes adatok funkció észleli az információk lehetséges jelenlétét:

- E-mail-cím
- Us Mailing cím
- IP-cím
- Amerikai telefonszám
- Uk Telefonszám
- Társadalombiztosítási szám (SSN)

A következő példa egy mintaválaszt mutat be:

```json
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
      "Index":212
    },
    { 
      "CountryCode":"UK",
      "Text":"+123 456 7890",
      "Index":208
    },
    { 
      "CountryCode":"UK",
      "Text":"0234 567 8901",
      "Index":228
    },
    { 
      "CountryCode":"UK",
      "Text":"0456 789 0123",
      "Index":245
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
      "Index":267
    }
  ]
}
```

## <a name="auto-correction"></a>Automatikus korrekció

Tegyük fel, hogy a bemeneti szöveg (az "lzay" és az "f0x" szándékos):

    The qu!ck brown f0x jumps over the lzay dog.

Ha automatikus javítást kér, a válasz a szöveg javított változatát tartalmazza:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Az egyéni kifejezéslisták létrehozása és kezelése

Bár az alapértelmezett, globális kifejezéslista a legtöbb esetben remekül működik, érdemes lehet az üzleti igényekre jellemző kifejezéseket megvizsgálni. Előfordulhat például, hogy ki szeretné szűrni a felhasználók bejegyzéseiből származó versenyképes márkaneveket.

> [!NOTE]
> A maximális korlát **5 kifejezéslista**, amelyek egyenként **nem haladhatják meg a 10 000 kifejezést**.
>

A következő példa a megfelelő listaazonosítót mutatja be:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

A tartalommoderátor [egy kifejezéslista API-t](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) biztosít az egyéni kifejezéslisták kezeléséhez. Kezdje a [Kifejezéslisták API-konzollal,](try-terms-list-api.md) és használja a REST API-kódmintákat. Tekintse meg a [Kifejezéslisták .NET rövid útmutatót](term-lists-quickstart-dotnet.md) is, ha ismeri a Visual Studio és a C# kifejezést.

## <a name="next-steps"></a>További lépések

Tesztelje a [szövegmoderálás API-konzolt,](try-text-api.md) és használja a REST API-kódmintákat. Tekintse meg a [.NET SDK rövidútmutató](dotnet-sdk-quickstart.md) szövegmoderálási szakaszát is, ha ismeri a Visual Studio és a C# programot.

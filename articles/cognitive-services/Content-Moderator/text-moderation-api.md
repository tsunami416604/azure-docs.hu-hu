---
title: Szöveges moderálás – Content Moderator
titleSuffix: Azure Cognitive Services
description: A szöveges moderálás használata a lehetséges nemkívánatos szövegekhez, személyes adatsorokhoz és a kifejezések egyéni listájához.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 1d147dcee31fa4b84b28ab62e8cd41aebc134a9b
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043948"
---
# <a name="learn-text-moderation-concepts"></a>A szöveg moderálásával kapcsolatos fogalmak megismerése

Használjon Content Moderator gépi támogatású szöveg-moderálást és [emberi felülvizsgálati](Review-Tool-User-Guide/human-in-the-loop.md) képességeket a szöveges tartalomhoz.

A szabályzatok és a küszöbértékek alapján blokkolhatja, jóváhagyhatja vagy áttekintheti a tartalmat. Ezzel kibővítheti a környezetek emberi moderálását, ahol a partnerek, az alkalmazottak és a felhasználók szöveges tartalmat hoznak. Ezek közé tartoznak a csevegő termek, a vitafórumok, a csevegőrobotok, az e-kereskedelmi katalógusok és a dokumentumok. 

A szolgáltatás válasza a következő információkat tartalmazza:

- Káromkodás: a kifejezésen alapuló egyeztetés a különböző nyelveken található, a profán kifejezések beépített listájával
- Besorolás: gépi támogatású besorolás három kategóriába
- Személyes adattárolás
- Automatikusan kijavított szöveg
- Eredeti szöveg
- Nyelv

## <a name="profanity"></a>Vulgáris

Ha az API bármely [támogatott nyelven](Text-Moderation-API-Languages.md)észleli a profán kifejezéseket, a válasz tartalmazza ezeket a kifejezéseket. A válasz az eredeti szöveg helyét (`Index`) is tartalmazza. A következő példában szereplő JSON-`ListId` az egyéni feltételekkel rendelkező [listában](try-terms-list-api.md) található kifejezésekre vonatkozik, ha vannak ilyenek.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> A **Language** paraméterhez rendeljen `eng`, vagy hagyja üresen, hogy megjelenjen a gép által támogatott **besorolási** válasz (előzetes verzió). **Ez a funkció csak az angol nyelvet támogatja**.
>
> A **trágár kifejezések** észleléséhez használja a cikkben felsorolt támogatott nyelvek [ISO 639-3 kódját](http://www-01.sil.org/iso639-3/codes.asp) , vagy hagyja üresen.

## <a name="classification"></a>Osztályozás

Content Moderator géppel támogatott **szöveg besorolási funkciója** **csak az angol nyelvet**támogatja, és segít felderíteni a vélhetően nemkívánatos tartalmakat. A megjelölt tartalmat a környezettől függően nem megfelelőként lehet értékelni. Ez az egyes kategóriák valószínűségét közvetíti, és az emberi felülvizsgálatot is javasolhatja. A szolgáltatás egy betanított modellt használ a lehetséges visszaélésszerű, különbözeti vagy diszkriminatív nyelv azonosítására. Ez magában foglalja a szlenget, a rövidített szavakat, a sértőt és a szándékosan hibásan írt szavakat véleményezésre. 

A JSON-kivonat következő kivonata egy példa kimenetet mutat be:

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

- `Category1` a nyelv lehetséges jelenlétére utal, amely bizonyos helyzetekben szexuálisan explicit vagy felnőttnek tekinthető.
- `Category2` a nyelv lehetséges jelenlétére utal, amely bizonyos helyzetekben szexuálisan szuggesztív vagy érett lehet.
- `Category3` a nyelv lehetséges jelenlétére utal, amely bizonyos helyzetekben sértő lehet.
- `Score` 0 és 1 között van. Minél magasabb a pontszám, annál nagyobb a modell, ami azt jelzi, hogy a kategória is alkalmazható. Ez a funkció egy statisztikai modellre támaszkodik, nem pedig manuálisan kódolt eredményeket. Javasoljuk, hogy tesztelje a saját tartalmait annak meghatározásához, hogy az egyes kategóriák hogyan illeszkednek a követelményekhez.
- `ReviewRecommended` értéke igaz vagy hamis a belső pontszám küszöbértéke alapján. Az ügyfeleknek fel kell mérniük, hogy ezt az értéket használják-e, vagy egyéni küszöbértékeket határoznak meg a tartalmi szabályzatok alapján.

## <a name="personal-data"></a>Személyes adattárolás

A személyes adatok funkció észleli az adatok lehetséges jelenlétét:

- E-mail-cím
- USA levelezési címe
- IP-cím
- Egyesült államokbeli telefonszám
- Egyesült Királyság telefonszáma
- Társadalombiztosítási szám (SSN)

A következő példa egy példaként szolgáló választ mutat be:

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
      "Text":"6657789887",
      "Index":56
    },
    { 
      "CountryCode":"US",
      "Text":"870 608 4000",
      "Index":212
    },
    { 
      "CountryCode":"UK",
      "Text":"+44 870 608 4000",
      "Index":208
    },
    { 
      "CountryCode":"UK",
      "Text":"0344 800 2400",
      "Index":228
    },
    { 
      "CountryCode":"UK",
      "Text":"0800 820 3300",
      "Index":245
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
      "Index":267
    }
  ]
}
```

## <a name="auto-correction"></a>Automatikus javítás

Tegyük fel, hogy a bemeneti szöveg (a "lzay" és a "f0x" szándékos):

    The qu!ck brown f0x jumps over the lzay dog.

Ha automatikus javítást kér, a válasz tartalmazza a szöveg javított verzióját:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>A kifejezések egyéni listájainak létrehozása és kezelése

Habár a kifejezések alapértelmezett globális listája a legtöbb esetben remekül működik, érdemes lehet az üzleti igényeknek megfelelő kifejezéseket használni. Előfordulhat például, hogy a felhasználók által létrehozott bejegyzésekből ki szeretné szűrni a versenytárs márkaneveit.

> [!NOTE]
> A maximális korlát **5 kifejezéslista**, amelyek egyenként **nem haladhatják meg a 10 000 kifejezést**.
>

A következő példa a megfelelő lista AZONOSÍTÓját mutatja be:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

A Content Moderator egy [kifejezés-lista API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) -t biztosít az egyéni kifejezések listáinak kezelésére szolgáló műveletekkel. Kezdje a [kifejezést a lists API-konzolra](try-terms-list-api.md) , és használja a REST API kódot. Tekintse meg a [.net rövid](term-lists-quickstart-dotnet.md) útmutatót is, ha ismeri a Visual studiót és C#a-t.

## <a name="next-steps"></a>Következő lépések

Tesztelje a [text moderációs API-konzolt](try-text-api.md) , és használja a REST API-kód mintáit. Tekintse meg a [.net SDK](dotnet-sdk-quickstart.md) gyors útmutató szöveg moderálás szakaszát is, ha már ismeri a Visual studiót C#és a-t.

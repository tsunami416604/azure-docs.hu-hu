---
title: Szövegmoderálás – a Content Moderator
description: Szövegmoderálás használni lehetséges nemkívánatos szöveg, személyazonosításra alkalmas adatok, és az egyéni kifejezések.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/30/2018
ms.author: sajagtap
ms.openlocfilehash: 4c4a0ccfc93a6a48a0178183b94cc03cb576930a
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226568"
---
# <a name="text-moderation"></a>Szövegmoderálás

Használja a Content Moderator szöveg gépi támogatású moderálása és [emberi vizsgálóeszközt](Review-Tool-User-Guide/human-in-the-loop.md) képességek közepes szöveges tartalommal.

Blokkolása, jóváhagyása vagy tekintse át a tartalmat, a házirendek és a küszöbértékek alapján. Használja azt, mivel megvédi a környezetek emberi moderálás, partnerek, az alkalmazottak és a fogyasztók készítése a szöveges tartalom. Ezek közé tartozik, csevegőszobák, vitafórumok, látás, e-kereskedelmi katalógusok és dokumentumokat. 

A szolgáltatás válasza az alábbi információkat tartalmazza:

- Káromkodás: kifejezés alapú egyezéseit profán feltételek különböző nyelvű beépített listája
- : Besorolási három kategóriába sorolhatók gépi támogatású besorolás
- Személyes azonosításra alkalmas adatok (PII)
- Automatikusan kijavíthatók szöveg
- Eredeti szöveg
- Nyelv

## <a name="profanity"></a>Káromkodás

Ha az API-t profán vonatkozó használati feltételek észlel valamelyik a [támogatott nyelvek](Text-Moderation-API-Languages.md), ezeket a kifejezéseket szerepelnek a választ. A válasz is tartalmaz a helyükre (`Index`) az eredeti szöveg. A `ListId` JSON található feltételeket hivatkozik az alábbi minta [egyéni kifejezéslisták](try-terms-list-api.md) ha rendelkezésre áll.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> A a **nyelvi** paraméter hozzárendelése `eng` , vagy hagyja üresen, hogy a gépi támogatású **besorolási** válasz (előzetes verziójú funkció). **Ez a funkció támogatja csak angol nyelven**.
>
> A **cenzúrázása feltételek** észlelése, használja a [ISO 639-3 kód](http://www-01.sil.org/iso639-3/codes.asp) ezen a támogatott nyelvek a cikket, vagy hagyja üresen.

## <a name="classification"></a>Besorolás

A Content Moderator a gépi támogatású **szöveg besorolási funkció** támogatja **csak angol nyelven**, és megkönnyíti a nehezen észlelhető potenciálisan nem kívánt tartalmat. A megjelölt tartalom megjelölése kifogásolhatóként környezettől függően előfordulhat, hogy kell értékelni. Ez közvetíti az egyes kategóriák valószínűségét, és javasolhatja egy emberi vizsgálóeszközt. A szolgáltatás a betanított modell azonosíthatja a lehetséges sértő, különbözeti vagy megkülönböztető nyelvet használja. Ez magában foglalja a szleng felismerését, rövidített szavak, tekintse át a sértő és szándékosan kijavítsa a hibásan leírt szavakat. 

A következő kivonatot a JSON-kivonat látható egy példa a kimenetre:

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

- `Category1` nyelv, amely nyíltan explicit vagy felnőtt bizonyos esetekben előfordulhat, hogy tekinteni a lehetséges jelenléte hivatkozik.
- `Category2` nyelv, amely nyíltan kétértelmű vagy érett bizonyos esetekben előfordulhat, hogy tekinteni a lehetséges jelenléte hivatkozik.
- `Category3` nyelv, amely bizonyos helyzetekben bántónak tekinthető lehetséges jelenléte hivatkozik.
- `Score` 0 és 1 között van. Minél nagyobb a pontszám, annál nagyobb a modell becslése, hogy a kategóriához lehet alkalmazni. Ez a szolgáltatás manuálisan kódolt kimenetek helyett egy statisztikai modellt használ. Azt javasoljuk, hogy a saját határozza meg, hogyan az egyes kategóriák az igényeinek megfelelően igazítja a tartalmat a teszteléshez.
- `ReviewRecommended` IGAZ vagy hamis attól függően, a belső pontszám küszöbértékek. Ügyfelek fel kell mérnie, hogy e használja ezt az értéket, vagy hozza meg, a tartalom házirendek alapján egyedi küszöbértékeket.

## <a name="personally-identifiable-information-pii"></a>Személyes azonosításra alkalmas adatok (PII)

A személyazonosításra alkalmas adatok funkció észleli a potenciális jelenléte ezt az információt:

- E-mail-cím
- Egyesült Államok levelezési címe
- IP-cím
- Egyesült Államokbeli telefonszámok esetén
- Egyesült Királyság telefonszám
- Társadalombiztosítási szám (SSN)

Az alábbi példa bemutatja egy mintaválasz:

    "PII": {
        "Email": [{
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 32
            }],
        "IPA": [{
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 72
            }],
        "Phone": [{
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 56
            }, {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 212
            }, {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 208
            }, {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 228
            }, {
            "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 245
            }],
        "Address": [{
            "Text": "1 Microsoft Way, Redmond, WA 98052",
            "Index": 89
            }],
        "SSN": [{
            "Text": "999999999",
            "Index": 56
            }, {
            "Text": "999-99-9999",
            "Index": 267
            }]
        }

## <a name="auto-correction"></a>Automatikus javítás

Tegyük fel, hogy a bemeneti szöveg (a "lzay" és "f0x" szándékosak):

    The qu!ck brown f0x jumps over the lzay dog.

Ha az automatikus javítás, a válaszban a szöveg a javított verzió:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Létrehozásába és kezelésébe az egyedi listákkal feltételek

Bár az alapértelmezett, globális neveinek listáját a legtöbb esetben működik jól, érdemes ellen, amelyek az üzleti igényeinek megfelelő feltételek képernyőn. Például érdemes a bejegyzések a felhasználók bármilyen versenyképes márkanevek szűréséhez.

> [!NOTE]
> A maximális korlát **5 kifejezés sorolja fel** az egyes lista **nem haladhatja meg a 10 000 feltételek**.
>

Az alábbi példa bemutatja a megfelelő lista azonosítója:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

A Content Moderator biztosít egy [kifejezés lista API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) egyéni kifejezés kezelésével kapcsolatos műveleteket sorolja fel. Kezdje a [kifejezés sorolja fel API-konzol](try-terms-list-api.md) , és a REST API-Kódminták. Emellett olvassa el a [kifejezés megjeleníti .NET – rövid útmutató](term-lists-quickstart-dotnet.md) Ha ismeri a Visual Studio és C#.

## <a name="next-steps"></a>További lépések

Próbálja ki a [moderálási API konzolablakba](try-text-api.md) , és a REST API-Kódminták. Emellett olvassa el a [szöveg moderálása .NET – rövid útmutató](text-moderation-quickstart-dotnet.md) Ha ismeri a Visual Studio és C#.

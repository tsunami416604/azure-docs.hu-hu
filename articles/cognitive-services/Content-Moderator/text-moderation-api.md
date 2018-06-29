---
title: Az Azure Content moderátor - szöveg moderálás |} Microsoft Docs
description: Szöveg moderálás használni lehetséges nemkívánatos szöveg, személyazonosításra alkalmas adatok, és egyéni kifejezések sorolja fel.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/30/2018
ms.author: sajagtap
ms.openlocfilehash: 6924807a64cec074d9688eaad158bb9bb638f6bb
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37085759"
---
# <a name="text-moderation"></a>Szövegmoderálás

Használja a tartalom moderátor szövegét gépet támogatású moderálás és [emberi felülvizsgálati](Review-Tool-User-Guide/human-in-the-loop.md) szöveges tartalom mérsékelt képességeket.

Blokkolja, hagyja jóvá vagy tekintse át a tartalmat, a házirendek és a küszöbértékek alapján. Ezzel a környezetek emberi moderálás révén ahol partnerek, az alkalmazottak és a fogyasztóknak készítése text tartalom. Ezek közé tartozik a Csevegés helyiségekben, vitafórumok, chatbots, kereskedelmi katalógusok és dokumentumok. 

A szolgáltatás válasza a következő információkat tartalmazza:

- Profanitás: beépített listájával, profán feltételek különböző nyelvű kifejezés alapú megfelelő
- Besorolás: három kategóriába soroltuk gép támogatású besorolás
- Személyes azonosításra alkalmas adatok (PII)
- Szöveg automatikus javítását
- Eredeti szöveg
- Nyelv

## <a name="profanity"></a>Profanitás

Ha az API-t profán feltételeket észlel bármelyikét a [támogatott nyelveket](Text-Moderation-API-Languages.md), a válaszban szereplő a feltételeket. A válasz is tartalmaz az helyükre (`Index`) az eredeti szöveg. A `ListId` JSON található feltételek hivatkozik a következő mintában [egyéni kifejezés listák](try-terms-list-api.md) ha rendelkezésre áll.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Az a **nyelvi** paraméter, rendelje hozzá `eng` , vagy hagyja üresen, hogy a gép támogatású **besorolás** választ (előzetes verziójú funkciók). **Ez a funkció csak angol támogatja**.
>
> A **Profanitás feltételek** észlelése, használja a [ISO 639-3 kód](http://www-01.sil.org/iso639-3/codes.asp) szerepel a támogatott nyelvekhez cikket, vagy hagyja üresen.

## <a name="classification"></a>Besorolás

Tartalom moderátor a gép támogatású **szöveg besorolási funkció** támogatja **csak angol nyelven**, és segítséget nyújt a potenciálisan nem kívánt tartalom. A megjelölt tartalom megjelölése kifogásolhatóként attól függően, hogy a környezet lehet értékelni. A különböző kategóriájú valószínűségét közvetíti, és javasolhatja emberi áttekintése. A szolgáltatás egy trained model esetleges visszaélést, különbözeti vagy megkülönböztető nyelvi azonosítására használ. Ez magában foglalja a zsargon, rövidített szavakat, felülvizsgálati sértő és szándékosan hibásan szavakat. 

A következő kivonat a JSON kivonatban látható egy példa a kimenetre:

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

- `Category1` nyelv, amely ivarilag explicit vagy bizonyos esetekben felnőtt tekinthetők lehetséges jelenléte hivatkozik.
- `Category2` nyelv, amely ivarilag kétértelmű vagy bizonyos esetekben érett tekinthetők lehetséges jelenléte hivatkozik.
- `Category3` nyelv, amely bizonyos esetekben bántónak tekinthető lehetséges jelenléte hivatkozik.
- `Score` 0 és 1 közé esik. Minél nagyobb a pontszám, annál magasabb a modell becslése, hogy a kategória alkalmazhatók. Ez az előnézet manuálisan kódolt eredményekkel helyett statisztikai modell támaszkodik. Azt javasoljuk, hogy a saját tartalom határozza meg, hogyan legyen a különböző kategóriájú az igényeinek megfelelően tesztelték.
- `ReviewRecommended` IGAZ vagy hamis függően a belső pontszám küszöbértékek. Az ügyfelek fel kell mérnie hogy ezt az értéket, vagy adja meg a tartalom házirendek alapján egyéni küszöbértékeket.

## <a name="personally-identifiable-information-pii"></a>Személyes azonosításra alkalmas adatok (PII)

A személyhez köthető adatokat a szolgáltatás észleli lehetséges ezt az információt:

- E-mail-cím
- USA levelezési címe
- IP-cím
- Egyesült Államokbeli telefonszám
- Egyesült Királyság telefonszám
- Társadalombiztosítási szám (SSN)

A következő példa bemutatja egy mintaválasz:

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

## <a name="auto-correction"></a>Automatikus javítási

Tegyük fel, hogy a bemeneti szöveg (a "lzay" és "f0x" nem szándékos):

    The qu!ck brown f0x jumps over the lzay dog.

Ha automatikus javítási kér, a válasz tartalmazza a szöveg javított verzió:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Létrehozását és kezelését a feltételek egyéni listája

Amíg az alapértelmezett globális listáját működik a legtöbb esetben jól, érdemes lehet szemben, amelyek és az üzleti igényeinek megfelelő feltételek képernyőn. Érdemes lehet például kiszűrését a bármely versenyképes védjegyek, a felhasználók bejegyzéseket.

> [!NOTE]
> Nincs a jelenlegi maximális műveletszámot **5 kifejezés listája** minden listájával, hogy **legfeljebb 10 000 feltételek**.
>

A következő példa bemutatja a megfelelő azonosítója:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

A tartalom moderátor biztosít egy [kifejezés lista API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) egyéni kifejezés kezelésére szolgáló műveletek sorolja fel. Indítsa el a [kifejezés felsorolja API konzol](try-terms-list-api.md) és a REST API-Kódminták használja. Emellett olvassa el a [kifejezés megjeleníti .NET gyors üzembe helyezés](term-lists-quickstart-dotnet.md) Ha ismeri a Visual Studio és a C#.

## <a name="next-steps"></a>További lépések

Tesztelése a [szöveg moderálás API konzol](try-text-api.md) és a REST API-Kódminták használja. Emellett olvassa el a [szöveg moderálás .NET gyors üzembe helyezés](text-moderation-quickstart-dotnet.md) Ha jártas a Visual Studio és a C#.

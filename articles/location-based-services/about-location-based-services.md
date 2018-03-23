---
title: Az Azure Location Based Services áttekintése | Microsoft Docs
description: Az Azure Location Based Services (előzetes verzió) bemutatása
services: location-based-services
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 02/05/2018
ms.topic: overview
ms.service: location-based-services
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 91df49aa3f522ea765698a1d64ed2686606f71f3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="an-introduction-to-azure-location-based-services-preview"></a>Az Azure Location Based Services (előzetes verzió) bemutatása
Az Azure Location Based Services olyan térinformatikai szolgáltatások portfóliója, amelyek elérhetővé teszik a térképek, a keresés, az útválasztás, a forgalom és az időzónák API-jainak elérését. Az Azure OneAPI kompatibilis szolgáltatásainak portfóliója lehetővé teszi, hogy ismerős fejlesztői eszközökkel gyorsan fejlesszen és méretezzen olyan megoldásokat, amelyek helyadatokat tesznek elérhetővé az Azure-megoldásokban. Az Azure Location Based Services a webes és mobilalkalmazások földrajzi kontextusának biztosításához szükséges, friss térképadatokat használó, hatékony térinformatikai funkciókat biztosít minden iparág fejlesztői számára. Az Azure Location Based Services egy Azure One API-kompatibilis REST API-készlet, amely webalapú JavaScript-vezérlővel rendelkezik a rendkívül egyszerű, rugalmas és különböző médiumokon elérhető fejlesztés érdekében. 

Az alábbi videó az Azure Location Based Services szolgáltatást mutatja be.

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Az Azure Location Based Services öt fő szolgáltatást tartalmaz a földrajzi kontextust igénylő Azure-alkalmazások támogatására. Az alábbiakban ezen szolgáltatások mindegyikét részletesen ismertetjük.

**Render Service** – A Render Service segítségéve a fejlesztők térképészeti jellegű webes és mobilalkalmazásokat fejleszthetnek. A szolgáltatás használhat nagy felbontású rasztergrafikákat, amelyek 19 nagyítási szinten érhetők el, vagy teljes körűen testreszabható, vektorformátumú térképeket.

![Azure Location Based Services Map.png](media/about-location-based-services/Introduction_Map.png)

**Route Service** – A Route Service valós infrastruktúra-geometriai számításokat és több közlekedési formához kapcsolódó útvonalakat tartalmaz. A szolgáltatás lehetővé teszi a fejlesztők számára, hogy többféle közlekedési formára (autó, teherautó, kerékpár, gyalog) vonatkozóan számítsák ki az útvonalakat, továbbá figyelembe vegyenek számos egyéb tényezőt, köztük a forgalmi viszonyokat, súlykorlátozásokat vagy a veszélyes anyagok szállítását.

![Azure Location Based Services Route.png](media/about-location-based-services/Introduction_Route.png)

**Search Service** – A Search Service lehetővé teszi a fejlesztők számára, hogy címeket, helyeket és cégjegyzékeket keressenek név, kategória vagy egyéb földrajzi adatok alapján. A Search Service képes [fordított geokódolással](https://en.wikipedia.org/wiki/Reverse_geocoding) meghatározni a címeket és útkereszteződéseket a hosszúsági/szélességi koordináták alapján. 

![Azure Location Based Services Search.png](media/about-location-based-services/Introduction_Search.png)

**Time Zone Service** – A Time Zone Service lehetővé teszi jelenlegi, korábbi és jövőbeli időzónaadatok lekérdezését hosszúsági/szélességi koordináták vagy egy [IANA-azonosító](http://www.iana.org/) alapján. Emellett a Time Zone Service használatával a Microsoft Windows időzóna-azonosítói IANA-időzónákká alakíthatók egy adott időzóna UTC-hez képest számolt eltérésének és az aktuális helyi idejének lekérésével. A Time Zone Service lekérdezéseire adott tipikus JSON-válasz a következőhöz hasonló:

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

**Traffic Service** – A Traffic Service egy fejlesztők számára készült webszolgáltatás-csomag, amellyel forgalmi adatokat igénylő webes és mobilalkalmazásokat fejleszthetnek. A szolgáltatás a következő részekre oszlik:
1. Forgalom áramlása – valós időben megfigyelt menetsebességeket és menetidőket biztosít a hálózaton belüli összes fontos útra vonatkozóan; 
2. Forgalmi zavarok – pontos képet ad az úthálózaton fennálló forgalmi dugókról és zavarokról.

![Azure Location Based Services Traffic](media/about-location-based-services/Introduction_Traffic.png)

Az Azure Location Based Services a mobilitás jegyében készült, valamint különböző platformokon futó alkalmazásokat is képes kiszolgálni, mivel a programozási modell platformfüggetlen és támogatja a JSON-kimeneteket a REST API-k használatával. Az Azure LBS továbbá tartalmaz egy kényelmes JavaScript-térképvezérlőt, amelynek egyszerű programozási modellje lehetővé teszi a webes és mobilalkalmazások gyors és egyszerű fejlesztését. 

Az Azure Location Based Services egy kulcsalapú hitelesítési sémát használ, így a szolgáltatások eléréséhez csupán létre kell hozni egy Azure Location Based Services-fiókot az [Azure Portal](http://portal.azure.com) használatával. A fiókhoz két előre létrehozott kulcs is jár. Használja ezen kulcsok egyikét az Azure Location Based Services szolgáltatás felé küldött kérésekben a helyalapú szolgáltatások közvetlenül az alkalmazásokba való integrálásának megkezdéséhez.

## <a name="unsupported-regions"></a>Nem támogatott régiók
Az API jelenleg nem érhető el az alábbi országokban. Kérjük, ellenőrizze a jelenlegi IP-címét, és győződjön meg arról, hogy az IP-címe helye nem az alábbi nem támogatott országok valamelyikében található.

* Argentína
* Kína
* India
* Marokkó
* Pakisztán
* Dél-Korea

## <a name="relationship-with-bing-maps"></a>Kapcsolat a Bing Térképek szolgáltatással
Az ebben a dokumentumban ismertetett Azure Location Based Services szolgáltatások nem azonosak a Bing Térképek szolgáltatásaival.  Bár számos funkciójuk megegyezik, ezek a szolgáltatások különbözőek, és nem kapcsolódnak egymáshoz.  Ennek az új, külön kezelt Azure-szolgáltatásnak az elérthetősége nem érinti a Bing Térképek termék által biztosított szolgáltatásokat és ütemterveket.

A Microsoft célja ezzel az, hogy választási lehetőséget biztosítson a fejlesztői közösség számára a helymeghatározási szolgáltatások körében.  Az alábbiakban néhány rövid fejlesztői útmutató olvasható közre a különféle felhasználási esetekben és ügyfélkapcsolati helyzetekben használatos szolgáltatásokról.  Vegye figyelembe, hogy ez az útmutató jelenleg csak a nyilvános előzetes verzióban elérhető Azure LBS-re vonatkozik, és frissíteni fogjuk, amikor a termék 2018 folyamán később elérhetővé válik.

| Ügyfél feltételei | Az Azure Location Based Services használata ajánlott | A Bing Térképek használata ajánlott |
| ------------- | ------------- | ------------- |
| Fejlesztési környezet | Más Azure-szolgáltatásokban történő létrehozás vagy azok használata | Harmadik féltől származó felhő vagy más fejlesztői környezet használata |
| Fejlesztési fázis  | Az Azure LBS jelenleg nyilvános előzetes verzióban érhető el, ezért a korai fázisú teszteléshez és megvalósíthatósági vizsgálatokhoz van optimalizálva | Éles környezetben vállalati szintű SLA-ra van szükség |
| Árképzési lehetőségek | Az előzetes fejlesztői árképzési lehetőségek elegendők | Testre szabott vállalati szintű árképzés szükséges |
| Esetkörnyezet használata | Járművön belüli használat szükséges | Nem szükséges járművön belüli használat |
| Földrajzi lefedettség | India, Kína, Japán és Dél-Korea nem szükséges | India, Kína, Japán és Dél-Korea térképes lefedettsége szükséges |
| Térképtartalom | Normál felszíni térképek elegendőek | Műholdas, légi és utcai képek is szükségesek |
| Alapul szolgáló térkép forrása | A TomTom-térképadatokat részesíti előnyben | A HERE-térképadatokat részesíti előnyben |

Regisztráljon [Azure Location Based Services-fiókot még ma](http://aka.ms/azurelbsportal)!

## <a name="next-steps"></a>További lépések

A cikk az Azure Location Based Services (előzetes verzió) szolgáltatásról nyújtott áttekintést. A következő lépésben kipróbálunk egy mintaalkalmazást, amely bemutatja a Location Based Services szolgáltatást, valamint létrehozunk egy átfogó forgatókönyvet a webalkalmazásban.

> [!div class="nextstepaction"]
> [Bemutató interaktív térképes keresés indítása az Azure Location Based Services (előzetes verzió) használatával](quick-demo-map-app.md)
> [Közeli hasznos helyek keresése az Azure Location Based Services használatával](tutorial-search-location.md)

---
title: "Az Azure Location Based Services áttekintése | Microsoft Docs"
description: "Az Azure Location Based Services (előzetes verzió) bemutatása"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: overview
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 9b4b54c3a4cf0ed4350f570259f6997e4398682b
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="an-introduction-to-azure-location-based-services-preview"></a>Az Azure Location Based Services (előzetes verzió) bemutatása
Az Azure Location Based Services olyan térinformatikai szolgáltatások portfóliója, amelyek elérhetővé teszik a térképek, a keresés, az útválasztás, a forgalom és az időzónák API-jainak elérését. Az Azure OneAPI kompatibilis szolgáltatásainak portfóliója lehetővé teszi, hogy ismerős fejlesztői eszközökkel gyorsan fejlesszen és méretezzen olyan megoldásokat, amelyek helyadatokat tesznek elérhetővé az Azure-megoldásokban. Az Azure Location Based Services a webes és mobilalkalmazások földrajzi kontextusának biztosításához szükséges, friss térképadatokat használó, hatékony térinformatikai funkciókat biztosít minden iparág fejlesztői számára. Az Azure Location Based Services egy Azure One API-kompatibilis REST API-készlet, amely webalapú JavaScript-vezérlővel rendelkezik a rendkívül egyszerű, rugalmas és különböző médiumokon elérhető fejlesztés érdekében. 

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

Regisztráljon [Azure Location Based Services-fiókot még ma!](http://aka.ms/azurelbsportal)

## <a name="next-steps"></a>Következő lépések

A cikk az Azure Location Based Services (előzetes verzió) szolgáltatásról nyújtott áttekintést. A következő lépésben kipróbálunk egy mintaalkalmazást, amely bemutatja a Location Based Services szolgáltatást, valamint létrehozunk egy átfogó forgatókönyvet a webalkalmazásban.

> [!div class="nextstepaction"]
> [Bemutató interaktív térképes keresés indítása az Azure Location Based Services (előzetes verzió) használatával](quick-demo-map-app.md)
> [Közeli hasznos helyek keresése az Azure Location Based Services használatával](tutorial-search-location.md)
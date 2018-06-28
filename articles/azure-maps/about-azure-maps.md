---
title: Az Azure Maps áttekintése | Microsoft Docs
description: Az Azure Maps bemutatása
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 239134e7665b80ef78b6a3df12c14156bff9ae29
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317058"
---
# <a name="an-introduction-to-azure-maps"></a>Az Azure Maps bemutatása
Az Azure Maps olyan térinformatikai szolgáltatások portfóliója, amelyek elérhetővé teszik a térképek, a keresés, az útválasztás, a forgalom és az időzónák API-jainak elérését. A szolgáltatásportfólió lehetővé teszi, hogy ismerős eszközökkel gyorsan fejlesszen és skálázzon olyan megoldásokat, amelyek helyadatokat biztosítanak az Azure-megoldásokban. Az Azure Maps a webes és mobilalkalmazások földrajzi kontextusának biztosításához szükséges, friss térképadatokat használó, hatékony térinformatikai funkciókat biztosít minden iparág fejlesztői számára. Az Azure Maps egy REST API-készlet, amely webalapú JavaScript-vezérlővel rendelkezik az egyszerű, rugalmas és különböző médiumokon elérhető fejlesztés érdekében. 

A következő videó bemutatja az Azure Mapset:

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Az Azure Maps öt fő szolgáltatást tartalmaz a földrajzi kontextust igénylő Azure-alkalmazások támogatására. Ezen szolgáltatások mindegyikét részletesen ismertetjük.

A **Render Service** segítségével a fejlesztők térképészeti jellegű webes és mobilalkalmazásokat fejleszthetnek. A szolgáltatás használhat nagy felbontású rasztergrafikákat, amelyek 19 nagyítási szinten érhetők el, vagy teljes körűen testreszabható, vektorformátumú térképeket.

![Azure Maps – Map.png](media/about-azure-maps/Introduction_Map.png)

A **Route Service** valós infrastruktúra-geometriai számításokat és több közlekedési formához kapcsolódó útvonalakat tartalmaz. A szolgáltatás lehetővé teszi a fejlesztők számára, hogy többféle közlekedési formára (autó, teherautó, kerékpár, gyalog) vonatkozóan számítsák ki az útvonalakat. Olyan tényezők figyelembe vételére is képes, mint a forgalmi viszonyok, a súlykorlátozások vagy a veszélyes anyagok szállítása.

![Azure Maps – Route.png](media/about-azure-maps/Introduction_Route.png)

A **Search Service** lehetővé teszi a fejlesztők számára, hogy címeket, helyeket és bejegyzett vállalkozásokat keressenek név, kategória vagy egyéb földrajzi adatok alapján. A Search Service képes [fordított geokódolással](https://en.wikipedia.org/wiki/Reverse_geocoding) meghatározni a címeket és útkereszteződéseket a hosszúsági/szélességi koordináták alapján. 

![Azure Maps – Search.png](media/about-azure-maps/Introduction_Search.png)

A **Time Zone Service** lehetővé teszi a jelenlegi, korábbi és jövőbeli időzónaadatok lekérdezését hosszúsági/szélességi koordináták vagy egy [IANA-azonosító](http://www.iana.org/) alapján. Emellett a Time Zone Service használatával a Microsoft Windows időzóna-azonosítói IANA-időzónákká alakíthatók egy adott időzóna UTC-hez képest számolt eltérésének és az aktuális helyi idejének lekérésével. A Time Zone Service lekérdezéseire adott tipikus JSON-válasz a következő mintához hasonló:

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

A **Traffic Service** egy fejlesztők számára készült webszolgáltatás-csomag, amellyel forgalmi adatokat igénylő webes és mobilalkalmazások fejleszthetők. A szolgáltatás két adattípust biztosít:
* Forgalom áramlása – valós időben megfigyelt menetsebességek és menetidők a hálózaton belüli összes fontos útra vonatkozóan; 
* Forgalmi zavarok – pontos kép az úthálózaton fennálló forgalmi dugókról és zavarokról.

![Azure Maps – Forgalom](media/about-azure-maps/Introduction_Traffic.png)

Az Azure Maps a mobilitás jegyében készült, valamint különböző platformokon futó alkalmazásokat is képes kiszolgálni, mivel a programozási modell platformfüggetlen és támogatja a JSON-kimeneteket a REST API-k használatával. Az Azure Maps továbbá tartalmaz egy kényelmes JavaScript-térképvezérlőt, amelynek egyszerű programozási modellje lehetővé teszi a webes és mobilalkalmazások gyors és egyszerű fejlesztését. 

Az Azure Maps egy kulcsalapú hitelesítési sémát használ, így a szolgáltatások eléréséhez csupán létre kell hozni egy Azure Maps-fiókot az [Azure Portalon](http://portal.azure.com). A fiókhoz két előre létrehozott kulcs is jár. Használja ezen kulcsok egyikét az Azure Maps szolgáltatás felé küldött kérésekben a helyalapú szolgáltatások közvetlenül az alkalmazásokba való integrálásának megkezdéséhez.

## <a name="unsupported-regions"></a>Nem támogatott régiók
Az Azure Maps API jelenleg nem érhető el minden országban. Ellenőrizze a jelenlegi IP-címét, és győződjön meg arról, hogy az IP-címének a helye nem az alábbi nem támogatott országok valamelyikében található:

* Argentína
* Kína
* India
* Marokkó
* Pakisztán
* Dél-Korea

## <a name="next-steps"></a>További lépések

A cikk az Azure Mapsről nyújtott áttekintést. A következő lépésben kipróbálunk egy mintaalkalmazást, amely bemutatja a szolgáltatást.

> [!div class="nextstepaction"]
> [Bemutató interaktív kereséses térkép indítása](quick-demo-map-app.md)

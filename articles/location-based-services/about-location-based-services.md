---
title: "Azure-beli hely áttekintése alapú szolgáltatások |} Microsoft Docs"
description: "Megismerkedhet az Azure-alapú helyszolgáltatás (előzetes verzió)"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: article
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c8ebce06a72bcaf769a11ec954702463d7489aa0
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="an-introduction-to-azure-location-based-services-preview"></a>Megismerkedhet az Azure-alapú helyszolgáltatás (előzetes verzió)
Az Azure-alapú helyszolgáltatás egy portfóliót földrajzi szolgáltatás, amely tartalmazza az API-k szolgáltatás Maps, Search, útválasztás, forgalom és időzóna. Azure OneAPI kompatibilis szolgáltatások portfóliót lehetővé teszi a megszokott fejlesztőeszközök segítségével gyorsan fejlesztése és helyére vonatkozó információkat integrálása az Azure megoldások méretezési megoldásokat. Az Azure-alapú helyszolgáltatás hatékony földrajzi képességek csomagolt friss leképezési adatok dolgozik a webes és mobilalkalmazások földrajzi környezetet biztosít az összes iparágakban a fejlesztők biztosít. Az Azure-alapú helyszolgáltatás egy egy Azure egy API-val kompatibilis REST API-k egy webalapú JavaScript-vezérléssel együtt, hogy fejlesztési super könnyen, rugalmas és hordozható több adathordozók között. 

Az Azure-alapú helyszolgáltatás földrajzi környezetet igénylő Azure alkalmazásokhoz fokozni öt elsődleges szolgáltatásból áll. Egyes szolgáltatások esetén, tekintse meg az alábbi részletesen.

**Szolgálatot** – célja, hogy a leképezési szolgáltatás a fejlesztők számára webes és mobilalkalmazásokhoz körül leképezés létrehozásához. A szolgáltatás minőségi bitképes képek, 19 nagyítási szint érhető el, vagy teljes mértékben testreszabható vektoros formátum térképi képeit használja.

![Azure-beli hely alapú szolgáltatások Map.png](media/about-location-based-services/Introduction_Map.png)

**Útvonal-szolgáltatás** – az útválasztási szolgáltatás valós robusztus infrastruktúrát geometriai számítások és több szállítására mód irányban épül. A szolgáltatás lehetővé teszi a fejlesztők számára a irányban kiszámításához utazás módok autó, teherautó, kerékpárgyártó vagy érdekében; például méretét valamint a bemeneti adatok, például forgalom feltételek, a súly korlátozások vagy veszélyes jelentős átviteli száma.

![Azure-beli hely alapú szolgáltatások Route.png](media/about-location-based-services/Introduction_Route.png)

**Keresési szolgáltatás** – a keresési szolgáltatás végzi a fejlesztők címek, helyek, név vagy a kategória üzleti listák és egyéb földrajzi információk keresése. A keresési szolgáltatás is [geocode fordított](https://en.wikipedia.org/wiki/Reverse_geocoding) címek és alhálózatok közötti utca a szélesség/hosszúsági alapján. 

![Azure-beli hely alapú szolgáltatások Search.png](media/about-location-based-services/Introduction_Search.png)

**Időzóna szolgáltatás** – az időzóna-szolgáltatás lehetővé teszi a lekérdezés aktuális, a korábbi és a jövőbeni időzóna-információk használata vagy szélességi-hosszúsági párok vagy egy [IANA azonosítója](http://www.iana.org/). Az időzóna-szolgáltatás is lehetővé teszi, hogy a Microsoft Windows időzóna azonosítók konvertálása IANA időzónák, UTC időzóna-eltolódás beolvasása és a jelenlegi időpontnál első megfelelő időzónára. Egy tipikus JSON-válasz egy lekérdezés az időzóna szolgáltatáshoz a következőképpen néznek:

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

**Szolgáltatás forgalom** – a forgalom szolgáltatás webes és mobilalkalmazásokhoz forgalom igénylő létrehozásához a fejlesztők számára tervezett webszolgáltatások együttese. Az elérhető a következő ossza fel:
1. Forgalom bonyolódjon - biztosít a valós idejű megfigyelt sebesség és a hálózat; minden kulcs utak idejének utazik Emellett a 
2. Incidensek forgalom - forgalom dzsemek és incidensek a közúti hálózati körül pontos nézete.

![Azure-beli hely alapú szolgáltatások forgalmát](media/about-location-based-services/Introduction_Traffic.png)

Az Azure-alapú helyszolgáltatás a mobilitás épül, és platformfüggetlen alkalmazások is teljesítmény, mivel független a programozási modellel, és támogatja a JSON-kimenetét REST API-k segítségével. Ezenfelül az Azure LBS egy kényelmes JavaScript térkép vezérlőelem a web- és mobilalkalmazások gyors könnyen fejlesztési egyszerű programozási modellt biztosít. 

Az Azure-alapú helyszolgáltatás egy kulcs-alapú hitelesítési sémát használja, a el a szolgáltatások, így kell navigáljon a [Azure-portálon](http://portal.azure.com) és Azure hely alapú Services-fiók létrehozása. Fiókja meg előre generált két kulcsot tartalmaz. Indítsa el a integrálása ezen hely képességek közvetlenül az alkalmazások az Azure-alapú helyszolgáltatás kéréseket a kulcsok használatával.

Regisztráljon egy [Azure-alapú helyszolgáltatás fiók még ma!](http://aka.ms/azurelbsportal)

## <a name="next-steps"></a>Következő lépések

Most már rendelkezik egy áttekintést az Azure-alapú helyszolgáltatás (előzetes verzió). A következő lépés egy mintaalkalmazást a területeknek a hely alapú szolgáltatások kipróbálására, valamint hozzon létre egy végpont forgatókönyvben a webalkalmazás-hoz.

> [!div class="nextstepaction"]
> [Indítsa el a bemutató interaktív térkép keresések Azure hely alapú szolgáltatások (előzetes verzió)](quick-demo-map-app.md)
> [közelben pont használatával, Azure-alapú helyszolgáltatás keresés](tutorial-search-location.md)
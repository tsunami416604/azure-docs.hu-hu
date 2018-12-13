---
title: Előzetes verziójú REST API-t az Azure Search 2017. 11. 11 – előzetes verzió – Azure Search szolgáltatásban
description: Az Azure Search szolgáltatás REST API-verzió 2017-11-11-előzetes szinonimák és moreLikeThis keresések kísérleti funkciókat tartalmaz.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 06/28/2018
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: e496e4e2e7e2f66033d090a0534911bff4c53baa
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310273"
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Az Azure Search szolgáltatás REST api-verzió 2017. 11. 11 – előzetes verzió
Ez a cikk ismerteti a `api-version=2017-11-11-Preview` verzióját az Azure Search szolgáltatás REST API-t kínáló kísérleti funkciók nem általánosan érhetők el.

> [!NOTE]
> Előzetes verziójú funkciók érhetők el a teszteléshez és kísérletezés a cél az, hogy visszajelzéseket, és változhatnak. Határozottan javasoljuk éles üzemi alkalmazások pedig az API-k előzetes tanúsítványokkal szemben.


## <a name="new-in-2017-11-11-preview"></a>2017. 11. 11 – előzetes verzió újdonságai

[**Automatikus kitöltés** ](search-autocomplete-tutorial.md) összekapcsolja a meglévő [javaslatok API](https://docs.microsoft.com/rest/api/searchservice/suggestions) hozzáadni a kiegészítő gépelés közbeni során lép fel, a keresősávba. Automatikus kitöltés adja vissza jelölt felhasználó dönthet a lekérdezési karakterlánc egy későbbi kereséshez lekérdezési kifejezéseket. Javaslatok az tényleges dokumentumokból a részleges bemenetek válaszul adja vissza: keresési eredmények azonnali és dinamikusan változik, a keresési kifejezés bemeneti növekedésével és sajátlagossága figyelembe.

[**A kognitív keresés**](cognitive-search-concept-intro.md), az Azure Search új Adatbővítés funkció megkeresi segít a rejtett adatokat nem szöveges forrásokból és magánháztartás szöveg-, az Azure Search teljes szöveges kereshető tartalmakká átalakítja őket. A következő források vannak vagy módosított a REST API előzetes verzióban érhető el. Más REST API-k ugyanaz, akár hívja az általánosan elérhető az előzetes verzióval.

+ [Képességcsoport operations(api-version=2017-11-11-Preview)](https://docs.microsoft.com/rest/api/searchservice/skillset-operations)

+ [Indexelő létrehozása (api-version = 2017-11-11-előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

+ [Előre megadott képesség](cognitive-search-predefined-skills.md)

Minden más REST API-k függetlenül állíthatja be, hogy az api-version ugyanaz. Ha például `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11-Preview` és `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11` (nélkül `Preview`) funkcionalitásukat tekintve azonosak.

## <a name="other-preview-features"></a>Egyéb előzetes verziójú funkciók

A korábbi előzetes verziók bejelentett funkciók vannak nyilvános előzetes verzióként. Ha egy korábbi előzetes api-verzióval rendelkező API hívása, akkor továbbra is azt a verziót, vagy váltson `2017-11-11-Preview` várt viselkedés módosítása nélkül.

+ [CSV-fájlok az Azure Blob-indexelés](search-howto-index-csv-blobs.md), a bevezetett `api-version=2015-02-28-Preview`, továbbra is előzetes verziójú funkció. Ez a funkció az Azure Blob-indexelés részét képezi, és egy paraméter beállítása keresztül meghívott. Minden sor egy CSV-fájl egy különálló dokumentumként indexelve van.

+ [Az Azure Blob-indexelés JSON-tömbök](search-howto-index-json-blobs.md), a bevezetett `api-version=2015-02-28-Preview`, továbbra is előzetes verziójú funkció. Ez a funkció az Azure Blob-indexelés részét képezi, és egy paraméter beállítása keresztül meghívott. Ha a tömb egyes elemei indexelt különálló dokumentumként.

+ [moreLikeThis lekérdezési paraméter](search-more-like-this.md) talál, amely egy adott dokumentum a dokumentumokat. Ez a funkció a korábbi verziókra lett. 


## <a name="how-to-call-a-preview-api"></a>Egy előzetes verziójú API meghívása

Régebbi verziókra továbbra is működőképesek, de idővel elavulttá váltak. Ha a kód `api-version=2016-09-01-Preview` vagy `api-version=2015-02-28-Preview`, ezeket a hívásokat továbbra is érvényesek. Azonban csak a legújabb előzetes verzióra frissül javításait. 

A következő példa szintaxist az előzetes API verzió hívását mutatja be.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

Az Azure Search szolgáltatás több verzió érhető el. További információkért lásd: [API-verziók](search-api-versions.md).

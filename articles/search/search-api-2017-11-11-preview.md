---
title: Az Azure Search szolgáltatás REST API-verzió 2017 11-11 – előzetes verzió |} Microsoft Docs
description: Az Azure Search szolgáltatás REST API-verzió 2017 11-11-előzetes verzió például szinonimák és moreLikeThis keresések kísérleti funkciót tartalmaz.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/17/2018
ms.author: HeidiSteen
ms.openlocfilehash: afcc8ac31c45c1a43d3d759ef6f5a1cee8166c0a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Az Azure Search szolgáltatás REST API-ja: Verzió 2017 11-11 – előzetes verzió
Ez a cikk ismerteti a `api-version=2017-11-11-Preview` Azure Search szolgáltatás REST API-t az ajánlat kísérleti funkciók nem még általánosan elérhető verziójának.

> [!NOTE]
> Előzetes verziójú funkciók érhetők el a teszteléshez és kísérletezhet azzal a céllal, a visszajelzéseket és bármikor megváltozhat. Kifejezetten ajánlott termelési alkalmazások API-k megtekintés ellen.


## <a name="new-in-this-preview"></a>Ebben az előzetes verzióban új

+ [Kognitív keresési](cognitive-search-concept-intro.md), az Azure Search új dúsító képessége nem szöveges források és magánháztartás szöveg átalakítása azt a teljes szöveges kereshető tartalom az Azure Search rejtett információkat talál.

A következő két művelet bevezetett, vagy a REST API minta módosítva. Minden más REST API-k megegyeznek a nyilvánosan elérhető hívás vagy (például előzetes verzió.

+ [Hozzon létre Skillset (api-version = 2017-11-11 – előzetes verzió)](ref-create-skillset.md)

+ [Hozzon létre indexelőt (api-version = 2017-11-11 – előzetes verzió)](ref-create-indexer.md)

Minden más REST API-k ugyanazok a nyilvánosan elérhető hívás vagy előzetes verzió. Például `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11-Preview` és `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11` (nélkül `Preview`) megegyezik.

## <a name="other-preview-features"></a>Más előzetes verziójú funkciók

A korábbi előzetes funkciók továbbra is szerepelnek, nyilvános előzetes verziójához.

+ [CSV-fájlok az Azure Blob indexelő](search-howto-index-csv-blobs.md), ahol egy CSV-fájlban szereplő minden egyes sor indexelése különálló dokumentumként.

+ [Az Azure Blob indexelő JSON-tömbök](search-howto-index-json-blobs.md), ahol a tömb egyes elemei indexelt különálló dokumentumként.

+ [`moreLikeThis` lekérdezésparaméter](search-more-like-this.md) , amelyek megfelelnek egy adott dokumentum a dokumentumok kereséséhez. Ez a szolgáltatás le lett korábbi előzetes verziójú funkciók. Ha ez az API korábbi verziójával api-hívás, továbbra is használhatja.


## <a name="how-to-call-a-preview-api"></a>Hogyan hívhatja meg a minta API

Régebbi előzetes továbbra is működik, de időbeli elavult válik. Ha a kód `api-version=2016-09-01-Preview` vagy `api-version=2015-02-25-Preview`, ezeket a hívások továbbra is érvényesek. Azonban csak a legújabb előzetes verziójával kapcsolatos fejlesztések frissül. 

A következő példa szintaxist API előzetes verzióját hívását mutatja be.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

Az Azure Search szolgáltatás több verzióban érhető el. További információkért lásd: [API-verziók](search-api-versions.md).

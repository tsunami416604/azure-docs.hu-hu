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
ms.openlocfilehash: ce5771777762414a0229cf83425c2f3601cb979a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655233"
---
# <a name="azure-search-service-rest-api-version-2017-11-11-preview"></a>Az Azure Search szolgáltatás REST api-version 2017-11-11 – előzetes
Ez a cikk ismerteti a `api-version=2017-11-11-Preview` Azure Search szolgáltatás REST API-t az ajánlat kísérleti funkciók nem még általánosan elérhető verziójának.

> [!NOTE]
> Előzetes verziójú funkciók érhetők el a teszteléshez és kísérletezhet azzal a céllal, a visszajelzéseket és bármikor megváltozhat. Kifejezetten ajánlott termelési alkalmazások API-k megtekintés ellen.


## <a name="new-in-2017-11-11-preview"></a>Új 2017-11-11-Preview

[Kognitív keresési](cognitive-search-concept-intro.md), az Azure Search új dúsító képessége nem szöveges források és magánháztartás szöveg átalakítása azt a teljes szöveges kereshető tartalom az Azure Search rejtett információkat talál.

A következő reources rendszerben jelent meg, vagy a REST API minta módosítva. Minden más REST API-k ugyanazok a nyilvánosan elérhető hívás vagy előzetes verzió.

+ [Hozzon létre Skillset (api-version = 2017-11-11 – előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)

+ [Hozzon létre indexelőt (api-version = 2017-11-11 – előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)

Minden más REST API-k megegyeznek az api-version beállításától függetlenül. Például `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11-Preview` és `GET https://[service name].search.windows.net/indexes/hotels?api-version=2017-11-11` (nélkül `Preview`) megegyezik.

## <a name="other-preview-features"></a>Más előzetes verziójú funkciók

A korábbi előzetes bejelentette funkció továbbra is nyilvános előzetes el. Ha egy API-t egy korábbi verziójával előnézeti api-hívás, továbbra is használhatja, vagy váltson `2017-11-11-Preview` az elvárt viselkedés nem történtek változások.

+ [CSV-fájlok az Azure Blob indexelő](search-howto-index-csv-blobs.md), a továbbfejlesztett `api-version=2015-02-28-Preview`, előzetes verziójú funkciók marad. Ez a funkció az Azure Blob indexelő része, és egy paraméter-beállítás segítségével meghívott. Soronként egy CSV-fájlban szereplő indexelt különálló dokumentumként.

+ [Az Azure Blob indexelő JSON-tömbök](search-howto-index-json-blobs.md), a továbbfejlesztett `api-version=2015-02-28-Preview`, előzetes verziójú funkciók marad. Ez a funkció az Azure Blob indexelő része, és egy paraméter-beállítás segítségével meghívott. Ha a tömb egyes elemei indexelt különálló dokumentumként.

+ [moreLikeThis lekérdezésparaméter](search-more-like-this.md) talál, amelyek megfelelnek egy adott dokumentum a dokumentumokat. Ez a szolgáltatás le lett korábbi előzetes verziójú funkciók. 


## <a name="how-to-call-a-preview-api"></a>Hogyan hívhatja meg a minta API

Régebbi előzetes továbbra is működik, de időbeli elavult válik. Ha a kód `api-version=2016-09-01-Preview` vagy `api-version=2015-02-28-Preview`, ezeket a hívások továbbra is érvényesek. Azonban csak a legújabb előzetes verziójával kapcsolatos fejlesztések frissül. 

A következő példa szintaxist API előzetes verzióját hívását mutatja be.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2017-11-11-Preview

Az Azure Search szolgáltatás több verzióban érhető el. További információkért lásd: [API-verziók](search-api-versions.md).

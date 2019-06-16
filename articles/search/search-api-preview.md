---
title: Az Azure Search a 2019-05-06-Preview - előzetes verzió a REST API-t az Azure Search
description: Az Azure Search szolgáltatás REST API-verzió a 2019-05-06-előzetes Tudásbázis store és az ügyfél által felügyelt titkosítási kulcsok kísérleti funkciókat tartalmaz.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 5374ff896613dd8f8563a2054be8a92103e63fbb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523902"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>Az Azure Search szolgáltatás REST api-verzió a 2019-05-06-előzetes verzió
Ez a cikk ismerteti a `api-version=2019-05-06-Preview` verzióját az Azure Search szolgáltatás REST API-t kínáló kísérleti funkciók nem általánosan érhetők el.

> [!NOTE]
> Előzetes verziójú funkciók érhetők el a teszteléshez és kísérletezés a cél az, hogy visszajelzéseket, és változhatnak. Határozottan javasoljuk éles üzemi alkalmazások pedig az API-k előzetes tanúsítványokkal szemben.


## <a name="new-in-2019-05-06-preview"></a>A 2019-05-06-Preview újdonságai

[**Tudásbázis store** ](knowledge-store-concept-intro.md) egy új cél-felderítési bővítést mesterséges intelligencián alapuló folyamat. Az index kívül ki van töltve az Azure storage-ban az indexelés során létrehozott adatstruktúrák most megmarad. A képességek alkalmazási lehetőségét, többek között az adatok lesznek formázva, hogy az adatok tárolása a Table storage- vagy Blob storage, és hogy van-e több nézetet elemeinek keresztül az adatok fizikai struktúrák szabályozhatja.

[**Ügyfél által felügyelt titkosítási kulcsok** ](search-security-manage-encryption-keys.md) Szolgáltatásoldali titkosítás inaktív is van egy új előzetes verziójú funkció. A beépített titkosítási inaktív a Microsoft felügyeli, mellett további, ahol Ön kizárólagos tulajdonosa, a kulcsok titkosítási réteget is alkalmazhat.

## <a name="other-preview-features"></a>Egyéb előzetes verziójú funkciók

A korábbi előzetes verziók bejelentett funkciók vannak nyilvános előzetes verzióként. Ha egy korábbi előzetes api-verzióval rendelkező API hívása, akkor továbbra is azt a verziót, vagy váltson `2019-05-06-Preview` várt viselkedés módosítása nélkül.

+ [moreLikeThis lekérdezési paraméter](search-more-like-this.md) talál, amely egy adott dokumentum a dokumentumokat. Ez a funkció a korábbi verziókra lett. 
* [Fürt megosztott kötetei szolgáltatás blob-indexelés](search-howto-index-csv-blobs.md) szöveges blob kiszolgálónként egy dokumentum figyelésekor soronként egy új dokumentumot hoz létre.
* [MongoDB API támogatása a Cosmos DB-indexelő](search-howto-index-cosmosdb.md) előzetes verzióban érhető el.


## <a name="how-to-call-a-preview-api"></a>Egy előzetes verziójú API meghívása

Régebbi verziókra továbbra is működőképesek, de idővel elavulttá váltak. Ha a kód `api-version=2016-09-01-Preview` vagy `api-version=2017-11-11-Preview`, ezeket a hívásokat továbbra is érvényesek. Azonban csak a legújabb előzetes verzióra frissül javításait. 

A következő példa szintaxist az előzetes API verzió hívását mutatja be.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Az Azure Search szolgáltatás több verzió érhető el. További információkért lásd: [API-verziók](search-api-versions.md).

## <a name="next-steps"></a>További lépések

Tekintse át az Azure Search szolgáltatás REST API dokumentációja. Ha problémákat tapasztal, megkérdezi a Súgó a [StackOverflow](https://stackoverflow.com/) vagy [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Keresési szolgáltatás REST API-referencia](https://docs.microsoft.com/rest/api/searchservice/)
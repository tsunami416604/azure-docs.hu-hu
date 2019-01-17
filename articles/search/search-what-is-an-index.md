---
title: Index definíciója és fogalmak – Azure Search
description: Index kifejezések és fogalmak az Azure Search, beleértve a fizikai structre fordított indexet bemutatása.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/08/2017
ms.custom: seodec2018
ms.openlocfilehash: 5a39021367c2f51125876081e9174eb372d7b9c9
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54353158"
---
# <a name="indexes-and-indexing-overview-in-azure-search"></a>Indexek és az Azure Search szolgáltatásban az indexelő áttekintése

Az Azure Search- *index* állandó tárolója van *dokumentumok* és egyéb szerkezetek szűrt és a teljes szöveges keresés az Azure Search szolgáltatás használt. A dokumentum az indexben lévő kereshető adatok egyedi egysége. Az elektronikus kereskedelemmel foglalkozó ügyfelek például minden egyes értékesített áru, egy hírközlő szervezet pedig minden egyes cikk esetében rendelkezhet dokumentumokkal. Ezen fogalmak ismertebb, adatbázisbeli megfelelőivel élve: az *index* koncepcionálisan egy *táblához* hasonlít, a *dokumentumok* pedig nagyjából a tábla *sorainak* felelnek meg.

Hozzáadásakor vagy dokumentumok feltöltése vagy keresési lekérdezéseknek az Azure Search küld kérelmeket az adott indexének a search szolgáltatás. Dokumentumok hozzáadása egy indexbe nevezzük *indexelő*.

## <a name="field-types-and-attributes-in-an-azure-search-index"></a>Mezőtípusok és -attribútumok egy Azure Search-indexben
A séma meghatározásakor az index minden egyes mezőjéhez nevet, típust és attribútumokat kell rendelni. Az adott mezőben található adatok osztályozása a mező típusa szerint történik. Az egyes mezők használati módjának megadásához attribútumokat állítunk be. Az itt megadható típusokat és attribútumokat az alábbi tábla sorolja fel.

### <a name="field-types"></a>Mezőtípusok
| Típus | Leírás |
| --- | --- |
| *Edm.String* |A teljes szöveges keresés (például szóhatároló, származtató) érdekében lehetőség van a szöveg tokenekre bontására. |
| *Collection(Edm.String)* |A teljes szöveges keresés érdekében lehetőség van a sztringlista tokenekre bontására. Az egyes gyűjteményekben lévő elemek számának nincs elméleti felső korlátja, a 16 MB-os adattartalom-méretkorlát azonban a gyűjteményekre is érvényes. |
| *Edm.Boolean* |Igaz/hamis értékeket tartalmaz. |
| *Edm.Int32* |32 bites egész számok. |
| *Edm.Int64* |64 bites egész számok. |
| *Edm.Double* |Kétszeres pontosságú numerikus adatok. |
| *Edm.DateTimeOffset* |A dátum- és időértékek OData V4 formátumban (például `yyyy-MM-ddTHH:mm:ss.fffZ` vagy `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`) jelennek meg. |
| *Edm.GeographyPoint* |A pont egy konkrét földrajzi helyet jelöl. |

Részletesebb információkat az Azure Search által [támogatott adattípusokról itt](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) talál.

### <a name="field-attributes"></a>Mezőattribútumok
| Attribútum | Leírás |
| --- | --- |
| *Kulcs* |Az egyes dokumentumok egyedi azonosítóját megadó sztring, amelyet a dokumentumok megkeresésére használunk. Minden egyes indexnek egy kulccsal kell rendelkeznie. A kulcs kizárólag egyetlen mező lehet, annak típusa pedig Edm.String kell legyen. |
| *Lekérhető* |Megadja, hogy az adott mező visszaadható-e egy keresési eredményben. |
| *Szűrhető* |Lehetővé teszi az adott mező szűrőlekérdezésekben történő használatát. |
| *Rendezhető* |Lehetővé teszi egy lekérdezés számára, hogy az adott mezőt használja egy rendezés alapjaként. |
| *Értékkorlátozó* |Lehetővé teszi az adott mező [értékkorlátozott navigációs](search-faceted-navigation.md) szerkezetben történő használatát a felhasználó által önállóan irányított szűrések során. Általában olyan ismétlődő értékeket tartalmazó mezők, amelyek dokumentumok csoportosítására használhatók (például adott márkához vagy szolgáltatási kategóriához tartozó dokumentumok esetében). |
| *Kereshető* |Azt jelzi, hogy az adott mező teljes szöveges keresésre alkalmas. |

Részletesebb információkat az Azure Search [indexattribútumairól itt](https://docs.microsoft.com/rest/api/searchservice/Create-Index) talál.

## <a name="guidance-for-defining-an-index-schema"></a>Útmutatás az indexsémák meghatározásához
Az index kialakításakor szánjon időt a tervezési fázisban az egyes döntések átgondolására. Fontos, hogy az index megtervezésekor a felhasználóként szerzett keresési tapasztalatának és üzleti igényeinek szem előtt tartásával járjon el, és az egyes mezőkhöz a [megfelelő attribútumokat](https://docs.microsoft.com/rest/api/searchservice/Create-Index) rendelje. Az index üzembe helyezést követő módosítása annak újraépítésével és az adatok újbóli feltöltésével jár.

Amennyiben az adattárolási követelmények idővel változnak, a partíciók hozzáadásával vagy eltávolításával növelheti vagy csökkentheti a rendszer kapacitását. Részletes információkat [A Search szolgáltatás kezelése az Azure-ban](search-manage.md) vagy a [Szolgáltatásokra vonatkozó korlátozások](search-limits-quotas-capacity.md) című cikkekben talál.


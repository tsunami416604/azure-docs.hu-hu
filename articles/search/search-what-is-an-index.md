<properties
    pageTitle="Azure Search-index létrehozása | Microsoft Azure | Üzemeltetett felhőalapú keresési szolgáltatás"
    description="Mi az Azure Search-index, és hogyan használható?"
    services="search"
    documentationCenter=""
authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# Azure Search-index létrehozása
> [AZURE.SELECTOR]
- [Áttekintés](search-what-is-an-index.md)
- [Portál](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

## Mit jelent az index?

Az *index* az Azure Search szolgáltatás által használt *dokumentumok* és egyéb szerkezetek állandó tárolója. A dokumentum az index egyedi egységként létező, kereshető adatsora. Az elektronikus kereskedelemmel foglalkozó ügyfelek például minden egyes értékesített áru, egy hírközlő szervezet pedig minden egyes cikk esetében rendelkezhet dokumentumokkal. Ezen fogalmak ismertebb, adatbázisbeli megfelelőivel élve: az *index* koncepcionálisan egy *táblához* hasonlít, a *dokumentumok* pedig nagyjából a tábla *sorainak* felelnek meg.

Dokumentumok hozzáadásakor/feltöltésekor, illetve keresési lekérdezéseknek az Azure Search szolgáltatásba történő küldésekor a kérések a Search szolgáltatás adott indexének lesznek elküldve.

## Mezőtípusok és -attribútumok egy Azure Search-indexben

A séma meghatározásakor az index minden egyes mezőjéhez nevet, típust és attribútumokat kell rendelni. Az adott mezőben található adatok osztályozása a mező típusa szerint történik. Az egyes mezők használati módjának megadásához attribútumokat állítunk be. Az itt megadható típusokat és attribútumokat az alábbi tábla sorolja fel.


### Mezőtípusok
|Típus|Leírás|
|------------|-----------|
|*Edm.String*|A teljes szöveges keresés (például szóhatároló, származtató) érdekében lehetőség van a szöveg tokenekre bontására.|
|*Gyűjtemény (Edm.String)*|A teljes szöveges keresés érdekében lehetőség van a karakterlánclista tokenekre bontására. Az egyes gyűjteményekben lévő elemek számának nincs elméleti felső korlátja, a 16 MB-os adattartalom-méretkorlát azonban a gyűjteményekre is érvényes.|
|*Edm.Boolean*|Igaz/hamis értékeket tartalmaz.|
|*Edm.Int32*|32 bites egész számok.|
|*Edm.Int64*|64 bites egész számok.|
|*Edm.Double*|Kétszeres pontosságú numerikus adatok.|
|*Edm.DateTimeOffset*|A dátum- és időértékek OData V4 formátumban (például `yyyy-MM-ddTHH:mm:ss.fffZ` vagy `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`) jelennek meg.|
|*Edm.GeographyPoint*|A pont egy konkrét földrajzi helyet jelöl.|

Részletesebb információkat az Azure Search által [támogatott adattípusokról az MSDN-en](https://msdn.microsoft.com/library/azure/dn798938.aspx) talál.



### Mezőattribútumok
|Attribútum|Leírás|
|------------|-----------|
|*Kulcs*|Az egyes dokumentumok egyedi azonosítóját megadó karakterlánc, amelyet a dokumentumok megkeresésére használunk. Minden egyes indexnek egy kulccsal kell rendelkeznie. A kulcs kizárólag egyetlen mező lehet, annak típusa pedig Edm.String kell legyen.|
|*Lekérdezhető*|Megadja, hogy az adott mező visszaadható-e egy keresési eredményben.|
|*Szűrhető*|Lehetővé teszi az adott mező szűrőlekérdezésekben történő használatát.|
|*Rendezhető*|Lehetővé teszi egy lekérdezés számára, hogy az adott mezőt használja egy rendezés alapjaként.|
|*Értékkorlátozó*|Lehetővé teszi az adott mező [értékkorlátozott navigációs](search-faceted-navigation.md) szerkezetben történő használatát a felhasználó által önállóan irányított szűrések során. Általában olyan ismétlődő értékeket tartalmazó mezők, amelyek dokumentumok csoportosítására használhatók (például adott márkához vagy szolgáltatási kategóriához tartozó dokumentumok esetében).|
|*Kereshető*|Azt jelzi, hogy az adott mező teljes szöveges keresésre alkalmas.|

Részletesebb információkat az Azure Search [index attribútumairól az MSDN-en](https://msdn.microsoft.com/library/azure/dn798941.aspx) talál.



## Útmutatás az indexsémák meghatározásához

Az index kialakításakor szánjon időt a tervezési fázisban az egyes döntések átgondolására. Fontos, hogy az index megtervezésekor a felhasználóként szerzett keresési tapasztalatának és üzleti igényeinek szem előtt tartásával járjon el, és az egyes mezőkhöz a [megfelelő attribútumokat](https://msdn.microsoft.com/library/azure/dn798941.aspx) rendelje. Az index üzembe helyezést követő módosítása annak újraépítésével és az adatok újbóli feltöltésével jár.


Amennyiben az adattárolási követelmények idővel változnak, a partíciók hozzáadásával vagy eltávolításával növelheti vagy csökkentheti a rendszer kapacitását. Részletes információkat [A Search szolgáltatás kezelése az Azure-ban](search-manage.md) vagy a [Szolgáltatásokra vonatkozó korlátozások](search-limits-quotas-capacity.md) című cikkekben talál.



<!--HONumber=sep16_HO1-->



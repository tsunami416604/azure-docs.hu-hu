<properties
    pageTitle="Az Azure Search-index lekérdezése az Azure portál használatával | Microsoft Azure | Üzemeltetett felhőalapú keresőszolgáltatás"
    description="Keresési lekérdezés küldése az Azure portál Keresési ablakában."
    services="search"
    documentationCenter=""
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="05/31/2016"
    ms.author="ashmaka"
/>
# Az Azure Search-index lekérdezése az Azure portál használatával
> [AZURE.SELECTOR]
- [Áttekintés](search-query-overview.md)
- [Portál](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Ez az útmutató bemutatja, hogyan kérdezhető le az Azure Search-index az Azure portálon.

Az útmutató megkezdése előtt [létre kell hozni egy Azure Search-indexet](search-what-is-an-index.md), amelyet [adatokkal is fel kell tölteni](search-what-is-data-import.md).

## I. Nyissa meg az Azure Search paneljét
1. Kattintson az „Összes erőforrás” pontra az [Azure portál](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) bal oldalán található menüben
2. Válassza ki az Azure Search szolgáltatást

## II. Válassza ki az indexet a kereséshez
1. Az Indexek csempéről válassza ki azt az indexet, amelyben keresést szeretne végezni.

![](./media/search-explorer/pick-index.png)

## III. Kattintson a Keresési ablak csempére
![](./media/search-explorer/search-explorer-tile.png)

## III. Indítson keresést
1. Az Azure Search-indexben végzett kereséshez kezdjen szöveget írni a *Lekérdezési karakterlánc* mezőbe, majd kattintson a **Keresés** gombra.
 * A Keresési ablak használatakor bármely [lekérdezési paramétert](https://msdn.microsoft.com/library/dn798927.aspx) megadhatja

2. Az *Eredmények* területen a lekérdezés eredménye nyers JSON-adatok formájában jelenik meg – ugyanúgy, ahogyan a HTTP-válasz törzsében is megjelennek, amikor keresési kérelmeket küld az Azure Search REST API-nak.
3. A rendszer a lekérdezési karakterláncot automatikusan elemzi, és megfelelő kérelem-URL-címet állít elő belőle, amellyel HTTP-kérelmet tud küldeni az Azure Search REST API-nak.

![](./media/search-explorer/search-bar.png)



<!--HONumber=jun16_HO2-->



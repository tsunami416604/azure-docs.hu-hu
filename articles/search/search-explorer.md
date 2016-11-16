---
title: "Az Azure Search-index lekérdezése az Azure portál használatával | Microsoft Docs"
description: "Keresési lekérdezés küldése az Azure portál Keresési ablakában."
services: search
manager: jhubbard
documentationcenter: 
author: ashmaka
ms.assetid: 8e524188-73a7-44db-9e64-ae8bf66b05d3
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a23372112e17703a3399e1bdc9eaf73b85a1f80d


---
# <a name="query-your-azure-search-index-using-the-azure-portal"></a>Az Azure Search-index lekérdezése az Azure portál használatával
> [!div class="op_single_selector"]
> * [Áttekintés](search-query-overview.md)
> * [Portál](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Ez az útmutató bemutatja, hogyan kérdezhető le az Azure Search-index az Azure portálon.

Az útmutató megkezdése előtt [létre kell hozni egy Azure Search-indexet](search-what-is-an-index.md), amelyet [adatokkal is fel kell tölteni](search-what-is-data-import.md).

## <a name="i-go-to-your-azure-search-blade"></a>I. Nyissa meg az Azure Search paneljét
1. Kattintson az „Összes erőforrás” pontra az [Azure portál](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) bal oldalán található menüben
2. Válassza ki az Azure Search szolgáltatást

## <a name="ii-select-the-index-you-would-like-to-search"></a>II. Válassza ki az indexet a kereséshez
1. Az Indexek csempéről válassza ki azt az indexet, amelyben keresést szeretne végezni.

![](./media/search-explorer/pick-index.png)

## <a name="iii-click-on-the-search-explorer-tile"></a>III. Kattintson a Keresési ablak csempére
![](./media/search-explorer/search-explorer-tile.png)

## <a name="iii-start-searching"></a>III. Indítson keresést
1. Az Azure Search-indexben végzett kereséshez kezdjen szöveget írni a *Lekérdezési karakterlánc* mezőbe, majd kattintson a **Keresés** gombra.
   
   * A Keresési ablak használatakor bármely [lekérdezési paramétert](https://msdn.microsoft.com/library/dn798927.aspx) megadhatja
2. Az *Eredmények* területen a lekérdezés eredménye nyers JSON-adatok formájában jelenik meg – ugyanúgy, ahogyan a HTTP-válasz törzsében is megjelennek, amikor keresési kérelmeket küld az Azure Search REST API-nak.
3. A rendszer a lekérdezési karakterláncot automatikusan elemzi, és megfelelő kérelem-URL-címet állít elő belőle, amellyel HTTP-kérelmet tud küldeni az Azure Search REST API-nak.

![](./media/search-explorer/search-bar.png)




<!--HONumber=Nov16_HO2-->



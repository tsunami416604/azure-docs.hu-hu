---
title: "Index lekérdezése (portál – Azure Search) | Microsoft Docs"
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
ms.date: 07/10/2017
ms.author: ashmaka
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: dd68d8ed073bf7b8666ddef35a2f1f84df690b4b
ms.contentlocale: hu-hu
ms.lasthandoff: 07/12/2017

---
# <a name="query-an-azure-search-index-using-search-explorer-in-the-azure-portal"></a>Azure Search-index lekérdezése a keresési ablakkal az Azure Portalon
> [!div class="op_single_selector"]
> * [Áttekintés](search-query-overview.md)
> * [Portál](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

Ez a cikk bemutatja, hogyan kérdezhet le Azure Search-indexeket a **keresési ablakkal** az Azure Portalon. A keresési ablakkal egyszerű vagy teljes Lucene lekérdezési karakterláncokat küldhet el a szolgáltatásban lévő bármely indexnek.

## <a name="open-the-service-dashboard"></a>A szolgáltatás irányítópultjának megnyitása
1. Az [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) bal oldali gyorselérési sávján kattintson a **Minden erőforrás** elemre.
2. Válassza ki az Azure Search szolgáltatást.

## <a name="select-an-index"></a>Index kiválasztása

Válassza ki az **Indexek** csempén azt az indexet, amelyből keresni kíván.

   ![](./media/search-explorer/pick-index.png)

## <a name="open-search-explorer"></a>A keresési ablak megnyitása

Kattintson a Keresési ablak csempére a keresősáv és az eredményeket tartalmazó ablaktábla megnyitásához.

   ![](./media/search-explorer/search-explorer-tile.png)

## <a name="start-searching"></a>Indítson keresést

A Keresési ablak használatakor [lekérdezési paraméterekkel](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) határozhatja meg a lekérdezést.

1. A **Lekérdezési karakterlánc** területen írjon be egy lekérdezést, majd kattintson a **Keresés** gombra. 

   A rendszer automatikusan átalakítja a lekérdezési sztringet a megfelelő kérelmi URL-címmé, és HTTP-kérelmet küldjön az Azure Search REST API-nak.   
   
   A kérelem létrehozásához bármilyen érvényes egyszerű vagy teljes Lucene lekérdezési szintaxist használhat. A `*` karakter üres vagy nem meghatározott keresésnek felel meg, amely az összes dokumentumot visszaadja véletlenszerű sorrendben.

2. Az **Eredmények** területen a lekérdezési eredmények nyers JSON formátumban jelennek meg, hasonlóan a HTTP-választörzsekben visszaadott hasznos adatokhoz, amikor programozott módon ad ki kéréseket.

   ![](./media/search-explorer/search-bar.png)

## <a name="next-steps"></a>Következő lépések

Az alábbi forrásokban további tudnivalókat és példákat találhat a lekérdezési szintaxisokról.

 + [Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Lucene lekérdezési szintaxis példái](https://docs.microsoft.com/azure/search/search-query-lucene-examples) 
 + [OData szűrési kifejezés szintaxisa](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 

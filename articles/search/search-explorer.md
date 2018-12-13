---
title: Az Azure Portalon az Azure Search – az indexek lekérdezése a keresési ablak
description: Az Azure portal-eszközök például a keresési ablak a lekérdezés indexek használata az Azure Search szolgáltatásban. Adja meg a keresési kifejezéseket, vagy teljesen minősített keresőkifejezések speciális szintaxissal.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 11f102fcb2a24f9062313f9a3234c29e70a3dfe0
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315663"
---
# <a name="how-to-use-search-explorer-to-query-indexes-in-azure-search"></a>Az Azure Search lekérdezési indexeket a keresési ablak használatával 

Ez a cikk bemutatja, hogyan kérdezhet le egy meglévő Azure Search-indexeket a **keresési ablak** az Azure Portalon. A keresési ablak használatával nyújt egyszerű vagy teljes Lucene lekérdezési karakterláncokat küldhet bármely meglévő index a szolgáltatásban.

## <a name="open-the-service-dashboard"></a>A szolgáltatás irányítópultjának megnyitása
1. Az [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) bal oldali gyorselérési sávján kattintson a **Minden erőforrás** elemre.
2. Válassza ki az Azure Search szolgáltatást.

## <a name="select-an-index"></a>Index kiválasztása

Válassza ki az **Indexek** csempén azt az indexet, amelyből keresni kíván.

   ![](./media/search-explorer/pick-index.png)

## <a name="open-search-explorer"></a>Nyissa meg a keresési ablak

Kattintson a keresési ablak csempére a keresősáv megnyitásához a és az eredmények ablaktáblán.

   ![](./media/search-explorer/search-explorer-tile.png)

## <a name="start-searching"></a>Indítson keresést

A keresési ablak használatakor megadhatja [lekérdezési paramétereket](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) határozhatja meg a lekérdezést.

1. A **Lekérdezési sztring** területen írjon be egy lekérdezést, majd kattintson a **Keresés** gombra. 

   A rendszer automatikusan átalakítja a lekérdezési sztringet a megfelelő kérelmi URL-címmé, és HTTP-kérelmet küldjön az Azure Search REST API-nak.   
   
   A kérelem létrehozásához bármilyen érvényes egyszerű vagy teljes Lucene lekérdezési szintaxist használhat. A `*` karakter üres vagy nem meghatározott keresésnek felel meg, amely az összes dokumentumot visszaadja véletlenszerű sorrendben.

2. Az **Eredmények** területen a lekérdezési eredmények nyers JSON formátumban jelennek meg, hasonlóan a HTTP-választörzsekben visszaadott hasznos adatokhoz, amikor programozott módon ad ki kéréseket.

   ![](./media/search-explorer/search-bar.png)

## <a name="next-steps"></a>További lépések

Az alábbi forrásokban további tudnivalókat és példákat találhat a lekérdezési szintaxisokról.

 + [Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Lucene lekérdezési szintaxis példái](https://docs.microsoft.com/azure/search/search-query-lucene-examples) 
 + [OData szűrési kifejezés szintaxisa](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 
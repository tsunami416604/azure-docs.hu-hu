---
title: "Eszközök kapcsolódó adatok megjelenítése az Azure Data Catalog |} Microsoft Docs"
description: "Ez a cikk ismerteti az Azure Data Catalog kiválasztott adategységet eszközeinek kapcsolódó adatok megjelenítése."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/01/2017
ms.author: maroche
ms.openlocfilehash: 0623847ea1e79c3a45a8fbdcc1d3a551864aefd6
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Kapcsolódó adatok eszközök megtekintéséhez az Azure Data Catalog hogyan?
Az Azure Data Catalog lehetővé teszi a kijelölt adatok eszköz és a nézet köztük lévő viszonyt is kapcsolatos adatok eszközök. 

## <a name="supported-data-sources"></a>Támogatott adatforrások 
A következő adatforrásokból származó adatok eszközök regisztrálásakor az Azure Data Catalog automatikusan regisztrálja a kijelölt adatok eszközök közötti illesztési kapcsolatokat metaadatainak. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

## <a name="view-related-data-assets"></a>Kapcsolódó adatok eszközök megtekintése
A kiválasztott adatkészlet kapcsolódó adategységeket megtekintéséhez használja a **kapcsolatok** lapon a következő ábrán látható módon: 

![Az Azure Data Catalog - kapcsolódó adatok eszközök megtekintése](media\data-catalog-how-to-view-related-data-assets\relationships-tab.png)

Ebben a példában a kiválasztott két olyan kapcsolattal vannak **ProductSubcategory** adategységet: 

- A termék tábla ProductSubcategoryID oszlopa Külsőkulcs-kapcsolatot a kijelölt ProductSubcategory táblázat ProductSubcategoryID oszloppal rendelkezik. 
- A ProductSubCategory tábla ProductCategoryID oszlopa Külsőkulcs-kapcsolatot a kijelölt ProductCategory táblázat ProductCategoryID oszloppal rendelkezik.

> [!NOTE]
> Figyelje meg a irányát, a kapcsolatok faszerkezetes nézetben.  

További részletek az oszlop teljesen minősített neve például vigye az egérmutatót, és megjelenik egy előugró ablak az alábbi képen hasonló: 

![Az Azure Data Catalog - kapcsolat előugró ablak](media\data-catalog-how-to-view-related-data-assets\relationship-popup.png)

Adja meg, amely már regisztrált eszközök közötti kapcsolatokat, regisztrálja újra azokat az eszközöket.

## <a name="next-steps"></a>Következő lépések
- [Adategységek felügyelete](data-catalog-how-to-manage.md)
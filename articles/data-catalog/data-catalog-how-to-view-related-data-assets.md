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
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 37d12209d28b73f0d7fc6d940ded344fbeae968d
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Kapcsolódó adatok eszközök megtekintéséhez az Azure Data Catalog hogyan?
Az Azure Data Catalog lehetővé teszi a kijelölt adatok eszköz és a nézet köztük lévő viszonyt is kapcsolatos adatok eszközök. 

## <a name="supported-data-sources"></a>Támogatott adatforrások 
A következő adatforrásokból származó adatok eszközök regisztrálásakor az Azure Data Catalog automatikusan regisztrálja a kijelölt adatok eszközök közötti illesztési kapcsolatokat metaadatainak. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> A Data Catalog két adatok eszközök közötti kapcsolat importálásához regisztrálnia kell az mindkét az eszközök egy időben. Ha egyiket külön kellett hozzáadott, vegye fel újra, és az egyéb adatok eszköz kapcsolat importálása.

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

## <a name="next-steps"></a>További lépések
- [Adategységek felügyelete](data-catalog-how-to-manage.md)
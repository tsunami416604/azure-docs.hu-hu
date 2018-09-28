---
title: Kapcsolódó adategységek megtekintése az Azure Data Catalog
description: Ez a cikk azt ismerteti, hogy a kiválasztott adategységet kapcsolódó adategységek megtekintése az Azure Data Catalog.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 156673bfac9bfa38772e4daca166e3431f81c09a
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405009"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Kapcsolódó adategységek megtekintése az Azure Data Catalog hogyan?
Az Azure Data Catalog lehetővé teszi a kijelölt adatok eszköz és a nézet közötti kapcsolatok őket kapcsolódó adategységek megtekintése. 

## <a name="supported-data-sources"></a>Támogatott adatforrások 
Ha regisztrálja a következő adatforrások származó adategységeket, az Azure Data Catalog automatikusan regisztrálja a kijelölt adategységek közötti illesztési kapcsolatokat metaadatait. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> A Data Catalog két adategységek közötti kapcsolat importálásához regisztrálnia kell az mindkét az eszközök egyszerre. Ha egyik külön-külön korábban hozzáadott, adja hozzá újra, és a többi adategységet közöttük kapcsolat importálásához.

## <a name="view-related-data-assets"></a>Kapcsolódó adategységek megtekintése
A kiválasztott adatkészlet kapcsolódó adategységek megtekintéséhez használja a **kapcsolatok** lapon az alábbi képen látható módon: 

![Az Azure Data Catalog – kapcsolódó adategységek megtekintése](media\data-catalog-how-to-view-related-data-assets\relationships-tab.png)

Ebben a példában a kiválasztott két kapcsolat nincsenek **ProductSubcategory** Adateszköz: 

- A Product tábla oszlopának ProductSubcategoryID kulcskapcsolatban ProductSubcategoryID oszloppal a kiválasztott ProductSubcategory tábla rendelkezik. 
- ProductCategoryID oszlop a ProductSubCategory tábla kulcskapcsolatban, a kiválasztott ProductCategory táblából ProductCategoryID oszloppal rendelkezik.

> [!NOTE]
> Figyelje meg, hogy a irányát, a kapcsolatok faszerkezetes nézetben.  

További részletek például a teljes nevet, az oszlop, vigye az egérmutatót, és megjelenik egy előugró ablak az alábbi képhez hasonló: 

![Az Azure Data Catalog – kapcsolat előugró ablak](media\data-catalog-how-to-view-related-data-assets\relationship-popup.png)

Adja meg a regisztrált eszközök közötti kapcsolatokat, regisztrálja újra azokat az eszközöket.

## <a name="next-steps"></a>További lépések
- [Adategységek felügyelete](data-catalog-how-to-manage.md)
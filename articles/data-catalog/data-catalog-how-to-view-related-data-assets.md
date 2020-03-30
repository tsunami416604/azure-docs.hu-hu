---
title: Kapcsolódó adateszközök megtekintése az Azure Data Catalogban
description: Ez a cikk bemutatja, hogyan tekintheti meg a kijelölt adateszköz kapcsolódó adateszközeit az Azure Data Catalogban.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 212ba647e6eb44e800a589928620f56fba65107c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68737018"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Hogyan tekintheti meg a kapcsolódó adatelemeket az Azure Data Catalogban?
Az Azure Data Catalog lehetővé teszi a kijelölt adateszközhöz kapcsolódó adateszközök megtekintését és a köztük lévő kapcsolatok megtekintését. 

## <a name="supported-data-sources"></a>Támogatott adatforrások 
Amikor a következő adatforrásokból regisztrál adateszközöket, az Azure Data Catalog automatikusan regisztrálja a kijelölt adateszközök közötti illesztési kapcsolatok metaadatait. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> Ahhoz, hogy a Data Catalog két adateszköz közötti kapcsolatot importáljon, mindkét eszközt egyszerre kell regisztrálnia. Ha az egyiket külön adta hozzá, adja hozzá újra, a másik adateszközt pedig a köztük lévő kapcsolat importálásához.

## <a name="view-related-data-assets"></a>Kapcsolódó adategységek megtekintése
A kijelölt adatkészlethez kapcsolódó adateszközök megtekintéséhez használja a **Kapcsolatok** lapot az alábbi képen látható módon: 

![Azure Data Catalog – Kapcsolódó adateszközök megtekintése](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

Ebben a példában két kapcsolat van a kiválasztott **ProductSubcategory** adateszközhöz: 

- A Termék tábla ProductSubcategoryID oszlopának idegen kulcsa van a kiválasztott ProductSubcategory table ProductSubcategoryID oszlopával. 
- A ProductSubCategory tábla ProductCategoryID oszlopának idegen kulcskapcsolata van a kiválasztott ProductCategory tábla ProductCategoryID oszlopával.

> [!NOTE]
> Figyelje meg a nyíl irányát a kapcsolatok fanézetében.  

További részleteket, például az oszlop teljesen minősített nevét szeretné látni, vigye az egeret az egérmutató fölé, és az alábbihoz hasonló előugró ablak jelenik meg: 

![Azure Data Catalog – Kapcsolat felugró ablak](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

A már regisztrált eszközök közötti kapcsolatok felvételéhez regisztrálja újra ezeket az eszközöket.

## <a name="next-steps"></a>További lépések
- [Adategységek felügyelete](data-catalog-how-to-manage.md)
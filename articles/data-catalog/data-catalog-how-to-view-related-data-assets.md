---
title: Kapcsolódó adategységek megtekintése Azure Data Catalog
description: Ez a cikk azt ismerteti, hogyan lehet megtekinteni a kiválasztott adategység kapcsolódó adategységeit Azure Data Catalogban.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: fb5352b4cc84801296d9e621053672328b02692a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86523366"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>Hogyan tekintheti meg a kapcsolódó adategységeket a Azure Data Catalogban?
Azure Data Catalog lehetővé teszi a kiválasztott adategységhez kapcsolódó adategységek megtekintését, valamint a közöttük fennálló kapcsolatok megtekintését. 

## <a name="supported-data-sources"></a>Támogatott adatforrások 
Ha adategységeket regisztrál a következő adatforrásokból, Azure Data Catalog automatikusan regisztrálja a metaadatokat a kiválasztott adategységek közötti csatlakozási kapcsolatokhoz. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> A két adategység közötti kapcsolat importálásához Data Catalog egyszerre regisztrálnia kell az adategységeket. Ha az egyiket külön adta hozzá, adja hozzá újra és a másik adategységet az egymás közötti kapcsolat importálásához.

## <a name="view-related-data-assets"></a>Kapcsolódó adategységek megtekintése
A kiválasztott adatkészlethez kapcsolódó adategységek megtekintéséhez használja a **kapcsolatok** lapot a következő képen látható módon: 

![Azure Data Catalog – kapcsolódó adategységek megtekintése](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

Ebben a példában két kapcsolat van a kiválasztott **ProductSubCategory** adategységhez: 

- A Product tábla ProductSubcategoryID oszlopa idegenkulcs-kapcsolattal rendelkezik a kiválasztott ProductSubcategory-tábla ProductSubcategoryID oszlopával. 
- A ProductSubCategory tábla ProductCategoryID oszlopa idegenkulcs-kapcsolattal rendelkezik a kijelölt ProductCategory tábla ProductCategoryID oszlopával.

> [!NOTE]
> Figyelje meg a nyíl irányát a kapcsolatok fanézetben.  

Ha további részleteket szeretne látni, például az oszlop teljes nevét, vigye az egérmutatót a fölé, és az alábbi képhez hasonló előugró ablak jelenik meg: 

![Azure Data Catalog – kapcsolat előugró ablak](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

Ha a már regisztrált eszközök közötti kapcsolatokat szeretné felvenni, regisztrálja újra az eszközöket.

## <a name="next-steps"></a>További lépések
- [Adategységek felügyelete](data-catalog-how-to-manage.md)

---
title: Az Azure Monitor Log Analytics lekérdezési nyelvre |} A Microsoft Docs
description: Megtudhatja, hogyan kell lekérdezéseket írni a Log Analytics az erőforrásokhoz való hivatkozásokat.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: f8bef8c6ab5c0639f9a99eb2c0443c33d7b6d84e
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244065"
---
# <a name="log-analytics-query-language"></a>Log Analytics lekérdezési nyelve
A log Analytics biztosít az Azure Monitor napló gyűjtése és elemzése. Azure adatkezelő épül és a egy verzióját használja, a lekérdezési nyelvre. A [Azure adatkezelő lekérdezési nyelvi dokumentáció](/azure/kusto/query) rendelkezik az összes részletes adat a nyelv és a Log Analytics-lekérdezések írására szolgáló elsődleges erőforrás kell lennie. Ez az oldal más forrásokra mutató hivatkozásokat biztosít, megtudhatja, hogyan kell lekérdezéseket írni és a nyelvet a Log Analytics végrehajtása kapcsán.

## <a name="getting-started"></a>Első lépések

- [Ismerkedés a Log Analytics használatával az Azure Portalon](get-started-analytics-portal.md) van egy leckében lekérdezések írása és használata az eredményeket az Azure Portalon.
-  [Ismerkedés a Log Analytics lekérdezéseinek](get-started-queries.md) van egy Log Analytics-adatok használatával összetevőjében leckében.

## <a name="concepts"></a>Alapelvek
- [Az Azure monitorban Log Analytics-adatok elemzése](../log-analytics-queries.md) lehetővé teszi a napló rövid áttekintést lekérdezése és ismerteti, hogyan épül fel Log Analytics-adatok.
- [A Log Analytics adatok megtekintésére és elemzésére](../log-analytics-log-search-portals.md) ismerteti a portálok, ahol létrehozni és a Log Analytics-lekérdezések futtatásához.

## <a name="reference"></a>Leírások

- [Lekérdezési nyelvi leírása](/azure/kusto/query) van a teljes nyelvi dokumentáció az adatkezelő lekérdezési nyelv.
- [Log Analytics lekérdezési nyelv különbségek](data-explorer-difference.md) az adatkezelő lekérdezési nyelv verziói közötti különbségeket ismerteti.
- [A Log Analytics-rekordok alapvető tulajdonságainak](../log-analytics-standard-properties.md) összes Log Analytics-adatok a szabványos tulajdonságokat ismerteti.
- [Hajtsa végre az erőforrások közötti naplókeresések a Log Analytics](../log-analytics-cross-workspace-search.md) ismerteti, hogyan lehet több Log Analytics-munkaterületek és Application Insights-alkalmazások adatait használó lekérdezéseket írni.


## <a name="examples"></a>Példák

- [Log Analytics lekérdezési példa](examples.md) biztosít a Log Analytics-adatok használatával például lekérdezések.



## <a name="lessons"></a>Leckék

- [Log Analytics-lekérdezéseket a karakterláncokkal való munka](string-operations.md) a karakterlánc használatát ismerteti.
- [Dátum-idő értékek a Log Analytics-lekérdezések használata](datetime-operations.md) dátum és idő adatok használatát ismerteti. 
- [A Log Analytics-lekérdezések összesítések](aggregations.md) és [speciális összesítések a Log Analytics-lekérdezések](advanced-aggregations.md) ismerteti, hogyan egyesítik és összegzik az adatokat.
- [A Log Analytics-lekérdezések csatlakozik](joins.md) azt ismerteti, hogyan lehet az adatok több táblából.
- [A JSON és használatához adatok struktúrák a Log Analytics-lekérdezések](json-data-structures.md) ismerteti, hogyan lehet json-adatok elemzése.
- [Lekérdezések a Log Analytics speciális írási](advanced-query-writing.md) összetett lekérdezések létrehozása és kód újrafelhasználása vonatkozó stratégiákat ismerteti.
- [Diagramok és ábrák hoz létre a Log Analytics-lekérdezések](charts.md) azt ismerteti, hogyan jelenítheti meg a lekérdezésből származó adatok.

## <a name="cheatsheets"></a>Hasznos tanácsok

-  [SQL-t a Log Analytics-nyelvi segédletünkkel](sql-cheatsheet.md) segít a felhasználóknak, akik már jól ismert SQL-lel.
-  [A Log Analytics-nyelvi segédletünkkel Splunk](sql-cheatsheet.md) segít a felhasználóknak, akik már ismerik az Splunk.
 
## <a name="next-steps"></a>További lépések

- A teljes hozzáférési [referenciadokumentációt az adatkezelő lekérdezési nyelv](/azure/kusto/query/).
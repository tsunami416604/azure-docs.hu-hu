---
title: Mi az Azure szinapszis Analytics (korábban SQL DW)?
description: Az Azure szinapszis Analytics (korábbi nevén SQL DW) egy korlátlan elemzési szolgáltatás, amely a nagyvállalati adattárházat és a Big adatelemzéseket is egyesíti.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: f21f1f897aa6bc1ed5d1151ed23b24d82909d168
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835121"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>Mi az Azure szinapszis Analytics (korábban SQL DW)?

> [!NOTE] 
>Ismerje meg az [Azure szinapszis (munkaterületek előzetes verzió) dokumentációját](../overview-what-is.md).
>

Az Azure szinapszis egy elemzési szolgáltatás, amely egyesíti a vállalati adattárházat és a Big adatelemzést. Lehetővé teszi, hogy saját tetszőleges módon kérje le az adatokat, kiszolgáló nélküli igény szerinti vagy kiosztott erőforrásokkal, nagy mennyiségben. Az Azure szinapszis az azonnali BI-és gépi tanulási igényekhez kapcsolódóan egységes felhasználói élményt nyújt az adatgyűjtéshez, előkészítéséhez, kezeléséhez és kiszolgálásához.

Az Azure szinapszis négy összetevőből áll:

- Szinapszis SQL: teljes T-SQL-alapú elemzés – általánosan elérhető
  - SQL-készlet (fizetés/DWU kiépítve)
  - Igény szerinti SQL-használat (fizetés TB-onként) (előzetes verzió)
- Spark: mélyen integrált Apache Spark (előzetes verzió)
- Szinapszis-folyamatok: hibrid Adatintegráció (előzetes verzió)
- Studio: egyesített felhasználói élmény. (előzetes verzió)

## <a name="synapse-sql-pool-in-azure-synapse"></a>Szinapszis SQL-készlet az Azure Szinapszisban

A szinapszis SQL-készlet az Azure Szinapszisban általánosan elérhető vállalati adattárház-funkciókra utal.

Az SQL-készlet a szinapszis SQL használatakor kiépített analitikus erőforrások gyűjteményét jelöli. Az SQL-készlet méretét az adattárház-egységek (DWU-EK) határozzák meg.

Importálja big data egyszerű, [alapszintű](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL-lekérdezésekkel, majd használja az MPP erejét a nagy teljesítményű elemzések futtatásához. Az integráció és az elemzés során a szinapszis SQL-készlet lesz az igazság egyetlen verziója, amelyet az üzleti vállalkozása a gyorsabb és megbízhatóbb adatelemzéshez is felhasználhat.  

## <a name="key-component-of-a-big-data-solution"></a>Egy big data-megoldás fő összetevője

Az adattárházak a felhőalapú, végpontok közötti big data megoldások egyik kulcsfontosságú összetevője.

![Adattárház-megoldás](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

A felhőalapú adatkezelő megoldásokban az adatok számos forrásból kerülnek a big data-tárakba. A big data-tárakba behúzott adatokat Hadoop-, Spark- és gépi tanulási algoritmusok készítik elő és dolgozzák be az intelligenciába. Ha az adatok készen állnak az összetett elemzésre, a szinapszis SQL-készlet a big data-tárolók lekérdezéséhez használ albase-t. A Base standard T-SQL-lekérdezéseket használ az adatok szinapszis SQL-készlet tábláiba való bevonásához.

A szinapszis SQL Pool oszlopos tárolással rendelkező kapcsolódó táblákban tárolja az adatkészleteket. Ez a formátum jelentős mértékben csökkenti a tárolási költségeket és javítja a lekérdezési teljesítményt. Az adattárolást követően nagy méretekben futtathat elemzéseket. A hagyományos adatbázisrendszerekhez képest az elemzési lekérdezések percek helyett másodpercek, napok helyett órák alatt végeznek.

Az elemzések eredményei globális jelentéskészítési adatbázisokba vagy alkalmazásokba küldhetőek. Az üzleti elemzők így az ezekből nyerhető betekintések révén tájékozott üzleti döntéseket hozhatnak.

## <a name="next-steps"></a>További lépések

- Az [Azure szinapszis architektúrájának](massively-parallel-processing-mpp-architecture.md) megismerése
- [SQL-készlet gyors létrehozása](create-data-warehouse-portal.md)
- [Mintaadatok betöltése](load-data-from-azure-blob-storage-using-polybase.md).
- [Videók](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse) megismerése

Vagy tekintse meg a többi Azure szinapszis-erőforrást.

- Keresési [Blogok](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Szolgáltatási kérelmek](https://feedback.azure.com/forums/307516-sql-data-warehouse) elküldése
- [Támogatási jegy létrehozása](sql-data-warehouse-get-started-create-support-ticket.md)
- Keresés [A Microsoft Q&A kérdéses oldalon](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
- [Stack overflow fórum](https://stackoverflow.com/questions/tagged/azure-sqldw) keresése

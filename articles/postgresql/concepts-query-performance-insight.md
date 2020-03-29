---
title: Lekérdezési teljesítmény insight - Azure-adatbázis postgreSQL - egyetlen kiszolgáló
description: Ez a cikk ismerteti a Lekérdezési teljesítmény Insight funkció az Azure Database for PostgreSQL – Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: dd5b4ec53d82421ddd9d680ca41e48eeecc43c2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768384"
---
# <a name="query-performance-insight"></a>Lekérdezési terheléselemző 

**A következőkre vonatkozik:** Azure Database for PostgreSQL – Egykiszolgálós verzió9.6, 10, 11

A Lekérdezési teljesítmény betekintés segítségével gyorsan azonosíthatja a leghosszabb ideig futó lekérdezéseket, hogyan változnak az idő múlásával, és milyen várakozások befolyásolják őket.

## <a name="permissions"></a>Engedélyek
A Lekérdezési terheléselemző lekérdezéseinek szövegét **Tulajdonos** vagy **Közreműködő** jogosultsággal lehet megjeleníteni. **Olvasó** jogosultsággal a diagramok és táblázatok megtekinthetők, de a lekérdezés szövege nem.

## <a name="prerequisites"></a>Előfeltételek
Ahhoz, hogy a Lekérdezési teljesítmény insight működjön, az adatoknak létezniük kell a [Lekérdezéstárolóban.](concepts-query-store.md)

## <a name="viewing-performance-insights"></a>Teljesítményelemzésmegtekintése
A Microsoft Azure Portal [Lekérdezési terheléselemző](concepts-query-performance-insight.md) nézete a Lekérdezéstárból származó fontos információk vizualizációit jeleníti meg. 

Az Azure Database for PostgreSQL-kiszolgáló portállapján válassza a **Lekérdezésteljesítmény-betekintés** lehetőséget a menüsor **Intelligens teljesítmény** szakaszában.

![Lekérdezési teljesítmény insight hosszú futó lekérdezések](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

A **Hosszú futó lekérdezések** lap az első öt lekérdezést jeleníti meg végrehajtásonkénti átlagos időtartam szerint, 15 perces időközönként összesítve. Több lekérdezés megtekintéséhez válasszon értéket a **Lekérdezések száma** legördülő menüből. A diagram színei ekkor egy adott lekérdezésazonosító tekintetében változhatnak.

Kattintással és húzással egy adott időablakra szűkítheti a diagramot. Másik lehetőségként használja a nagyítási és kicsinyítési ikonokat egy kisebb vagy nagyobb időtartam megtekintéséhez.

A diagram alatti táblázat további részleteket tartalmaz az adott időablakban hosszan futó lekérdezésekről.

A kiszolgálóval kapcsolatos megfelelő várakozási adatok vizualizációinak megjelenítéséhez válassza a **Várakozási statisztikák** fület.

![A Lekérdezési teljesítmény betekintése statisztikát vár](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="considerations"></a>Megfontolandó szempontok
* A Lekérdezési teljesítmény insight nem érhető el [olvasási replikákhoz.](concepts-read-replicas.md)

## <a name="next-steps"></a>További lépések
- További információk az Azure Database for PostgreSQL [monitoringjához és finomhangolásához](concepts-monitoring.md).



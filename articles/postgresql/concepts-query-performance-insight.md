---
title: A PostgreSQL-hez készült Azure Database lekérdezési Terheléselemző
description: Ez a cikk ismerteti a lekérdezési Terheléselemző funkció az Azure Database for postgresql-hez.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/28/2019
ms.openlocfilehash: 56abdd819e78312e64209078c3966826385df7bc
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620733"
---
# <a name="query-performance-insight"></a>Lekérdezési terheléselemző 

**A következőkre vonatkozik:** Azure Database for PostgreSQL 9.6 és 10

Lekérdezési Terheléselemző segít gyorsan azonosítani a leghosszabban futó lekérdezések vannak, hogyan változnak idővel és milyen feladatot hatással vannak, azokat.

## <a name="permissions"></a>Engedélyek
A Lekérdezési terheléselemző lekérdezéseinek szövegét **Tulajdonos** vagy **Közreműködő** jogosultsággal lehet megjeleníteni. **Olvasó** jogosultsággal a diagramok és táblázatok megtekinthetők, de a lekérdezés szövege nem.

## <a name="prerequisites"></a>Előfeltételek
A lekérdezési Terheléselemző függvénynek, adatok szerepelniük kell a [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Teljesítményelemzés megtekintése
A Microsoft Azure Portal [Lekérdezési terheléselemző](concepts-query-performance-insight.md) nézete a Lekérdezéstárból származó fontos információk vizualizációit jeleníti meg. 

Az Azure Database for PostgreSQL-kiszolgáló portáloldalán válassza **lekérdezési teljesítmény Insight** alatt a **intelligens teljesítmény** a menüsávon szakaszában.

![Lekérdezési Terheléselemző hosszú ideig futó lekérdezések](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

A **hosszú ideig futó lekérdezések** lapon látható az öt legaktívabb lekérdezések által végrehajtásonkénti, átlagos időtartam összesített 15 perces időközönként. Több lekérdezés megtekintéséhez válasszon értéket a **Lekérdezések száma** legördülő menüből. A diagram színei ekkor egy adott lekérdezésazonosító tekintetében változhatnak.

Kattintással és húzással egy adott időablakra szűkítheti a diagramot. Azt is megteheti a bejövő és kimenő ikonok a nagyítás segítségével megtekintheti a kisebb vagy nagyobb bizonyos idő jelölik.

A diagramot az alábbi táblázatban további tájékoztatást nyújt a hosszú ideig futó lekérdezéseket az adott időtartományban.

A kiszolgálóval kapcsolatos megfelelő várakozási adatok vizualizációinak megjelenítéséhez válassza a **Várakozási statisztikák** fület.

![Lekérdezési Terheléselemző vár statisztika](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>További lépések
- További információk az Azure Database for PostgreSQL [monitoringjához és finomhangolásához](concepts-monitoring.md).



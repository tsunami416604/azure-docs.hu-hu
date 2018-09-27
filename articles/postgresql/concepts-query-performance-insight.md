---
title: A PostgreSQL-hez készült Azure Database lekérdezési Terheléselemző
description: Ez a cikk ismerteti a lekérdezési Terheléselemző funkció az Azure Database for postgresql-hez.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: cc041104169ca8c4344b9d3de597283d122e63db
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394760"
---
# <a name="query-performance-insight"></a>Lekérdezési terheléselemző 

**A következőkre vonatkozik:** Azure Database for PostgreSQL 9.6 és 10

> [!IMPORTANT]
> A lekérdezési Terheléselemző használata korlátozott számú régióban nyilvános előzetes verzióban érhető el. 

Lekérdezési Terheléselemző segít gyorsan azonosítani a leghosszabban futó lekérdezések vannak, hogyan változnak idővel és milyen feladatot hatással vannak, azokat.

## <a name="permissions"></a>Engedélyek
**Tulajdonos** vagy **közreműködői** a lekérdezési Terheléselemző lekérdezések szövege megtekintéséhez szükséges engedélyekkel. **Olvasó** diagramok és táblázatok megtekintheti, de nem a lekérdezés szövege.

## <a name="prerequisites"></a>Előfeltételek
A lekérdezési Terheléselemző függvénynek, adatok szerepelniük kell a [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Teljesítményelemzés megtekintése
A [lekérdezési Terheléselemző](concepts-query-performance-insight.md) megtekintése az Azure Portalon a Vizualizációk a kulcsadatokat felületen a Query Store. 

Az Azure Database for PostgreSQL-kiszolgáló portáloldalán válassza **lekérdezési teljesítmény Insight** alatt a **támogatás + hibaelhárítás** a menüsávon szakaszában.

![Lekérdezési Terheléselemző hosszú ideig futó lekérdezések](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

A **hosszú ideig futó lekérdezések** lapon látható a 5 leggyakoribb lekérdezések végrehajtásonkénti, átlagos időtartam szerint összesítve a 15 perces időközönként. Több lekérdezés megtekintéséhez válassza ki a **száma a lekérdezések** legördülő menü. A diagram színeinek ekkor egy adott lekérdezés azonosítójú változhatnak.

Kattintson a gombra, és húzza el a diagram egy adott időpont ablak szűkítéséhez. Azt is megteheti a bejövő és kimenő ikonok a nagyítás segítségével megtekintheti a kisebb vagy nagyobb bizonyos idő jelölik.

A diagramot az alábbi táblázatban további tájékoztatást nyújt a hosszú ideig futó lekérdezéseket az adott időtartományban.

Válassza ki a **várjon statisztika** fülre kattintva megtekintheti a megfelelő Vizualizációk a várakozik a kiszolgálón.

![Lekérdezési Terheléselemző várakozási statisztikák](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [megfigyelés és finomhangolás](concepts-monitoring.md) az Azure Database for postgresql-hez.



---
title: A MySQL-hez készült Azure Database lekérdezési Terheléselemző
description: Ez a cikk ismerteti a lekérdezési Terheléselemző funkció az Azure Database for MySQL-hez
author: ajlam
ms.author: andrela
ms.service: MySQL
ms.topic: conceptual
ms.date: 06/05/2019
ms.openlocfilehash: 1243ae8ae20d08ea643661606639abedbc56ab9c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078780"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>A MySQL-hez készült Azure Database lekérdezési Terheléselemző

**A következőkre vonatkozik:**  Azure Database for MySQL 5.7.

> [!NOTE]
> Lekérdezési Terheléselemző az előzetes verzióban. Lekérdezési Terheléselemző támogatása az Azure Portalon tesszük elérhetővé, és előfordulhat, hogy még nem érhető el a régióban.

Lekérdezési Terheléselemző segít gyorsan azonosítani a leghosszabban futó lekérdezések vannak, hogyan változnak idővel és milyen feladatot hatással vannak, azokat.

## <a name="permissions"></a>Engedélyek

**Tulajdonos** vagy **közreműködői** a lekérdezési Terheléselemző lekérdezések szövege megtekintéséhez szükséges engedélyekkel. ** Olvasó** diagramok és táblázatok megtekintheti, de nem a lekérdezés szövege.

## <a name="prerequisites"></a>Előfeltételek

A lekérdezési Terheléselemző függvénynek, adatok szerepelniük kell a [Query Store](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Teljesítményelemzés megtekintése

A Microsoft Azure Portal [Lekérdezési terheléselemző](concepts-query-performance-insight.md) nézete a Lekérdezéstárból származó fontos információk vizualizációit jeleníti meg.

Az Azure Database for MySQL-kiszolgáló portáloldalán válassza **lekérdezési Terheléselemző** alatt a **intelligens teljesítmény** a menüsávon szakaszában.

![Lekérdezési Terheléselemző hosszú ideig futó lekérdezések](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

A **hosszú ideig futó lekérdezések** lapon látható a 5 leggyakoribb lekérdezések által végrehajtásonkénti, átlagos időtartam összesített 15 perces időközönként. Több lekérdezés megtekintéséhez válassza ki a **száma a lekérdezések** legördülő menü. A diagram színei ekkor egy adott lekérdezésazonosító tekintetében változhatnak.

Kattintással és húzással egy adott időablakra szűkítheti a diagramot. Használhatja a bejövő és kimenő ikonok nagyítás egy kisebb vagy nagyobb időszakban rendre megtekintéséhez.

Válassza ki a **várjon statisztika** fülre kattintva megtekintheti a megfelelő Vizualizációk a várakozik a kiszolgálón.

Lekérdezések jelennek meg a várakozási Statisztika nézetben a lekérdezések, amelyeket a legnagyobb vár a megadott időintervallumban szerint vannak csoportosítva.

![Lekérdezési Terheléselemző vár statisztika](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [megfigyelés és finomhangolás](concepts-monitoring.md) az Azure Database for MySQL-hez.